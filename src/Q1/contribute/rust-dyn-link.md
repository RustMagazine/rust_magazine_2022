## Rust与动态链接

作者： 王江桐、周紫鹏

---

### 静态链接库与动态链接库

静态链接库（Statically-linked Library）和动态链接库（Dynamically-linked Library）是两种实现共享函数库概念的实现方式。

静态链接指：

> 把要调用的函数或者过程链接到可执行文件中，成为可执行文件的一部分。换句话说，函数和过程的代码就在程序的exe文件中，该文件包含了运行时所需的全部代码。当多个程序都调用相同函数时，内存中就会存在这个函数的多个拷贝，这样就浪费了宝贵的内存资源。

使用静态链接库时，连接器会找出程序所需的函数，然后将它们拷贝到执行文件中。由于这种拷贝是完整的，所以一旦连接成功，静态程序库也就不再需要了。同时，由于是完整拷贝，二进制文件的大小会相对比较大，也不能手动移除lib代码。

动态链接指：

> 动态链接是相对静态链接而言的，动态链接所调用的函数代码并没有被拷贝到应用程序的可执行文件中去，而是仅仅在其中加入了所调用函数的描述信息（往往是一些重定位信息）。仅当应用程序被装入内存开始运行时，在Windows的管理下，才在应用程序与相应的DLL之间建立链接关系。当要执行所调用DLL中的函数时，根据链接产生的重定位信息，Windows才转去执行DLL中相应的函数代码。一般情况下，如果一个应用程序使用了动态链接库，Win32系统保证内存中只有DLL的一份复制品。

因此，当某个程序在运行中要调用某个动态链接库函数时，操作系统首先会查看所有正在运行的程序，检查在内存里是否已有此库函数的拷贝。如果有，则共享该拷贝；如果没有，则链接载入。运行时，动态库可以动态地加载和移除，节省内存空间。



### Crate类型

crate type可以定义当前库通过`cargo build`生成的文件类型，由`crate-type`属性声明。该属性只能修改库（Library）与例子（Example）的生成类型，二进制（Binaries）、测试（Tests）以及Benchmarks永远生成的都是“bin”类型。`crate-type`可以通过`Cargo.toml`设置，RFC 3180也支持了命令行设置，指令为`rustc --crate-type=<crate-type>`，优先级高于`Cargo.toml`中设置的类型。也就是说，如果命令行参数定义值与`Cargo.toml`中定义值冲突，Cargo会使用命令行输入值。

默认类型如下：

| Target             | Crate Type     |
| ------------------ | -------------- |
| Normal library     | `"lib"`        |
| Proc-macro library | `"proc-macro"` |
| Example            | `"bin"`        |

可声明选项具体解释可见"[Linkage](https://doc.rust-lang.org/reference/linkage.html#linkage)"。概括如下：

- `bin`：可执行文件。不可以作为其他crate的依赖，通常编译为可运行的二进制程序，并且包含一个` main`函数作为开始运行的入口点。
- `lib`：Rust库，具体类型以及表达方式由编译器决定，输出库可以被rustc使用。泛指非`bin`类型的crate，不必包含`main`函数，可以作为其他crate的依赖。具体编译结果取决于其他依赖于它的crate。
- `rlib`：Rust库文件，类似于静态库，但是会在未来的编译中被编译器链接，这一点形似动态库。存储的目标文件（`*.o`）包含了rustc生成的元数据（rmeta），根据元数据，rlib可以连接到其他rust库。`rlib`不包括其依赖的代码，通过元数据查找依赖。
- `dylib`：动态库，可以作为其他库或是可执行文件依赖，文件类型在Linux中为`*.so`，macOS中为`*.dylib`，Windows中为`*.dll`。包含元数据。依赖于动态库的Rust二进制类型crate会在运行前，由操作系统的动态库加载器加载该依赖。
- `cdylib`：动态系统库，当编译需要提供给其他语言调用的库时使用，会静态链接Rust的标准库以及该库的所有依赖，输出动态库只暴露库所声明的符号，不依赖于标准库的符号。不可以作为Rust的依赖出现。文件类型在Linux中为`*.so`，macOS中为`*.dylib`，Windows中为`*.dll`。
- `staticlib`：静态库，为其他语言提供的静态库，会静态链接Rust标准库以及该库的所有依赖。不可以作为Rust的依赖出现。文件类型在Linux、macOS、Windows（minGW）中为`*.a`，Windows（MSVC）中为`*.lib`。
- `proc-macro`：过程宏编译输出结果，会静态连接到`libstd`，`proc_macro`。要求提供`-L`路径，编译器将认为输出文件是一个可以用于某个程序的宏，并且依赖于当前架构编译。由这个选项编译的crate应当只输出过程宏。

