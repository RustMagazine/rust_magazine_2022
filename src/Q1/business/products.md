# 商业观察 ｜ 优秀产品


## Fermyon 推出WebAssembly 框架 spin 

可以配合TO-D杂志第二季第一篇文章来看，能让人深刻理解这些开源项目背后的动机，这比单看他们的开源项目可有趣的多。话说回来，我觉得 Fermyon 的成立，应该是 deislabs 团队这这几年尝试的过程中看到了一些新的风口，他们的项目应该多多关注。

> 《TO-D 杂志》第二季第一篇文章正式发布，这篇文章是关于微软 DeisLabs 的故事，欢迎大家阅读转发 [https://2d2d.io/s2/deislabs/](https://2d2d.io/s2/deislabs/) ，Deis Labs 开发了不少非常前沿的 WebAssembly 和 Rust 项目，现在他们团队中九人出来创业，成立新公司 Fermyon。

> 关于 Fermyon 名称由来的猜想：这个单词比较贴近费米子（fermion）。所以查了下相关概念，得出一些推论。费米子和玻色子都是宇宙基本粒子，都有自旋属性。但是费米子喜欢独来独往（泡利不相容原理，只适合费米子。 在任何量子系统中，两个费米子无法占据同一个量子态），而玻色子喜欢共享。用费米子来对应wasm的沙盒隔离性。而他们的主打项目名叫 spin 。他们这么费劲起这个名字，我想到一个原因就是避免别人抢注商标，这也算是一种商标保护。

**为什么是 Spin ？**

Fermyon 对在 WebAssembly 中编写微服务和服务器端 Web 应用程序的前景感到极度兴奋。他们在构建 [Wagi](https://github.com/deislabs/wagi) 时对此进行了预览。但有了 Spin，我们将事情提升到了一个新的水平。更具体地说，Spin 提供了一个构建应用程序的框架。

Spin 是一个用于 Web 应用程序、微服务和其他类似服务器的应用程序的框架。它提供了用于编写 WebAssembly 模块的接口，这些模块可以执行诸如响应 HTTP 请求之类的操作。Spin 的一个独特之处在于它是一个多语言框架。Rust 和 Go 在 Spin 中都有强大的支持，但您也可以编写 Python、Ruby、AssemblyScript、Grain、C/C++ 和其他语言。

Spin 是一项基础性的新技术，它为 Fermyon 正在构建的东西设定了步伐。Fermyon 的目标是为下一代微服务和 Web 应用程序构建一个优秀的开发平台，实现无服务器计算长期以来追求的许多目标。


- [https://github.com/fermyon/spin](https://github.com/fermyon/spin)
- [https://www.fermyon.com/blog/introducing-spin](https://www.fermyon.com/blog/introducing-spin)

## Meilisearch 获 500 万美元种子轮融资

Meilisearch 是一个开源的搜索引擎项目，并使用 Rust 作为首选开发语言。

Meilisearch 接下来的计划包括：

- 向开发者提供需要的工具和支持，使他们能够用 Meilisearch 创建令人惊叹的网站和应用程序。
- 继续研发 Meilisearch Cloud，帮助用户使用由 Meilisearch 团队管理的实例并进行优化。

[Meilisearch raises a $5M Seed to change the world of user-facing search](https://blog.meilisearch.com/meilisearch-raised-5meu-seed-fundraising/)

## 分析API平台cube.js 发布新版本

Cube.js 是一个开源的分析 API 平台。 它主要用于构建内部商业智能工具或将面向客户的分析添加到现有应用程序中。

Cube.js 旨在与无服务器数据仓库和查询引擎（如 Google BigQuery 和 AWS Athena）一起使用。多阶段查询方法使其适用于处理数万亿个数据点。 大多数现代 RDBMS 也可以与 Cube.js 一起使用，并且可以进一步调整性能。

[https://github.com/cube-js/cube.js](https://github.com/cube-js/cube.js)

## Fornjot 发布v0.5

Fornjot 是创建下一代 Code-CAD 应用程序，它目前属于一个早期项目。然而，该项目正在稳步推进，这个新版本支持更多平台、更灵活的定义 CAD 模型的工具、各种修复以使其更强大，以及大量内部清理以实现未来改进。

基本建模功能

在这一点上，Fornjot支持基本的2D形状（由线段、圆和它们之间的有限组合组成的草图），沿着直线路径扫过这些2D形状以创建3D形状，以及对构造实体几何（CSG）的一些非常不完整的支持。

短期到中期的优先事项是提供固体CSG支持、更灵活的草图和更灵活的扫描（沿着圆或螺旋）。长期计划是不断增加更高级的CAD建模功能，以支持甚至复杂的模型和工作流程。

平台支持

截至目前，Fornjot在Linux、Windows和macOS上运行。该项目主要是在Linux上开发的，所以其他平台可能会有bug。

中短期的计划是增加对网络平台的支持，因此Fornjot可以在浏览器中运行。长期来看，计划是增加对主要移动平台的支持。

导出到3MF

支持将模型导出到3D制造格式（3MF），这在3D打印中使用。

[https://www.fornjot.app/blog/fornjot-0-5-0/](https://www.fornjot.app/blog/fornjot-0-5-0/)

## CADBase 平台发布

经过4年的设计和开发，我们团队很高兴地宣布CADBase项目正式启动(https://cadbase.rs/)。

CADBase设计用于存放图纸和相关材料(标准、供应商、支持文件)的信息。

资源的主要部分是包含以下数据的组件页面:

- 组件的基本信息、特性和相关文件。
- 修改组件的参数和相关文件。
- CAD和其他程序解决方案的文件集。
- 组件相关材料信息: 标准、供应商、目录、关键词。

CADBase 在后端使用 Actix-web ( https://crates.io/crates/actix-web )，在前端使用 Yew ( https://crates.io/crates/yew )，CSS 框架 Bulma ( https:// bulma.io/）。

[https://cadbase.rs/](https://cadbase.rs/)

## superconsole : 用于构建 Rust TUI 的基于组件的框架

提供了强大的基于行的抽象，而不是基于文本的终端呈现。它还提供了基本的构建块，如行操作，以及更高级别的可组合组件。包括一组基本的“电池”组件，以帮助开发人员尽快创建 TUI。它是跨平台的，支持 Windows 7+、Linux 和 MacOS。想要创建非交互式 TUI 的 Rustaceans 可以使用组件组合构建块系统来快速部署他们的代码。

本项目已进入facebook孵化器。

[https://github.com/facebookincubator/superconsole](https://github.com/facebookincubator/superconsole)