# 开源观察 | 嵌入式Rust

## 乐鑫芯片Rust 2022 第一季度进展报告

摘要：

- 使用内置的 esp-idf 构建系统来编译 esp-idf（称为“本机”构建）。
- 默认 esp-idf 版本提升为 v4.4。4.4 版有一些质量改进，以及一些功能。
- 编译器和Xtensa代码生成。Rust 一直是 Xtensa LLVM 后端的良好压力测试。团队在上个季度发现了一些对压缩很重要的错误，因为最终 esp-idf 将使用 clang 而不是 GCC 编译（希望到那时，clang将作为rustup 组件提供）。还修复了标准库端口的一些问题。
- 再见 `esp-rs/esp32-hal`，你好 [`esp-rs/esp-hal`](https://github.com/esp-rs/esp-hal)。esp32 的原始 HAL 现在处于维护模式，一旦所有驱动程序都移植到新的多芯片 HAL，很快就会被弃用。
- 初步 WiFi 支持。
- espflash 1.4.0 版本即将发布，其中包含一些很棒的功能和修复。
- 发布了[xtensa-toolchain](https://github.com/esp-rs/xtensa-toolchain)，这是一个 Github Action，用于安装支持 Xtensa 的分叉 Rust 编译器。可以在[此处](https://github.com/marketplace/actions/rust-toolchain-for-xtensa)找到有关如何使用的说明。
- 为了针对基于 Xtensa 的芯片（esp32、esp32s2、esp32s3 等），我们需要一个自定义的 rust 编译器，以及一个用于 Xtensa 的自定义 LLVM 后端。这给开始使用基于 Xtensa 的芯片增加了一些阻力。我们已经提供了用于下载预构建编译器工具链的脚本，但我们很高兴地宣布[`esp-rs-devcontainer`](https://github.com/SergioGasquez/esp-rs-devcontainer)。它使用远程容器功能集成到 Visual Studio Code 中，允许已经熟悉容器的开发人员轻松设置。有关支持的配置和初始化步骤，请参阅[设置部分](https://github.com/SergioGasquez/esp-rs-devcontainer#setup)。
- 开发了一个开源开发板，专注于为 Rust 开发做好准备，基于 RISCV esp32c3。如果想自己做一个开发板，可以看[https://github.com/esp-rs/esp-rust-board](https://github.com/esp-rs/esp-rust-board)

未来还有很多事情要做！


[https://mabez.dev/blog/posts/esp-rust-04-04-2022/](https://mabez.dev/blog/posts/esp-rust-04-04-2022/)