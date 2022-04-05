# 开源观察 ｜ Rust 与 其他编程语言

## HVM: 基于 Rust 实现的用于大规模并行的最佳函数式运行时

HVM 全称 High-order Virtual Machine，高阶虚拟机，虽然只是一个原型，但性能优于 Haskell 的 GHC。HVM 的核心是一种非常简单的语言，类似于无类型的 Haskell。

HVM 采用最新的计算模型，即交互网络（Interaction Net）。该计算模型是图灵机（Turing Machine）和Lambda演算（Lambda Calculus）的结合，该模型的先前实现在实践中效率低下，然而，最近的一项突破大大提高了其效率，催生了 HVM。尽管是一个原型，但它在许多情况下已经击败了成熟的编译器，并且将朝着未知的性能水平扩展。

HVM 的运行时没有引用。相反，它具有一个`.clone()`成本为零的原语，直到需要时才会读取Clone的值（Lazy）。一旦完成，它不会被整个复制，而是逐层按需完成。换句话说，可以把 HVM 看作是一个将借用检查换做低成本Clone的 Rust。

特点：

- Lazy
- non-garbage-collected 
- massively parallel

- [HVM](https://github.com/Kindelia/HVM)
- [交互网络相关论文](https://www.researchgate.net/publication/220054026_Interaction_nets_programming_language_design_and_implementation)
- 作者相关的其他编程语言 [yatima](https://github.com/yatima-inc/yatima)

## frawk: 高效的类 awk 语言

frawk 是一种小型编程语言，用于编写处理文本数据的短程序。大致上，它是 AWK语言的一种实现。

类似于 awk：

```
awk - F','' NR > 1 { SUM += $2 } END { print SUM }'
```

这段代码可以对文件中的第二列求和：

```
Item,Quantity
Carrot,2
Banana,4
```

但是 frawk 性能更高。

- [https://github.com/ezrosent/frawk](https://github.com/ezrosent/frawk)
- [介绍](https://github.com/ezrosent/frawk/blob/master/info/overview.md)

## leo-lang: 用于零知识应用的函数式、静态类型语言


该语言被用于专门构建私有应用程序。是 [aleo](https://aleo.org) 公司出品，配合该公司开源的去中心化操作系统 [snarkOS](https://github.com/AleoHQ/snarkOS)。

[https://github.com/AleoHQ/leo](https://github.com/AleoHQ/leo)

## rulex: 一个新的正则表达式语言

为什么要用 rulex？

正常的正则表达式非常简洁，但是当它们变得更长时，它们变得越来越难以理解。Rulex 用一种新的、更简单的语法解决了这些问题。Rulex 目前与 PCRE、JavaScript、Java、.NET、Python、Ruby 和 Rust 兼容。正则表达式风格必须在编译期间指定，因此 rulex 可以确保生成的正则表达式在目标正则表达式引擎上按需要工作。

[https://github.com/Aloso/rulex](https://github.com/Aloso/rulex)

## melody: 一种编译为ECMAScript正则表达式的语言

目的和 rulex 类似，为了提升正则表达式的可读性和易维护性

- [https://github.com/yoav-lavi/melody](https://github.com/yoav-lavi/melody)
- 作者写了篇文章来记录用 Rust 实现语言的要点： [https://blog.yoavlavi.com/takeaways-from-building-melody/](https://blog.yoavlavi.com/takeaways-from-building-melody/)

## Glicol - 以图为导向的音乐实时编程语言

Glicol 可以帮助您使用代码制作音乐。您可以简单地连接不同的节点以形成循环，用 Rust 编写，得益于 WebAssembly，它可以在浏览器中丝滑运行。

- [https://glicol.org/](https://glicol.org/)
- [https://github.com/chaosprint/glicol](https://github.com/chaosprint/glicol)

## 将Ruby 的 JIT 编译器YJIT迁移到Rust

YJIT 是用于 Ruby 的 JIT 编译器，它采用了惰性基础块版本管理 (lazy Basic Block Versioning, LBBV) 架构。与当前的 CRuby 解释器相比，在进行实际基准测试时，YJIT 的平均速度大约提升了 23%。

现在YJIT团队希望探索使用Rust来帮助开发YJIT。而CRuby的其余部分将继续使用非Rust的工具链进行构建，而且构建Ruby应用时仍然支持YJIT开关。

价值主张：

- Rust的类型系统将尽早捕获更多的bug，有助于防止新的bug；
- 更易于管理日益复杂的YJIT；
- 更容易维护代码库；
- 对于新手来说更容易，因为编译器捕获了更多的bug；
- 更好的性能，因为我们可以实现更复杂的优化；
- 更容易添加对新平台的支持（这增加了复杂性）；
- Rust拥有成熟且易于安装的工具，如源代码格式化程序和编辑器插件；
- Rust作为一个编程语言社区，其背后有着巨大的热情。这可以转化为对YJIT和Ruby的更大热情。

集成：

- YJIT将只依赖于Rust语言和标准库，而不依赖于其他依赖项；
- YJIT将能够在没有互联网连接的情况下进行构建；
- Rust对交叉编译有很好的支持；
- Rust在我们计划使用YJIT支持的所有平台（Mac、Linux、Windows）上都受支持；
- 已编译的CRuby二进制文件不会对共享库有任何新的依赖项；
- 在禁用YJIT的情况下，CRuby仍然可以在没有rustc的情况下进行构建

Ruby 之父 Matz 对此表态：“我可以使用 Zig 来实现 YJIT，只要解决了可预见的可移植性问题（如上所述）。
除此之外，作为一名长期的 C 程序员，我更喜欢 Zig 而不是 Rust，但这一切都取决于你。”

发起人回复：“在与团队进行更多讨论后，我们决定暂时继续使用 Rust。Rust 拥有如此庞大的爱好者社区这一事实是一个重要因素。这使得查找文档、帮助以及招聘人才变得更加容易。语言和生态系统的成熟也是一大优势。我本人非常偏爱 Zig，因为它有一些非常强大的功能（例如编译时代码执行），并且与 C 的互操作比与 Rust 的互操作要容易一些。然而，Zig 还没有达到 1.0 版本的事实对我们来说有点问题。”

完整的意见说明参阅：[Porting YJIT to Rust (request for feedback)](https://bugs.ruby-lang.org/issues/18481)

## Juliaup，Rust开发的 Julia 安装程序

Julia 即将推出安装程序 Juliaup，它由两个二进制文件组成：juliaup 和 julialauncher。juliaup 创建者在 Julia 更新计划中讲述了他为什么用 Rust 编写它。

> juliaup 本身最初是用 Julia 编写的，然后使用 PackageCompiler.jl 进行编译。但这从来都不是一个真正顺利的体验，原因有很多。生成的二进制发行版非常庞大（几百 MB），这对于一个简单的小型命令行工具来说确实不理想，启动延迟不是很好（不可怕，但不是很好），编译时间真的很长，这一切都只是真的粗糙的。因此，在某个时候，当我已经将 julialauncher 移植到 Rust 时，我也将其余部分也移植了过来。我对这个选择非常非常满意，静态类型，非常不同的错误处理模型等，使其成为科学代码的完全非入门者，但对于像 Juliaup 这样的东西来说，它是一个非常好的匹配（比像 Julia、IMO 这样的动态语言要好得多）。—— juliaup creator: David Anthoff

[https://discourse.julialang.org/t/plans-for-updating-julia/75880/15](https://discourse.julialang.org/t/plans-for-updating-julia/75880/15)

## Rust的动态语言扩展？

C语言程序员使用Lua来扩展他们应用程序的功能，Rust可以使用哪些类似的语言？

以下是一个可嵌入Rust代码中的脚本语言的汇总列表：

[https://arewegameyet.rs/ecosystem/scripting/](https://arewegameyet.rs/ecosystem/scripting/)

## rs-lisp：一个用Rust编写的小型lisp解释器

[https://github.com/ktfth/rs-lisp](https://github.com/ktfth/rs-lisp)