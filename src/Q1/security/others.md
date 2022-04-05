# Rust 安全参考 ｜ 其他类型

## Rust 编译到 WebAssembly 可能出现的侧信道攻击

这是一篇来自 [Trail of Bits](https://www.trailofbits.com/) 安全公司博客的文章，介绍了 Rust 利用 LLVM 编译到 WebAssembly 时可能出现新的侧信道攻击风险。

许多工程师选择 Rust 作为他们实现加密协议的首选语言，因为它具有强大的安全保证。尽管 Rust 使安全的密码工程更容易，但仍有一些挑战需要注意。其中之一是需要保留恒定时间（constant-time）属性，这确保无论输入如何，代码都将始终花费相同的时间来运行。这些属性在防止时序攻击（timing attack）方面很重要，但它们可能会受到编译器优化的影响。

> 时序攻击，是侧信道攻击时序攻击属于侧信道攻击/旁路攻击(Side Channel Attack)，侧信道攻击是指利用信道外的信息，比如加解密的速度/加解密时芯片引脚的电压/密文传输的流量和途径等进行攻击的方式，一个词形容就是“旁敲侧击”。举一个最简单的计时攻击的例子，某个函数负责比较用户输入的密码和存放在系统内密码是否相同，如果该函数是从第一位开始比较，发现不同就立即返回，那么通过计算返回的速度就知道了大概是哪一位开始不同的，这样就实现了电影中经常出现的按位破解密码的场景。密码破解复杂度成千上万倍甚至百万千万倍的下降。最简单的防御方法是：“发现错误的时候并不立即返回，而是设一个标志位，直到完全比较完两个字符串再返回”。
> 来源知乎：[https://www.zhihu.com/question/20156213/answer/43377769](https://www.zhihu.com/question/20156213/answer/43377769)

**恒定时间（constant-time）**

密码学很难正确实现。除了担心整体正确性和可能以意想不到的方式暴露秘密的边缘情况外，潜在的侧信道泄漏和时序攻击也是令人深感担忧的问题。

时序攻击试图利用应用程序的执行时间可能微妙地依赖于输入这一事实。如果应用程序根据私密数据（例如随机数生成器或私钥的种子）做出与控制流相关的决策，这可能会稍微影响应用程序的执行时间。同样，如果使用私密数据来确定从内存中的哪个位置读取，这可能会导致缓存未命中，进而影响应用程序的执行时间。在这两种情况下，有关私密数据的信息都会在程序执行期间通过时间差异泄露。

为了防止这种时间差异，密码学工程师通常避免根据私密数据实施决策。但是，在代码需要根据私密数据做出决策的情况下，有一些巧妙的方法可以在恒定时间内实现它们，也就是说，无论输入如何，总是在相同的时间内执行。例如，考虑以下Rust函数，它在变量`a`和 `b`之间执行条件选择。

```rust
#[inline]
fn conditional_select(a: u32, b: u32, choice: bool) -> u32 {
    if choice { a } else { b }
}
```

根据编译器工具链和目标指令集的不同，编译器可以选择使用分支指令来实现条件选择，比如x86上的`jne`或ARM上的`bne`。这将在函数的执行过程中引入一个时间差，这可能会泄露关于选择变量的信息。下面的Rust实现使用了一个巧妙的技巧，在恒定时间内执行相同的条件选择。

```rust
// 设置标志位，避免马上返回
#[inline]
fn conditional_select(a: u32, b: u32, choice: u8) -> u32 {
    // if choice = 0, mask = (-0) = 0000...0000
    // if choice = 1, mask = (-1) = 1111...1111
    let mask = -(choice as i32) as u32;
    b ^ (mask & (a ^ b))
}
```

理想情况下，这种方式好像已经解决了问题。但实践中，也存在固有风险。由于编译器没有时间概念，因此它不会将时间差异视为可观察到的行为。这意味着可以自由地重写和优化恒定时间代码，这可能会在程序中引入新的时间泄漏。像上面这样精心编写的恒定时间实现仍然可以由编译器优化为分支指令，这会泄漏`choice`!

如何阻止编译器优化破坏代码的恒定时间呢？有几种方案：

-  使用 `-C opt-level=0` 关闭所有优化。这种方案基本不可行，因为我们需要编译器的优化。
-  使用来自 [subtle](https://github.com/dalek-cryptography/subtle) crate 的构造来尝试阻止 LLVM 优化恒定时间代码路径的尝试。
- 语言内置私密类型来支持。之前有一个 [Rust RFC](https://github.com/rust-lang/rfcs/pull/2859) 引入了 secret types，但这已被推迟，等待 LLVM 支持。

来自 subtle crate 中的构造，用于屏蔽优化：

```rust

#[inline(never)]
fn black_box(input: u8) -> u8 {
    // 使用 read_volatile 来告诉编译器 &input 的内存是易失的，编译器不应该对它做任何假设
    // 起到了一个优化屏障的作用
    unsafe { core::ptr::read_volatile(&input as *const u8) }
}

// 通过检查生成的汇编指令，可以确定该函数将始终在恒定时间内运行，是有效的
pub fn test_with_barrier(a: u32, b: u32, choice: bool) -> u32 {
    let choice = black_box(choice as u8); // 使用上面定义的优化屏障
    conditional_select(a, b, choice) 
}
```

**从 Rust 编译到 WebAssembly 会如何？**

在node中使用 WASM，意味着需要经过两次优化：

- 由 Rust 经过 LLVM 编译为 WebAssembly
- 再由 V8 的 Turbofan JIT 编译器再次对 WAMS 进行编译优化

上面的优化屏障代码对 LLVM 有用，但是对 Turbofan 是否有用？

实践证明，Turbofan 没有对 `black_box` 函数进行优化，由于 Wasm 中没有任何内容与 Rust 中的 volatile 读取相对应，理论上， Turbofan 没有理由继续保留  `black_box` ，但实际上 `black_box`有写内存行为，不保证一定没有副作用，所以 Turbofan 没有优化掉它。这同时也证明了，`black_box`中的私密值`&input`被泄露到了wasm内存中，这不是好现象。

如果使用 `llvm_asm!` 重新实现 `black_box`函数：

```rust
#[inline(never)]
fn black_box(input: u8) -> u8 {
    unsafe { llvm_asm!("" : "+r"(input) : : : "volatile"); }
 
    input
}
```

此函数不会将私密值input泄露到内存中。

**总结**

很明显，通过插入优化屏障来对抗 LLVM 并不是提供恒定时间保证的好方法。正在努力在语言层面解决这个问题。私密类型 RFC和[CT-Wasm 项目](https://github.com/PLSysSec/ct-wasm)分别为 Rust 和 Wasm 引入了秘密类型，是这种努力的两个很好的例子。缺少的是一种将机密类型和相应语义导入 LLVM 的方法。这很可能是 Rust 实现向前发展的先决条件。（Rust RFC 目前被推迟，等待 LLVM 的相应 RFC。）如果没有 LLVM 支持，很难看出依赖 LLVM 的高级语言如何提供任何绝对恒定时间的保证。在那之前，我们都在和编译器后端玩捉迷藏。

**后续探索**

该团队后续探索出一种方法：在 Rust 编译器 (rustc) 中实现一个功能，让用户可以更好地控制生成的代码。相关代码见 [https://github.com/trailofbits/rust-optnone](https://github.com/trailofbits/rust-optnone)，等测试完成后会向 Rust 官方提交 PR。

出发点在于，我们是否可以影响编译器不优化conditional_select函数，而不是全局禁止优化？Cargo 和 rustc 接受全局禁用优化的参数，但在整个系统上这样做通常是不可能的。一种可能的解决方案是阻止对特定功能的优化。

该团队利用 LLVM 的 [`optnone`](https://llvm.org/docs/LangRef.html#function-attributes) 属性来禁用函数级别优化。

> 此函数属性表示大多数优化传递将跳过此函数，但过程间优化传递除外。代码生成默认为“快速”指令选择器。该属性不能与 alwaysinline 属性一起使用；此属性也与 minsize 属性和 optsize 属性不兼容。此属性还需要在函数上指定 noinline 属性，因此该函数永远不会内联到任何调用者中。只有具有 alwaysinline 属性的函数才是内联到该函数主体的有效候选者。

根据文档，该功能还需要该noinline属性。碰巧的是，我们已经用该属性`#[inline(never)]`标记了函数。现在，只需在 Rust 中实现另一个属性，该属性在编译时将为函数生成`optnone`和`noinline`属性。

基于已经实现的 [`optimize`](https://github.com/rust-lang/rust/issues/54882) 属性改造。

```rust
// 为 optimize 属性添加 never 选项
#[optimize(never)]
fn conditional_select(a: u32, b: u32, choice: bool) -> u32 {
    let mask = -(choice as i32) as u32;
    b ^ (mask & (a ^ b))
}
```

在 part2 文章里介绍了这个实现过程，可以看原文了解详情。

但这样是彻底解决问题了吗？非也。机器代码生成过程中依赖特定平台依然会有一些优化。

未来通过引入私密类型可能会解决问题，但是目前，只能依赖于我们已经掌握的信息，依赖于 `#[optimize(never)]` 来向前迈出一小步了。

- [Part I: https://blog.trailofbits.com/2022/01/26/part-1-the-life-of-an-optimization-barrier/](https://blog.trailofbits.com/2022/01/26/part-1-the-life-of-an-optimization-barrier/)
- [Part II: https://blog.trailofbits.com/2022/02/01/part-2-rusty-crypto/](https://blog.trailofbits.com/2022/02/01/part-2-rusty-crypto/)


## RUSTSEC-2022-0011： 在 rust-crypto 中执行 AES 加密时计算错误

目前还未有任何补丁。

[https://rustsec.org/advisories/RUSTSEC-2022-0011.html](https://rustsec.org/advisories/RUSTSEC-2022-0011.html)
