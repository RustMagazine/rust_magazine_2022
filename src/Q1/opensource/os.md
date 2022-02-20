# 操作系统

---

##  用 Rust 实现的低级 NTFS 文件系统库

NTFS是所有 Windows 版本中的主要文件系统（从 1993 年的 Windows NT 3.1 开始）。此板条箱适用于 Windows 2000 到当前 Windows 11 中使用的 NTFS 3.x 版本。

crate 是no_std兼容的，因此可用于从固件级代码到用户模式应用程序。

- [https://github.com/ColinFinck/ntfs](https://github.com/ColinFinck/ntfs)
- [https://fosdem.org/2022/schedule/event/misc_ntfs_rust/](https://fosdem.org/2022/schedule/event/misc_ntfs_rust/)

## Rust for Linux 第四次补丁提审

补丁的具体细节可以在Linux 邮件列表中找到：

- RFC: https://lore.kernel.org/lkml/20210414184604.23473-1-ojeda@kernel.org/
- v1:  https://lore.kernel.org/lkml/20210704202756.29107-1-ojeda@kernel.org/
- v2:  https://lore.kernel.org/lkml/20211206140313.5653-1-ojeda@kernel.org/
- v3:  https://lore.kernel.org/lkml/20220117053349.6804-1-ojeda@kernel.org/

第二次补丁改进摘要可参考：[Rust for Linux 源码导读 | Ref 引用计数容器](https://rustmagazine.github.io/rust_magazine_2021/chapter_12/ref.html)。

**第三次补丁改进摘要：**

- 对 Rust 的支持有一些改进：
    - 升级到 Rust 1.58.0
    - 增加了自动检测来判断是否有合适的 Rust 可用工具链（`CONFIG_RUST_IS_AVAILABLE`，用于替换`HAS_RUST`）
    - 移除`!COMPILE_TEST` 
    - 其他构建系统的改进
    - 文档改进
    - 需要的不稳定功能之一，`-Zsymbol-mangling-version=v0`，在 1.59.0 中变得稳定。另一个，“maybe_uninit_extra”，将在 1.60.0 中。
- 对抽象和示例驱动程序的一些改进：
    - 加了将在总线中使用的“IdArray”和“IdTable”，以允许驱动程序指定在编译时保证为零终止（zero-terminated）的设备 ID 表。
    - 更新了 `amba` 以使用新的常量设备 ID 表支持。
    - 初始通用时钟框架抽象。
    - 平台驱动程序现在通过实现特质（trait）来定义。包括用于简化平台驱动程序注册的新宏和新示例/模板。
    - `dev_*` 打印宏。
    - `IoMem<T>` 的`{read,write}*_relaxed` 方法。
    - 通过删除 `FileOpener` 来简化文件操作。
    - 在驱动程序注册的参数中添加了“ThisModule”。
    - 添加用 Rust 编写的树外 Linux 内核模块的基本模板： [https ://github.com/Rust-for-Linux/rust-out-of-tree-module](https ://github.com/Rust-for-Linux/rust-out-of-tree-module)

**第四次补丁改进摘要：**

- 基础设施更新：
    - 整合 CI : 英特尔 0DAY/LKP 内核测试机器人 / kernelCI/ GitHub CI
    - 内核模块不需要写 crate 属性，`#![no_std]` 和 `#![feature(...)]` 不再存在，删除样板。
    - 添加了单目标支持，包括`.o`、`.s`、`.ll`和`.i`（即宏扩展，类似于 C 预处理源）。
    - 对`helpers.c`文件的解释和对helpers的许可和导出的许可。
    - 文档Logo现在基于矢量 (SVG)。此外，已经为上游提出了 Tux 的矢量版本，并且用于改进自定义Logo支持的 RFC 已提交至上游 Rust。
    - 添加了关于注释 (`//`) 和代码文档的编码指南(`///`)。
    - `is_rust_module.sh` 返工。
    - 现在跳过了叶子模块的`.rmeta`的生成。
    - 其他清理、修复和改进。
- 抽象和驱动更新:
    - 增加了对静态（全局共享变量）同步原语的支持。`CONFIG_CONSTRUCTORS`被用于实现。
    - 通过使用标记类型简化了锁防护，即`Guard`和`GuardMut`统一为一个参数化的类型。如果标记是 `WriteLock`，那么 `Guard`就会实现`DerefMut`（以前只由`GuardMut`实现）。

    - 可选参数添加到杂项设备（misc device）的注册中。遵循构建者模式，例如:
        ```rust
        miscdev::Options::new()
            .mode(0o600)
            .minor(10)
            .parent(parent)
            .register(reg, c_str!("sample"), ())
        ```
    - 增加了 "RwSemaphore "的抽象，该抽象包裹了C端`struct rw_semaphore`。
    - 新的`mm`模块和VMA抽象（包装C端`struct vm_area_struct`）用于`mmap`。
    - GPIO PL061现在使用最近增加的`dev_*!` Rust宏。
    - 支持`！CONFIG_PRINTK`情况。
    - 其他清理、修复和改进。


## kerla：用 Rust 编写的具有 Linux ABI 兼容性的新操作系统内核

Kerla 是一个用 Rust 从头开始​​编写的操作系统内核，旨在与 Linux ABI 兼容，也就是说，它无需任何修改即可运行 Linux 二进制文件。

- 实现 *NIX 进程概念：上下文切换、信号`fork(2)`、`execve(2)`、`wait4(2)`等。
- 支持常用的系统调用，如`write(2)`, `stat(2)`, `mmap(2)`, `pipe(2)`, `poll(2)`, ...
- 目前不支持磁盘：`initramfs` 被挂载为根文件系统。
- 伪文件系统：`tmpfs` 和 `devfs`。
- 基于`smoltcp`的 TCP/IP 支持。
- 实现 `tty` 和伪终端 (pty)。
- 支持 QEMU 和 Firecracker（带有 virtio-net 设备驱动程序）。
- 支持 x86_64。
- 基于 Docker 的 initramfs 构建系统。

- [https://kerla.dev/](https://kerla.dev/)
- [https://github.com/nuta/kerla](https://github.com/nuta/kerla)
- [Writing a Linux-compatible kernel in Rust](https://seiya.me/writing-linux-clone-in-rust)