对于Rust库而言，依赖的库只有两种编译形式，`rlib`和`dylib`。当输出的库分别为以下形式时，Rust对于依赖的判定如下：

- 输出`bin`：
  - 设置`-C prefer-dynamic`情况下：倾向于使用`dylib`编译依赖，根据情况依赖类型或可能混合包含`rlib`和`dylib`。
  - 未设置情况下：倾向于使用`rlib`编译，当某个依赖不能作为`rlib`编译，例如强行设定依赖类型为`dylib`，依赖类型混合包含`rlib`和`dylib`。
- 输出`rlib`：依赖可以是`rlib`或`dylib。`
- 输出`dylib`、`cdylib`：倾向于使用`dylib`编译依赖，根据情况依赖类型或可能混合包含`rlib`和`dylib。`
- 输出`staticlib`：依赖只能是`rlib`，如果包含`dylib`，则编译失败。

当依赖图中存在两个`dylib`以不同方式链接同一个库时，可能存在以下情况：

- 两个`dylib`动态链接同一个`dylib`，随后动态链接这两个`dylib`：合法
- 两个`dylib`静态链接同一个`rlib`，随后动态链接这两个`dylib`：报错，静态链接会复制被调用函数或过程，导致这两个`dylib`提供相同的符号

在第二种报错情况，Rustc会使用贪心算法避免该情况，将依赖类型修改为没有冲突。策略为，被两个及以上`dylib`依赖的库，生成类型为`dylib`，反之则为`rlib`。



### 静态链接Rust库

在`Cargo.toml`中添加依赖相当于静态链接依赖库。

例如，对于二进制crate `hello`以及库crate `mylib`，添加后者至前者依赖：

```toml
[dependencies]
mylib = { path="../mylib" }
```

编译`hello`，使用`exa -T target --ignore-glob incremental`查看依赖图以及编译出的目标文件：

```bash
> cd hello
> cargo build -v
   Compiling mylib v0.1.0 (/tmp/rust/mylib)
     Running `rustc --crate-name mylib --edition=2018 /tmp/rust/mylib/src/lib.rs --error-format=json --json=diagnostic-rendered-ansi --crate-type lib --emit=dep-info,metadata,link -C embed-bitcode=no -C debuginfo=2 -C metadata=ff19b248b0012d99 -C extra-filename=-ff19b248b0012d99 --out-dir /tmp/rust/hello/target/debug/deps -C incremental=/tmp/rust/hello/target/debug/incremental -L dependency=/tmp/rust/hello/target/debug/deps -C target-feature=-crt-static`
   Compiling hello v0.1.0 (/tmp/rust/hello)
     Running `rustc --crate-name hello --edition=2018 src/main.rs --error-format=json --json=diagnostic-rendered-ansi --crate-type bin --emit=dep-info,link -C embed-bitcode=no -C debuginfo=2 -C metadata=0cf1e5265f244c28 -C extra-filename=-0cf1e5265f244c28 --out-dir /tmp/rust/hello/target/debug/deps -C incremental=/tmp/rust/hello/target/debug/incremental -L dependency=/tmp/rust/hello/target/debug/deps --extern mylib=/tmp/rust/hello/target/debug/deps/libmylib-ff19b248b0012d99.rlib -C target-feature=-crt-static`
    Finished dev [unoptimized + debuginfo] target(s) in 0.39s
> exa -T target --ignore-glob incremental
target
├── CACHEDIR.TAG
└── debug
   ├── build
   ├── deps
   │  ├── hello-0cf1e5265f244c28
   │  ├── hello-0cf1e5265f244c28.d
   │  ├── libmylib-ff19b248b0012d99.rlib
   │  ├── libmylib-ff19b248b0012d99.rmeta
   │  └── mylib-ff19b248b0012d99.d
   ├── examples
   ├── hello
   └── hello.d
```

