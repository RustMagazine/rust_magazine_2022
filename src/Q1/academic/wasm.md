# WebAssembly

## 卡内基梅隆大学论文：使用 WebAssembly 的可证明安全的多语言沙箱

该论文探索了两种生成可证明wasm代码的方法：
- 传统形式化方法生成数学的、经过机器检查的安全性证明。对应实现为 vWasm 编译器。
- 将 wasm 语义小心地嵌入到安全的 Rust 代码中，以便 Rust 编译器能够以良好的性能emit安全的可执行代码。对应实现为 rWasm 编译器。

本文贡献：

- vWasm 是第一个经过验证的沙盒编译器，通过传统的机器检查证明实现。
- rWasm 是第一个可证明安全的沙箱编译器，具有极具竞争力的运行时性能。通过利用 Rust 的安全保证，rWasm 实现了第一个多语言多平台的沙箱编译器。它是提供了可证明的保证，而无需编写正式的证明。

rWasm 机制：

- 编译策略：
    1. 通过一个编译前端将wasm编译为 IR
    2. 包括一个栈和死代码分析器
    3. 通过一个后端将 IR 打印为 Rust 代码
    4. 通过 rustc 编译器生成机器代码
- rWasm是通过 Safe Rust 实现的
- rWasm的关键是： 通过将低级代码(Wasm)提升为高级代码（Rust）来完成对低级代码的仿真，从而得到高级语言的保证。因为想要证明的安全沙盒的高级属性本质就是内存安全。

[https://www.jaybosamiya.com/publications/2022/usenix/provably-safe-sandboxing-wasm.pdf](https://www.jaybosamiya.com/publications/2022/usenix/provably-safe-sandboxing-wasm.pdf)