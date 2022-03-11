# 官方动态

---

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



[https://blog.rust-lang.org/inside-rust/2022/02/22/compiler-team-ambitions-2022.html](https://blog.rust-lang.org/inside-rust/2022/02/22/compiler-team-ambitions-2022.html)