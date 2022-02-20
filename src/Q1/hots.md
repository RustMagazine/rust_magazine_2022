# 社区热点

---

## AdaCore 和 Ferrous Systems 合作为 Rust 工具链 Ferrocene 认证各种安全标准

AdaCore 与 Rust 编程语言专家公司 Ferrous Systems 建立了战略合作伙伴关系，以进一步开发 Ferrous Systems 的 Ferrocene （二茂铁）Rust 工具链，以支持嵌入式任务和安全关键应用程序，并使其符合相关行业软件安全标准。

在工业应用中，人们对 Rust 的兴趣和使用越来越多，但对于汽车、航空航天和国防等安全关键市场，目前还没有经过安全认证的 Rust 工具链。Ferrocene 合作伙伴关系将支持希望将新兴的 Rust 语言技术集成到其安全关键应用程序中的客户的认证需求。

> 25 年来，AdaCore 通过投资新架构、语言和技术来支持安全和关键任务行业不断变化的需求。我们相信，在安全和安保关键领域都需要 Ada 和 Rust - AdaCore 产品管理和业务开发负责人 Quentin Ochem 如是说


对于 Ferrous Systems 而言，Ferrocene 是一个利用 Rust 的技术专长和与 Rust 社区的关系将 Rust 转变为任务和安全关键嵌入式软件开发的关键位置的机会。

对于 AdaCore 而言，通过与 Ferrocene 的开发合作，它补充了公司对 Ada 的长期承诺，并提供了将在安全认证的 Ada 工具链开发中获得的专业知识扩展到 Rust 社区的机会。

[https://www.adacore.com/press/adacore-joins-forces-ferrous-systems-support-rust](https://www.adacore.com/press/adacore-joins-forces-ferrous-systems-support-rust)

## 出现首个用 Rust 编写的勒索软件即服务(RaaS)

2021 年 12 月，研究人员发现了一种名为ALPHV（又名“ BlackCat ”）的新型勒索软件即服务（RaaS, ransomware-as-a-service），它被认为是第一个创建和使用以Rust编程语言编写的勒索软件的专业网络犯罪组织。

以下是一些关键要点： 

- 该组织正在积极招募前 REvil、BlackMatter 和 DarkSide 运营商 
- 自 2021 年 11 月以来活动增加 
- 丰厚的会员支出（高达 90%） 
- 基于 Rust 的勒索软件可执行文件（快速、跨平台、针对每个受害者进行大量定制） 
- 默认 AES 加密 
- 内置权限提升（UAC 绕过、Masquerade_PEB、CVE-2016-0099） 
- 可以通过 PsExec 传播到远程主机 
- 使用 VSS Admin 删除卷影副本 
- 停止 VMware ESXi 虚拟机并删除快照 

与许多其他勒索软件威胁不同，ALPHV 是使用 Rust 开发的，Rust 是一种以其快速性能和跨平台功能而闻名的编程语言，导致在 2021 年 12 月和 2022 年 1 月期间观察到 Linux 和 Windows 变体。  

尽管许多人认为 ALPHV 可能是第一个使用这种语言的“野外”勒索软件威胁，但Rust 勒索软件的[概念验证已于 2020 年 6 月在 GitHub 上](https://github.com/cdong1012/Rust-Ransomware)发布，尽管没有任何迹象表明这两者有任何关系有关的。 

Catalin Cimpanu为The Record写道，更多恶意软件转向Rust的一个担忧是，与 C 和 C++ 相比，它被认为是一种更安全的编程语言。结果呢？安全防御者一直在寻找许多勒索软件中的编码弱点，如果更多人开始转向 Rust，那么找到这些弱点可能会变得更加困难。

最近观察到的 ALPHV 样本使用了 2021 年 11 月 4 日才发布的[Zeroize](https://crates.io/crates/zeroize)版本 1.4.3，同时还使用了被 2021 年 11 月 16 日和 17 日发布的版本所取代的公钥加密版本。  

虽然使用的许多 Rust crate 都有些明显，例如使用命令行界面和加密库，但使用 Zeroize（一个从内存中安全清除机密的库）似乎是故意尝试防止加密机密被泄露从受感染的主机中恢复。 

[https://krebsonsecurity.com/2022/01/who-wrote-the-alphv-blackcat-ransomware-strain/](https://krebsonsecurity.com/2022/01/who-wrote-the-alphv-blackcat-ransomware-strain/)

## AWS: Rust 的可持续性


[https://aws.amazon.com/blogs/opensource/sustainability-with-rust/](https://aws.amazon.com/blogs/opensource/sustainability-with-rust/)