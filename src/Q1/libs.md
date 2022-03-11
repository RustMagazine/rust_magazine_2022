# Rust 开发基础库

---

## deser: 一个新的实验性 Rust 序列化库

它想探索 JSON 或 msgpack 等结构化格式的序列化和反序列化的可能性。但不会支持 bincode 这种非自描述格式(non self describing formats)。

> 自描述格式包含描述消息格式和含义的数据和元数据

**设计目标：**

- 快速编译时间： deser 通过鼓励动态分发来避免过度的单态化。
- 无限递归：现实世界很糟糕，传入的数据可能嵌套严重。无论数据有多深，都不能耗尽调用堆栈。
- 简单数据模型： deser 简化了序列化和反序列化接口上的数据模型，但通过提供描述符来弥补，这些描述符为序列化程序想要处理它时提供辅助信息。
- 可扩展数据模型： deser 希望能够使用非序列化接口原生的类型来扩展数据模型。例如，如果数据格式想要支持任意大小的整数，这应该是可能的，而无需退回到带内信令。
- 元信息： deser 通过提供保存元信息的空间来补偿简化的数据模型。
- 本机字节序列化： deser 具有内置的专门化功能，用于将字节和字节向量序列化为与切片和向量不同的格式。


[deser](https://github.com/mitsuhiko/deser)

## 【实验性】diplomat: 用于生成 FFI 定义的工具

使用 Diplomat，您可以简单地定义要通过 FFI 公开的 Rust API，并自动获得高级 C、C++ 和 JavaScript 绑定！

- [https://github.com/rust-diplomat/diplomat](https://github.com/rust-diplomat/diplomat)
- [https://rust-diplomat.github.io/book/](https://rust-diplomat.github.io/book/)

## Clap 3.1 发布

Clap v3 开发了好几年，并且经过几次维护者变更，所以变更日志已经变得不再连续完整。

v3 最大的改变是集成了 [StructOpt](https://docs.rs/structopt/)。

[https://github.com/clap-rs/clap/blob/master/CHANGELOG.md](https://github.com/clap-rs/clap/blob/master/CHANGELOG.md)


## deku : 声明式二进制读写

为结构体和枚举提供位级（bit-level）、置换（symmetric）、序列化/反序列化实现。支持 `no_std` 和  `wasm`。

[https://github.com/sharksforarms/deku](https://github.com/sharksforarms/deku)

## nextest : Rust 的下一代测试执行器

特点：
- 干净美观的用户界面
- 比 `cargo test`性能快 `60%`
- 自动重试失败的测试，如果稍后通过测试，就能将其标记为 不可靠测试（`flaky`），对于发现系统中的问题比较有用
- 分区测试跨多个 CI Jobs运行。如果您的测试在 CI 中运行的时间过长，nextest 可以自动为您将它们拆分到多个Jobs中。
- 跨平台。nextest 可在 Unix、Mac 和 Windows 上运行，因此无论您使用什么平台，您都可以获得更快的测试运行的好处。
- 其他（还在完善中）

> 不可靠测试（Flaky Tests），就是指在被测对象和测试条件都不变的情况下，有时候失败、有时候成功的测试。产生这类测试的原因包括：异步等待、并发、资源泄漏、远程服务、依赖其他测试、平台依赖性、系统时间、随机数等。
> 相关论文：[https://www.cs.cornell.edu/courses/cs5154/2021sp/resources/LuoETAL14FlakyTestsAnalysis.pdf](https://www.cs.cornell.edu/courses/cs5154/2021sp/resources/LuoETAL14FlakyTestsAnalysis.pdf)

- [https://github.com/nextest-rs/nextest](https://github.com/nextest-rs/nextest)
- [nextest book](https://nexte.st/index.html)