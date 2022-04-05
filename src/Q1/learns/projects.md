# 开源项目

值得学习的开源项目

---

## type-exercise-in-rust: 数据库表达式执行的黑魔法：用 Rust 做类型体操

该项目通过在数据库系统中实现表达式框架来学习 Rust 黑魔法，围绕 Rust 程序员如何使用 Rust 编程语言构建数据库系统展开：利用 Rust 类型系统来最小化运行时成本，并使用安全的 Nightly Rust 使开发过程更容易。

[https://github.com/skyzh/type-exercise-in-rust](https://github.com/skyzh/type-exercise-in-rust)

## jotsy: 一个自托管、免费和开源的笔记应用程序

后台基于 Rust / [Skytable](https://github.com/skytable/skytable) / [Tokio](https://tokio.rs/) / [Axum](https://github.com/tokio-rs/axum) / [Askama](https://github.com/djc/askama) 实现。前端就是纯 HTML、CSS 和 JavaScript 构建。

功能非常简单，可以基于该项目去扩展功能，也可以学习 Rust 使用。

- [https://github.com/ohsayan/jotsy](https://github.com/ohsayan/jotsy)
- [介绍文章](https://ohsayan.github.io/2022/02/jotsy-a-self-hosted-notes-app/)

## bevy 实现的扫雷游戏

- [系列文章](https://dev.to/qongzi/series/16975)
- [项目](https://gitlab.com/qonfucius/minesweeper-tutorial)

## git-smart-checkout: Rust实现的智能 git checkout 命令行应用

这是作者写的第一个 Rust 项目，然后reddit 网友给他的代码留了一些 review 建议：

- [https://github.com/craciuncezar/git-smart-checkout/blob/main/src/git_client.rs#L7](https://github.com/craciuncezar/git-smart-checkout/blob/main/src/git_client.rs#L7) 这些都可以只是函数。Rust不是OOP，所以如果你不需要状态，不需要用结构把所有这些东西绑定在一起也是可以的。
- 对于GitBranch，你可以只需要`#derive(Clone)`而不是自己实现它。[https://github.com/craciuncezar/git-smart-checkout/blob/main/src/git_client/git_branch.rs#L4](https://github.com/craciuncezar/git-smart-checkout/blob/main/src/git_client/git_branch.rs#L4) 参考文献：[https://doc.rust-lang.org/rust-by-example/trait/derive.html](https://doc.rust-lang.org/rust-by-example/trait/derive.html)。
- 在Rust中，如果你能在可能的情况下将`clone`给调用者负责，而不是自己去做，那是最好的，在 [https://github.com/craciuncezar/git-smart-checkout/blob/main/src/git_client/utils.rs#L3](https://github.com/craciuncezar/git-smart-checkout/blob/main/src/git_client/utils.rs#L3) ，我认为`to_vec`在底层做了一个clone。我的意思是，如果你需要某个东西的明确拷贝，就用所有权而不是引用，但让调用者决定他是想给你所有权还是克隆这个值给你。
- 在这个只在一个地方使用的`fn`的具体案例中，我认为你一般不需要它，但如果你要在多个地方使用它，你可以把`Vec`传成可变的，并立即排序，不需要clone。你也可以将`Vec<GitBranch>`包装成一个新的类型，并为其实现`fn` [https://rust-unofficial.github.io/patterns/patterns/behavioural/newtype.html](https://rust-unofficial.github.io/patterns/patterns/behavioural/newtype.html)。
- 你的模块和类型的另一种构造可以是`git`、`git::client`、`git::client::switch_to_branch`、`git::Branch`。
- 当把一个`Vec`引用传递给一个函数时，通常作为切片接收：`branches: &[GitBranch]`更方便。调用代码保持不变，但现在可以传递数组以及Vec去调用该函数。通常情况下，`String`和`&str`也是如此。
- 建议是试一下`anyhow`。你会发现错误处理会变的容易。


## 【系列】 数据库表达式执行的黑魔法

 type-exercise-in-rust (GitHub) 中已经包含了一整套数据库执行器的类型设计，本系列将详细介绍整个设计过程中的思考过程和解决方案。

- [文章合集](https://www.zhihu.com/column/c_1338891056075272192)
- [https://github.com/skyzh/type-exercise-in-rust](https://github.com/skyzh/type-exercise-in-rust)

## mwbot-rs：快速构建WikiBot

用 Rust 构建快速的 Wikipedia 机器人。

- [https://blog.legoktm.com/2022/01/21/building-fast-wikipedia-bots-in-rust.html](https://blog.legoktm.com/2022/01/21/building-fast-wikipedia-bots-in-rust.html)
- [https://gitlab.com/mwbot-rs](https://gitlab.com/mwbot-rs)

## flux: 用 Rust 和 wasm 重新创建 macOS的 Drift 屏幕保护程序

作者一直迷恋 Drift 屏幕保护程序，自从它出现在macOS Catalina。作者使用 Rust 和 wasm 重新在浏览器中创建了该效果.

[https://github.com/sandydoo/flux](https://github.com/sandydoo/flux)

## Jotsy:一个由Skytable、Axum和Tokio支持的自托管笔记应用程序

我很高兴宣布jotsy - 一个自托管，免费和开源（Apache-2.0）的笔记程序，使用Skytable，Axum和Tokio构建。jotsy最重要的目标是简单，专注于最重要的事情，记笔记。

- [https://www.reddit.com/r/rust/comments/skizcp/jotsy_a_selfhosted_notes_app_powered_by_skytable/](https://www.reddit.com/r/rust/comments/skizcp/jotsy_a_selfhosted_notes_app_powered_by_skytable/)
- [https://github.com/ohsayan/jotsy](https://github.com/ohsayan/jotsy)


## 让我们用 Rust 和 Yew 0.19 构建一个 WebSockets 项目

作者介绍如何使用 Yew、yew-router、yew-agent 和其他几个项目构建一个聊天应用程序

[https://blog.devgenius.io/lets-build-a-websockets-project-with-rust-and-yew-0-19-60720367399f](https://blog.devgenius.io/lets-build-a-websockets-project-with-rust-and-yew-0-19-60720367399f)

## 用 Rust 实现 NTFS 文件系统

是时候用 Rust 重写我们的系统软件并摆脱 C 的陷阱了。这个过程中还包括文件系统支持等基础知识。作者接受了在 Rust crate 中实现 NTFS（Windows 使用的主要文件系统）的挑战，并且从固件级别到用户模式都同样可用。

[https://fosdem.org/2022/schedule/event/misc_ntfs_rust/](https://fosdem.org/2022/schedule/event/misc_ntfs_rust/)

## Rust 实现 flappybird 游戏

[https://github.com/JerzySpendel/flappybird](https://github.com/JerzySpendel/flappybird)

## toipe: 终端打字测试应用

[https://github.com/ChaosStudyGroup/toipe](https://github.com/ChaosStudyGroup/toipe)

## neural-network-from-scratch 项目是一个用 Rust 从头开始​​编写的神经网络库

以及一个用于构建 + 训练神经网络 + 可视化其输出的基于 Web 的应用程序

[https://github.com/Ameobea/neural-network-from-scratch](https://github.com/Ameobea/neural-network-from-scratch)

