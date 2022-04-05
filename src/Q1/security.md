# 安全动态

记录 Rust 生态中的安全问题，来自 [RustSec](https://rustsec.org/advisories/) 和 [GitHub](https://github.com/advisories?query=ecosystem%3Arust) 公告。

---

分类:

- [dos](./security/dos.md)
- [memory](./security/memory.md)
- [unsound](./security/unsound.md)
- [others](./security/others.md)

## CVE-2022-21658: Rust 标准库中存在竞争条件可以允许攻击者删除他们无权删除的文件

Rust 安全响应工作组被通知`std::fs::remove_dir_all` 标准库函数容易受到启用符号链接跟踪的竞争条件 (CWE-363) 的影响。攻击者可以利用此安全问题诱使特权程序删除攻击者无法访问或删除的文件和目录。

[https://blog.rust-lang.org/2022/01/20/cve-2022-21658.html](https://blog.rust-lang.org/2022/01/20/cve-2022-21658.html)

## CVE-2022-24713: `regex` crate 存在 DoS 漏洞

因为 regex crate 没有正确限制它解析的正则表达式 (regex) 的复杂性，攻击者可以使用此安全问题执行拒绝服务，方法是向接受不受信任的正则表达式的服务发送特制的正则表达式。使用受信任的正则表达式解析不受信任的输入时，不存在已知漏洞。

1.5.4 之前或等于 1.5.4 的所有版本的regex crate 都会受到此问题的影响。修复包括从 regex1.5.5 开始。

[https://blog.rust-lang.org/2022/03/08/cve-2022-24713.html](https://blog.rust-lang.org/2022/03/08/cve-2022-24713.html)