文件列表详解如下：

- `*.d`：纯文本文件，使用类似makefile格式记录源码和中间文件依赖关系，通过rustc的`--emit=dep-info`选项生成。
- `*.rmeta`：`rlib`库生成的二进制文件的元数据文件，通过rustc的`--emit=metadata`选项生成。
- `*.rlib`：`rlib`库编译成的二进制文件，可以使用readelf查看。
- `hello-0cf1e5265f244c28`：二进制可执行程序，crate依赖通过rustc的`--extern <crate name>=<path to crate rlib>`传递。

`rlib`的十六位十六进制后缀，即`ff19b248b0012d99`，就是metadata的哈希值，通过rustc的`-C metadata=<HASH> -C extra-filename=-<HASH>`选项传递。哈希值由包ID、crate名称、版本号、url、启用特性、依赖元数据、编译时profile、mode、target名称与类型、rustc版本号等信息进行哈希计算得出。Rustflags的改变不会影响元数据的哈希结果，不更改包ID的情况下，修改源代码也不会产生影响。如果一个库的哈希值变动，那么所有依赖于这个库的哈希值都会产生变化。

不过，Cargo并不通过元数据的哈希值判断是否需要重新编译crate，而是通过指纹（Fingerprint）十六位十六进制哈希值判断。两者非常类似，但是包含的信息不同。简要来说，以下信息包含对比表格摘自`cargo/blob/master/src/cargo/core/compiler/fingerprint.rs`：

```rust
//! Fingerprints and Metadata are similar, and track some of the same things.
//! The Metadata contains information that is required to keep Units separate.
//! The Fingerprint includes additional information that should cause a
//! recompile, but it is desired to reuse the same filenames. A comparison
//! of what is tracked:
//!
//! Value                                      | Fingerprint | Metadata
//! -------------------------------------------|-------------|----------
//! rustc                                      | ✓           | ✓
//! Profile                                    | ✓           | ✓
//! `cargo rustc` extra args                   | ✓           | ✓
//! CompileMode                                | ✓           | ✓
//! Target Name                                | ✓           | ✓
//! TargetKind (bin/lib/etc.)                  | ✓           | ✓
//! Enabled Features                           | ✓           | ✓
//! Immediate dependency’s hashes              | ✓[^1]       | ✓
//! CompileKind (host/target)                  | ✓           | ✓
//! __CARGO_DEFAULT_LIB_METADATA[^4]           |             | ✓
//! package_id                                 |             | ✓
//! authors, description, homepage, repo       | ✓           |
//! Target src path relative to ws             | ✓           |
//! Target flags (test/bench/for_host/edition) | ✓           |
//! -C incremental=… flag                      | ✓           |
//! mtime of sources                           | ✓[^3]       |
//! RUSTFLAGS/RUSTDOCFLAGS                     | ✓           |
//! LTO flags                                  | ✓           | ✓
//! config settings[^5]                        | ✓           |
//! is_std                                     |             | ✓
//!
//! [^1]: Build script and bin dependencies are not included.
//!
//! [^3]: See below for details on mtime tracking.
//!
//! [^4]: `__CARGO_DEFAULT_LIB_METADATA` is set by rustbuild to embed the
//!        release channel (bootstrap/stable/beta/nightly) in libstd.
//!
//! [^5]: Config settings that are not otherwise captured anywhere else.
//!       Currently, this is only `doc.extern-map`.
```

