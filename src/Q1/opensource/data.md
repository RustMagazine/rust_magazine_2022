# 开源观察 ｜ 数据处理

---


## Skytable: 高性能、安全可靠的实时 NoSQL 数据库

Skytable 致力于提供最好的 键值对(key/value) 存储、文档(document)存储和列式(columnar)数据库。Skytable 以前称为 TerrabaseDB（然后是 Skybase），也被社区昵称为“STable”、“Sky”和“SDB”。

Skytable 目前正在开发中，但可以用作高性能、安全和持久的键值存储。

- [路线图](https://github.com/skytable/skytable/issues/203)
- [https://skytable.io/](https://skytable.io/)
- [文档](https://docs.skytable.io/)
- [https://github.com/skytable/skytable](https://github.com/skytable/skytable)


## roaring-rs: 更好的压缩位集（bitset）

[相关论文： 使用 Roaring bitmap 获得更好的位图性能](https://arxiv.org/pdf/1402.6407v4.pdf) 中描述到：

> 位图索引常用于数据库和搜索引擎。通过利用位级并行性，它们可以显着加速查询。但是，它们可以使用大量内存。因此我们可能更喜欢压缩位图索引。在 Oracle 的引领下，位图通常使用运行长度编码 (RLE) 进行压缩。在这项工作中，我们引入了一种新形式的压缩位图，称为 Roaring，它使用打包数组而不是 RLE 进行压缩。我们将其与两种基于 RLE 的高性能位图编码技术进行比较：WAH（字对齐混合压缩方案）和 Concise（压缩“n”可组合整数集）。在合成数据和真实数据上，我们发现 Roaring 位图通常压缩得更好。
> 
> Roaring 位图数据结构的 Rust 移植版本。位图（或位集）是一个二进制数组，我们可以将其视为整数集的有效且紧凑的表示。例如，集合和可能以二进制形式存储为 `10011000` 和`10110000`。我们可以使用位图（例如，在我们的例子中为`10111000`和`10010000`）上的按位运算（`OR`、`AND`）来计算两个这样的对应列表之间的并集或交集。
> 
> Roaring 将位图集条目作为 `32` 位整数存储在简洁的两级索引中。与两种有竞争力的位图压缩方案 WAH 和 Concise 相比，Roaring 使用的内存更少，速度更快。

Roaring BitMap 简称为RBM，其实现思路：

将32位无符号整数按照高16位分桶，即最多可能有`2^16=65536`个桶，论文内称为container。存储数据时，按照数据的高16位找到container（找不到就会新建一个），再将低16位放入container中。也就是说，一个RBM就是很多container的集合。

> 更多内容参考： https://www.jianshu.com/p/818ac4e90daf

- [roaring-rs](https://github.com/RoaringBitmap/roaring-rs)

## Quickwit 0.2 为 ClickHouse 和 Kafka 带来全文搜索

Quickwit 号称用于日志管理的云原生和高性价比搜索引擎。Quickwit 是为日志构建的下一代搜索和分析引擎。它是 Elasticsearch 的一种高度可靠且具有成本效益的替代方案。

当前 0.2 版本发布，将支持 ClickHouse 和 Kafka 的全文搜索，同时新版本主要介绍以下功能：

- 准确一次性提取 Kafka 原生数据
- 搜索流 API
- PostgreSQL 元存储
- 标签修剪
- 适当的索引管道

- [https://github.com/quickwit-oss/quickwit](https://github.com/quickwit-oss/quickwit)
- [https://quickwit.io/blog/quickwit-0.2/](https://quickwit.io/blog/quickwit-0.2/)

## Mako: 机器学习的数据处理库

Mako是一个数据处理库，主要用于机器学习。它提供了高效的 pipe 来构建有向非循环数据流图，并提供了一个 DataLoader 来在单独的线程中运行该图。它还提供了通用的标记器和批处理工具来处理文本数据。

[https://github.com/Sidekick-AI/mako](https://github.com/Sidekick-AI/mako)

## Arrow2 发布 v0.9

Arrow2 是 Apache Arrow 的 Rust 实现，是继 C++ 实现之后，Arrow 功能最完整的实现。v0.9 的主要更新：

由 std Vec 支持，从而使其成为：

- 与 Rust 生态的其它的零拷贝
- 更少的 unsafe
- 更符合人体工程学
- 编译速度更快
- 相同的性能
- 支持同步和异步读取和写入 Apache Avro

flatbuffers 依赖被 planus 取代，这是 Rust 中 flatbuffers 规范的重新实现

安全性改进和常规维护

完整的更改列表：[https://github.com/jorgecarleitao/arrow2/releases/tag/v0.9.0](https://github.com/jorgecarleitao/arrow2/releases/tag/v0.9.0)

Apache Datafusion 正在考虑采用它作为其后端，请参阅 [https://github.com/apache/arrow-datafusion/issues/1532](https://github.com/apache/arrow-datafusion/issues/1532)

## Databend 发布0.7版本，部署更容易，查询更迅速

- 共享存储（弹性）
- 存储计算分离（弹性）：存算分离后，增加计算节点时不需要做数据迁移，可以达到秒级增加计算资源的效果。算力的扩张很快。
- 计算资源可以根据不同的业务做划分（弹性）：例如下图中的node4可以作为数据导入专用节点，node3作为BI计算专用节点。
- 资源扩张秒级弹性（弹性）：这样的架构计算节点异构化，资源分配粒度更细，资源控制更加灵活。

Blog: [https://databend.rs/blog/databend-0-7-0-release](https://databend.rs/blog/databend-0-7-0-release)

## PancakeDB 的分享

- [https://rustacean-station.org/episode/059-martin-loncaric/](https://rustacean-station.org/episode/059-martin-loncaric/)
- [https://pancakedb.com/](https://pancakedb.com/)
- [https://github.com/pancake-db/pancake-db](https://github.com/pancake-db/pancake-db)