# Rust 异步与取消

作者： 王江桐、周紫鹏

---

在 Rust 中，异步原语可分为两类：

1. Future，是 Rust 语言以及标准库的一部分，实现了 `Future` trait 的实例可以调用 `await`，试图获取异步任务执行的状态，在运行时的调度下，Future 的实际代码逻辑会转化为 Task（任务）；
2. Task（任务）则和 Future 相反，并不是 Rust 语言以及标准库的一部分，同时在一般的异步中，也不是用户直接接触到的部分。异步执行逻辑组成 Task 单元，由异步运行时管理，可以在多线程上运行。执行器在多线程中执行、移动以及调度 Task，Task 返回 Future，当任务执行完成时，用户通过 Future 获取任务执行的返回值。

在很多其它语言中，例如 JavaScript，这些语言处理异步时，使用的是类如 Task 的任务单元，并且不提供类如 Future 的结构。语言自带的运行时则会优化任务的调度以及运行。Rust 则相反，并没有自带运行时，用户接触到的是 Future 而非 Task。运行时以及任务调度则由三方库提供，例如 tokio 以及 asnyc-std。

在一些场景下，我们可能需要取消已生成的实现 `Future` trait 的实例。一个不完全贴切的例子是，如果出于某些原因，我们误生成了下载动作，以及相关的后续逻辑，那么我们应当有方法取消这一误操作产生的结果。



## 取消 Futures

不考虑`async drop`的情况下，不论是取消单一的 Future 还是一整个 Future 树，或是取消 `await` 之前或之后的 Future，取消都非常简单，只需要调用 `drop` 函数即可，Rust 标准库会处理后续的流程。

