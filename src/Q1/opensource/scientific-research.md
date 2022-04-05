# 科学艺术

---

## 科学计算

### Slas v0.2.0 —— Rust静态线性代数系统

提供静态分配的向量、矩阵和张量类型，高效的实现了blas/blis接口，默认情况下使用写时复制行为（又名cow）。

与v0.1.1版本相比，新版本的Slas有很多突破性的变化和功能，包括：

- 模块化后端；
- 更好的性能；
- 支持矩阵和张量（张量仍然做不了多少）；
- 向量支持可选的COW行为；
- 更好的基准测试和文档；

[https://github.com/unic0rn9k/slas](https://github.com/unic0rn9k/slas)


### Panther 一个用 Rust 和 Numpy C API 编写的高性能 python 技术分析库

平均而言，Panther 对一些常见指标的计算比使用 Pandas（行业标准方法）快 9 倍。

[https://github.com/gregyjames/Panther](https://github.com/gregyjames/Panther)

## savage: Rust 编写的全新的计算机代数系统

Savage是一个全新的计算机代数系统，用纯Rust从头编写而成。它的目标依次是正确性、简单性和可用性。整个系统编译成一个单独的、没有依赖关系的可执行文件，大小只有2.5 MB。虽然这个可执行文件将随着Savage的成熟而增长，但他们的计划是最终交付一个有用的计算机代数系统，其大小不超过5 MB。

[https://github.com/p-e-w/savage](https://github.com/p-e-w/savage)

---

## 生物信息学

> 什么是生物信息学？
>
> 生物信息学是信息与系统科学和生命科学高度交叉的前沿学科，是自动化学科群中的重要部分，包括计算生物学、系统生物学与合成生物学等方向。生物信息学涉及多个学科领域，信息、控制与系统的理论、方法和技术在其中发挥着重要作用，同时，它也把控制科学与工程的研究对象从机械、电子、物理、化学等系统扩展到了以分子和细胞为基本元件的生命系统。
>
> - 生物信息学中计算机和大数据各扮演什么样的角色？ https://www.zhihu.com/question/21476539
> - 生物信息学学科发展报告 https://zhuanlan.zhihu.com/p/47508742

### flopp: 用于基因定相的Rust实现的软件

用于从长读长测序中对多倍体生物进行单个单倍体定相。定相（Phasing）包括将每个染色体的父本母本遗传的拷贝分离成单倍型，以获得遗传变异的完整图谱。

定相在研究遗传变异领域非常重要，定相算法本身基本都是计算密集型的，做起来也比较耗时间，有时即使是在超算集群中也得跑很长时间。所以，Rust 语言应该是非常适合该场景。

flopp 非常快，多线程，并且完全用 rust 编程语言编写。与其他多倍体单倍体定相算法相比，flopp 提供了一个数量级的加速和更好的准确性。


> 什么是 定相（Phasing）？
>
> Phasing，或者说Genotype Phasing，它的中文名有很多：基因定相、基因分型、单倍体分型、单倍体构建等在不同的语境下都有人说过。但不管如何，所谓Phasing就是要把一个二倍体（甚至是多倍体）基因组上的等位基因（或者杂合位点），按照其亲本正确地定位到父亲或者母亲的染色体上，最终使得所有来自同一个亲本的等位基因都能够排列在同一条染色体里面。
>
>现在流行的NGS测序技术，都是把序列打乱混在一起测序的，测完之后，我们是无法直接区分这些序列中哪一个是父源，哪一个是母源的。我们通常都只是检测出基因组上有哪些变异，以及这些变异的碱基组成（纯合、杂合），也就是平时所说的基因型（Genotype）。只有经过Phasing，才能够实现这个区分。
>
> 为什么要Phasing ?
>
> 因为Phasing很重要。Phasing的重要性可以分为两个方面。
>
> 一方面, Phasing与遗传变异的功能诠释密切相关。这体现在遗传咨询师或者科学家需了解基因突变的相位后, 才能更好地判断基因突变是否会产生临床表型。比如在一个基因上发生多个Loss of function variants(LOF)，通常当这些变异出于不同的单倍型时(这称为trans-configuration)，即两个拷贝的姐妹基因都发生了变异, 才会导致基因表达计量(Gene expression dosage)的错误且产生危害。而当它们出于同一个单倍型时(这称为cis-configuration)，因为还有一个正常拷贝的基因（作为备胎）, 基因表达很可能不会发生改变也不会产生危害。
>
> 另一方面, Phasing在遗传学研究中也有诸多应用，具体如下：
> 
> - 第一、人群Phasing后形成的单倍型参考序列集(Reference panel)是基因型推断（Imputation）必须的数据材料。而基因型推断（Imputation）是基因型-表型关联分析研究中必不可少的环节。高质量的Reference Panel能提升关联分析的统计功效；
> - 第二、除了Reference Panel的制造需要使用Phasing技术之外，对被研究的对象进行预先Phasing(Pre-phasing)也可以极大地提高基因型推断（Imputation）的准确性；
> - 第三、使用多个位点组成的Haplotype，而不是简单的单位点基因型, 可实现群体遗传历史的推断；
> - 第四、可通过Phased后的家系人群单倍型序列，估算染色体重组率、重组热点等重要遗传参数；
> - 第五、Phasing可用于探测频发突变、选择信号以及基因表达的顺势调控。
>
> Phasing说起来容易，做起来却很难
> 
> 虽然Phasing理解起来并不难，但实现起来却不容易，即使在理论上也是如此。这需要相关的统计学和计算机算法技术，求解的过程往往还是一个NP问题。目前通常采用马尔科夫链蒙特卡洛算法来完成，因此，Phasing算法本身基本都是计算密集型的，做起来也比较耗时间，有时即使是在超算集群中也得跑很长时间。
>
> 详细：[https://zhuanlan.zhihu.com/p/36289359](https://zhuanlan.zhihu.com/p/36289359)

[https://github.com/bluenote-1577/flopp](https://github.com/bluenote-1577/flopp)

### alevin-fry: 高效灵活的单细胞测序数据处理工具

alevin-fry是一套工具，用于快速、准确和节省内存地处理单细胞和单核测序数据。

该套工具是马里兰大学[计算生物学和网络进化实验室](https://combine-lab.github.io/)开源的。该实验室的 GitHub 组织下还有其他的计算生物学相关的 Rust 库。

> 背景
> 单细胞测序技术将伴随着高通量技术给临床以及学术界带来新的革命。理解单细胞水平的遗传信息异质性，为我们理解遗传、发育、疾病机理打开了新的大门。
> 参考： https://www.plob.org/article/22832.html

[https://github.com/COMBINE-lab/alevin-fry](https://github.com/COMBINE-lab/alevin-fry)

## 其他

## 用 Rust 和 WASM 重建世界上最短的数学论文

1769年，欧拉提出了欧拉猜想（sum of powers）。1966 年，L. J. Lander 和 T. R. Parkin 用他们使用 CDC 6600 大型计算机发现的反例反驳了这个猜想。 众所周知，他们的论文只包含两句话。

2022年，你用在手机上的浏览器，就可在2秒左右找到他们的反例，以及更多反例。

可以通过该网页 [https://carlkcarlk.github.io/shortestpaper/](https://carlkcarlk.github.io/shortestpaper/) 亲自尝试。 该页面使用 Rust 和 WASM。


