# 博文教程

## 一本开源的线上 Rust 新书 《High Assurance Rust: Developing Secure and Robust Software》

看上去作者像是安全从业背景。不过目前内容还未写完，看内容组织结构比较适合进阶。

[https://highassurance.rs/](https://highassurance.rs/)

## Rust 借用检查规则注定被“打破”

这篇文章想表达的内容并不是真的要“打破”借用检查，它是提供了一个视角来介绍 `Rc/Arc/Cell/RefCell/Mutex/Atomics`等。

- [https://blog.warp.dev/rules-are-made-to-be-broken/](https://blog.warp.dev/rules-are-made-to-be-broken/)
- [Reddit 讨论](https://www.reddit.com/r/rust/comments/t54pab/rusts_rules_are_made_to_be_broken/)

## Shaking off the Rust : 系列 Rust 学习博客

通过实际构建东西，将在此过程中了解一系列技术概念，并提高 Rust 的能力。

[https://www.sotr.blog/](https://www.sotr.blog/)

## nom 解析比特流

该文章介绍了如何使用 Nom v7 来解析二进制数据，比如 DNS 协议头。

在该文章相关的 Reddit 讨论中，有网友提出解析 DNS 协议头并不需要使用 nom，而利用位运算来提取相关部分是常规操作。并提供了一个库 [bitstream-io](https://github.com/tuffy/bitstream-io)，提供了方便的接口来处理媒体编解码时候可能出现的一些位偏移问题。但作者使用nom是为了玩 [https://adventofcode.com/](https://adventofcode.com/)，学习目的。

其他网页也给出了一些看法和库，了解细节可以看看 reddit 讨论。

当然，Rust 生态也有生产级的 DNS 解析库 : [trust-dns](https://github.com/bluejekyll/trust-dns) 。


- [https://blog.adamchalmers.com/nom-bits/](https://blog.adamchalmers.com/nom-bits/)
- [https://www.reddit.com/r/rust/comments/t52iuz/parsing_bitstreams_with_nom/](https://www.reddit.com/r/rust/comments/t52iuz/parsing_bitstreams_with_nom/)

## Bevy 游戏开发者指南（非官方）

- [https://bevy-cheatbook.github.io/introduction.html](https://bevy-cheatbook.github.io/introduction.html)
- [中文翻译](https://yiviv.com/bevy-cheatbook/introduction.html)


## Rust写Python扩展的9个原则

- 创建一个同时包含 Rust 和 Python 项目的仓库。
- 使用 `maturin` & `PyO3` 在 Rust 中创建 Python 可调用的翻译器函数。
- 让 Rust 翻译器函数调用 “好的” Rust 函数。
- 在 Python 中预分配内存。
- 将好的 Rust 错误处理转换为好的 Python 错误处理。
- 使用 `Rayon` 和 `ndarray::parallel` 的多线程，返回任何错误。
- 允许用户控制并行线程数。
- 将好的动态类型 Python 函数转换为好的 Rust 通用函数。
- 同时创建 Rust 和 Python 的测试。

[https://towardsdatascience.com/nine-rules-for-writing-python-extensions-in-rust-d35ea3a4ec29](https://towardsdatascience.com/nine-rules-for-writing-python-extensions-in-rust-d35ea3a4ec29)


## 了解原子和内存排序的工作原理

作者使用一个带有简单单元测试的 repo 增强对这个主题的理解。其中，包含了一个简单例子和一个复杂例子。

[https://github.com/blasrodri/atomic-story](https://github.com/blasrodri/atomic-story)

## Rust 和 Valgrind 配合使用

这篇文章讲了为什么 Valgrind 对 Rust 是有用的。Valgrind 不止是内存错误探测工具。

[https://nnethercote.github.io/2022/01/05/rust-and-valgrind.html](https://nnethercote.github.io/2022/01/05/rust-and-valgrind.html)

## 让 `include_dir` 更加现代化

`include_dir` 这个crate提供了一个`include_dir!`宏，用于在编译时将整个目录树嵌入你的二进制文件中。这就像`include_str!`，但针对整个目录。

作者最近做了大量的工作来改善这个crate，增加了想要的功能，改善了编译时间，并巩固了它的核心目标。

你可以查看[入门指南](https://github.com/crev-dev/cargo-crev/blob/master/cargo-crev/src/doc/getting_started.md)以了解更多。

[https://adventures.michaelfbryan.com/posts/bringing-include_dir-into-the-modern-era/](https://adventures.michaelfbryan.com/posts/bringing-include_dir-into-the-modern-era/)

## 使用 Rust 为 RISC-V OS 实现自旋锁

自旋锁是最基本的同步实现之一，也是实现操作系统时首先要考虑的组件之一。

本文将简要回顾自旋锁的基础知识，如何在 Rust 中为自制操作系统实现它，以及它相对于 C 语言的优势。

本文针对 RISC-V 架构平台。

CPU 支持原子操作指令，这些指令也用于实现自旋锁。在 RISC-V 的情况下，有 AMO（原子内存操作）和 Load-Link/Store-Conditional 指令，它们都可以执行或实现以下操作。

[https://vmm.dev/en/rust/spinlock.md](https://vmm.dev/en/rust/spinlock.md)

## 使用 Nom 解析文本

本教程使用Nom解析输入文件到一个Advent of Code的谜题。

[https://blog.adamchalmers.com/nom-chars/](https://blog.adamchalmers.com/nom-chars/)

## Bevy是如何使用 Rust traits 来添加标签的

出于好奇，作者最近开始关注Bevy的开发，Bevy是一个用Rust编写的游戏引擎。今天作者想谈谈Bevy如何使用Rust特性让用户非常方便地为元素添加标签。

注意: 这里的实现实际上是非常通用的, 您可以很容易地将其应用到任何其他Rust项目

[https://deterministic.space/bevy-labels.html](https://deterministic.space/bevy-labels.html)

## （基础）精美图表详解线段树！

给定一个长度为N的数组arr，如果有以下操作：

查询区间[l,r]之间的最大值/最小值；
- 更新给定下标i的对应的值arr[i]；
- 则可以使用线段树来高效解决上述场景的查询和更新需求；

线段树的时空复杂度：
- 时间复杂度：O(logN)
- 空间复杂度：O(N)

[(Basic) Segment Trees with beautiful diagrams!](https://desmondwillowbrook.github.io/blog/competitive-programming/dsa-explanations/basic-segment-tree/)

## 为什么静态语言会受到复杂性的影响

编程语言设计社区的人们努力使他们的语言更具表现力，具有强大的类型系统，主要是通过避免最终软件中的代码重复来增加人体工程学；然而，他们的语言越有表现力，重复就越突然地渗透到语言本身。

这篇文章与语言无关，对编程语言理论感兴趣的朋友可以一读。

[https://hirrolot.github.io/posts/why-static-languages-suffer-from-complexity](https://hirrolot.github.io/posts/why-static-languages-suffer-from-complexity)

## quickref.me Rust

QuickRef.ME 是一个汇聚了大部分语言的语法索引页, 其中也包含了 Rust, 可以帮助大家快速找到想用的语法.

[https://quickref.me/rust](https://quickref.me/rust)

## 博文：深入探讨Rust中的内存分配

[https://ysantos.com/blog/malloc-in-rust](https://ysantos.com/blog/malloc-in-rust)

## 使用 Rust 将你的步行数据可视化到地图上

结合 Rust，OSRM, OpenStreetMap 的一次尝试。

[https://blog.dend.ro/mapping-my-walks-osrm-rust/](https://blog.dend.ro/mapping-my-walks-osrm-rust/)

## 一次 Rust 硬件开发的尝试

作者开发了一个 USB 硬件密码存储器。

[https://github.com/sjm42/blackpill-usb-pwdstore](https://github.com/sjm42/blackpill-usb-pwdstore)

## async-await：协作式调度 vs 抢占式调度

抢占式调度

抢占式调度是指任务的调度不受开发人员控制，完全由运行时管理。无论程序员是启动同步任务还是异步任务，代码都没有区别。例如，Go编程依赖于抢占式调度。它的优点是更容易学习：对于开发人员来说，同步代码和异步代码之间没有区别。此外，几乎不可能误用：运行时会处理所有事情。

协作式调度

通过协作调度开发人员负责告诉运行时一个任务何时需要花费一些时间等待 I/O。await 这是关键字的确切用途。这是运行时（和编译器）的指示，该任务将花费一些时间等待操作完成，因此计算资源可以同时用于另一个任务。它的优点是速度极快。基本上开发人员和运行时正在协同工作，以充分利用可支配的计算能力。协作调度的主要缺点是它更容易被误用：如果 aawait 被遗忘（幸运的是，Rust 编译器会发出警告），或者如果事件循环被阻塞超过几微秒，它会对系统的性能产生灾难性的影响。 文章对两种调度进行了更细致的介绍：

[https://kerkour.com/cooperative-vs-preemptive-scheduling/](https://kerkour.com/cooperative-vs-preemptive-scheduling/)

## 为什么 Rust 标准库要保持足够小？

通常，刚接触 Rust 编程语言的人会问“为什么标准库中没有随机数生成/正则表达式/日期时间/序列化等基本功能？为什么我需要为这些功能导入质量未知的第三方库？

这篇文章给你解释。

[https://blog.nindalf.com/posts/rust-stdlib/](https://blog.nindalf.com/posts/rust-stdlib/)

## Panic 与 异步取消 Part 1

Rust 语言团队 leader Niko的这篇文章的重点是解释 Rust 的恐慌（Panic）哲学，并解释为什么niko认为恐慌和取消非常相似。

[https://smallcultfollowing.com/babysteps//blog/2022/01/27/panics-vs-cancellation-part-1/](https://smallcultfollowing.com/babysteps//blog/2022/01/27/panics-vs-cancellation-part-1/)

## Rust 的热加载

本文探讨了 Rust 中的热加载在 windows 和 Linux 上的不同差异.

[https://johnaustin.io/articles/2022/hot-reloading-rust](https://johnaustin.io/articles/2022/hot-reloading-rust)

## Rust中的内存映射文件

作为一名Rust新手，作者想在自己的第一个项目中处理一些非常大的二进制文件，读取它们，遍历它们，等等。 但是作者在搜索支持内存映射文件的Rust库时发现，memmap有超过700万次下载，但是它已经超过3年没有更新。 以下是作者的一些顾虑：

- 可以想象在过去的三年里，很多事情都发生了变化。但是这个库三年没有更新了，这个库还有用吗？
- 有没有更好的（非废弃的）替代库？
- 如果我用这个库作为依赖项启动一个大型项目，我将来会遇到什么问题？

其实针对这个问题，RUSTSEC上已经有条目(RUSTSEC-2020-0077)针对这个风险作出了提示，并给出了两个可选的替代开源库:

- [mapr](https://github.com/filecoin-project/mapr)
- [memmap2](https://github.com/RazrFalcon/memmap2-rs)

## 数据结构和算法（Rust）

内容非常丰富的一本开源 Rust 算法和数据结构的书。

[https://github.com/QMHTMY/RustBook](https://github.com/QMHTMY/RustBook)

## cargo 和 pip 是不同的

文章作者总结了11条，这两个包管理器的不同之处，其实这些不同也牵涉到语言级的原因。

[https://blog.williammanley.net/2022/02/23/pip-and-cargo-are-not-the-same.html](https://blog.williammanley.net/2022/02/23/pip-and-cargo-are-not-the-same.html)

## 好文：对Rust中unsized变量的分析

在Rust语言中，unsized变量一直都没有人去认真做过这方面的分析工作，编译器一直也没有对这块做太多的功能支持。作者在这个主题上从历史开始做了深入的详尽的分析。值得学习。

[https://poignardazur.github.io/2022/02/23/rust-unsized-vars-analysis/](https://poignardazur.github.io/2022/02/23/rust-unsized-vars-analysis/)

## Rust 和 OpenCV

这是一篇 OpenCV 在 Rust 中的使用指导, 非常的详尽.

[https://blog.devgenius.io/rust-and-opencv-bb0467bf35ff](https://blog.devgenius.io/rust-and-opencv-bb0467bf35ff)

## 我的第一个 Clippy Lint

作者自己动手写了一个clippy的 lint,其过程比他想象中的要简单和容易测试的多. Clippy团队在提供示例、快速代码审查和大量易于使用的共享utils代码方面做了大量工作。

[https://jamesmcm.github.io/blog/2022/03/26/my-first-clippy-lint/#en](https://jamesmcm.github.io/blog/2022/03/26/my-first-clippy-lint/#en)

## Rust wgpu 图形编程系列教程

- 视频：[https://youtu.be/-QXj0UexUw0](https://youtu.be/-QXj0UexUw0)
- 文章：[https://github.com/jack1232/wgpu-step-by-step](https://github.com/jack1232/wgpu-step-by-step)

## Rust 使用 I2C 的方式读取传感器数据

本文讲述了如何使 Embedded Rust 提供的驱动, 通过 I2C 的方式来读取和修改传感器的数据.

[https://lupyuen.github.io/articles/rusti2c](https://lupyuen.github.io/articles/rusti2c)

## 过程宏内幕详解：Part 1

你有没有想过Rust的过程宏如何工作？在这个博客文章中，我们将进入细节！我们团队的成员Vladislav Beskrovny，最近谈到了RustCon的主题。该系列基于这个主题，有一些略微的修改和添加。

在这篇文章中，我们将查看Rust宏的基础知识并进入过程宏细节，密切关注他们的API。

[https://blog.jetbrains.com/rust/2022/03/18/procedural-macros-under-the-hood-part-i/](https://blog.jetbrains.com/rust/2022/03/18/procedural-macros-under-the-hood-part-i/)

## 开源书籍《Effective Rust》

模仿 《Effective C++》 内容风格而编写的一本 Rust 新的开源书。

构成本书的项目分为六个部分：

- Types：围绕 Rust 的核心类型系统提出的建议。
- 概念：构成 Rust 设计的核心思想。
- Dependencies：关于使用 Rust 包生态系统的建议。
- 工具：关于如何通过超越 Rust 编译器来改进代码库的建议。
- 异步 Rust：使用 Rustasync机制的建议。
- Beyond Standard Rust：当你必须在 Rust 的标准、安全环境之外工作时的建议。

[https://www.lurklurk.org/effective-rust/intro.html](https://www.lurklurk.org/effective-rust/intro.html)

## 使用Rust实现一个有大小限制可过期的LRU缓存

跟着作者一起学习，如何使用Rust编程语言为DNS服务器实现一个有大小限制的LRU缓存，并有过期的条目。

[https://memo.barrucadu.co.uk/dns-cache.html](https://memo.barrucadu.co.uk/dns-cache.html)

## Rust 交叉编译

[https://kerkour.com/rust-cross-compilation](https://kerkour.com/rust-cross-compilation)