### 动态链接Rust库

对于同样的例子，修改编译选项：

```bash
> RUSTFLAGS="-C prefer-dynamic -C target-feature=-crt-static" cargo build -v
   Compiling mylib v0.1.0 (/tmp/rust/mylib)
     Running `rustc --crate-name mylib --edition=2018 /tmp/rust/mylib/src/lib.rs --error-format=json --json=diagnostic-rendered-ansi --crate-type lib --emit=dep-info,metadata,link -C embed-bitcode=no -C debuginfo=2 -C metadata=ff19b248b0012d99 -C extra-filename=-ff19b248b0012d99 --out-dir /tmp/rust/hello/target/debug/deps -C incremental=/tmp/rust/hello/target/debug/incremental -L dependency=/tmp/rust/hello/target/debug/deps -C prefer-dynamic -C target-feature=-crt-static`
   Compiling hello v0.1.0 (/tmp/rust/hello)
     Running `rustc --crate-name hello --edition=2018 src/main.rs --error-format=json --json=diagnostic-rendered-ansi --crate-type bin --emit=dep-info,link -C embed-bitcode=no -C debuginfo=2 -C metadata=0cf1e5265f244c28 -C extra-filename=-0cf1e5265f244c28 --out-dir /tmp/rust/hello/target/debug/deps -C incremental=/tmp/rust/hello/target/debug/incremental -L dependency=/tmp/rust/hello/target/debug/deps --extern mylib=/tmp/rust/hello/target/debug/deps/libmylib-ff19b248b0012d99.rlib -C prefer-dynamic -C target-feature=-crt-static`
    Finished dev [unoptimized + debuginfo] target(s) in 0.38s
> exa -T target --ignore-glob incremental
target
├── CACHEDIR.TAG
└── debug
   ├── build
   ├── deps
   │  ├── hello-0cf1e5265f244c28
   │  ├── hello-0cf1e5265f244c28.d
   │  ├── libmylib-ff19b248b0012d99.rlib
   │  ├── libmylib-ff19b248b0012d99.rmeta
   │  └── mylib-ff19b248b0012d99.d
   ├── examples
   ├── hello
   └── hello.d
> ./target/debug/hello
Error loading shared library libstd-aef788a827ed39d9.so: No such file or directory (needed by ./target/debug/hello)
Error relocating ./target/debug/hello: _ZN3std2rt19lang_start_internal17h6606fb5e766e2b1cE: symbol not found
Error relocating ./target/debug/hello: rust_eh_personality: symbol not found
Error relocating ./target/debug/hello: _ZN3std2io5stdio6_print17h1ded803b1aed6cccE: symbol not found
Error relocating ./target/debug/hello: _ZN4core3fmt3num3imp52_$LT$impl$u20$core..fmt..Display$u20$for$u20$i32$GT$3fmt17h05aa13cbe62062a9E: symbol not found
> readelf -d target/debug/hello | grep NEEDED
  0x0000000000000001 (NEEDED)       Shared library: [libstd-aef788a827ed39d9.so]
  0x0000000000000001 (NEEDED)       Shared library: [libgcc_s.so.1]
  0x0000000000000001 (NEEDED)       Shared library: [libc.so]
> LD_LIBRARY_PATH=~/.rustup/toolchains/nightly-x86_64-unknown-linux-musl/lib/rustlib/x86_64-unknown-linux-musl/lib ./target/debug/hello
Hello, 1 world!
```

`libstd`已预编译，并且同时提供`rlib`和`dylib`两种格式，由于添加了`prefer-dynamic` flag，因此选择`dylib`格式。对于`mylib`，既可以使用`rlib`也可以使用`dylib`，因为它并不依赖于`dylib`，因此rustc自动选择了`rlib`。

如果对于`mylib`的`Cargo.toml`加入以下行：

```toml
[lib]
crate-type = ["dylib", "rlib"]
```

那么该库可以选择编译为`dylib`或`rlib`，并在有`prefer-dynamic` flag的情况下主动选择编译为`dylib`。

