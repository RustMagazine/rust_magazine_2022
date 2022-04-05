# 社区热点

---

## 俄乌战争开启，Rust 官方博客向乌克兰报以人道主义关怀

Rust 官方在发布 1.59 稳定版的博客中，写道：

> Today's release falls on the day in which the world's attention is captured by the sudden invasion of Ukraine by Putin's forces. Before going into the details of the new Rust release, we'd like to state that we stand in solidarity with the people of Ukraine and express our support for all people affected by this conflict.

中文翻译：

> 今天的发布正值普京的部队突然入侵乌克兰，吸引了全世界的注意力。在详细介绍新的Rust版本之前，我们要声明，我们声援乌克兰人民，并对所有受这场冲突影响的人表示支持。

因为国内外政治风向不同，这件事引起国内社区的热议，包括其他技术社区，尤其是前端社区，甚至还出现了去 react 官网留言辱骂的行为。

个人认为没有必要做出这么极端的行为。 Rust 官方在很早之前就说过，技术是离不开政治的。

那么，我们应该如何看待 Rust 官方这种行为呢？ 

1. 从人道主义角度来说，支持乌克兰人民是必须的。但我们不仅仅支持乌克兰人民，我们也支持世界上任何一个受战争迫害国家的人民。
2. Rust 官方有官方自己的立场，我们也无权干涉。
3. 我们可以不在技术社区谈论政治，但作为世界公民有义务关注政治形势，因为我们每个人都不可能独善其身。
4. 虽然技术离不开政治，但是在开源社区，至少还有开源行为规范，我们应该尽力遵守行为规范，共同维护和谐交流的社区。
5. 无论是技术上还是政治觉悟上，要努力提升自己，不随大流，不被当枪使，不要人云亦云，有自己的独立思考。

