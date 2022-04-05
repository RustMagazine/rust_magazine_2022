# 开源观察 ｜ 性能工具

## Rust Benchmarks 小技巧
为什么你的 Rust Benchmark 可能出现问题，如何正确使用标准库中的 std::hint::black_box 呢？ Gendignoux 在一篇文章中分享了他在深入挖掘Benchmark 出现的问题之后得出的结论。

[https://gendignoux.com/blog/2022/01/31/rust-benchmarks.html](https://gendignoux.com/blog/2022/01/31/rust-benchmarks.html)

## 字符串连接操作的基准测试

基于 Hendrik Sollich 在 4 年前的工作，作者添加了一些常用的函数和宏的基准结果：

```rust
0 ns/iter (+/- 0)        from_bytes
10 ns/iter (+/- 0)       concat_string_macro
10 ns/iter (+/- 0)       concat_strs_macro
10 ns/iter (+/- 0)       mut_string_with_capacity_push_str_char
10 ns/iter (+/- 0)       string_concat_macro
10 ns/iter (+/- 1)       mut_string_with_capacity_push_str
14 ns/iter (+/- 0)       concat_in_place_macro
19 ns/iter (+/- 10)      mut_string_with_too_much_capacity_push_str
22 ns/iter (+/- 0)       array_join
24 ns/iter (+/- 0)       array_concat
24 ns/iter (+/- 0)       array_join_long
24 ns/iter (+/- 0)       mut_string_push_str
27 ns/iter (+/- 0)       string_from_plus_op
27 ns/iter (+/- 0)       to_string_plus_op
29 ns/iter (+/- 0)       to_owned_plus_op
30 ns/iter (+/- 0)       collect_from_array_to_string
34 ns/iter (+/- 0)       collect_from_vec_to_string
39 ns/iter (+/- 0)       mut_string_with_too_little_capacity_push_str
43 ns/iter (+/- 1)       string_from_all
52 ns/iter (+/- 0)       format_macro
53 ns/iter (+/- 0)       format_macro_implicit_args
68 ns/iter (+/- 1)       mut_string_push_string
```

[https://github.com/hoodie/concatenation_benchmarks-rs](https://github.com/hoodie/concatenation_benchmarks-rs)
