# 数据处理

---

## 数据库


### Skytable: 高性能、安全可靠的实时 NoSQL 数据库

Skytable 致力于提供最好的 键值对(key/value) 存储、文档(document)存储和列式(columnar)数据库。Skytable 以前称为 TerrabaseDB（然后是 Skybase），也被社区昵称为“STable”、“Sky”和“SDB”。

Skytable 目前正在开发中，但可以用作高性能、安全和持久的键值存储。

- [路线图](https://github.com/skytable/skytable/issues/203)
- [https://skytable.io/](https://skytable.io/)
- [文档](https://docs.skytable.io/)
- [https://github.com/skytable/skytable](https://github.com/skytable/skytable)


## 基础工具


### roaring-rs: 更好的压缩位集（bitset）

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

