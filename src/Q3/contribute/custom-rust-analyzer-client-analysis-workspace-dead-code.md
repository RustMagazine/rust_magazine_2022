# 定制 rust-analyzer 客户端分析 workspace 级别死代码

作者: 吴翱翔

---

## 分析 workspace 级别的死代码

因为 cargo/rustc 只能分析单个 crate 的死代码，不能分析多个 crate 中某个 crate 的 pub fn 没有被 workspace 使用的情况

所以笔者经常看到很多项目都有些 common/util 包中定义了很多对外不公开的函数

在 workspace 内部没有任何调用，但是函数定义成 `pub fn` rustc 也不会进行死代码检查了

这时候用 rust-analyzer(往后简称为 ra) 的 codeLens/reference 一看引用次数为零确定是死代码

死代码导致编译变慢编译产物变大，我也不可能对每个项目几千个函数逐个点击 find reference 看看引用次数

因此我编写一个 ra 客户端自动扫描 workspace 下所有 public 的函数并自动调用 find reference 检查并报告出死代码

完整实现在我这个代码仓库: <https://github.com/pymongo/lsp_client>

之前国内 Rust 社区也有 workspace 级别死代码分析的尝试: [RustChinaConf2020 - The Practices of Rust Code Analysis](https://github.com/rustcc/RustChinaConf2020/blob/master/rustchinaconf2020/RustChinaConf2020-28.%E5%B0%B9%E6%80%9D%E7%BB%B4-%E3%80%8ARust%20Code%20Analysis%20%E5%AE%9E%E8%B7%B5%E3%80%8B.pdf)

上述分析直接用 ra 源码去分析，容易出现 salsa `Internal error, cycle detected` 报错且 ra 源码老不兼容更新作为库的使用方维护成本很大

调用 ra 源码 project_model::ProjectWorkspace::load 无法得到任何缓存也无法复用 cargo check 缓存

用 project_model 加载项目通常要 10-60s 比 vscode 上 ra 1-2s 加载项目慢得多

由于 LSP 协议的 API 参数基本是稳定不变，所以不如直接写一个 LSP 客户端去调用 ra

## 深入理解 ra

笔者作为 rust-analyzer 的 contributor 贡献着分享一个通过自制 ra 客户端实现 workspace 级别的静态分析查找死代码的功能

个人感觉看 ra 作者的介绍视频不如看源码和写客户端对接效率高，尤其是当我实现了一个 ra 客户端之后对 ra 的理解又加深了好几层

然后 LSP 也熟悉不少，在我 2022 年 8 月先前的 LSP 文章中也详细介绍了 LSP 的通信协议，本文尝试自制 ra 客户端完成 workspace 级别死代码的分析

根据 ra 的 goto_def 例子指引 + LSP 文档我总算摸清 ra 建立连接和断开连接的代码实现

<https://github.com/rust-lang/rust-analyzer/blob/master/lib/lsp-server/examples/goto_def.rs>

### LSP 背景知识

想要理解 ra 工作机制必须先理解 LSP(Language Server Protocol)，LSP 核心概念的简介绍可以看 [我 LSP 介绍的文章](https://pymongo.github.io/#/2022/08/LSP.md)

### ra 的线程池模型

```rust
RequestDispatcher { req: Some(req), global_state: self }
    .on_sync_mut::<lsp_ext::ReloadWorkspace>(handlers::handle_workspace_reload)
    .on_sync::<lsp_ext::OnEnter>(handlers::handle_on_enter)
    .on::<lsp_types::request::Rename>(handlers::handle_rename)
```

如上述 ra 源码的 main_loop.rs 中所示，on_sync 的请求在主线程阻塞执行，其余请求都会分发到线程池异步执行

ra 主要有三类线程: 客户端消息收发线程 + 主线程 + 工作线程

主线程负责将消息分发(dispatcher)到 handler 线程池中

少部分需要修改全局状态的请求只能在主线程阻塞执行，例如重新加载配置

大部分请求消息都异步的扔到线程池去执行，客户端能取消掉执行中的异步请求

### ra 取消请求的实现

如果是异步代码，我能想象到的 async cancel 实现是每个请求都 spawn 一个 task 去执行，记录 request_id -> handle 的映射表，

如果收到取消请求，可以通过 tokio Task handle 的 abort API 中止运行中的协程。如果协程正常运行结束再从映射表移除掉自身的 requestId

由于 ra 是纯同步代码，且 Linux 的 pthread_cancel 不够好用所以在 ra 正在进行中的请求是不能被取消的

只有当如果请求在 pending 待执行的队列中才能被取消

---

## 启动 ra

```rust
let mut lsp_server_process = std::process::Command::new("rust-analyzer")
    .env("RA_LOG", "rust_analyzer=info")
    .stdin(std::process::Stdio::piped())
    .stdout(std::process::Stdio::piped())
    .stderr(unsafe {
        use std::os::unix::prelude::{FromRawFd, IntoRawFd};
        let log_file = std::fs::File::create("target/ra.log").unwrap();
        std::process::Stdio::from_raw_fd(log_file.into_raw_fd())
    })
    .spawn()
    .unwrap();
let req_to_ra = lsp_server_process.stdin.take().unwrap();
let rsp_from_ra = std::io::BufReader::new(lsp_server_process.stdout.take().unwrap());
```

由于我先前 cargo install 过 ra 所以直接 process::Command 就行

ra 的日志过滤的环境变量用的是 RA_LOG 而不是 RUST_LOG

由于 LSP 都用管道通信所以 ra 日志只能打在 stderr 我重定向到一个日志文件中

注意 from_raw_fd 要跟 into_raw_fd 进行 from/into 搭配使用

ra 子进程通过 fork 的时候会自动 CLOSE_EXEC 关掉父进程的日志文件 fd 避免 fd 泄漏

如果 File.as_raw_fd 得到的 fd 是 borrowFd 搭配 from_raw_fd 会不安全

## ra 连接初始化

这个过程没什么好说的，用 lsp-types 库看看请求参数有那些字段再用 lsp-server 库进行序列化反序列化和消息解析

1. client->server: InitializeRequest
2. server->client: InitializeResponse
3. client->server: InitializedNotification

## blocking for cargo check

ra 初始化后会进行耗时很长的 Cargo check, 我们需要随便发一个请求给 ra 不断重试确保 ra 已经完成 cargo check

看 ra 源码也发现 ra 其实用了很 hack 的错误码 ErrorCode::ContentModified 来表达正在 cargo check 拒绝一切请求

但 ContentModified 的本身含义并非如此，事实上 LSP 协议似乎也没有一种错误消息表达服务器正在初始化，繁忙的错误码

```rust
fn wait_rust_analyzer_cargo_check(&mut self) {
    let req = lsp_server::Request {
        id: self.req_id.inc(),
        method: <rust_analyzer::lsp_ext::AnalyzerStatus as Request>::METHOD.to_string(),
        params: serde_json::to_value(&rust_analyzer::lsp_ext::AnalyzerStatusParams {
            text_document: None,
        })
        .unwrap(),
    };
    let start = std::time::Instant::now();
    for delay_ms in [40, 80, 160, 160, 320, 320, 640, 2560, 10240] {
        let mut req_ = req.clone();
        req_.id = self.req_id.inc();
        let msg = lsp_server::Message::Request(req_);
        msg.write(&mut self.req_to_ra).unwrap();
        let rsp = lsp_server::Message::read(&mut self.rsp_from_ra).unwrap().unwrap().as_resp();
        if let Some(err) = rsp.error {
            // error: waiting for cargo metadata or cargo check
            if err.code != lsp_server::ErrorCode::ContentModified as i32 {
                panic!("{err:?}");
            }
        } else {
            println!(
                "rust-analyzer blocking for cargo check total wait is {:?}",
                start.elapsed()
            );
            assert!(rsp.error.is_none());
            return;
        }
        std::thread::sleep(std::time::Duration::from_millis(delay_ms));
    }
    unreachable!("req_to_ra timeout")
}
```

## ra 初始化配置

只需要给 ra 初始化请求的消息里面传入一个 root_uri 字段(也就是想要分析的项目的 Cargo.toml 所在文件)就能完成初始化

ra 开发者文档有写在 InitializeParams 的 initialization_options 字段写入配置即可

```rust
lsp_server::Message::from(lsp_server::Request {
    id: self.req_id.inc(),
    method: <lsp_types::request::Initialize as Request>::METHOD.to_string(),
    params: serde_json::to_value(&lsp_types::InitializeParams {
        root_uri: Some(
            lsp_types::Url::parse("file:///home/w/repos/temp/unused_pub_test_case")
                .unwrap(),
        ),
        // crates/rust-analyzer/src/bin/main.rs `fn run_server` config.update
        // rust_analyzer::config::ConfigData sturct is private
        initialization_options: Some(
            serde_json::to_value(&serde_json::json!({
                "checkOnSave": {
                    "enable": false
                }
            }))
            .unwrap(),
        ),
        ..Default::default()
    })
    .unwrap(),
})
.write(&mut self.req_to_ra)
.unwrap();
```

但是有些配置项只在 vscode 插件源码的 config.ts 中，例如 `rust-analyzer.server.trace = "verbose"`

在 initialization_options 中并不能设置，只能在 vscode 插件配置中设置

## ra 关闭连接

这也算一个坑点了，ra 收到 ShutdownRequest 之后居然没有给客户端发 ShutdownResponse

实际上客户端直接给 ra 发 ShutdownNotification 也能关掉 ra

## list symbol refs

我想分析 workspace 里面的死代码，就必须先借助 ra 帮忙列出所有代码文件的所有符号(函数/结构体定义)

ra 提供了两个查询符号的 API documentSymbol 和 WorkspaceSymbol

所以我的思路是先调用 WorkspaceSymbol 得到全部符号，再遍历所有符号，每个符号都调用一次 references API 搜索引用

如果引用次数为 0 则可能是死代码

```rust
let workspace_symbol_rsp = lsp_ctx.send_req(workspace_symbol_req).unwrap();
let workspace_symbol_rsp = serde_json::from_value::<
    <rust_analyzer::lsp_ext::WorkspaceSymbol as Request>::Result,
>(workspace_symbol_rsp)
.unwrap();
for symbol in workspace_symbol_rsp.unwrap() {
    if symbol.kind != lsp_types::SymbolKind::FUNCTION {
        continue;
    }
    if symbol.name == "main" {
        continue;
    }
    let path = symbol.location.uri.to_string();

    let mut p = symbol.location.range.start;
    p.character += "pub fn ".len() as u32 + 1;
    let find_refs_req = lsp_server::Request {
        id: lsp_ctx.req_id.inc(),
        method: <lsp_types::request::References as Request>::METHOD.to_string(),
        params: serde_json::to_value(lsp_types::ReferenceParams {
            text_document_position: lsp_types::TextDocumentPositionParams {
                text_document: lsp_types::TextDocumentIdentifier {
                    uri: symbol.location.uri,
                },
                position: p,
            },
            work_done_progress_params: lsp_types::WorkDoneProgressParams::default(),
            partial_result_params: lsp_types::PartialResultParams::default(),
            context: lsp_types::ReferenceContext {
                include_declaration: false,
            },
        })
        .unwrap(),
    };
    let rsp = match lsp_ctx.send_req(find_refs_req) {
        Some(rsp) => rsp,
        None => {
            println!("References return None");
            continue;
        }
    };
    let rsp = serde_json::from_value::<lsp_types::GotoDefinitionResponse>(rsp).unwrap();
    let refs_cnt = match rsp {
        lsp_types::GotoDefinitionResponse::Scalar(_) => 1,
        lsp_types::GotoDefinitionResponse::Array(arr) => arr.len(),
        lsp_types::GotoDefinitionResponse::Link(arr) => arr.len(),
    };
    if refs_cnt == 0 {
        eprintln!("dead_code found {path} {}", symbol.name);
    }
}
```

由于 ra 列出的 symbol 返回的是例如一个函数定义的代码行号范围 TextRange

而 reference 需要光标选中函数的名称(也就是 ident)，所以我代码很丑陋的让 symbol 的起始坐标偏移了 "pub fn" 的长度

## 测试

我测试了一个 cargo workspace, package a 有两个 pub fn 但是 package b 只使用了其中的一个 pub fn

```
Cargo.toml:
[workspace]
members = [
    "crates/callee",
    "crates/pub_util",
]

crates/pub_util/src/lib.rs:
pub fn used_pub() {}
pub fn unused_pub() {}

crates/callee/src/main.rs:
fn main() {
    pub_util::used_pub();
}
```

测试输出结果如下，成功找到 unused_pub 函数未被调用

```
rust-analyzer blocking for cargo check total wait is 442.456581ms
dead_code found file:///home/w/repos/temp/unused_pub_test_case/crates/pub_util/src/lib.rs unused_pub
```

## 结语

我自制的 ra 客户端代码还是能找出在 workspace 中没有调用的函数名和位置成功发现了死代码

本文也算抛砖引玉了，还有诸多问题没有解决:

1. 得到 symbol location 之后应该先调用 AST 解析获取 symbol 的 ident 标识符位置
2. workspace_symbol 无法列出 impl 内的函数

不过还是看到了定制 ra 客户端请求实现 workspace 级别死代码分析的可能
