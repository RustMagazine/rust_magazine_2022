# Rust 安全参考 ｜ DoS 漏洞

## RUSTSEC-2022-0004： `rustc_serialize` 解析深度嵌套的 JSON 时栈溢出

该漏洞会导致 DoS（denial-of-service）风险。

```rust
// 触发漏洞示例代码
fn main() {
    let _ = rustc_serialize::json::Json::from_str(&"[0,[".repeat(10000));
}
```

推荐使用 serde 作为 rustc_serialize 的替代品。

[https://rustsec.org/advisories/RUSTSEC-2022-0004.html](https://rustsec.org/advisories/RUSTSEC-2022-0004.html)