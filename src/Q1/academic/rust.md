# 学术动态 ｜ Rust


## 来自于 MIT-PDOS 的研究型项目: terra

一个用 Rust 编写的大型地形渲染库，基于 wgpu。

> [https://github.com/mit-pdos](https://github.com/mit-pdos)


[https://github.com/fintelia/terra](https://github.com/fintelia/terra)

## 清华大学： 2022 全国大学生操作系统比赛 

[https://github.com/oscomp](https://github.com/oscomp)

## 论文 ｜ 删除（Yanked） Rust crate 注册仓库中已发布包的实证研究

> Rust 的软件包管理器 Cargo 提供了一个 yank 机制来支持发布级别的弃用，这可以防止包依赖于 yanked 的版本。大多数先前的研究都集中在代码级（即弃用的 API）和包级弃用（即弃用的包）上。然而，很少有研究关注发布级别的弃用。在这项研究中，调查了 yank 机制的使用频率和方式、其使用背后的基本原理以及在 Cargo 生态系统中采用 yank 发布。我们的研究表明，Cargo 中 9.6% 的包裹至少有一个 yanked release，并且 yanked release 的比例从 2014 年到 2020 年不断增加。包所有者出于其他原因而不是撤回有缺陷的 release，例如修复不遵循语义版本控制的版本或指示已删除或替换包的版本。此外，我们发现 46% 的包直接采用了至少一个 yanked 版本，并且 yanked 版本通过依赖网络传播，这导致生态系统中 1.4% 的版本具有未解决的依赖关系。

[https://arxiv.org/abs/2201.11821](https://arxiv.org/abs/2201.11821)


## 论文 ｜ DuVisor：通过委托虚拟化的用户级管理程序

当今的主流虚拟化系统由两个协作组件组成：访问虚拟化硬件的内核驻留驱动程序和提供 VM 管理和 I/O 虚拟化的用户级帮助进程。然而，这种虚拟化架构在安全性（大的攻击面）和性能方面都存在内在问题。虽然通过将函数卸载到用户模式来尝试最小化内核驻留驱动程序的工作很长，但他们面临着安全性和性能之间的基本权衡：更多的卸载可能会减少内核攻击面，但会增加运行时环之间的交叉辅助进程和驱动程序，因此性能成本更高。

本文探索了一种称为委托虚拟化的新设计，它将控制平面（内核驱动程序）与数据平面（辅助进程）完全分离，从而消除了内核驱动程序的运行时干预。一旦内核驱动程序完成初始化，由此产生的用户级管理程序，称为 DuVisor，可以处理所有 VM 操作，而不会陷入内核。DuVisor 使用新的委托虚拟化扩展来改进现有的硬件虚拟化支持，以直接处理 VM 退出、配置虚拟化寄存器、管理第 2 阶段页表和用户模式下的虚拟设备。目前已经在开源 RISC-V CPU 上实现了硬件扩展，并在硬件之上构建了一个基于 Rust 的管理程序。对 FireSim 的评估表明，DuVisor 的性能比 KVM 高 47 倍。

[https://arxiv.org/pdf/2201.09652.pdf](https://arxiv.org/pdf/2201.09652.pdf)

## 论文 ｜ Minotaur：多资源区块链共识

基于资源的共识是无需许可的分布式账本系统的支柱。此类协议的安全性从根本上依赖于系统中积极参与的资源水平。各种不同的资源（以及相关的证明协议，在文献中有时称为 PoX）提出了一个基本问题，即是否可以串联利用其中的许多资源并构建多资源共识协议。组合不同资源的挑战在于实现它们之间的可替代性，从某种意义上说，只要所有资源的累积对抗性力量有限，安全性就会保持不变。

在这项工作中，论文提出了Minotaur，这是一种多资源区块链共识协议，它结合了工作量证明 (PoW) 和权益证明 (PoS)，并证明了它的最佳可替代性。他们用 Rust 实现了客户端。

- [https://arxiv.org/pdf/2201.11780.pdf](https://arxiv.org/pdf/2201.11780.pdf)
- [https://anonymous.4open.science/r/Minotaur-D037/README.md](https://anonymous.4open.science/r/Minotaur-D037/README.md)

## 论文 ｜ 使用多方会话类型(Multiparty Session Types)的 Rust 中的无死锁异步消息重新排序

Rumpsteak 是一个 Rust 框架，用于安全有效地实现 消息传递 异步程序。它使用多方会话类型来静态保证不存在通信错误，例如死锁和异步子类型化，以优化通信。

- [https://arxiv.org/abs/2112.12693](https://arxiv.org/abs/2112.12693)
- [https://github.com/zakcutner/rumpsteak](https://github.com/zakcutner/rumpsteak)

## 论文 ｜ Zef：低延迟、可扩展、私人支付

这是第一个支持任意规模匿名数字硬币支付的拜占庭容错 (BFT) 协议。Zef 遵循 FastPay 的通信和安全模型。

[https://arxiv.org/pdf/2201.05671.pdf](https://arxiv.org/pdf/2201.05671.pdf)