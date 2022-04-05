# 开源观察 ｜ Web开发

## Rust Web 框架

Rust社区有很多 Web 框架库，参考《[Web Frameworks](https://www.arewewebyet.org/topics/frameworks/)》以及《[Which Rust web framework to choose in 2022 (with code examples)](https://kerkour.com/rust-web-framework-2022)》，补充整理以后总表如下：

| 库        | 总下载量  | 介绍 / 部分特性                                              |
| --------- | --------- | ------------------------------------------------------------ |
| actix-web | 5,134,720 | HTTP / 1.x，HTTP / 2 <br/>兼容 Tokio <br/>支持压缩和解压 <br/>Openssl 或 Rustls 支持 <br/>中间件 (Logger, Session, CORS, etc) <br/>兼容 Rust 1.54+ |
| warp      | 4,222,391 | 实现路由和参数提取功能 <br>实现头请求以及提取功能 <br>实现请求字段反序列化功能 <br>支持 Gzip, Deflate, and Brotli 压缩 <br>基于 hyper 编写，支持 HTTP / 1.x，HTTP / 2，Async |
| rocket    | 1,471,930 | 需要 nightly Rust                                            |
| iron      | 1,018,360 | 基于 hyper 编写，支持 HTTP / 1.x，HTTP / 2，Async <br>支持并行运行 <br>支持负载平衡 |
| tide      | 491,651   | 支持异步                                                     |
| axum      | 276,608   | 实现路由功能 <br>基于 hyper  实现 <br>基于 tower 和 tower-http 生态实现，可利用其中中间件、服务以及实用程序 |
| nickel    | 205,611   | 简易轻量Rust web 框架支持                                    |
| gotham    | 91,435    | 支持异步                                                     |
| poem      | 54,160    | 兼容 tower::Service 以及 tower::Layer <br>支持压缩与解压     |
| thruster  | 25,521    | 支持异步 <br>兼容 hyper <br>支持 ssl <br>支持 gRPC           |

WebAssembly 前端库表如下：

| 库     | 总下载量 | 介绍 / 部分特性                                              |
| ------ | -------- | ------------------------------------------------------------ |
| yew    | 239,796  | 使用 WebAssembly 来创建多线程的前端 web 应用，支持与 JavaScript 交互 |
| seed   | 138,857  | 前端 Rust 框架，可创建类似于 elm 架构的 web 程序，不需要 nightly 版本 |
| iced   | 106,406  | 跨平台 GUI 库                                                |
| sauron | 24,648   | 可用于服务端或客户端开发，适用于开发使用渐进式渲染的 Web 应用程序 |
| mogwai | 3,213    | GUI 库                                                       |
| percy  | 397      | /                                                            |

对于 actix-web，warp，axum 这三个库，《Which Rust web framework to choose in 2022 (with code examples)》文中比较总结如下：

| 库        | Github Stars | Json 序列化以及反序列化 | 路由 (Routing) | 中间件 (Middleware) | 数据共享 (State) |
| --------- | ------------ | ----------------------- | -------------- | ------------------- | ---------------- |
| actix-web | ~13.3k       | 支持                    | 相对支持最差   | 支持                | 支持             |
| warp      | ~6k          | 支持                    | 支持           | 相对支持最好        | 支持             |
| axum      | ~3.6k        | 支持                    | 相对支持最好   | 支持                | 支持             |



## 参考

Which Rust web framework to choose in 2022 (with code examples)，https://kerkour.com/rust-web-framework-2022

Web Frameworks，https://www.arewewebyet.org/topics/frameworks/

## Actix Web 发布 v4.0

Actix Web 是一个强大的高性能 Web 框架，用于创建从小型到单体的 Web 服务。

v4.0 版本是由社区推动的，57 位贡献者提交了超过 600 次！主要变化包括：

- 完全兼容 Tokio v1 生态系统，包括#[tokio::main] 支持。
- 使 actix-http 更精简。这个 crate 支持 Actix Web，包含我们的 HTTP/1 实现和较低级别的 HTTP 处理。
- API 改进，通常是为了提高表现力和开发人员的生产力。
- 减少特定 API 中的 paper-cuts 和 non-obvious 行为的数量。
- 大大改进了大量关键项目的文档。

[迁移指南](https://github.com/actix/actix-web/blob/master/actix-web/MIGRATION-4.0.md)包含了如何更新的解释和差异。

## 热议 - 2022 年你会选择哪种 Web 框架

Sylvain Kerkour ，Bloom 的创建者也是 Black Hat Rust 一书的作者，发文对比了 actix-web、warp 和 axum 三个 Web 框架，结合代码示例进行了一个简单的评估。尽管作者心属 axum，但还是选择 actix-web 作为 Bloom 的首选。

Reddit 上的网友则提供了更多讨论，包括 tide ，rocket 以及 poem 。

[https://www.reddit.com/r/rust/comments/szl0im/which_rust_web_framework_to_choose_in_2022_with/](https://www.reddit.com/r/rust/comments/szl0im/which_rust_web_framework_to_choose_in_2022_with/)

## axum-auth: HTTP auth extractor

提供 Axum 下的 HTTP auth extractor. 支持 Bearer 和 Basic Auth.

[https://github.com/Owez/axum-auth](https://github.com/Owez/axum-auth)

## 如何使用axum的 "Type safe routing"？

一篇讲解axum的博文：

[https://mixi-developers.mixi.co.jp/how-to-use-type-safe-routing-of-axum-c06c1b1b1ab](https://mixi-developers.mixi.co.jp/how-to-use-type-safe-routing-of-axum-c06c1b1b1ab)