[https://blog.rust-lang.org/2022/02/24/Rust-1.59.0.html](https://blog.rust-lang.org/2022/02/24/Rust-1.59.0.html)

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

## AWS: Rust与可持续性

亚马逊官方博客上发表了“Sustainability with Rust”一文，通过研究和比较表明，Rust 在可持续性方面优于其他语言。该文章随后被ZDNet 报道。在 ZDNet 报道之后，Go 技术负责人 Russ Cox 发布了 14 条推文，称这篇文章严重误导了 Go 语言。

在亚马逊，Rust 已成为大规模构建基础设施的关键。2019 年，亚马逊成为 Rust 项目的赞助商。2020 年开始招募 Rust 维护者和贡献者，并与谷歌、华为、微软和 Mozilla 联手组建 Rust 基金会。

这篇文章引发的争议主要是以下几个方面：

1. AWS引用的2017年前的研究存在偏见

2017 年的一项研究数据被用来证明 Rust 是一种节能编程语言。该研究侧重于测试 27 种编程语言的 10 个基准问题，并测量执行时间、能耗和内存使用峰值。这是引起争议的文章的第一部分。Russ Cox 认为其中使用 Go 1.6（2016 年 2 月）进行测试。“那已经是永远的过去式了。”最重要的是，这个“真正有趣的研究”假设计算机语言基准游戏是个可比较的程序来源，但了解这个网站的话就知道这完全不正确。

Cox 说 “几乎每个 C 程序都是有效的 C++ 程序，所以 C++ 不会输，尤其是没那么糟糕！” （此话存疑啊）

2. 对比不公平

Cox 指出， Discord 的原帖中展示了一张 Go 服务器和等效 Rust 服务器的图表。Rust 具有更可预测的性能，并避免了像 Go 中的延迟峰值，但它们的性能大致相当。

在亚马逊的帖子里，在 Rust 和 Go 图表标出了“ms”与“µs”的时间刻度对比。但该对比的前提是，Rust 服务器进行了重大重写，并使用了新的数据结构和更多内存。

“这要么是完全没有理解 Discord 的帖子，要么就是公然说谎。”Cox 认为，这种对比方法在诚实、公平的情况下是很好的表达方式，“但 AWS 的帖子并非如此。”
 
不过，Cox 指出 Discord 的帖子是公平的。“它将 Go 服务器和 Rust 服务器进行了对比，并在文章后面单独绘制了 Rust 服务器在使用重写的数据结构和额外内存后的变化图，而 AWS 的帖子曲解了这一点。”

“在我看来，与其阅读那些将 Go 与 Rust 视为零和游戏的文章，我更愿意关注那些将 Go 和 Rust 视为相互补充、能很好协同工作的文章。”Cox 在最后说道。

编程语言之争从来没有停过，每种语言都有大批的拥护者，甚至存在“鄙视链”的说法，每年的编程语言榜单也是大家竞相关注的热点。但编程语言之间并非水火不容，开发者需要学会选择适合自己的语言。

- [https://aws.amazon.com/blogs/opensource/sustainability-with-rust/](https://aws.amazon.com/blogs/opensource/sustainability-with-rust/)
- [https://www.infoq.cn/article/svgbvixxhcdf5mqvk3tv](https://www.infoq.cn/article/svgbvixxhcdf5mqvk3tv)


## gitoxide 可在一秒内检出（checkout）Linux 内核代码

gitoxide 是 Rust 实现的一个 Git 客户端。

测试结果是 v5.16 版在 `~874ms` 内检出 Linux 内核，基于 Mac M1 pro (10 核) 测试。

[https://github.com/Byron/gitoxide/discussions/349](https://github.com/Byron/gitoxide/discussions/349)

## Fermyon 推出WebAssembly 框架 spin 

可以配合TO-D杂志第二季第一篇文章来看，能让人深刻理解这些开源项目背后的动机，这比单看他们的开源项目可有趣的多。话说回来，我觉得 Fermyon 的成立，应该是 deislabs 团队这这几年尝试的过程中看到了一些新的风口，他们的项目应该多多关注。

> 《TO-D 杂志》第二季第一篇文章正式发布，这篇文章是关于微软 DeisLabs 的故事，欢迎大家阅读转发 [https://2d2d.io/s2/deislabs/](https://2d2d.io/s2/deislabs/) ，Deis Labs 开发了不少非常前沿的 WebAssembly 和 Rust 项目，现在他们团队中九人出来创业，成立新公司 Fermyon。

> 关于 Fermyon 名称由来的猜想：这个单词比较贴近费米子（fermion）。所以查了下相关概念，得出一些推论。费米子和玻色子都是宇宙基本粒子，都有自旋属性。但是费米子喜欢独来独往（泡利不相容原理，只适合费米子。 在任何量子系统中，两个费米子无法占据同一个量子态），而玻色子喜欢共享。用费米子来对应wasm的沙盒隔离性。而他们的主打项目名叫 spin 。他们这么费劲起这个名字，我想到一个原因就是避免别人抢注商标。因为之前发生过 Wasmer 抢注商标的事（Wasmer 抢注了WasmEdge的商标，甚至还把 WebAssembly 抢注为自己公司的商标）。这也算是一种商标保护。


- [https://github.com/fermyon/spin](https://github.com/fermyon/spin)
- [https://www.fermyon.com/blog/introducing-spin](https://www.fermyon.com/blog/introducing-spin)

## 漏洞赏金平台 Hackerone 接受 Rust 的漏洞报告

Rust 官方博客之前报告的 `std::fs::remove_dir_all` 函数漏洞，价值 4000 刀。

[https://hackerone.com/reports/1520931](https://hackerone.com/reports/1520931)

## AUTOSAR 宣布成立新的汽车软件Rust编程语言工作组

> 在 Rust 社区中广为人知的 Christof Petig 已同意接任（Rust工作组）发言人的角色。他拥有 25 年的C++经验，在此期间已成为 Rust 爱好者。Christof 在最初的讨论中总结道，"用 Rust 编写的代码经过检查，以确保内存安全且不受数据抢占的影响。同时，由于所有可能的检查都是在编译时检查的，因此运行时开销可以忽略不计。这意味着 Rust 的性能可与C++相媲美。”由于汽车领域的其他标准化机构（如Khronos或SAE）符合此类评估，因此嵌入式软件的重点是共同努力实现有效的标准化。

> 所有这些对于 AUTOSAR开发合作伙伴关系及其社区来说并不陌生。AUTOSAR在C++14编码指南方面经验丰富，希望保持其创新传统，并成为以功能安全和汽车网络安全为重点的标准化机构。也因此最终决定，在功能安全工作组（WG-SAF）内成立一个小组并调查如何在AUTOSAR自适应平台中将Rust运用起来。该小组将于2022年4月正式启动，并计划制作两份文件。其中一份文件将提供有关如何在 AUTOSAR Adaptive Platform 项目环境中使用 Rust 的指导。另一个文档将提出 Rust 上的编码指南。

这篇消息目前被官方网站删除，但是在 GitHub 相关issues中，Christof Petig  透露会在之后修改文章以后重新放出来。工作组的事情正在继续中。

关于如何看待 Autosar 成立 Rust 工作组这件事，可以看知乎用户 @萧猛 的一个答案：[https://www.zhihu.com/question/522187444/answer/2393987811](https://www.zhihu.com/question/522187444/answer/2393987811)

## Areweyet 列表更新

有关 Rust 目前在各个领域的状态的汇总。

[https://wiki.mozilla.org/Areweyet](https://wiki.mozilla.org/Areweyet)

## 《Rust 编码规范》更新到 V0.3 版本

《Rust 编码规范》更新到 V0.3 ， 欢迎大家持续评审、补充和参考

[https://rust-coding-guidelines.github.io/rust-coding-guidelines-zh/](https://rust-coding-guidelines.github.io/rust-coding-guidelines-zh/)

## Alacritty六周年生日快乐!

```HTML
<video src="https://assets.repography.com/file/repography-assets/blog/happy-birthday-alacritty/alacritty-structure.mp4" controls="controls" width="500" height="300"></video>
```

六年前的2月21号(2016年2月21号)，Joe Wilm提交了第一个commit到[Alacritty](https://github.com/alacritty/alacritty)的Git仓库：

```
commit 621776cd94890936b24f3abb8b7ec1f36dad9150
Author: Joe Wilm <joe@jwilm.com>
Date:   Sun Feb 21 08:15:41 2016 -0800

    Initialize new cargo binary project
```

今天是Alacritty的6周岁生日，这篇文章使用[Repography](https://repography.com/) 分析和回顾了Alacritty的repo历史

[https://repography.com/blog/happy-birthday-alacritty](https://repography.com/blog/happy-birthday-alacritty)

## Google对25名Rust开源贡献者做出奖励

Rust 是系统级编程语言，重点关注内存安全。Google 在一些项目中使用了 Rust：包括 Android、Fuchsia 和 ICU4X；并一直参与在 Linux 内核中评估 Rust 的工作。Google 也是 Rust 基金会的创始成员。

[https://opensource.googleblog.com/2022/03/Rewarding-Rust-contributors-with-Google-Open-Source-Peer-Bonuses.html](https://opensource.googleblog.com/2022/03/Rewarding-Rust-contributors-with-Google-Open-Source-Peer-Bonuses.html)

## 利用 gitoxide 可以在一秒内检出 Linux 内核

gitoxide 是用纯 Rust 实现的一个精简、快速和安全的 Git 实现。此外，它还以各种小 crates 的形式提供了一套易于使用的API，方便我们轻松实现自己的工具。

“checkout” 代表检出文件。如果没有索引，git 将从源码树中创建一个索引，并使用内存中的索引来检出文件。

gix 是由 gitoxide 驱动的二进制程序，它比 git 的多线程 checkout 速度快 1.8 倍。这意味着在一台 10 核 MacBook + tmpfs 的组合上，可以在 ~874ms 内检出 Linux 内核 v5.16 。

- [https://github.com/Byron/gitoxide](https://github.com/Byron/gitoxide)
- [https://github.com/Byron/gitoxide/discussions/349](https://github.com/Byron/gitoxide/discussions/349)