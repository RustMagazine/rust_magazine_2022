# Rust 开发基础库

---

## deser: 一个新的实验性 Rust 序列化库

它想探索 JSON 或 msgpack 等结构化格式的序列化和反序列化的可能性。但不会支持 bincode 这种非自描述格式(non self describing formats)。

> 自描述格式包含描述消息格式和含义的数据和元数据

**设计目标：**

- 快速编译时间： deser 通过鼓励动态分发来避免过度的单态化。
- 无限递归：现实世界很糟糕，传入的数据可能嵌套严重。无论数据有多深，都不能耗尽调用堆栈。
- 简单数据模型： deser 简化了序列化和反序列化接口上的数据模型，但通过提供描述符来弥补，这些描述符为序列化程序想要处理它时提供辅助信息。
- 可扩展数据模型： deser 希望能够使用非序列化接口原生的类型来扩展数据模型。例如，如果数据格式想要支持任意大小的整数，这应该是可能的，而无需退回到带内信令。
- 元信息： deser 通过提供保存元信息的空间来补偿简化的数据模型。
- 本机字节序列化： deser 具有内置的专门化功能，用于将字节和字节向量序列化为与切片和向量不同的格式。


[deser](https://github.com/mitsuhiko/deser)

## 【实验性】diplomat: 用于生成 FFI 定义的工具

使用 Diplomat，您可以简单地定义要通过 FFI 公开的 Rust API，并自动获得高级 C、C++ 和 JavaScript 绑定！

- [https://github.com/rust-diplomat/diplomat](https://github.com/rust-diplomat/diplomat)
- [https://rust-diplomat.github.io/book/](https://rust-diplomat.github.io/book/)

## Clap 3.1 发布

Clap v3 开发了好几年，并且经过几次维护者变更，所以变更日志已经变得不再连续完整。

v3 最大的改变是集成了 [StructOpt](https://docs.rs/structopt/)。

[https://github.com/clap-rs/clap/blob/master/CHANGELOG.md](https://github.com/clap-rs/clap/blob/master/CHANGELOG.md)


## deku : 声明式二进制读写

为结构体和枚举提供位级（bit-level）、置换（symmetric）、序列化/反序列化实现。支持 `no_std` 和  `wasm`。

[https://github.com/sharksforarms/deku](https://github.com/sharksforarms/deku)

## nextest : Rust 的下一代测试执行器

特点：
- 干净美观的用户界面
- 比 `cargo test`性能快 `60%`
- 自动重试失败的测试，如果稍后通过测试，就能将其标记为 不可靠测试（`flaky`），对于发现系统中的问题比较有用
- 分区测试跨多个 CI Jobs运行。如果您的测试在 CI 中运行的时间过长，nextest 可以自动为您将它们拆分到多个Jobs中。
- 跨平台。nextest 可在 Unix、Mac 和 Windows 上运行，因此无论您使用什么平台，您都可以获得更快的测试运行的好处。
- 其他（还在完善中）

> 不可靠测试（Flaky Tests），就是指在被测对象和测试条件都不变的情况下，有时候失败、有时候成功的测试。产生这类测试的原因包括：异步等待、并发、资源泄漏、远程服务、依赖其他测试、平台依赖性、系统时间、随机数等。
> 相关论文：[https://www.cs.cornell.edu/courses/cs5154/2021sp/resources/LuoETAL14FlakyTestsAnalysis.pdf](https://www.cs.cornell.edu/courses/cs5154/2021sp/resources/LuoETAL14FlakyTestsAnalysis.pdf)

- [https://github.com/nextest-rs/nextest](https://github.com/nextest-rs/nextest)
- [nextest book](https://nexte.st/index.html)

## aquamarine: Rustdoc 中支持 mermaid 作图

aquamarine 通过为 rustdoc 扩展过程宏来支持 Rust 文档中可视化组件。

`#[aquamarine]` 宏通过将mermaid.js嵌入到生成的 rustdoc HTML 页面中，修改 doc 注释属性来工作。

```rust
#[cfg_attr(doc, aquamarine::aquamarine)]
/// ```mermaid
/// graph LR
///     s([Source]) --> a[[aquamarine]]
///     r[[rustdoc]] --> f([Docs w/ Mermaid!])
///     subgraph rustc[Rust Compiler]
///     a -. inject mermaid.js .-> r
///     end
/// ```
pub fn example() {}
```

[https://github.com/mersinvald/aquamarine](https://github.com/mersinvald/aquamarine)

## fp-bindgen: 全栈 WASM 插件绑定生成器

[https://github.com/fiberplane/fp-bindgen](https://github.com/fiberplane/fp-bindgen)

## zbus 2.0 发布

简要介绍一下 zbus 是什么：

D-Bus 是一种在 Linux（尤其是桌面和嵌入式系统）上非常流行的进程间通信 (IPC) 机制。 而 zbus 是一个纯粹的 Rust 库，旨在使 D-Bus 处理尽可能简单，许多服务（例如 systemd、NetworkManager、Geoclue 等）都使用它，所以如果你发现自己在 Rust 中与它们交互，你可以使用 zbus。

而大家期待已久的2.0 稳定版发布了！ 😎 虽然 1.x 版本很受欢迎，但缺少异步 API。 2.0 使用了全新的设计，将异步 API 作为主要的 API，阻塞 API 只是一个包装器。

- [https://docs.rs/zbus/latest/zbus/](https://docs.rs/zbus/latest/zbus/)
- [zbus book](https://dbus.pages.freedesktop.org/zbus/)

## LibAFL - 高性能 Rust fuzzer

Fuzzing 可以完全自动化地发现对安全至关重要的错误。而 LibAFL 是从零开始用 Rust 编写的 fuzzing 框架，可以创建几乎所有的模糊测试器。

在谷歌的 fuzzbench 基准测试中，它已经超过了许多著名的覆盖引导模糊器，包括 honggfuzz、libfuzzer/entropic 以及 old-skool afl。最重要的是，LibAFL 可以更好地跨内核和机器扩展。

您还可以使用它在 Windows、Android、macOS 和 LibAFL 上对纯二进制目标进行模糊测试，也完全与 no_std 兼容。

[https://github.com/AFLplusplus/LibAFL](https://github.com/AFLplusplus/LibAFL)

## 字节码联盟 将 Rust 的 std 移植到 rustix

Rustix 是一个具有多个后端的系统调用包装库。它有一个原始的 Linux 系统调用后端，以及一个 libc 后端，其他后端正在开发中。Rustix 专为内存安全、I/O 安全和性能而设计。rustix 简化了与 C 整数类型大小相关的系统调用 API 中的一些小缺陷。举个例子：

```rust
let len = cmp::min(buf.len(), <wrlen_t>::MAX as usize) as wrlen_t;
   let ret = cvt(unsafe {
       c::send(self.inner.as_raw(), buf.as_ptr() as *const c_void, len, MSG_NOSIGNAL)
   })?;
```

将会变成

```rust
   let ret = rustix::net::send(&self.inner, buf, SendFlags::NOSIGNAL)?;
```

这将重点放在`send`操作上，而没有`unsafe`、原始指针、`wrlen_t`类型和`cvt`错误处理的干扰。

Rustix 从 std 中移除了很多 unsafe 块。也是向 Linux 上不依赖 libc 的 Rust 工具链迈出的一步。Rustix 还能够从 Rust 代码直接进行 Linux 系统调用。比如 [origin](https://github.com/sunfishcode/mustang/tree/main/origin#origin) 是一个 Rust 库，它能够启动和关闭进程和线程（类似于 crt1.o 和 libpthread）。有了这些，我们就有了在 Linux 上运行 Rust 程序所需的所有东西。

将 std 移植到 rustix 的目标是最终提议将其合并到上游 Rust。

[https://github.com/bytecodealliance/rustix](https://github.com/bytecodealliance/rustix)

## cargo-release 发布 v0.19

cargo-release用于自动完成crate的release过程。例如，使用clap，我所做的就是添加变更日志，并运行cargo release修补程序，cargo release负责更新文件、发布到crates.io、标记和推送。

[https://github.com/crate-ci/cargo-release](https://github.com/crate-ci/cargo-release)

## scapegoat: 通过基于 arena 的 scapegoat tree（内存高效、自平衡二叉搜索树）实现的有序集合和映射数据结构

- 嵌入式友好：默认 `#![no_std]`
- 安全：`#![forbid(unsafe_code)]`，包含所有依赖
- 针对标准库的 BTreeSet 和 BTreeMap，通过差分模糊验证

两个 API：

- 有序 Set SgSet，BTreeSet 的子集
- 有序 Map SgMap，BTreeMap 的子集

- 分享 Slide：[https://tiemoko.com/slides/SafeAndPortableDataStructureDesign_CodeAndSupply_Dec2021.pdf](https://tiemoko.com/slides/SafeAndPortableDataStructureDesign_CodeAndSupply_Dec2021.pdf)
- [https://github.com/tnballo/scapegoat](https://github.com/tnballo/scapegoat)

## RedisJSON : 一个 Redis 模块，实现了 ECMA-404 JSON 数据交换标准作为原生数据类型。

它允许从 Redis 键（文档）存储、更新和获取 JSON 值。主要特点：

- 完全支持 JSON 标准
- 在文档中选择元素使用类似 JSONPath 的语法
- 文档存储为树结构中的二进制数据，允许快速访问子元素
- 所有 JSON 值类型支持类型化原子操作
- 基于 RediSearch 的二级索引支持

- 文档：[https://oss.redis.com/redisjson/](https://oss.redis.com/redisjson/)
- GitHub：[https://github.com/RedisJSON/RedisJSON](https://github.com/RedisJSON/RedisJSON)



## Serde Playground : 可以快速验证自己的序列化结果

[https://hkalbasi.github.io/serde-rs.github.io/playground.html](https://hkalbasi.github.io/serde-rs.github.io/playground.html)

## mail-builder: 电子邮件解析和生成库

作者刚刚发布了一个名为mail-builder的新库，可以在Rust中轻松生成MIME电子邮件。这个库与作者在几周前发布的邮件解析器mail-parser相结合，为构建和解析Rust中任何复杂的RFC5322电子邮件提供了全面支持。

- [https://crates.io/crates/mail-builder](https://crates.io/crates/mail-builder)
- [https://crates.io/crates/mail-parser](https://crates.io/crates/mail-parser)

## Thalo.rs —— Rust中的事件溯源库

Thalo是一个基于以下模式构建大型系统的事件溯源框架：

- 事件溯源
- CQRS
- 事件驱动
- 事务发件箱
- DDD

它的设计是模块化的，带有实现大多数功能的额外库。

[https://github.com/thalo-rs/thalo](https://github.com/thalo-rs/thalo)

## QCell: 0.5.0 版本发布

QCell 是 RefCell 和 RwLock 替代品.

QCell 在运行时不会像使用 RefCell 那样会 panic 的 Cell 类型, 相反他会给出编译错误，或者使用 RwLock 交换细粒度锁定，以获得单独的所有者对象的粗粒度锁定。

[https://github.com/uazu/qcell](https://github.com/uazu/qcell)

## diesel_async - diesel 的异步版本

大家呼唤很久了，现在 diesel 的维护者亲自开坑了。

[https://github.com/weiznich/diesel_async](https://github.com/weiznich/diesel_async)

## cargo-msrv：为项目查找支持的最低 Rust 版本

如何使用：

```
cargo msrv 或 cargo msrv --linear 在当前工作目录中查找 Cargo 项目的 MSRV
cargo msrv --path <dir> 在 <dir> 目录中查找 Cargo 项目的 MSRV
cargo msrv -- <command> 使用 <command> 作为兼容性检查
cargo msrv --verify 验证由 crate 作者指定的 MSRV
cargo msrv list 列出作者指定的依赖项的 MSRV
cargo msrv show 显示当前指定的 MSRV
```

- [https://foresterre.github.io/cargo-msrv/index.html](https://foresterre.github.io/cargo-msrv/index.html)
- [https://github.com/foresterre/cargo-msrv](https://github.com/foresterre/cargo-msrv)

## dtmf: 一个无std 的 Rust DTMF解码器

dtmf是一个无std 的 DTMF解码器。可以在 microcontroller 或普通PC上使用。

> DTMF（Dual Tone Multi Frequency），双音多频信号，电话系统中电话机与交换机之间的一种用户信令，通常用于发送被叫号码。由高频群和低频群组成，高低频群各包含4个频率。一个高频信号和一个低频信号叠加组成一个组合信号，代表一个数字。DTMF信号有16个编码。利用DTMF信令可选择呼叫相应的对讲机。

```rust
// Load in our audio samples
// This can also be done in real time from the sound card
let mut sample_file = File::open("data/dtmf_test.wav").unwrap();
let (header, data) = wav::read(&mut sample_file).unwrap();
let data = data.try_into_sixteen().unwrap();

// set up our decoder
let mut decoder = Decoder::new(header.sampling_rate, |tone, state| {
	println!("{:?}: {:?}", tone, state);
});

// can process all samples at once, or in smaller batches
decoder.process(&data);
```

[https://crates.io/crates/dtmf](https://crates.io/crates/dtmf)

## RVV 编码器

- rvv-encode RISC-V V 扩展指令库
- rvv-asm RISC-V V 扩展指令过程宏
- rvv-as RISC-V V 扩展指令的命令行工具

[https://github.com/TheWaWaR/rvv-encoder](https://github.com/TheWaWaR/rvv-encoder)


## Tokio Metrics 0.1

今天，我们很高兴地宣布初始发布Tokio-Metrics，一个用于获得Tokio应用程序的运行时和任务级别指标的crate。Tokio-Metrics使Tokio用户更容易通过提供生产中的运行时行为来调试性能问题。

如今，Tokio已成功用于亚马逊、微软、Discord等公司的大规模生产部署。然而，我们通常会从处理调试问题的工程师那里收到问题。

[https://tokio.rs/blog/2022-02-announcing-tokio-metrics](https://tokio.rs/blog/2022-02-announcing-tokio-metrics)

## TZ-RS，Libc函数localtime, gmtime和mktime的重新实现

Libc函数limetime，gmtime和mktime的纯Rust重新实现。

这个crate主要用于UNIX平台。由于时区数据库文件未包含在此crate中，因此非UNIX用户可以在IANA站点上下载时区数据库的副本，并将时区数据库文件编译为本地目录。

[https://github.com/x-hgg-x/tz-rs](https://github.com/x-hgg-x/tz-rs)

## cargo zigbuild 使用zig作为链接器

宣布 cargo-zigbuild。用zig作为链接器编译Cargo项目。

zig 是一门编程语言，可以直接编译到C语言，提供了更好的跨平台编译能力。

[https://github.com/messense/cargo-zigbuild](https://github.com/messense/cargo-zigbuild)

## bacon：一个后台 rust 代码检查器

它是为最小化交互而设计

- [https://github.com/Canop/bacon](https://github.com/Canop/bacon)
- [https://dystroy.org/bacon/](https://dystroy.org/bacon/)

## cargo-nextest: 全新版本的 test runner

cargo nextest 是一个 cargo test 的改进版本

其作者指出，当前的 cargo test 有几点不足：

- 缺少针对每一个 unittest 测试结果的结构化表示
- 测试二进制文件串行执行，一个发生错误，测试会中断
- 可能会被单个测试用例阻塞，导致性能瓶颈

针对这些问题，cargo nextest 采用了更加并行化的策略，提出了一个更加先进的测试框架。

- [https://nexte.st/index.html](https://nexte.st/index.html)
- [https://github.com/nextest-rs/nextest](https://github.com/nextest-rs/nextest)

## rust-protobuf 的过去、现在和未来

作者可能无心维护该项目了，建议使用 [prost](https://github.com/tokio-rs/prost) 代替。

[https://github.com/stepancheg/rust-protobuf/blob/master/doc/past-present-future.md](https://github.com/stepancheg/rust-protobuf/blob/master/doc/past-present-future.md)

## ttokio-cron-scheduler: 0.4 版本发布

在异步 tokio 环境中使用类似cron的调度。也可以在一个瞬间安排任务，或者在固定的时间内重复它们。

[https://github.com/mvniekerk/tokio-cron-scheduler](https://github.com/mvniekerk/tokio-cron-scheduler)


## fluent-uri：一个快速、简单和严格的URI解析器
Announcing fluent-uri: a fast, easy and strict URI parser

简介

一个严格遵守IETF RFC 3986和RFC 6874的Rust URI解析器。

特性

- 快速：零拷贝解析，据观察，比Rust中常见的URI解析器快2-25倍；
- 简单：精心设计和文档良好的API；
- 严格：解析RFCs中定义的所有可能的URI，并拒绝其他任何东西。

[https://github.com/yescallop/fluent-uri-rs](https://github.com/yescallop/fluent-uri-rs)

## coca: 具有恒定容量的数据结构

Rust 的标准集合库提供了最常见的通用编程数据结构的有效实现。通过使用标准实现，两个库应该可以在没有大量数据转换的情况下进行通信。

但是，这些标准实现使用分配器管理自己的内存 ，默认为全局堆。这通常很方便，但在某些情况下可能会造成问题，例如在（软）实时应用程序中，或在内存受限的嵌入式系统中，其中 alloc crate 甚至可能不可用。

coca旨在通过提供在给定的后备内存块上运行的数据结构来充当此类环境中的替代品，而无需自行分配。它们在存储类型上是通用的，可以是以下任何一种：

- InlineStorage: 将内容存储在 中struct，没有间接性。需要真正的容量const，即在编译时静态已知。
- AllocStorage：将内容存储在全局分配的内存中。需要alloc功能标志。
- SliceStorage：仅对于类似数组的数据结构，将内容存储在任何给定的未初始化内存片中。
- ArenaStorage：coca包括一个竞技场（Arena）分配器，并允许使用竞技场分配的内存作为存储来构建符合人体工程学的数据结构。

[https://github.com/teryror/coca](https://github.com/teryror/coca)