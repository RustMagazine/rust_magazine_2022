# DatenLord | Rust原子类型和内存排序

作者：潘政

## 简介

原子类型在构建无锁数据结构，跨线程共享数据，线程间同步等多线程并发编程场景中起到至关重要的作用。本文将从Rust提供的原子类型和原子类型的内存排序问题两方面来介绍。

## Rust原子类型

Rust标准库提供的原子类型在[std::sync::atomic](https://doc.rust-lang.org/std/sync/atomic/index.html)模块下。Rust提供了AtomicBool, AtomicU8, AtomicU16, AtomicUsize等原子类型。下面我们以AtomicUsize为例介绍原子类型提供的原子操作。基本的load，store， swap原子操作就不过多介绍了。第一个要介绍的就是重要的compare-and-swap(CAS)原子操作，绝大部分无锁数据结构都或多或少依赖CAS原子操作。Rust提供的compare_and_swap接口如下：
```Rust
pub fn compare_and_swap(&self,
    current: usize,
    new: usize,
    order: Ordering
) -> usize
```
compare_and_swap接受一个期望的值和一个新的值，这里我们先忽略掉Ordering，后面会详细介绍，如果变量的值等于期望的值，就将变量值替换成新的值返回成功，否则不做任何修改并返回失败。compare_and_swap从语义上包含了读(load)语义和写(store)语义，先读出变量的值，和期望值比较，然后写入内存。原子操作保证了这三个步骤是原子的，在三个步骤之间不会插入其他指令从而导致变量值被修改。从1.50.0开始compare_and_swap被deprecated了，现在需要使用compare_exchange和compare_exchange_weak接口来实现CAS原子操作。
```Rust
pub fn compare_exchange(
    &self,
    current: usize,
    new: usize,
    success: Ordering,
    failure: Ordering
) -> Result<usize, usize>

pub fn compare_exchange_weak(
    &self,
    current: usize,
    new: usize,
    success: Ordering,
    failure: Ordering
) -> Result<usize, usize>
```
compare_exchange比compare_and_swap多了一个Ordering，两个Ordering分别作为CAS成功的Ordering和失败的Ordering，后面会有讲解，这里先跳过。从源代码可以看出compare_and_swap就是用compare_exchange实现的，只是compare_and_swap直接用成功情况下的Ordering生成在失败情况下的Ordering，compare_exchange则有更高的灵活性。
```Rust
pub fn compare_and_swap(&self, current: $int_type, new: $int_type, order: Ordering) -> $int_type {
    match self.compare_exchange(current,
                                new,
                                order,
                                strongest_failure_ordering(order)) {
        Ok(x) => x,
        Err(x) => x,
    }
}
```
既然有了compare_exchange，那compare_exchange_weak是做什么用的呢？从官方文档中可以看出两个API的唯一区别是compare_exchange_weak允许spuriously fail。那么什么是spuriously fail，在x86平台上CAS是一条指令完成的，这两个API在x86平台上效果没有区别，但是在arm平台上，CAS是由两条指令完成的LL/SC(Load-link/store-conditional)，在arm平台下会发生spuriously fail，来自Wikipedia的解释
```
Real implementations of LL/SC do not always succeed even if there are no concurrent updates to the memory location in question. Any exceptional events between the two operations, such as a context switch, another load-link, or even (on many platforms) another load or store operation, will cause the store-conditional to spuriously fail.
```
简单的翻译就是，即使变量的值没有被更新LL/SC也不是100%成功，在LL/SC之间的异常事件如上下文切换，另外的LL，甚至load或者store都会导致spuriously fail。由于spuriously fail的存在，arm平台上compare_exchange是compare_exchange_weak加上一个loop实现的。通常我们在使用CAS的时候会把它放在一个loop中，反复重试直到成功，在这种情况下用compare_exchange_weak会获得一定的性能提升，如果用compare_exchange则会导致循环套循环。那我们该如何选择compare_change和compare_exchange_weak呢？如果你想在loop中使用CAS，绝大部分情况下使用compare_exchange_weak，除非你在每一次loop中做的事情很多，spuriously fail会导致很大的overhead即使它很少发生，这种情况下使用compare_exchange。再或者你使用loop就是为了避免spuriously fail，那直接使用compare_exchange就可以达到你的目的。

接下来介绍另外一个重要的原子操作fetch-and-add。
```Rust
pub fn fetch_add(&self, val: usize, order: Ordering) -> usize
```
fetch_add也包含了读写两层语义，只是和CAS比起来它不关心变量当前的值，所以它一定成功。fetch_add一般用来做全局计数器。
Rust提供了一系列的fetch_and_xxx操作，其中比较有趣的是fetch_update
```Rust
pub fn fetch_update<F>(
    &self,
    set_order: Ordering,
    fetch_order: Ordering,
    f: F
) -> Result<usize, usize>
where
    F: FnMut(usize) -> Option<usize>,
```
它会接受一个函数，并将函数应用到变量上，把生成的值写回变量中，因为CPU不支持类似的指令，所以其实fetch_update是使用CAS来实现原子性的。源代码如下，我们可以看这里使用的是compare_exchange_weak，因为它在一个loop中。
```Rust
pub fn fetch_update<F>(&self,
                       set_order: Ordering,
                       fetch_order: Ordering,
                       mut f: F) -> Result<$int_type, $int_type>
where F: FnMut($int_type) -> Option<$int_type> {
    let mut prev = self.load(fetch_order);
    while let Some(next) = f(prev) {
        match self.compare_exchange_weak(prev, next, set_order, fetch_order) {
            x @ Ok(_) => return x,
            Err(next_prev) => prev = next_prev
        }
    }
    Err(prev)
}
```
## 内存排序
Rust提供了五种内存排序，由弱到强如下，并且内存排序被标记为`#[non_exhaustive]`表示未来可能会加入新的类型。
```Rust
#[non_exhaustive]
pub enum Ordering {
    Relaxed,
    Release,
    Acquire,
    AcqRel,
    SeqCst,
}
```
Rust的内存排序和C++20保持一致。内存排序作用是通过限制编译器和CPU的reorder，来使得多个线程看到的内存顺序和我们程序所期望的一样，所以内存排序主要针对的是内存的读(load)写(store)操作。编译器和CPU会在编译时和运行时来reorder指令来达到提升性能的目的，从而导致程序中代码顺序会和真正执行的顺序可能会不一样，但是reorder的前提是不会影响程序的最终结果，也就是说编译器和CPU不会reorder相互有依赖的指令从而破坏程序原本的语义。比方说两条CPU指令，指令A读取一块内存，指令B写一块内存，如果CPU发现指令A要读取的内容在cache中没有命中需要去内存中读取，需要花额外的CPU cycle，如果指令B要操作的内存已经在cache中命中了，它可以选择先执后面的指令B。这时候内存排序的作用就体现出来了，内存排序告诉编译器和CPU哪些指令可以reorder哪些不可以。接下来分别介绍每一种内存排序的意义。
- Relaxed：Relaxed Ordering不施加任何限制，CPU和编译器可以自由reorder，使用了Relaxed Ordering的原子操作只保证原子性。
```Rust
// Global varible
static x: AtomicU32 = AtomicU32::new(0);
static y: AtomicU32 = AtomicU32::new(0);
// Thread 1
let r1 = y.load(Ordering::Relaxed);
x.store(r1, Ordering::Relaxed);
// Thread 2
let r2 = x.load(Ordering::Relaxed); // A
y.store(42, Ordering::Relaxed); // B
```
这段程序是允许产生r1 == r2 == 42。按照正常的程序执行，这个结果看似不合理，但是因为使用了Relaxed Ordering，CPU和编译器可以自由reorder指令，指令B被reorder到指令A之前，那就会产生r1 == r2 == 42。
- Release：Release Ordering是针对写操作(store)的，一个使用了Release Ordering的写操作，任何读和写操作（不限于对当前原子变量）都不能被reorder到该写操作之后。并且所有当前线程中在该原子操作之前的所有写操作（不限于对当前原子变量）都对另一个对同一个原子变量使用Acquire Ordering读操作的线程可见。Release Ordering写和Acquire Ordering读要配对使用从而在两个或多个线程间建立一种同步关系。具体例子在介绍完Acquire之后一起给出。
- Acquire：Acquire Ordering是针对读操作(load)的，一个使用了Acquire Ordering的读操作，任何读和写操作（不限于对当前原子变量）都不能被reorder到该读操作之前。并且当前线程可以看到另一个线程对同一个原子变量使用Release Ordering写操作之前的所有写操作（不限于对当前原子变量）。

如果前面的例子中load使用Acquire Ordering，store使用Release Ordering，那么reorder就不会发生，r1 == r2 == 42的结果就不会产生。Acquire和Release动作特别像锁的加锁和释放锁的操作，因此Acquire Ordering和Release Ordering常被用在实现锁的场景。看下面的例子
```Rust
// Global varible
static DATA: AtomicU32 = AtomicU32::new(0);
static FLAG: AtomicBool = AtomicBool::new(false);
// Thread 1
DATA.store(10, Ordering::Relaxed); // A
FLAG.store(true, Ordering::Release); // B
// Thread 2
while !FLAG.load(Ordering::Acquire) {} // C
assert!(DATA.load(Ordering::Relaxed) == 10); // D
```
这段程序展示了两个线程之间同步的一种方式，在线程1中我们在共享内存中写入数据，然后把FLAG置成true，表明数据写入完成，在线程2中，我们用一个while循环等待FLAG被置成true，当FLAG被置成true之后，线程2一定会读到共享内存中的数据。线程1中的Release Ordering写和线程2中的Acquire Ordering读建立了顺序。当线程2跳出C行的循环表明它可以读到线程1在B行对FLAG的写入，按照Release-Acquire Ordering的保证，A行对DATA的写入不会被reorder到把FLAG置成true之后，并且对DATA的写入也会对线程2可见。假如这里没有使用Release-Acquire Ordering，那么线程对DATA的写入用可能会被reorder到写FLAG之后，那么线程2就会出现读到了FLAG但是读不到DATA的情况。
- AcqRel：AcqRel Ordering主要用于read-modify-write类型的操作，比如compare_and_swap，表明了它同时具有Acquire和Release的语义，读的部分用Acquire Ordering，写的部分用Release Ordering。
- SeqCst：SeqCst是Sequential Consistent的缩写，是一种最强的Ordering，在对读使用Acquire Ordering，对写使用Release Ordering，对read-modify-write使用AcqRel Ordering的基础上再保证所有线程看到所有使用了SeqCst Ordering的操作是同一个顺序，不论操作的是不是同一个变量。

这里包含了两层意思，第一层意思SeqCst禁止了所有的reorder，针对内存读(load)写(store)的reorder一共有四种情况：
- loadload reorder：Arquire Ordering保证
- loadstore reorder：Arquire Ordering和Release Ordering保证
- storestore reorder：Release Ordering保证
- storeload reorder：SeqCst Ordering保证

看下面的例子
```Rust
// Global varible
static x: AtomicU32 = AtomicU32::new(0);
static y: AtomicU32 = AtomicU32::new(0);
// Thread 1
x.store(1, Ordering::SeqCst);  // A
let r1 = y.load(Ordering::SeqCst); // B
// Thread 2
y.store(1, Ordering::SeqCst); // C
let r2 = x.load(Ordering::SeqCst);  // D
```
这里如果不使用SeqCst Ordering就会出现r1 == r2 == 0的结果，原因是每一个线程中的load可以被reorder到store之前，即使我们分别对load和store使用Acquire Ordering和Release Ordering，因为它们都不保证storeload的reorder。

SeqCst Ordering的第二层意思是所有使用了SeqCst Ordering的操作在全局有一个顺序，并且所有的线程都看到同样的顺序。比如说全局的顺序是A->B->C->D，那么r1 == 0 && r2 == 1，并且第三个线程如果看到了y == 1，那么它一定能看到x == 1，这就是SeqCst Ordering全局唯一顺序代表的意义。虽然使用SeqCst Ordering可以保证更严格的全局一致性，但是它也会带来性能损失，使用正确并且合适的内存排序才能获得最优的性能。

最后解释一下compare_exchange两个Ordering的含义，CAS包含1.读变量，2.和期望值比较，3.写变量三个步骤，第一个Ordering表示CAS成功下即变量当前的值等于期望值时候，整个操作的Ordering，第二个Ordering表示如果当前比较失败了情况下，第一步读操作的Ordering。看一个用CAS实现自旋锁的例子
```Rust
// Global lock
static LOCK: AtomicBool = AtomicBool::new(false);
// Thread 1
// Get lock
while(LOCK.compare_exchange_weak(false, true, Ordering::Acquire, Ordering::Relaxed).is_err()) {}
do_something();
// Unlock
LOCK.store(false, Ordering::Release);

// Thread 2
// Get lock
while(LOCK.compare_exchange_weak(false, true, Ordering::Acquire, Ordering::Relaxed).is_err()) {}
do_something();
// Unlock
LOCK.store(false, Ordering::Release);
```
## 总结
本文介绍了Rust提供的原子类型，原子操作以及和原子操作配合使用的内存排序。深入地理解内存排序才能写出正确并且性能最优的程序。内存排序是一个很深的话题，如有错误，欢迎指正，欢迎在评论区留言交流。
