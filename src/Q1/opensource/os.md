# 开源观察 ｜ 操作系统

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

## snarkOS: 去中心化操作系统

snarkOS是一个用于私有应用程序的去中心化操作系统。它构成了[Aleo](https://aleo.org/)的主干，使应用程序能够以可公开验证的方式验证和存储状态。

[https://github.com/AleoHQ/snarkOS](https://github.com/AleoHQ/snarkOS)


## Aero ： Rust 编写的新的现代、实验性、类似 unix 的操作系统

这是 Linux 发行版吗？ 不，Aero 运行自己的内核，该内核并非源自 Linux，并且不与 Linux 内核共享任何源代码或二进制文件。

Aero 遵循单片内核设计，并受到 Linux 内核的启发。Aero 支持现代PC 功能，例如长模式、5 级分页和 SMP（多核）等。

目标:

- 打造现代、安全、美观、快速的操作系统。
- 针对现代 64 位架构和 CPU 功能。
- 与 Linux 良好的源代码级兼容性，因此我们可以轻松地移植程​​序。
- 制作一个可以在真实硬件上运行的可用操作系统，而不仅仅是在模拟器或虚拟机上。

Aero 使用自定义构建系统，它包装了Cargo 负责构建内核和用户空间。它还构建 initramfs 和磁盘映像。

[https://github.com/Andy-Python-Programmer/aero](https://github.com/Andy-Python-Programmer/aero)

## Rust 与 NuttX 操作系统

[Apache NuttX OS](https://lupyuen.github.io/articles/nuttx) 是一个实时嵌入式操作系统 RTOS，可在许多平台（如：8 位到 64 位平台）上移植，并且像 Linux 的小型版本一样工作（因为它符合 POSIX 标准）。现在，我们可以在 NuttX 上使用 Rust 创建更安全的嵌入式应用程序，也可以定制自己的驱动程序。

在所有嵌入式操作系统中，Apache NuttX 是真正独一无二的，因为……

- NuttX 在8 位、16 位、32 位和 64 位微控制器上运行……跨越RISC-V、Arm、ESP32、AVR、x86等流行平台
- NuttX严格遵守 POSIX。这意味着 NuttX 应用程序应通过调用`open()`、`read()`、`write()`、`ioctl()`来访问微控制器硬件……（看起来像 Linux Lite！）
- 对于BL602 和 BL604：NuttX 和 FreeRTOS 是 Bouffalo Lab 的 RISC-V + WiFi + 蓝牙 LE SoC 上唯一支持的操作系统。

NuttX 以其创建者Gregory Nutt的名字命名。X，因为它符合 POSIX 标准。

[https://lupyuen.github.io/articles/rust2](https://lupyuen.github.io/articles/rust2)

## 使用 crosvm 在 Rust 中实现应用程序和操作系统虚拟化

虽然 QEMU 仍然是开源 VMM（虚拟机管理器）的首选，但 Rust 替代品可能对安全性很有帮助。

现在可以通过修改 crosvm 来虚拟化 Linux 中的 GUI 应用程序和操作系统，这是一个基于 rust 的出色开源 VMM，由 Google 开源。

- [https://blog.openw3b.org/crosvm-for-os-and-app-virtualization-on-linux/](https://blog.openw3b.org/crosvm-for-os-and-app-virtualization-on-linux/)
- [https://google.github.io/crosvm/](https://google.github.io/crosvm/)

## KDE 正在为 KConfig 开发一个 Rust wrapper

Ayush Singh 将为 KConfig 开发一个 Rust wrapper 。有了这个 wrapper ，以及针对 qmetaobject 和 ki18n 的现有 wrapper ，在 Rust 中开发 KDE 应用程序将变得更加容易。可以在 kde-devel 邮件列表中找到更多信息。

- [https://crates.io/crates/qmetaobject](https://crates.io/crates/qmetaobject)
- [https://crates.io/crates/ki18n](https://crates.io/crates/ki18n)

## snarkOS: 去中心化操作系统

snarkOS是一个用于私有应用程序的去中心化操作系统。它构成了[Aleo](https://aleo.org/)的主干，使应用程序能够以可公开验证的方式验证和存储状态。

[https://github.com/AleoHQ/snarkOS](https://github.com/AleoHQ/snarkOS)

## Linux RamFS文件系统移植到Rust

RamFS是Linux中基于Ram的文件系统。它被描述为一个简单的文件系统，用于学习创建新的Linux文件系统（link）所需的最小实现。

在2021年秋季与弗吉尼亚理工大学的张宇民博士一起学习高级 Linux 内核编程。Connor Shugg 和我(Chase Minor)将它从c移植到Rust，以了解移植到内核内部的过程。我们在这里提供我们的资源和知识，以供使用，包括或学习。

我们工作的主要贡献是RAMFS文件系统的移植。但是，我们也向内核添加了各种其他东西，这可能对其他使用Rust的Linux开发人员有益。

- [https://github.com/acminor/linux/tree/ramfs-rust/fs/ramfs_rust](https://github.com/acminor/linux/tree/ramfs-rust/fs/ramfs_rust)
- [https://austincminor.com/20211030000942-ramfs_rust.html](https://austincminor.com/20211030000942-ramfs_rust.html)

## PopOS正在与Relm4配合，以帮助用Rust在PopOS上开发GTK程序
PopOS是要开发Rust Linux发行版的System76的Linux发行版，现在正致力于和Relm4合作，帮助用Rust在PopOS上更容易地开发GTK程序。

Relm4是基于GTK上层的一套框架，提供类Elm的编程模式。

[https://www.reddit.com/r/rust/comments/sxwi46/popos_collaboration_with_relm4_writing_gtk/](https://www.reddit.com/r/rust/comments/sxwi46/popos_collaboration_with_relm4_writing_gtk/)

## 【视频】Miguel & Wedson：Rust for Linux 当前状态

Rust for Linux 是一个旨在将 Rust 支持作为头类语言引入 Linux 内核的项目。这意味着为用 Rust 编写内核模块（例如驱动程序或文件系统）提供支持，并且使用尽可能少的不安全代码。

本次会议将简要解释 Rust 支持如何在内核中工作，并将提供项目状态的更新，以及近期的计划。它还显示可以帮助该项目的 Rust 功能的愿望清单。

Youtube链接：[https://www.youtube.com/watch?v=fVEeqo40IyQ](https://www.youtube.com/watch?v=fVEeqo40IyQ)

## RustSBI软件发布v0.2.2版本

洛佳发布了RustSBI 0.2.2版本。RustSBI现已支持最新的RISC-V SBI 1.0.0正式批准版的规范标准。另外，本次更新预留了主从机不同的指针宽度，准备为虚拟化软件提供SBI接口实现支持。

相比0.2.0版本，新版修复了一个较为严重的逻辑漏洞，推荐0.2.0版本用户立即更新。

RISC-V SBI是RISC-V桌面与服务器平台不可或缺的基础软件，而RustSBI是RISC-V SBI标准采纳的软件实现之一。RustSBI完全由嵌入式Rust编写，自从2020年RustSBI发布第一个版本以来，软件久经考验，是成熟的RISC-V内核支持接口实现。

[https://github.com/rustsbi/rustsbi/releases/tag/v0.2.2](https://github.com/rustsbi/rustsbi/releases/tag/v0.2.2)

## stardust-oxide - 基于 Rust 的 Unikernel

基于 Rust 的 Unikernel。

- 支持日志的控制台驱动程序
- 页框映射和表生成
- 授予表创建/销毁
- XenStore 界面
- XenBus 接口
- 简单的异步执行器

[https://github.com/StardustOS/stardust-oxide](https://github.com/StardustOS/stardust-oxide)