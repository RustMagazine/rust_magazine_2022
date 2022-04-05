# 开源观察 ｜ 云原生

## Fermyon 推出WebAssembly 框架 spin 

可以配合TO-D杂志第二季第一篇文章来看，能让人深刻理解这些开源项目背后的动机，这比单看他们的开源项目可有趣的多。话说回来，我觉得 Fermyon 的成立，应该是 deislabs 团队这这几年尝试的过程中看到了一些新的风口，他们的项目应该多多关注。

> 《TO-D 杂志》第二季第一篇文章正式发布，这篇文章是关于微软 DeisLabs 的故事，欢迎大家阅读转发 [https://2d2d.io/s2/deislabs/](https://2d2d.io/s2/deislabs/) ，Deis Labs 开发了不少非常前沿的 WebAssembly 和 Rust 项目，现在他们团队中九人出来创业，成立新公司 Fermyon。

> 关于 Fermyon 名称由来的猜想：这个单词比较贴近费米子（fermion）。所以查了下相关概念，得出一些推论。费米子和玻色子都是宇宙基本粒子，都有自旋属性。但是费米子喜欢独来独往（泡利不相容原理，只适合费米子。 在任何量子系统中，两个费米子无法占据同一个量子态），而玻色子喜欢共享。用费米子来对应wasm的沙盒隔离性。而他们的主打项目名叫 spin 。他们这么费劲起这个名字，我想到一个原因就是避免别人抢注商标。因为之前发生过 Wasmer 抢注商标的事（Wasmer 抢注了WasmEdge的商标，甚至还把 WebAssembly 抢注为自己公司的商标）。这也算是一种商标保护。

**为什么是 Spin ？**

Fermyon 对在 WebAssembly 中编写微服务和服务器端 Web 应用程序的前景感到极度兴奋。他们在构建 [Wagi](https://github.com/deislabs/wagi) 时对此进行了预览。但有了 Spin，我们将事情提升到了一个新的水平。更具体地说，Spin 提供了一个构建应用程序的框架。

Spin 是一个用于 Web 应用程序、微服务和其他类似服务器的应用程序的框架。它提供了用于编写 WebAssembly 模块的接口，这些模块可以执行诸如响应 HTTP 请求之类的操作。Spin 的一个独特之处在于它是一个多语言框架。Rust 和 Go 在 Spin 中都有强大的支持，但您也可以编写 Python、Ruby、AssemblyScript、Grain、C/C++ 和其他语言。

Spin 是一项基础性的新技术，它为 Fermyon 正在构建的东西设定了步伐。Fermyon 的目标是为下一代微服务和 Web 应用程序构建一个优秀的开发平台，实现无服务器计算长期以来追求的许多目标。


- [https://github.com/fermyon/spin](https://github.com/fermyon/spin)
- [https://www.fermyon.com/blog/introducing-spin](https://www.fermyon.com/blog/introducing-spin)

**Fermyon WebAssembly Language Guide**

本指南跟踪对将语言编译为 WebAssembly 的支持。它分为三个部分：支持前 20 种语言、WebAssembly 特定语言和其他著名语言。我们跟踪该语言是否可以编译为在浏览器、其他非浏览器环境和WASI环境中运行。在每种语言的详细信息页面中，我们尽最大努力不仅说明当前的支持级别，还指出一系列有用的资源。

- [https://github.com/fermyon/wasm-languages](https://github.com/fermyon/wasm-languages)
- [https://www.fermyon.com/wasm-languages/webassembly-language-support](https://www.fermyon.com/wasm-languages/webassembly-language-support)

## Blueboat 是 Cloudflare Workers 的开源替代品。

Blueboat 旨在成为面向无服务器 Web 应用程序的开发人员友好型多租户平台。遵循整体方法：我们尝试在 Rust 中本地实现常用库（在 Web 应用程序上下文中）的特性，以替换原生 Node 插件，提高性能并减少重复代码。Blueboat 的架构可确保平台的安全性，防止代码重复并保持低开销。

[https://github.com/losfair/blueboat](https://github.com/losfair/blueboat)


## Starlane 是一个资源网格

使微服务能够在整个企业中创建、查找、监视和使用各种微资源以及向其他微服务发送消息。 Starlane 还提供了用于部署、执行和连接客户端和服务器端 WebAssembly 参与者（称为 Mechtrons）的机制。

Starlane 使在云、边缘、桌面、移动和物联网环境中安全的 WebAssembly 代码的部署和互操作变得容易。

- [http://starlane.io/](http://starlane.io/)
- [http://starlane.io/docs/tutorial/](http://starlane.io/docs/tutorial/)
- [https://github.com/mechtronium/starlane/](https://github.com/mechtronium/starlane/)

## Youki v0.0.3 版本更新，增加 WASM 支持

youki 是一个 Rust 实现的容器运行时，是 OCI 运行时规范的实现，类似于 runc。此次 0.0.3 版本带来了 wasm 支持。

[https://github.com/containers/youki](https://github.com/containers/youki)


## 去容器化! 如何在 k8s 上借助 Rust 运行 Wasm

Wasm 是当下最火热的技术之一，作者介绍了如何将其与 K8s 结合起来，同时说明了为何 Rust 是最适合开发 Wasm 的语言。

[https://developer.okta.com/blog/2022/01/28/webassembly-on-kubernetes-with-rust](https://developer.okta.com/blog/2022/01/28/webassembly-on-kubernetes-with-rust)