再次编译结果如下：

```bash
> RUSTFLAGS="-C prefer-dynamic -C target-feature=-crt-static" cargo build -v
   Compiling mylib v0.1.0 (/tmp/rust/mylib)
     Running `rustc --crate-name mylib --edition=2018 /tmp/rust/mylib/src/lib.rs --error-format=json --json=diagnostic-rendered-ansi --crate-type dylib --crate-type rlib --emit=dep-info,link -C prefer-dynamic -C embed-bitcode=no -C debuginfo=2 -C metadata=b6125a00ae42b63c --out-dir /tmp/rust/hello/target/debug/deps -C incremental=/tmp/rust/hello/target/debug/incremental -L dependency=/tmp/rust/hello/target/debug/deps -C prefer-dynamic -C target-feature=-crt-static`
   Compiling hello v0.1.0 (/tmp/rust/hello)
     Running `rustc --crate-name hello --edition=2018 src/main.rs --error-format=json --json=diagnostic-rendered-ansi --crate-type bin --emit=dep-info,link -C embed-bitcode=no -C debuginfo=2 -C metadata=29d11507098fa904 -C extra-filename=-29d11507098fa904 --out-dir /tmp/rust/hello/target/debug/deps -C incremental=/tmp/rust/hello/target/debug/incremental -L dependency=/tmp/rust/hello/target/debug/deps --extern mylib=/tmp/rust/hello/target/debug/deps/libmylib.so --extern mylib=/tmp/rust/hello/target/debug/deps/libmylib.rlib -C prefer-dynamic -C target-feature=-crt-static`
    Finished dev [unoptimized + debuginfo] target(s) in 0.43s
> exa -T target --ignore-glob incremental
target
├── CACHEDIR.TAG
└── debug
   ├── build
   ├── deps
   │  ├── hello-29d11507098fa904
   │  ├── hello-29d11507098fa904.d
   │  ├── libmylib.rlib
   │  ├── libmylib.so
   │  └── mylib.d
   ├── examples
   ├── hello
   ├── hello.d
   └── libmylib.so
> ./target/debug/hello
Error loading shared library libmylib.so: No such file or directory (needed by ./target/debug/hello)
Error loading shared library libstd-aef788a827ed39d9.so: No such file or directory (needed by ./target/debug/hello)
Error relocating ./target/debug/hello: rust_eh_personality: symbol not found
Error relocating ./target/debug/hello: _ZN3std2io5stdio6_print17h1ded803b1aed6cccE: symbol not found
Error relocating ./target/debug/hello: _ZN4core3fmt3num3imp52_$LT$impl$u20$core..fmt..Display$u20$for$u20$i32$GT$3fmt17h05aa13cbe62062a9E: symbol not found
Error relocating ./target/debug/hello: _ZN5mylib3num17hefef8e0aa2fb2876E: symbol not found
Error relocating ./target/debug/hello: _ZN3std2rt19lang_start_internal17h6606fb5e766e2b1cE: symbol not found
> readelf -d target/debug/hello | grep NEEDED
  0x0000000000000001 (NEEDED)       Shared library: [libmylib.so]
  0x0000000000000001 (NEEDED)       Shared library: [libstd-aef788a827ed39d9.so]
  0x0000000000000001 (NEEDED)       Shared library: [libgcc_s.so.1]
  0x0000000000000001 (NEEDED)       Shared library: [libc.so]
> LD_LIBRARY_PATH=~/.rustup/toolchains/nightly-x86_64-unknown-linux-musl/lib/rustlib/x86_64-unknown-linux-musl/lib:./target/debug ./target/debug/hello
Hello, 1 world!
```

`/dep`文件夹中现在包含`libmylib.so`文件，即为动态链接库文件。动态链接库文件名并不包含元数据哈希。设置`crate-type = ["dylib", ...]`之后，`dylib`可以在多个rust二进制文件之间共享，而不会发生重复拷贝或找不到符号的情况。不过，只有设置了`crate-type`的库才可以动态链接，用户无法非常轻松地让所有的crate都使用动态链接，在Rust中使用`dylib`具有局限性。

