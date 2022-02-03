# 基于Rust的新编程语言

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