# Rust Magazine 2022 第一季

作者： 张汉东

---

## 发刊通告

Rust Magazine 于 2021 年 1 月创刊，其初衷是想成为国内 Rust 用户分享自己学习和使用 Rust 的经验平台。然而事与愿违，虽然 2021 年期刊完整输出了，也受不少 Rust 社区朋友的喜欢，但是其实它并没有达成既定的目标。

所以，2022年 《Rust Magazine》 将重新定位：**Rust 生态观察者**。

《Rust Magazine》将按季度发布，分类记录 Rust 生态中的一举一动。Rust 用户可以在这里找到不错的学习资源、项目实践的灵感、有趣的开源项目，并且可以及时了解 Rust 官方动态、社区事件等。

当然，也继续接受投稿。投稿更加简单，只需要找到每个季度的 `contribute` 目录直接发 PR 即可，内容只限于 Rust，只要你觉得你写的内容对大家有帮助，就值得分享。

## Rust 生态第一季回顾

2022 年，在整个世界都还在意犹未尽地总结 2021 年有什么收获且对新的一年做出展望的时候，俄乌战争爆发了。这场战争，不仅仅波及了俄乌两国的人民，还聚焦了整个世界的关注，包括我们 Rust 社区。

Rust 官方在发布 1.59 稳定版的博客中，写道：

> Today's release falls on the day in which the world's attention is captured by the sudden invasion of Ukraine by Putin's forces. Before going into the details of the new Rust release, we'd like to state that we stand in solidarity with the people of Ukraine and express our support for all people affected by this conflict.

中文翻译：

> 今天的发布正值普京的部队突然入侵乌克兰，吸引了全世界的注意力。在详细介绍新的Rust版本之前，我们要声明，我们声援乌克兰人民，并对所有受这场冲突影响的人表示支持。

因为国内外政治风向不同，这件事引起国内社区的热议，包括其他技术社区，尤其是前端社区，甚至还出现了去 react 官网留言辱骂的行为。个人认为没有必要做出这么极端的行为。 Rust 官方在很早之前就说过，技术是离不开政治的。

那么，我们应该如何看待 Rust 官方这种行为呢？ 

1. 从人道主义角度来说，支持乌克兰人民是必须的。但我们不仅仅支持乌克兰人民，我们也支持世界上任何一个受战争迫害国家的人民。
2. Rust 官方有官方自己的立场，我们也无权干涉。
3. 我们可以不在技术社区谈论政治，但作为世界公民有义务关注政治形势，因为我们每个人都不可能独善其身。
4. 虽然技术离不开政治，但是在开源社区，至少还有开源行为规范，我们应该尽力遵守行为规范，共同维护和谐交流的社区。
5. 无论是技术上还是政治觉悟上，要努力提升自己，不随大流，不被当枪使，不要人云亦云，有自己的独立思考。

也因为此次事件，很多人关心，是不是 Rust 语言也会受到影响呢？

其实没必要惊慌，真实的开源世界是很复杂的。GitHub 作为美国的公司，肯定要遵循美国的法律。Rust 在 GitHub 上开源，但 Rust 是全球的，代码是完全可以 fork 到其他任何地方的。

> 美国向这个世界贡献了那么多好的技术、理念、文化，因此才能成为世界的领导者。而中国如果想要崛起，想要成为多极世界中的一极，同样需要贡献足够多的“礼物”。 - 开源社庄表伟 [《面对真实的开源世界，丢掉幻想，但也不必恐慌》](https://chowdera.com/2022/04/202204010912259347.html)

所以，如果我们需要 Rust，那么我们就需要给 Rust 做更多贡献。

在 2022 年第一季度，Rust 生态整体来说平稳发展。但也有一些值得关注的点：

- Rust 语言团队 2024 Roadmap 已经发布。Rust 语言团队将聚焦于以多种不同的方式扩展授权，让越来越多的人受益，也就是说，让 Rust 广泛应用，将专注于三个主要主题来完成这个目标：拉平学习曲线、扩展生态系统、扩展项目。
- WebAssembly 发展初露苗头。DeisLabs 团队中九人集体离职成立初创公司 Fermyon，并推出WebAssembly 框架 spin。DeisLabs 属于微软，他们的开源项目基本都是围绕 Rust 和 WebAssembly 在云原生的一些应用，有好几个 CNCF 项目。现在突然九人一起离职创建新公司，并专注于 WebAssembly，那证明他们一定看到了 WebAssembly 的广泛前景。
- Rust 生态中终端应用不断涌现出优秀应用。说明目前在终端应用领域，Rust 是非常成熟了。
- Rust 跨平台开发已凸显出了它的优势，尤其在 GUI 领域。
- Rust 在区块链领域的应用也非常值得关注。
- Rust 安全依然不容忽视，从  [RustSec](https://rustsec.org/advisories/) 和 [GitHub](https://github.com/advisories?query=ecosystem%3Arust) 公告中看出，Rust 中内存安全和线程安全漏洞依然多于其他漏洞。
- Rust 在生物信息科技领域也有好几个开源应用，比想象中要多，主要集中在基因测序方向上。
- 有人开始用 Rust 来重写经典软件了，比如 Fornjot 要创建下一代 Code-CAD 。
- 在学术领域，研究 Rust 和 WebAssembly 的论文越来越多了。


还有很多内容，希望大家可以从本刊提供的信息中受益！


## 资源

-  [Rust Magazine 2021 期刊](https://rustmagazine.github.io/rust_magazine_2021/)



