# Rust 安全参考 ｜ 内存相关漏洞


## RUSTSEC-2022-0002： dashmap 中的引用出现 UAF

由Ref（和类似类型）的一些方法返回的引用可能会超过Ref并逃脱锁。这将导致未定义的行为，并可能导致一个段错误。

- [https://rustsec.org/advisories/RUSTSEC-2022-0002.html](https://rustsec.org/advisories/RUSTSEC-2022-0002.html)
- [https://github.com/xacrimon/dashmap/issues/167](https://github.com/xacrimon/dashmap/issues/167)

## RUSTSEC-2022-0008： Windows-rs 中 Delegate 函数缺乏 `Send` 限定

- [https://rustsec.org/advisories/RUSTSEC-2022-0008.html](https://rustsec.org/advisories/RUSTSEC-2022-0008.html)
- [https://github.com/microsoft/windows-rs/issues/1409](https://github.com/microsoft/windows-rs/issues/1409)

## RUSTSEC-2022-0012: Arrow2 在 Safe 代码中出现双重释放（double-free）

`Ffi_ArrowArray` 结构体错误实现 `#derive(Clone)` ，因为它是一个 FFi 绑定，实现 Clone 会导致出现两份指针，从而导致双重释放。

- [https://github.com/jorgecarleitao/arrow2/issues/880](https://github.com/jorgecarleitao/arrow2/issues/880)
- [https://rustsec.org/advisories/RUSTSEC-2022-0012.html](https://rustsec.org/advisories/RUSTSEC-2022-0012.html)

## RUSTSEC-2022-0003: ammonia 中存在格式化注入漏洞

`clean_text`中错误映射 HTML 的 Form Feed，导致注入漏洞。

```rust
let html = format!("<div title={}>", clean_text(user_supplied_string));
```

- [https://github.com/rust-ammonia/ammonia/pull/147](https://github.com/rust-ammonia/ammonia/pull/147)

## RUSTSEC-2022-0006: `thread_local` crate 中的 `RawIter::next` 存在数据竞争

主要是因为内存顺序指定错误而引起的，解决起来也比较简单，修改为正确的内存顺序即可。

- [https://rustsec.org/advisories/RUSTSEC-2022-0006.html](https://rustsec.org/advisories/RUSTSEC-2022-0006.html)
- [https://github.com/Amanieu/thread_local-rs/issues/33](https://github.com/Amanieu/thread_local-rs/issues/33)

## RUSTSEC-2022-0016： `wasmtime` 的 `externref` 在启用 `epoch` 中断时会导致 UAF

epoch 中断会导致 wasmtime 在执行 GC 时错误回收还在使用的内存，从而导致 UAF。

- [https://github.com/bytecodealliance/wasmtime/security/advisories/GHSA-gwc9-348x-qwv2](https://github.com/bytecodealliance/wasmtime/security/advisories/GHSA-gwc9-348x-qwv2)
- [https://rustsec.org/advisories/RUSTSEC-2022-0016.html](https://rustsec.org/advisories/RUSTSEC-2022-0016.html)