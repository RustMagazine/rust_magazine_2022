# 官方动态

---

## Rust 语言团队 2024 Roadmap 发布

Rust 语言团队将聚焦于以多种不同的方式扩展授权，让越来越多的人受益，也就是说，让 Rust 广泛应用。将专注于三个主要主题来完成这个目标：拉平学习曲线、扩展生态系统、扩展项目。

[https://blog.rust-lang.org/inside-rust/2022/04/04/lang-roadmap-2024.html](https://blog.rust-lang.org/inside-rust/2022/04/04/lang-roadmap-2024.html)

## 2021 Rust调研

2021年12月时，Rust项目组对于Rust发布了一次调研。详细调研结果可见《[Rust Survey 2021 Results](https://blog.rust-lang.org/2022/02/15/Rust-Survey-2021.html)》，部分结果概括如下：

- 90%的参与者目前正在使用Rust；
- 78%的参与者使用英文填写问卷；
- 23%的参与者主要使用Rust编程，59%的参与者至少偶尔在工作中使用Rust；
- 关于为何在工作中使用Rust，96%的参与者认同Rust的正确性，92%的参与者认同性能，89%的参与者认同安全性。


## Rust 1.59.0

[Rust 1.59.0版本](https://blog.rust-lang.org/2022/02/24/Rust-1.59.0.html)发布于2022年2月24日。

新的稳定版本内容大致如下：

- 编写汇编代码时，可以将变量名写到花括号内了，形同`format!`更新后的使用方式。例如，现在支持这种写法：

  ```rust
  use std::arch::asm;
  
  // Multiply x by 6 using shifts and adds
  let mut x: u64 = 4;
  unsafe {
      asm!(
          "mov {tmp}, {x}",
          "shl {tmp}, 1",
          "shl {x}, 2",
          "add {x}, {tmp}",
          x = inout(reg) x,
          tmp = out(reg) _,
      );
  }
  assert_eq!(x, 4 * 6);
  ```

  - 支持的汇编语言为：x86，x86-64，ARM，AArch64，RISC-V

- 支持包含未初始化变量的元组、数组、或结构体语法作为赋值命令的左值，并拆分右值的元组、数组、结构体，为左值中的变量一一赋值。例如：

  ```rust
  let (a, b, c, d, e);
  
  (a, b) = (1, 2);
  [c, .., d, _] = [1, 2, 3, 4, 5];
  Struct { e, .. } = Struct { e: 5, f: 3 };
  
  assert_eq!([1, 2, 1, 4, 5], [a, b, c, d, e]);
  
  // 但是不可以这样写
  // let mut (a, b);
  // (a, b) = (1, 2);
  // `+=` 的写法是不允许的
  // (a, b) += (1, 2);
  ```

- 常量泛型可以指定默认值了，同时不再要求常量泛型必须写在一般泛型之后，例如：

  ```rust
  struct ArrayStorage<T, const N: usize = 2> {
      arr: [T; N],
  }
  
  // 指定默认值
  impl<T> ArrayStorage<T> {
      fn new(a: T, b: T) -> ArrayStorage<T> {
          ArrayStorage {
              arr: [a, b],
          }
      }
  }
  
  // 不再要求常量泛型必须写在一般泛型之后
  fn cartesian_product<
      T, const N: usize,
      U, const M: usize,
      V, F
  >(a: [T; N], b: [U; M], f: F) -> [[V; N]; M]
  where
      F: FnMut(&T, &U) -> V
  {
      // ...
  }
  ```

- 优化了 Rust 编译器报错，当后续更新版本不兼容当前代码时，Cargo 产生告警并提前预知用户。可以通过 `cargo report` 指令检查新版本是否兼容当前代码。

- 可以通过设置 `Cargo.toml` 而非手动的方式去除不需要的二进制符号了。例如：

  ```toml
  [profile.release]
  # 对于 release 版本，去除所有的 debug 符号
  strip = "debuginfo"
  ```

  目前可选选项如下：

  | 选项                                     | 释义                                                     |
  | ---------------------------------------- | -------------------------------------------------------- |
  | strip = "none" (default) / strip = false | 不去除任何符号                                           |
  | strip = "debuginfo"                      | 去除 debug 符号以及关于行号的 debug 信息相关的二进制内容 |
  | strip = "symbols" / strip = true         | 去除所有不必要符号                                       |

  更详细的说明可见 [Cargo Book #Strip](https://doc.rust-lang.org/beta/cargo/reference/profiles.html#strip)。

- 增量编译默认值设置为1，这样可以减轻编译时可能出现的反序列化错误的影响。增量编译的详细解释可见本篇 [#Rust与编译速度](#Rust与编译速度)。

- 将一些功能引入Stable版本。

## Rust 1.58


通常来说，Rust每隔6个星期会发布一次新版本。Rust [1.58.0版本](https://blog.rust-lang.org/2022/01/13/Rust-1.58.0.html)发布于2022年1月13日，主要针对格式化字符串、Windows可执行文件搜索、以及对于一些功能的稳定化；而Rust [1.58.1版本](https://blog.rust-lang.org/2022/01/20/Rust-1.58.1.html)发布于2022年1月20日，主要是修复了1.58.0版本中的Bug，保证系统安全。升级到新版本的方法可参考《[This Week in Rust #404：Rust 2021 与 周期性服务发现](http://openx.huawei.com/Ylong_Rust/dynamicDetail/3212)》"如何升级到Rust新版本"部分。


新的稳定版本内容大致如下：


- 格式化字符串可以像Python一样，将内容对应变量直接写入`{}`符号之中了，例如：

  ```rust
  println!("Hello, {}!", get_person());                // implicit position
  println!("Hello, {0}!", get_person());               // explicit index
  println!("Hello, {person}!", person = get_person()); // named
  
  let person = get_person();
  println!("Hello, {person}!"); // captures the local `person`
  
  // 也可以使用变量值
  let (width, precision) = get_format();
  for (name, score) in get_scores() {
  	println!("{name}: {score:width$.precision$}");
  }
  ```

  唯一的例外是`panic!`宏，在2015与2018版本中，`panic!("{ident}")`的写法依然就产生告警。不过，2021版本维持了格式化字符串的一致性，因此这个特性在2021版本中可以正常使用。


- 对于Windows平台，`std::process::Command`指令不再于当前工作文件夹中搜索可执行文件了。此前，由于Win32的`CreateProcess`接口，当当前程序创造子进程时，Rust会按照 子进程环境路径 - 应用文件夹 - 父进程文件夹 - 32位/16位系统文件夹 - Windows文件夹 - `Path`环境变量 的顺序搜索可执行文件。然而，在父进程文件夹搜索可能会导致未知行为，甚至是恶意行为，例如拦截子进程。在搜索父进程文件夹时，`ripgrep`命令使得攻击者可以通过`-z/--search-zip`或`--pre`标识来运行任意程序。具体的[报告](https://www.cve.org/CVERecord?id=CVE-2021-3013)可见CVE。因此，Rust不再搜索父进程文件夹，同时不再搜索旧系统遗留的16位系统文件夹。

- 修复调用`std::fs::remove_dir_all`时会产生竞争场景的漏洞。具体的描述可见[CVE](https://www.cve.org/CVERecord?id=CVE-2022-21658)以及Rust官网发布的[推文](https://blog.rust-lang.org/2022/01/20/cve-2022-21658.html)，概括一下即是，对于攻击者无权限删除的某一文件夹`\sensitive`，攻击者可以利用`remove_dir_all`，从有权限的文件夹`\temp`创建引用链接至`\sensitive`，然后在有权限应用中调用`remove_dir_all`，从而删除本无权限操作的文件夹`\sensitive`。此前，为了避免这种情况，`remove_dir_all`只会在判断后删除引用链接，但是在判断和真实删除之间存在时延，导致攻击者可以利用这个时延来替换删除目标，导致该漏洞。

- 对于标准库的一些函数，新增了更多的`#[must-use]`属性，这一属性会检测返回值是否被合理处理，例如检查返回Result时，错误是否被处理。

- 将如下功能引入Stable版本：

  - Metadata::is_symlink

  - Path::is_symlink

  - {integer}::saturating_div

  - Option::unwrap_unchecked

  - Result::unwrap_unchecked

  - Result::unwrap_err_unchecked

  - File::options

- 将如下功能的返回值更新为`const`：

  - Duration::new

  - Duration::checked_add

  - Duration::saturating_add

  - Duration::checked_sub

  - Duration::saturating_sub

  - Duration::checked_mul

  - Duration::saturating_mul

  - Duration::checked_div

- 对于Rust工具（Clippy， Rustc，Fmt等）进行了更新，具体可见1.58.0以及1.58.1更新日志，在此暂略。

## Rust 核心团队有三人宣布退出

- Steve Klabnik， 将离开核心团队和安全响应工作组。Steve 9年前开始使用 Rust。自 2014 年以来，他是 Rust 编程语言书（the book）的主要作者和核心团队的一员。他一直在运行 @rustlang Twitter 帐户，因此形成了该项目的许多“声音”。Steve 现在可以专注于使用Rust 而不是制作Rust。

- Florian Gilcher， 正在退出他所有的项目职位。他于 2019 年成为核心团队的观察员，并于 2020 年成为核心团队的正式成员。他还担任过 Rust 基金会董事会的项目总监。在此之前，他自 2015 年以来一直是社区团队的一员。由于他专注于自己的公司并承担其他业务义务，因此他正在为项目中的其他人腾出空间。

- Pietro Albini， 将离开核心团队，专注于项目的其他部分，包括基础设施团队和安全响应工作组。他于 2019 年底加入核心团队，在成为基础设施团队的联合负责人后不久，但核心团队的工作量让他很难在项目中的其他角色上花费足够的时间。

[https://blog.rust-lang.org/2022/01/31/changes-in-the-core-team.html](https://blog.rust-lang.org/2022/01/31/changes-in-the-core-team.html)

## 2022年异步Rust的改进计划

Niko Matsakis 和 Tyler Mandry 代表 Async 工作组发布了一篇文章，阐述了在 2022 年异步 Rust 的改进计划。

**Rust 2024 Edition 中使用异步 Rust 的愿景**

文章中先假设了一个 Rust 2024 edtion 中实现的异步愿景。

假设在 Rust 2024 Edition 中，你使用异步 Rust 创建一个新项目：使用crates.io 的第三方库 `crabbycat`来遍历指定 GitHub 仓库的issues列表：

```rust,ignore
async fn main() {
    for await? issue in crabbycat::issues("https://github.com/rust-lang/rust") {
        if meets_criteria(&issue) {
            println!("{issue:?}");
        }
    }
}
```

你的项目似乎工作正常，这个时候有人给你发起 PR: 增加 GitLab 支持。你看到 PR 的代码有下面改进：

```rust,ignore

// 增加了 Async Trait `IssueProvider` 来抽象 issues 接口
// 兼容 GitHub/Gitlab/Gitothers
trait IssueProvider {
    // 这个 issues 方法只需要遍历issues即可
    // 所以需要是一个实现AsyncIterator的类型
    async fn issues(&mut self, url: &str)
        -> impl AsyncIterator<Item = Result<Issue, Err>>;
}

#[derive(Debug)]
struct Issue {
    number: usize,
    header: String,
    assignee: String,
}

// 使用独立的方法打印issues列表
// provider代表issues提供方是GitHub 还是 其他，比如 Gitlab
fn process_issues(provider: &mut dyn IssueProvider) {
    for await? issue in provider.issues("https://github.com/rust-lang/rust") {
        if meets_criteria(&issue) {
            println!("{issue:?}");
        }
    }
}
```

你合并了该 PR，一切OK。后面有人想将你的项目移植到 Chartreuse 操作系统，而 Chartreuse 操作系统是基于 Actor 模型的，并且有自己的自定义异步运行时。然而，你其实并不需要关心这些。因为你所有代码都能够无缝地将底层运行时实现切换到 Chartreuse 异步运行时。

**在 2022 年将要完成的目标**

看得出来，异步 Rust 在 2024 Edition 会更加的完善，使用起来更加方便。但是为了达到这个目标，2022年必须完成一些基础工作，并且可以预料到的是，在这个过程中，异步 Rust 的很多细节会发生大量变化，不出意外的话，生成器的语法应该会备受争议。但我们的整体愿景并不会改变：编写异步 Rust 代码应该像编写同步代码一样简单，除了偶尔出现的`async`和`await`关键字。

为此，Async 工作组组织成许多不同的计划，每个计划都在追求愿景的一部分：

- [异步基础计划](https://rust-lang.github.io/async-fundamentals-initiative/)，由 [tmandry](https://github.com/tmandry)领导，聚焦于解决trait中支持`async fn`的难题。过去一年的工作：
    - 协调和支持 [`GAT`](https://rust-lang.github.io/generic-associated-types-initiative/) 和 [`impl Trait`](https://rust-lang.github.io/impl-trait-initiative/) 计划。
    - 起草 [RFC #3185](https://rust-lang.github.io/rfcs/3185-static-async-fn-in-trait.html)，在 trait 中实现静态 `async fn`，用于支持静态分发，即，可以返回`impl Trait`。
    - 致力于动态分发的设计，Niko 有一系列博文阐述相关内容：[Dyn async traits](http://smallcultfollowing.com/babysteps//blog/2022/01/07/dyn-async-traits-part-7/)。
    - 其他改进，比如[Contexts and capabilities in Rust](https://tmandry.gitlab.io/blog/posts/2021-12-21-context-capabilities/)。
- [异步迭代器计划](https://estebank.github.io/rust-iterator-item-syntax.html)，由[estebank](https://github.com/estebank)领导，探索生成器和异步生成器。Estebank 为生成器制作了一个程序宏的原型，并呼吁讨论语法和其他细节。
- [可移植性计划](https://www.ncameron.org/blog/portable-and-interoperable-async-rust/)，在[nrc](https://github.com/nrc)的带领下，[探索如何让代码在运行时之间轻松移植](https://www.ncameron.org/blog/portable-and-interoperable-async-rust/)，从标准化的特征开始，例如`AsyncRead`和`AsyncWrite`。
- [抛光打磨（Polish）计划](https://rust-lang.github.io/wg-async/vision/roadmap/polish.html)，由[eholk](https://github.com/eholk)领导，专注于通过较小的变化来提高现有的能力，这些变化共同产生了巨大的影响。比如：
    - [PR #91032](https://github.com/rust-lang/rust/pull/91032)，改进生成器当变量在 `yield` 点之前被移动时的捕获分析。
    - [PR #92508](https://github.com/rust-lang/rust/pull/92508)，它生成了临时作用域的精确范围，进一步避免不必要的生成器捕获。
    - [guswynn](https://github.com/guswynn)提交了[PR #88865](https://github.com/rust-lang/rust/pull/88865)，创建了 `must_not_suspend` lint，用于捕获一些不应该跨 `await` 存活的值。
    - 开始研究 async stack traces 如何更加可读，更有帮助。
- [工具计划]()，由[pnkfelix](https://github.com/pnkfelix)领导，致力于支持异步生态系统中那些正在创建有趣的工具来支持异步Rust的开发者们，诸如：
    - Michael Woerister正在探索[异步crashdump恢复](https://github.com/rust-lang/async-crashdump-debugging-initiative)，提供一种机制来恢复和检查基于crashdump的异步Rust程序的状态。
    - Eliza Weisman和[其他许多人](https://tokio.rs/blog/2021-12-announcing-tokio-console#thanks-to)最近宣布了他们0.1版本的[`tokio-console`](https://github.com/tokio-rs/console) （异步Rust程序的诊断和调试工具）。

上面这些计划都可以在 [roadmap](https://rust-lang.github.io/wg-async/vision/roadmap.html) 中找到。

**你如何参与**

如果你想参与，最好的路线是先从「打磨抛光计划」开始，在其页面上有["如何帮助 "部分](https://rust-lang.github.io/wg-async/vision/roadmap/polish.html#-how-to-help)可以参考。

[官方原文](https://blog.rust-lang.org/inside-rust/2022/02/03/async-in-2022.html)

## Rust 2024 ：敢于要求更多

Rust 语言团队 Leader Niko 在其博文上畅想 Rust 2024 Edition：

- 敢于要求更符合人体工程学、更具表现力的 Rust
- 敢于要求更符合人体工程学、表现力更强的异步Rust
- 敢于要求更符合人体工程学、表现力更强的Unsafe Rust
- 敢于要求从 Rust 的工具中获得更丰富、更具交互性的体验
- 敢于为Unsafe Rust要求更丰富的工具

[https://smallcultfollowing.com/babysteps//blog/2022/02/09/dare-to-ask-for-more-rust2024/](https://smallcultfollowing.com/babysteps//blog/2022/02/09/dare-to-ask-for-more-rust2024/)

## Rust 2022 编译器团队的雄心

最近，Rust编译器团队发布了2022年编译器这边要做的一些事情。有三大目标：

1. 填充Rust的承诺。性能，可靠性，生产力，这三块都还有补足的空隙；
2. 让开发者更开心/舒心。编译速度，运行方式，IDE等；
3. 建设更好的贡献工作流。

[https://blog.rust-lang.org/inside-rust/2022/02/22/compiler-team-ambitions-2022.html](https://blog.rust-lang.org/inside-rust/2022/02/22/compiler-team-ambitions-2022.html)


## Cargo Team 新增了新成员 

公开宣布，Weihang Lo和Ed Page 加入 Cargo 团队。 与此同时， Alex Crichton 退出 Cargo 团队。Alex 不仅仅是对 Cargo 做了很多贡献，他对 Rust 语言和 Rust 整体生态的贡献也是非常大的。当然， Alex 只是退出 Cargo 团队而已，他还活跃在 wasmtime 等wasm相关生态上。

Weihang Lo 写了一篇文章来纪念这件事：[【不必是眾星拱月那個月 也能替世界增添光芒 - 感謝 Alex Crichton 在 Cargo 的長期貢獻，沒有你就沒有現在的 Rust】](https://weihanglo.tw/posts/2022/make-the-world-better-even-you-arent-a-super-hero/) 。

[https://blog.rust-lang.org/inside-rust/2022/03/31/cargo-team-changes.html](https://blog.rust-lang.org/inside-rust/2022/03/31/cargo-team-changes.html)

## rustc编译器现已支持risc-v 的 `v`指令集和`k`指令集

现在 Rust标准库上就能做risc-v的内核开发和宿主机（虚拟机）开发了。

具体来说是标准库支持了hypervisor和supervisor的扩展指令。顺便加了个`is_riscv_feature_detcted!`宏，能在用户态检测riscv扩展是否存在，已经在真机上跑通，合理利用检测宏，能彻底解决“生态碎片”问题。

现在又新增了 V指令集和 K 指令集支持，预计进1.61稳定版rust。

由洛佳提供支持。相关 PR 。

- [https://github.com/rust-lang/rust/pull/94995](https://github.com/rust-lang/rust/pull/94995)
- [https://github.com/rust-lang/rust/pull/92574](https://github.com/rust-lang/rust/pull/92574)
- [https://github.com/rust-lang/stdarch/pull/1271](https://github.com/rust-lang/stdarch/pull/1271)


## nrc 博文｜ Rust in 2022

Rust 度过了美好的一年。从用户数量、领域多样性、用户承诺、用户规模等方面来看，Rust 现在拥有非常稳固的用户基础。Rust 还拥有大量慷慨的赞助商，无论是通过基金会还是通过公司支付民间费用。社区大多是健康的。在技​​术方面，Rust 语言、库和工具现在都非常可靠，2021版也顺利发布。

2022 年的 Rust，需要关注些什么呢？

2022 年，要解决的重要问题是项目领导问题和组织债务，并重新关注 Rust 的成熟度。同时，需要继续做下面这些正确的事：

- 维护工作、保持 Rust 运行（错误修复、性能改进、持续支持等），这是一项艰巨且大多乏味的任务。
- 基础设施和版本。
- 开发和维护 Rustup 和 Rustdoc 等基本工具。
- 完善 IDE 支持，这仍旧是 Rust 的一个弱点，但仍在不断改进。
- 研究人体工程学，并打磨语言、库、工具、文档等方面的不足之处
- 在更广泛的社区中使用 Rust 库。
- 审核和其他社区工作。
- 重构、偿还技术债务和核心工具（例如，Chalk）。
- 致力于形式化 Rust、Rust 的内存模型、更好地理解不安全代码、开发 miri 等。
- 改进错误处理库。
- 使用 const 泛型。

[https://www.ncameron.org/blog/rust-in-2022-2/](https://www.ncameron.org/blog/rust-in-2022-2/)

## 感谢 Joshua Nelson 为 rustdoc 所做的工作

有的人可能知道，jynelson 在成为 rustdoc 团队成员的 2 年后（后来成为联合负责人），最近辞去了该团队的领导职务。他在 rustdoc、docs.rs 和 rustc dev-guide 上的工作（通常是吃力不讨好的）有助于使 rust 成为一种令人惊叹的语言，可以为每个人编写和阅读文档。出于这个原因，我想花几分钟来强调一些我们有时认为理所当然的重大变化。

毫无疑问，最显而易见的功能改进是文档内链接。这允许您像路径导入一样（例如 [Foo](foo::Foo)）编写指向其他位置的链接，轻松无压力。这个概念已经进行了很长时间，但它曾经从根本上被破坏并且在多个方面无法使用。在超过 54 个 PR 中，文档内链接从一个破碎的概念变成了编写 Rust 文档的基本特征。

像所有组成 rust 治理的优秀团队成员一样，Joshua 也一直站在 FCP、PR、问题、RFC 等的前沿。在后台进行的这项繁琐的工作有助于使 rust 成为一种很棒的语言，然而无聊而且常常吃力不讨好。

还有无数的内部修复，以使 rustdoc 更少错误、更快、更清洁等。或者他在 docs.rs 和 rustc 开发指南上的工作。

[https://www.reddit.com/r/rust/comments/s0pcfa/lets_take_a_moment_to_thank_joshua_nelson_for_his/](https://www.reddit.com/r/rust/comments/s0pcfa/lets_take_a_moment_to_thank_joshua_nelson_for_his/)

## Nicholas: 2022年如何加速 Rust 编译器

在 2016 年到 2020 年间，Nicholas 写了一系列名为“如何加速 Rust 编译器”的博文。 这些主要是关于其在 Rust 编译器上的工作，以及总体上关于 Rust 编译器速度进展的一些更新。现在他作为 Futurewei 的 Rust 团队一员，是时候重新开始这个博客系列了。

- [https://nnethercote.github.io/2022/02/25/how-to-speed-up-the-rust-compiler-in-2022.html](https://nnethercote.github.io/2022/02/25/how-to-speed-up-the-rust-compiler-in-2022.html)
- [Compiler performance roadmap for 2022](https://hackmd.io/YJQSj_nLSZWl2sbI84R1qA?view)

## IDE 和 proc 宏

这篇文章探讨了 rust-analyzer 如何计算 proc 宏，以及列出当前 ra 存在的问题（包括其他 IDE），并给出可行的解决方案：改进 proc 宏，让它对 IDE 更加友好。

[https://veykril.github.io/posts/ide-proc-macros/](https://veykril.github.io/posts/ide-proc-macros/)

## Rust 的Unsafe Pointer类型需要大修

来自 gankra 的博文。作者经常思考 Rust 中的Unsafe Pointer问题。

作者写了一本关于 [unsafe Rust 的书（编按：也就是大家常说的 ”unsafe rust黑魔法”）](https://doc.rust-lang.org/nightly/nomicon)，还有关于 [Rust 实现链表的书](https://rust-unofficial.github.io/too-many-lists)。同时，作者重新设计了 Rust 的指针 API，并为不安全的堆分配缓冲区设计了标准库的抽象，并维护替代的 Vec 布局。

作者经常思考 Rust 中Unsafe Pointer的问题，并表示讨厌它们。但不要误会作者的意思，他认为自己的所有工作都让它们变得更好，但它们仍然存在严重缺陷。事实上，他们已经变得更糟了。不是因为 API 发生了变化，而是因为我们处理这些东西时，我们对指针应该如何工作的理解太天真了。

他认为 Unsafe 指针类型中的三个大问题：

- 允许把整数声明为指针 （Integer-To-Pointer Casts Are The Devil）
- 引用的有效断言过于严格（References Make Really Strong Assertions）
- 偏移和位置很混乱（Offsets And Places Are A Mess）

并且在文中给出了一些解决方案。

[https://gankra.github.io/blah/fix-rust-pointers](https://gankra.github.io/blah/fix-rust-pointers)

## Rust 中`#[doc]`属性的改进

从 Rust1.54 开始，我们现在可以在属性中使用类似函数的宏。 #[doc] 属性有很多优点，让我们来看看其中的一些！

例如只需在lib.rs文件的顶部编写 README 即可：
```rust
#![doc = include_str!("README.md")]
```
同时 README 文件现在将成为 crate 的文档，并且其中的代码示例也将被测试。

[https://blog.guillaume-gomez.fr/articles/2021-08-03+Improvements+for+%23%5Bdoc%5D+attributes+in+Rust](https://blog.guillaume-gomez.fr/articles/2021-08-03+Improvements+for+%23%5Bdoc%5D+attributes+in+Rust)

## let chains 有望在 1.62 到达稳定状态

如果下面的 pull 通过, let chains 的语法支持有望在 1.62 到到达稳定. 届时,下面的语法将会被支持.

很多小伙伴已经等了很久了.

```rust

fn main() {
    let outer_opt = Some(Some(1i32));
    
    if let Some(inner_opt) = outer_opt
        && let Some(number) = inner_opt
        && number == 1
    {
        println!("Peek a boo");
    }
}
```

[https://github.com/rust-lang/rust/pull/94927](https://github.com/rust-lang/rust/pull/94927)


## RFC 2789: Sparse Index

[RFC 2789](https://rust-lang.github.io/rfcs/2789-sparse-index.html) 不涉及具体的 Rust 语言特性，而是针对于 crates-io 索引的下载方式进行了变更，将原先使用的预先 Git Clone 模式改为了按需使用 HTTP 传输，相对优化了下载速度。



## RFC 3151: Scoped Threads

标准库新增了带有命名域限制的线程，具体可见[RFC 3151](https://rust-lang.github.io/rfcs/3151-scoped-threads.html)。举例如下：

```rust
let var = String::from("foo");

thread::scope(|s| {
    s.spawn(|_| println!("borrowed from thread #1: {}", var));
    s.spawn(|_| println!("borrowed from thread #2: {}", var));
});
```

线程可以借用父线程的变量，且在命名域之外无效。也就是说，当命名域结束的时候，所有的线程会调用`join()`方法，等待线程结束。不过同时，如果任何线程在调用`join()`时`panic`，那么`thread::scope()`也会`panic`。不过，也可以手动调用`join()`，例如：

```rust
let greeting = String::from("Hello world!");

thread::scope(|s| {
    let handle1 = s.spawn(|_| {
        println!("thread #1 says: {}", greeting);
    });

    let handle2 = s.spawn(|_| {
        println!("thread #2 says: {}", greeting);
    });

    // 也可以不调，当 thread::scope 结束的时候，会自动调用 join
    // 等待未结束的线程结束
    handle1.join().unwrap();
    handle2.join().unwrap();
});
```



对于`scope`以内的变量，线程无法直接借用，需要通过参数的形式传递。例如：

```rust
thread::scope(|s| {
    s.spawn(|s| {
        s.spawn(|_| {
            println!("I belong to the same `thread::scope()` as my parent thread")
        });
    });
});
```
