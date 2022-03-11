# 博文教程

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