Rust 语言组还没有实现 `async drop`。关于这一点，可见《[This Week in Rust #412：Rust Async trait更新与多线程](http://openx.huawei.com/Ylong_Rust/dynamicDetail/3379)》。

举例来说，对于取消调用 `await` 之前的 Future：

```rust
use std::time::Duration;

struct Guard;
impl Drop for Guard {
    fn drop(&mut self) {
        println!("2");
    }
}

async fn foo(guard: Guard) {
    println!("3");
    task::sleep(Duration::from_secs(1)).await;
    println!("4");
}

fn main() {
    println!("1");
    let guard = Guard {};
    let fut = foo(guard);
    // 取消
    drop(fut);
    println!("done");
}
```

打印结果是：

```shell
> 1
> 2
> done
```



取消调用了一次 `await` 的 Future：

```rust
use std::{ptr, task};
use async_std::task;
use std::time::Duration;

async fn foo() {
    println!("2");
    task::sleep(Duration::from_secs(1)).await;
    println!("3");
}

let mut fut = Box::pin(foo());
let mut cx = empty_cx();

println!("1");
assert!(fut.as_mut().poll(&mut cx).is_pending());
drop(fut);
println!("done");

/// Create an empty "Waker" callback, wrapped in a "Context" structure.
/// How this works is not particularly important for the rest of this post.
fn empty_cx() -> task::Context { ... }
```

打印结果是：

```shell
> 1
> 2
> done
```

在 Future 逻辑的两个 `await` 之间，或是在最后一个 `await` 调用之后，无法取消 Future。在这种情况下，没有 `await`，即没有异步逻辑，Future 的内部逻辑执行没有暂停点，会持续执行直到通过 `await` 暂停或返回。

取消 Future 树：

```rust
use async_std::prelude::*;
use async_std::task;
use std::time::Duration;

async fn foo() {
    println!("2");
    bar().timeout(Duration::from_secs(3)).await;
    println!("5");
}

async fn bar() {
    println!("3");
    task::sleep(Duration::from_secs(2)).await;
    println!("4");
}

println!("1");
foo().timeout(Duration::from_secs(1)).await;
println!("done");
```

对于 Future 树：

```
main
  -> foo (times out after 1 sec)
    -> bar (times out after 3 secs)
      -> task::sleep (wait for 2 secs)
```

取消后的打印结果为：

```shell
> 1
> 2
> 3
> done    # `4` and `5` are never printed
```

虽然在大部分情况下，当实例理论上退出生命周期时，Rust 都会调用它们的析构函数，但是在一些场景下，析构函数并不能保证被调用，例如引用循环以及多线程中使用 `Rc` 。在其它语言，例如 Swift 和 Go 中，语言层级上的解决方案是使用 `defer` 关键字。这一关键字可以保证匿名析构函数一定会被调用。这部分的解析可见《[The defer keyword in Swift: try/finally done right](https://www.hackingwithswift.com/new-syntax-swift-2-defer)》。在 Rust 中，目前则暂时没有运行时直接回应这个问题，Rust 的三方库  [scopeguard](https://docs.rs/scopeguard/1.1.0/scopeguard/#macros) 提供了一个类似于 `defer` 的宏，以及相应的套件。 使用 `defer` 会生成析构函数运行保证器，不过它会直接获取数据的所有权。如果我们只希望当 Future 中途取消时，在退出的时候才将所有权移交，即在此之前所有权应当由用户管理，而不是保证器的话，那么可以使用 [`scopeguard::SopeGuard`](https://docs.rs/scopeguard/1.1.0/scopeguard/struct.ScopeGuard.html)，然后通过 `Deref / DerefMut` trait 来获取其中数据。



## 取消 Task

Task 并不是 Rust 标准化的实例，因此无法通过 `drop` 或其他非常直观的手段暂停：

```rust
// 以 async-std 运行时为例
use async_std::task;

let handle = task::spawn(async {
    task::sleep(Duration::from_secs(1)).await;
    println!("2");
});

println!("1");
drop(handle);     // Drop the task handle.
task::sleep(Duration::from_secs(2)).await;
println!("done");
```

打印结果为：

```shell
> 1
> 2
> done
```

可以看到，drop handle 并不会取消异步 Task 的运行。对于 Task，async-std 运行时的逻辑与 tokio 相同。生成的异步任务与返回的 handle 无关，handle 生命周期结束并不会连带结束它所指向的 Task，而是使得这一 Task 与 handle 分离。这一语义被称为 "detach on drop"，即析构时分离。

一般来说，针对这种情况，不同的运行时会提供不同的处理方式。例如，async-std 使用 `JoinHandle::cancel`，tokio 使用 `JoinHandle::abort` 来终止单一 Task 的运行：

```rust
use async_std::task;

let handle = task::spawn(async {
    task::sleep(Duration::from_secs(1)).await;
    println!("2");
});

println!("1");
handle.cancel().await;    // Cancel the task handle
task::sleep(Duration::from_secs(2)).await;
println!("done");
```

打印结果为：

```shell
> 1
> done
```

然而，由于 Task 的取消并不能通过析构 handle 来实现，取消 Task 树并不像取消 Future 树，会自动向下传递取消行为。逻辑上，使用取消单一 Task 的方式可能可以成功取消根 Task，但是这可能导致树中的其他 Task 悬垂，并继续运行，而非取消执行并结束生命周期。

在三方库运行时中，smol 运行时会自动传递 Task 树的取消：

```rust
smol::block_on(async {
    println!("1");
    let task = smol::spawn(async {
        println!("2");
    });
    drop(task);
    println!("done")
});
```

打印结果为：

```shell
> 1
> done
```

然而，并不是所有运行时都像 smol 一样会自动传递 Task 树的取消。对于其他的一些运行时，如果要传递这一行为，用户可能要考虑手动写一个包装器（Wrapper），确保调用 drop 时，所有指向树中对应 Task 的 handle 都会调用 `abort`：

```rust
use tokio::task;
use core::future::Future;
use core::pin::Pin;
use core::task::{Context, Poll};

/// Spawn a new tokio Task and cancel it on drop.
pub fn spawn<T>(future: T) -> Wrapper<T::Output>
where
    T: Future + Send + 'static,
    T::Output: Send + 'static,
{
    Wrapper(task::spawn(future))
}

/// Cancels the wrapped tokio Task on Drop.
pub struct Wrapper<T>(task::JoinHandle<T>);

impl<T> Future for Wrapper<T>{
    type Output = Result<T, task::JoinError>;
    fn poll(mut self: Pin<&mut Self>, cx: &mut Context<'_>) -> Poll<Self::Output> {
        unsafe { Pin::new_unchecked(&mut self.0) }.poll(cx)
    }
}

impl<T> Drop for Wrapper<T> {
    fn drop(&mut self) {
        // do `let _ = self.0.cancel()` for `async_std::task::Task`
        self.0.abort();
    }
}
```



### 结构化并发 (Structured Concurrency)

参考维基百科，结构化并发的定义为：

> **结构化并发**是一种编程范式，旨在通过使用结构化的并发编程方法来提高计算机程序的清晰度、质量和开发时间。
>
> 结构化并发的核心概念是，通过具有明确出入口，并可以确保所有生成线程在主线程退出之前结束的控制流结构，并行线程的封装。这里的线程包括内核和用户的线程，以及进程。这种封装允许并发线程中的错误传播到控制结构的父域，即调用域或上一层上下文环境，并由每种特定计算机语言的本地错误处理机制进行管理。如果想要保持结构化并发范式，那么当前程序的所有层级都必须应用这一模型，否则并发线程可能会在中途泄漏、成为孤立线程或无法正确传播运行时错误。
>
> 结构化并发类似于结构化编程，后者引入了封装顺序语句和子例程的控制流构造。
>
> （有翻译，参考了对于维基百科页面《[Structued Concurrency](https://en.wikipedia.org/wiki/Structured_concurrency)》的谷歌机翻，同时人工修改了一些不通顺的语句）

同时补充一个题外话，结构化编程的定义是：

> **结构化编程**（Structured programming）是一种编程典范。它采用子程序、块结构、for循环以及while循环等结构，即控制流结构，来取代传统的 goto。希望借此来改善计算机程序的明晰性、质量以及开发时间，并且避免写出混乱且晦涩的代码。
>
> 结构化的程序是以一些简单、有层次的程序流程架构所组成，可分为循序（Sequence）、选择（Selection）及重复（Repetition）。
>
> - 循序是指程序正常的执行方式，执行完一个指令后，执行后面的指令。
> - 选择是依程序的状态，选择数段程序中的一个来执行，一般会使用`if..then..else..endif`或`switch`、`case`等关系字来识别。
> - 重复是指一直执行某一段程序，直到满足特定条件，或是一集合体中的所有元素均已处理过，一般会使用`while`、`repeat`、`for`、`do..until`或`do...while`等关键字识别。一般会建议每个循环只能有一个进入点（戴克斯特拉的结构化编程要求每个循环只能有一个进入点及一个结束点，有些编程语言仍有此规定）。
>
> 若一个编程语言的语法允许用成对的关键字包围一段程序，形成一个结构，这种编程语言称为有“块结构”，这类的结构包括用ALGOL 68的`if..fi`包围的程序，或是在PL/I中用`BEGIN..END`包围的一段程序，或是在C语言中用大括号`{...}`包围的一段程序。

也就是说，对于 Rust 异步而言，如果要保持结构化并发结构，那么 Futures 和 Tasks 需要满足如下条件：

1. 子 Future / Task 的生命周期不能长于父 Future / Task；
2. 如果父 Future / Task 被取消，那么子 Future / Task 也应当被取消；
3. 如果子 Future / Task 产生了错误，那么父 Future / Task 应当可以获取这个错误，并对其进行处理，不管是继续报错还是安全处理。

如前文所说，Future 可以保持这一结构，而在很多运行时中， Task 不行，在树中传递取消需要用户手动增加包装器，而非像 Future 一样自动传递。同时，很多运行时以及异步代码仅仅是生成 Task，随后 Task 将与主线程分离，并在产生错误时记录日志，而非向上层传递。例如：

```rust
// The async-std "echo tcp server" example.
use async_std::io;
use async_std::net::{TcpListener, TcpStream};
use async_std::prelude::*;
use async_std::task;

// Listen for new TCP connections on port 8080.
let listener = TcpListener::bind("127.0.0.1:8080").await?;

// Iterate over the incoming connections, and move each task to a multi-threaded
// runtime.
let mut incoming = listener.incoming();
while let Some(stream) = incoming.next().await {
    task::spawn(async {
        // If an error occurs, log it to stderr.
        if let Err(err) = run(stream).await {
            eprintln!("error: {}", err);
        }
    });
}

// The main logic of our listen loop. This is a simple echo server.
async fn run(stream: io::Result<TcpStream>) -> io::Result<()> {
    let stream = stream?;
    let (reader, writer) = &mut (&stream, &stream);
    io::copy(reader, writer).await?;
    Ok(())
}
```

对此，一个新的封装概念是 TaskGroup（任务组），将 Task 树打包认作是一整个 TaskGroup，内部的逻辑由 TaskGroup 封装处理，即析构一整个 TaskGroup 会按序取消一整个 Task 树，而树中任何的错误都会向上传递至 TaskGroup 实例，并且导致树中所有 Task 取消。虽然目前没有运行时支持这一概念，不过 crate.io 上也有库提供了这一功能，例如 [task-group](https://crates.io/crates/task-group)。



### 不能被取消的 Async trait

[Async Foundation 工作小组](https://rust-lang.github.io/compiler-team/working-groups/async-await/)此前曾讨论过，是否要增加无法被取消的 Future trait，即实现该 trait 的 Future 实例一定保证会运行完成，无论是报错还是顺利结束。主要原因有两个：

1. 与 C++ 的异步环境兼容，在 C++ 中，Future 运行中断这一行为是未定义行为（Undefined Behavior）；
   - 举例来说，在 Linux 环境下，如果用户使用 `io_uring` 调用异步 `read()`，但是随后取消了，那么用户必须使用某些方式保证内核不会使用 Rust 释放的 buffer。方法包括通过 `IORING_REGISTER_BUFFERS` 使得内核获取 buffer 的所有权，但是这样写可能会带来其他的不便利。《[Notes on io-uring](https://without.boats/blog/io-uring/)》对这一现象有比较详细的解释，不过认为内核获取 buffer 所有权并不是大问题，可以直接在其上设计其他的高性能 API。
2. 对于异步初学者而言，这样可以减少问题复杂度。
   - 不过，关于这一点，随着异步的功能完善以及便利封装，工作小组暂时不会再考虑这一理由。



## 参考

Async Cancellation I，https://blog.yoshuawuyts.com/async-cancellation-1/#notes

Structured Concurrency，https://en.wikipedia.org/wiki/Structured_concurrency

结构化编程，https://zh.wikipedia.org/wiki/%E7%BB%93%E6%9E%84%E5%8C%96%E7%BC%96%E7%A8%8B