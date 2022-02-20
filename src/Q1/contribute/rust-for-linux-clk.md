# Rust for Linux 源码导读 | Rust 驱动开发与通用时钟管理框架抽象

作者： 张汉东

> 推荐阅读： [Rust for Linux 源码导读 | Ref 引用计数容器](https://rustmagazine.github.io/rust_magazine_2021/chapter_12/ref.html)


## 背景概念

Rust for Linux 这个项目的目的就是为了将 Rust 引入 Linux，让 Rust 成为 C 语言之后的第二语言。但它最初的目的是：实验性地支持Rust来写内核驱动。

以往，Linux 内核驱动的编写相对于应用其实是比较复杂的，具体复杂性主要表现在以下两个方面：

- 编写设备驱动必须了解Linux 内核基础概念、工作机制、硬件原理等知识
- 设备驱动中涉及内存和多线程并发时容易出现 Bug，linux驱动跟linux内核工作在同一层次，一旦发生问题，很容易造成内核的整体崩溃

引入 Rust 从理想层面来看，一方面在代码抽象和跨平台方面比 C 更有效，另一方面代码质量会更高，有效减少内存和多线程并发类 Bug 。但具体实践如何，是否真如理想中有效，这就需要后续的实验。

Rust for Linux 就是为了帮助实现这一目标，为 Linux 提供了 Rust 相关的基础设施和方便编写 Linux 驱动的安全抽象。

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

## Rust 与 Linux 设备驱动开发

### 基础概念

和应用程序不同，驱动程序是可以直接和硬件设备进行通讯的。驱动程序作为 Linux 内核的一种模块被动态加载到内核中。编译好的模块一般以`.ko`（kernel object）为文件扩展后缀 (这让我想起自己在 Linux 下安装 Nvidia 驱动报错找不到 `nvidia.ko` 文件时有多么崩溃)。

>  但要注意的是，驱动不一定必须是模块，有些驱动是直接编译进内核的；同时模块也不全是驱动。

**创建内核驱动基本模式如下：**

1. 通过实现初始化入口点和退出点来对应内核加载和卸载驱动的行为

基于 Rust for Linux 提供的 `kernel` 库来实现驱动的话，代码如下：

```rust
// From: https://github.com/Rust-for-Linux/linux/blob/rust/samples/rust/rust_miscdev.rs

// RustMiscdev 是某个设备
// 通过实现 KernelModule trait 来进行加载时初始化
impl KernelModule for RustMiscdev {
    fn init(name: &'static CStr, _module: &'static ThisModule) -> Result<Self> {
        pr_info!("Rust miscellaneous device sample (init)\n");

        let state = SharedState::try_new()?;

        Ok(RustMiscdev {
            _dev: miscdev::Registration::new_pinned(fmt!("{name}"), state)?,
        })
    }
}

// 通过实现 Drop trait 来进行卸载时的清理
impl Drop for RustMiscdev {
    fn drop(&mut self) {
        pr_info!("Rust miscellaneous device sample (exit)\n");
    }
}
```

2. 编写驱动逻辑

比如实现文件操作之类：

```rust
// From: https://github.com/Rust-for-Linux/linux/blob/rust/samples/rust/rust_miscdev.rs

struct Token;

// 通过实现 kernel crate 中抽象的 FileOperations 接口
impl FileOperations for Token {
    type Wrapper = Ref<SharedState>;
    type OpenData = Ref<SharedState>;

    kernel::declare_file_operations!(read, write);

    fn open(shared: &Ref<SharedState>, _file: &File) -> Result<Self::Wrapper> {
        Ok(shared.clone())
    }

    fn read(
        shared: RefBorrow<'_, SharedState>,
        _: &File,
        data: &mut impl IoBufferWriter,
        offset: u64,
    ) -> Result<usize> {
        // do_something
    }

    // do more things
}
```

3. 编译、加载和测试等。

编写 Linux 驱动有很多细节内容，这里只是介绍一个简单的模式，来帮助理解 Rust for Linux 中的 `kernel` 抽象的作用。

### Rust 驱动开发示例

去年有人提交过一份 C vs Rust 驱动代码示例：[A GPIO driver in Rust](https://lwn.net/Articles/863459/)。可以看得出来，Rust 对硬件抽象的表达能力显著要高于 C 语言。

下面是这段驱动代码实现的摘要：

```rust
use core::ops::DerefMut;
use kernel::{
    amba, bit, declare_id_table, device, gpio,
    io_mem::IoMem,
    irq::{self, IrqData, LockedIrqData},
    power,
    prelude::*,
    sync::{IrqDisableSpinLock, Ref},
};

impl gpio::Chip for PL061Device { 
    // do_something
}

impl irq::Chip for PL061Device {
    // do_something
}

impl amba::Driver for PL061Device {
    // do_something
}

impl power::Operations for PL061Device {
    // do_something
}

module_amba_driver! {
    type: PL061Device,
    name: b"pl061_gpio",
    author: b"Wedson Almeida Filho",
    license: b"GPL v2",
}

```

这段驱动代码中使用了 Rust for Linux 中提供的 `kernel` 库，用其中包括的抽象 `gpio`、`irq`、`amba`、`power`准确表达了这段驱动代码中的结构和实现意图，可以更好地和驱动开发者对驱动开发建立的心智模型对应起来，这就是 Rust 语言的抽象表达能力。

- `gpio::Chip` trait 是对 gpio chip 的一种跨平台抽象接口，它也是一种依赖倒置。让底层不同的gpio chip都依赖同一个接口。
- `irq::Chip` trait 是对内核中 `irq_data`结构体的抽象接口。中断描述符中会包括底层irq chip相关的数据结构，内核中把这些数据组织在一起，形成`struct irq_data`。
- `amba::Driver` trait 是 amba（高级微控制器总线结构，）设备驱动抽象接口，由于ARM众多的合作伙伴都会或多或少的使用ARM提供的片内外设，所以众多厂商的ARM处理器的一些外设可以使用相同的驱动。这个 GPIO 驱动也是针对 ARM 平台写的，所以用到这个。
- `power::Operations` trait 是对 Linux 电源管理中各种 Callback 数据结构`dev_pm_ops`的抽象接口。`power` 模块中还定义了方便设置callback的宏等。

看得出来，基于 Rust for Linux 的 `kernel` 抽象，可以方便地使用 Rust 开发架构良好、可读性强、易维护且更加健壮的内核驱动代码。


## Linux 通用时钟框架介绍


### 背景介绍

当下通用计算机中的CPU中各个模块都需要时钟驱动，内核就需要一套通用的机制来进行时钟管理。这套通用机制还必须跨平台地方便管理CPU上所有的时钟资源。

Linux 平台中提供一套通用时钟框架（common clock framework）来管理系统clock资源的子系统，其职能可以分为下面三个部分：

- 向其它driver提供操作clocks的通用API。
- 实现clock控制的通用逻辑，这部分和硬件无关。
- 将和硬件相关的clock控制逻辑封装成操作函数集，交由底层的platform开发者实现，由通用逻辑调用。

现在主流的 Linux 处理器平台都包含了非常复杂的 clock tree，对应很多clock相关的器件。而这个通用时钟框架管理的对象就是这些clock器件。框架的主要功能包括：

- 使能（enable/disable）clk
- 设置clk频率
- 选择clk的parent

通用时钟框架的通用接口定义在 Linux 内核中（[include/linux/clk.h](https://github.com/Rust-for-Linux/linux/blob/rust/include/linux/clk.h)）。

每个时钟源对象使用一个`struct clk`结构来表示。而`struct clk`结构的具体内容由各平台自己定义。`clk.h`头文件定义了操作一个clk对象的所有接口。内核的其他地方可以也只能使用`clk.h`中提供的这些接口函数来操作`clk`。Rust for Linux 的 `kernel` crate 就是对 `clk.h` 的封装。这也是 Rust for Linux 第四次提审的补丁改进的内容之一。

关于通用时钟框架还有很多细节，这里为了帮助理解代码，只做简要介绍，对细节感兴趣的朋友可以自行检索学习资料（例如：[http://www.wowotech.net/pm_subsystem/clk_overview.html](http://www.wowotech.net/pm_subsystem/clk_overview.html)）。


### Rust for Linux 中的简单抽象

Rust 对 `clk.h` 对绑定相对比较简单，只绑定了部分控制clock的接口，比如 `prepare_enable/disable_unprepare` 和 `get_rate`，还有很多接口没有绑定。另外只能用于 atomic 上下文。

但我们能从中学习到 Unsafe Rust的一些最佳实践（注意代码中的注释）：

```rust
// From: https://github.com/Rust-for-Linux/linux/blob/rust/rust/kernel/clk.rs

use crate::{bindings, error::Result, to_result};
use core::mem::ManuallyDrop;

/// 表示 `struct clk *`
///
/// # Invariants （不变性说明）
///
/// 这个指针来自 C语言 端，这里默认 C 端来的是有效指针，信任 C 端
/// 这种信任对性能有益：零成本（没有检查开销）
pub struct Clk(*mut bindings::clk);

impl Clk {
    /// Creates new clock structure from a raw pointer.
    ///
    /// # Safety
    ///
    /// The pointer must be valid.
    /// 这里使用unsafe 函数，是因为无法保证传入的指针是否有效，只能靠其调用者来保证
    /// 所以无法将其抽象为安全防范
    pub unsafe fn new(clk: *mut bindings::clk) -> Self {
        Self(clk)
    }

    /// Returns value of the rate field of `struct clk`.
    pub fn get_rate(&self) -> usize {
        // SAFETY: The pointer is valid by the type invariant.
        // 安全性说明：这个指针已经有上嘛结构体定义时的不变性来保证安全了，所以这是个安全方法
        unsafe { bindings::clk_get_rate(self.0) as usize }
    }

    /// Prepares and enables the underlying hardware clock.
    ///
    /// This function should not be called in atomic context.
    pub fn prepare_enable(self) -> Result<EnabledClk> {
        // SAFETY: The pointer is valid by the type invariant.
        // 同上
        to_result(|| unsafe { bindings::clk_prepare_enable(self.0) })?;
        Ok(EnabledClk(self))
    }

    impl Drop for Clk {
    fn drop(&mut self) {
        // SAFETY: The pointer is valid by the type invariant.
        unsafe { bindings::clk_put(self.0) };
    }
}


// 对clock 使能
/// A clock variant that is prepared and enabled.
pub struct EnabledClk(Clk);

impl EnabledClk {
    // 获取频率
    /// Returns value of the rate field of `struct clk`.
    pub fn get_rate(&self) -> usize {
        self.0.get_rate()
    }

    /// Disables and later unprepares the underlying hardware clock prematurely.
    ///
    /// This function should not be called in atomic context.
    pub fn disable_unprepare(self) -> Clk {
        let mut clk = ManuallyDrop::new(self);
        // SAFETY: The pointer is valid by the type invariant.
        unsafe { bindings::clk_disable_unprepare(clk.0 .0) };
        core::mem::replace(&mut clk.0, Clk(core::ptr::null_mut()))
    }
}

impl Drop for EnabledClk {
    fn drop(&mut self) {
        // SAFETY: The pointer is valid by the type invariant.
        // 安全性同上
        unsafe { bindings::clk_disable_unprepare(self.0 .0) };
    }
}
```

上面 `clk` Rust 安全绑定代码虽然简单，但它反映出了 Rust for Linux 中 Unsafe Rust 安全抽象的一种规范：**利用文档注释中不变性声明，强调了对依赖的内核 C 代码的信任，从而减少了检查达到零成本抽象。** 关于这一点在 [https://github.com/Rust-for-Linux/linux/pull/324](https://github.com/Rust-for-Linux/linux/pull/324) 中有很多讨论。

除clk之外还有 Device 也有部分相关代码。设备驱动在操作设备的clock之前，需要先获取和该clock关联的`struct clk`指针，在 Rust 代码中就是 `clk_ptr`。

```rust
// From: https://github.com/Rust-for-Linux/linux/blob/rust/rust/kernel/device.rs

#[cfg(CONFIG_COMMON_CLK)]
use crate::{clk::Clk, error::from_kernel_err_ptr};

// 该 trait 是一个 Unsafe trait
// 注释表明，在实现该 trait 的时候需要注意满足其安全条件
/// A raw device.
///
/// # Safety
///
/// Implementers must ensure that the `*mut device` returned by [`RawDevice::raw_device`] is
/// related to `self`, that is, actions on it will affect `self`. For example, if one calls
/// `get_device`, then the refcount on the device represented by `self` will be incremented.
///
/// Additionally, implementers must ensure that the device is never renamed. Commit a5462516aa994
/// has details on why `device_rename` should not be used.
pub unsafe trait RawDevice {
    
    // do more things

    // clk_get 方法是一个默认实现
    /// Lookups a clock producer consumed by this device.
    ///
    /// Returns a managed reference to the clock producer.
    #[cfg(CONFIG_COMMON_CLK)]
    fn clk_get(&self, id: Option<&CStr>) -> Result<Clk> {
        let id_ptr = match id {
            Some(cstr) => cstr.as_char_ptr(),
            None => core::ptr::null(),
        };

        // 安全性保证： `id_ptr`是一个可选值，要么是有效指针 要么是空指针，应该不会产生UB
        // 但是有效性也是依赖对 Linux C 语言端绑定指针的信任
        // SAFETY: `id_ptr` is optional and may be either a valid pointer
        // from the type invariant or NULL otherwise.
        let clk_ptr = unsafe { from_kernel_err_ptr(bindings::clk_get(self.raw_device(), id_ptr)) }?;

        // 因为上面的 `bindings::clk_get` 安全，那其返回值也认为是有效指针
        // SAFETY: Clock is initialized with valid pointer returned from `bindings::clk_get` call.
        unsafe { Ok(Clk::new(clk_ptr)) }
    }

    // do more things 
}

```

看得出来， `SAFETY` 注释粒度非常细，因为它依赖一个前提

另外，关于更多 Unsafe Rust 代码规范可以参考：[Rust 编码规范 - Unsafe Rust 部分](https://rust-coding-guidelines.github.io/rust-coding-guidelines-zh/safe-guides/coding_practice/unsafe_rust.html)