如果在C语言中使用动态库，那么动态库更新时，如果ABI（Application Binary Interface）没有变化，用户并不需要重新编译依赖它的程序。Rust的动态链接的另一个局限性在于，它无法做到这一点，需要重编译依赖者。

ABI是应用程序二进制接口，通常与API（Application Programming Interface）相对应。API指：

> 一个程序或库对开发者提供的一组接口，包括了函数，常量，全局变量，结构体，枚举的定义，这样开发者可以在不了解函数实现的前提下使用该程序和库。通常API稳定是指现有的函数名和签名不发生变化，常量和全局变量名称不变，结构体现有各个成员的名称和类型不变，枚举现有各个成员的名称和类型不变，且前面所述不变的各个项目语义没有发生变化。API稳定是源码级的，即API稳定的库在升级后，重新编译依赖其的程序，就可以继续使用，无需修改程序。

ABI也是两程序模块间的接口，但是其稳定性更加严格：

> 常量的类型和值不能发生变化，函数的符号和调用约定不能发生变化，全局变量的符号和类型不能发生变化，包括结构体和枚举在内的各个类型的类型大小、布局、对齐不能发生变化。

然而，Rust在默认情况下并没有稳定的ABI。即使是定义完全相同的结构体，其布局也可能发生变化。出于性能原因，Rust调Rust的调用约定和数据布局在编译器中随时可能改变，因此，结构字段在内存中的布局与代码中的写入顺序可能会完全不同，甚至不一定连续。实际上，在Rust所有支持的调用约定中，只有C调用约定是稳定的，可以使用`#[repr(C)]`修饰结构体来使用C调用约定，但是这可能对性能使用并不友好。

不过，就算使用C调用可以实现稳定ABI，升级版本号以后，动态链接库符号名后的哈希值依旧会发生变化。这个哈希值被称为SVH（Strict Version Hash），是对于整个库计算出的哈希结果。SVH是一个64位哈希值，用于保证库依赖正确加载以及增量编译，跟踪节点在编译之间是否发生更改，以及同一crate是否使用时包含多个版本。其计算受到各个因素的影响，其中包括HIR节点哈希值、所有上游库哈希值、所有源文件名、一些命令行flags的哈希值、etc。具体的解释可见《[Guide to Rustc Development：Strict Version Hash](https://rustc-dev-guide.rust-lang.org/backend/libs-and-metadata.html#strict-version-hash)》，源码可见[`/nightly-rustc/stc/rustc_ast_loiwering/lob.rs`](https://doc.rust-lang.org/nightly/nightly-rustc/src/rustc_ast_lowering/lib.rs.html#414-431)中函数`compute_hir_hash`。

当版本号变化时，哈希值变化，Cargo会判定原二进制文件不可用，从而重新编译。同一crate的不同版本计算可以得到不同的SVH，因此Rust允许同时使用同一crate的不同版本，除非该crate包含了#[no_mangle]修饰的符号。但是，由于Cargo会进行重新编译，Rust无法像C语言中使用动态库一样，仅升级动态库，而不重新编译依赖者。

目前唯一的解决方案是使用`cdylib`，使用C接口以及C动态库特性。纯Rust生态下的解决方案仍然有待探索。



## 参考

- 动态链接库与静态链接库的区别: [https://blog.csdn.net/fuzhongmin05/article/details/54616520](https://blog.csdn.net/fuzhongmin05/article/details/54616520)
- 静态链接库和动态链接库的区别: [https://cloud.tencent.com/developer/article/1531843](https://cloud.tencent.com/developer/article/1531843)
- "Rust does not have a stable ABI": [https://people.gnome.org/~federico/blog/rust-stable-abi.html#rust_does_not_have_a_stable_abi](https://people.gnome.org/~federico/blog/rust-stable-abi.html#rust_does_not_have_a_stable_abi)

