# 商业观察 ｜ 生产实践

## Amazon Prime Video 使用 Wasm 和 egui

Amazon Prime Video 通过 8k 多种设备类型向数百万客户提供内容，例如游戏机、电视、机顶盒和流媒体棒。当想要进行应用更新时，需要在可更新性和性能之间做出艰难的权衡。

过去一年，Amazon Prime Video 使用了 WASM 和 egui，他们认为对 Rust 和 WebAssembly 的投资得到了回报，经过一年的开发，共编写了 37,000 行 Rust 代码，显著地提高了性能、稳定性和 CPU 消耗并降低了内存利用率。

[https://www.amazon.science/blog/how-prime-video-updates-its-app-for-more-than-8-000-device-types](https://www.amazon.science/blog/how-prime-video-updates-its-app-for-more-than-8-000-device-types)


## Rust与C++交互操作

Rust与C++交互是工业界非常重要的课题。作者整理对比了 bindgen，cpp!, cxx, autocxx 这个库四种方法的优劣。最后做了一个非常有价值的表格。

[https://blog.tetrane.com/2022/Rust-Cxx-interop.html](https://blog.tetrane.com/2022/Rust-Cxx-interop.html)

## transactional-storage-framework： SAP 也在尝试使用 Rust

在 SAP 的 GitHub 组织下有一个 事务存储框架（transactional-storage-framework） 项目，其开发者个人名下也有另一个 Rust 项目 scalable-concurrent-containers 。

事务存储框架是一个软件框架，为一个完整的事务存储系统提供关键操作接口和基本功能。Container类似于数据库管理软件中的数据库表。它的数据是根据嵌入在容器中的元数据组织的。容器是分层管理的，可以用字符串唯一标识。

- [https://github.com/SAP/transactional-storage-framework](https://github.com/SAP/transactional-storage-framework)
- [https://github.com/wvwwvwwv/scalable-concurrent-containers](https://github.com/wvwwvwwv/scalable-concurrent-containers)