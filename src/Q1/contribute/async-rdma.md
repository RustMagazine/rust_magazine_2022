# async-rdma:使高性能网络应用开发更简单

作者：王恒宇

> 王恒宇，中科院软件所基础软件实验室研究生。主要研究方向为软硬件融合，对物联网、操作系统、Serverless等方向感兴趣。DatenLord社区async-rdma项目贡献者之一，曾获嵌入式芯片与系统设计竞赛一等奖等多项国家级奖项，参与编写《openEuler操作系统》一书。

[async-rdma](https://github.com/datenlord/async-rdma)是由[DatenLord社区](https://github.com/datenlord)发起的Rust异步RDMA编程库，致力于提升高性能网络应用的开发效率。在介绍async-rdma之前我们先来了解一下RDMA的概念及其使用方式。

## RDMA简介

RDMA(Remote Direct Memory Access)直译为远程直接内存访问，是一种将一台主机内存中的内容直接传输到另一台主机内存中的技术。
计算机中常用的DMA技术避免了CPU忙于将数据从I/O设备拷贝到内存，RDMA则将这一能力拓展到了通过网络连接的多台主机之间。

RDMA在初始化阶段完成后可以由用户态程序通过用户态驱动直接操作RDMA网卡收发数据，无需陷入内核，避免了上下文切换，也无需CPU进行内存拷贝。
凭借绕过内核(kernel bypass)和零拷贝(zero copy)等特性，RDMA能以极低的CPU占用率实现高吞吐、低时延网络通信。
这里简要介绍下文中用到的RDMA的核心概念，若想进一步了解RDMA的细节可以关注知乎的[系列博客](https://zhuanlan.zhihu.com/p/164908617)，或更进一步翻阅[RDMA协议规范](https://cw.infinibandta.org/document/dl/8566)了解细节。

* MR(Memory Region)：RDMA应用程序向操作系统申请和注册的一片内存，用于后续RDMA操作。MR不光有地址和长度，还包含其他RDMA特有的元数据，如权限位，除本地读写权限还有远端读写权限等。

* 两种操作类型：

    1. 单端操作(SEND & RECV)

        * 发送、接收数据，一端Send前需要对端先Receive
        * 对端CPU需要感知每次收发操作
        * 用于交换元数据等小数据量操作

    2. 双端操作(READ & WRITE)

        * 直接对目标主机内存进行读写，请求发送前后对端无需进行操作
        * 对端CPU不感知单端操作，读写完成后网卡自动响应
        * RDMA的主要优势所在，适用于大数据量传输

## 现有RDMA开发方法

我们使用Socket API编写基于TCP/IP的网络应用，基于RDMA的网络应用开发则使用由C语言编写的Verbs API。
Verbs API提供了统一的编程接口，屏蔽了RDMA底层协议复杂多样的实现，保障了应用程序的通用性。
以下是两个Verbs API实例，暂且不关注其各个参数的具体含义，只观察其类型就可以发现其中隐含的内存安全问题。
我们无法确定裸指针所指数据是否有效，在开发过程中很容易遇到空指针、野指针、内存泄露和多次释放等常见内存安全问题。

```c
// 注册MR，使用前需先申请内存
struct ibv_mr *ibv_reg_mr(struct ibv_pd *pd, void *addr, size_t length, enum ibv_access_flags access)
// 数据请求发送接口
int ibv_post_send(struct ibv_qp *qp, struct ibv_send_wr *wr, struct ibv_send_wr **bad_wr)
```

Rust提供了以上内存安全问题的高效解决方案，从语言层面帮助我们避免了上述问题。
DatenLord社区提供了Verbs API的Rust封装库：[rdma-sys](https://github.com/datenlord/rdma-sys)。

## 为什么要做async-rdma？

然而，Verbs API低层级抽象导致的开发效率低的问题是单纯使用Rust封装所无法解决的。
这就是为什么有了rdma-sys库后还要再做一个async-rdma。
具体来说，就`ibv_post_send()`接口而言,参数所用到的数据结构是非常复杂的，直接操作这些参数需要对RDMA有很深入的了解，这会给开发者带来很大认知负担，并且使用时极易出错。
以下是Verbs复杂数据结构的一个例子供大家体会，其成员变量还包含其他让人头大的数据结构。

```c
// Verbs复杂数据结构示意，无需关注细节
struct ibv_send_wr 
{
    uint64_t wr_id;
    struct ibv_send_wr *next;
    struct ibv_sge *sg_list;
    int num_sge;
    enum ibv_wr_opcode opcode;
    enum ibv_send_flags send_flags;
    uint32_t imm_data;/* network byte order */
    union
    {
        struct 
        {
            uint64_t remote_addr;
            uint32_t rkey;
        } rdma;
        struct 
        {
            uint64_t remote_addr;
            uint64_t compare_add;
            uint64_t swap;
            uint32_t rkey;
        } atomic;
        struct 
        {
            struct ibv_ah *ah;
            uint32_t remote_qpn;
            uint32_t remote_qkey;
        } ud;
    } wr;
    uint32_t xrc_remote_srq_num;
};
```

### 简化API

每次发送数据请求都要处理这些复杂的数据结构显然是非常痛苦的。
因此async-rdma的首项工作就是对这些数据结构之间的关系进行整理，简化操作逻辑。
例如库中的`Rdma`数据结构记录了双端建立稳定连接所需的各种参数，后续通信过程中用到相关数据可直接复用而无需用户填充。
通过高层次抽象的数据结构封装，使得每次数据操作只需要用户填入必要的信息。

如下代码所示，申请注册MR时用户只需要指定存放的数据类型或大小即可。
SEND操作也只需要指定要发送哪个MR的数据，底层Verbs接口所用到的复杂参数由库填充。
当然，为了实现操作的便捷性，库中除了提供类型间转换的逻辑外，还使用了一些默认参数。
这些对于这些参数将提供修改接口供进阶开发者配置。

```rust
// 在本地申请一片存放i32类型数据的用于RDMA操作的内存空间。
let mut lmr = rdma.alloc_local_mr(Layout::new::<i32>())?;
// 将上述申请的内存区域中的数据发送到对端。
rdma.send(&lmr).await?;
```

### 辅助资源管理

只将这些参数的处理过程进行封装也还是不够的，因为这解决不了资源管理困难的问题。
复杂的数据结构背后是各种需要管理的资源，如本地注册的内存和从远端申请的内存，以及它们的状态、权限等。

要达到在远端主机CPU不感知的情况下对其内存进行读写，需要双端事先和事后交换元数据用以申请和释放远端资源。
即不光要管理本地资源，还要与远端主机协同管理远端资源，相比传统的网络应用开发复杂性更高。
async-rdma提供了Verbs API之外面向应用层语义的上层接口，使得建立连接、远端资源协同管理等操作变得简单。

在建立连接时，后台会自动运行Agent服务线程，其接收到上层接口发出的请求后，通过与远端Agent交互进行元数据交换，完成建立连接、远端资源申请释放等操作。
借助于Rust的生命周期机制，当本地或远端资源被Drop时，Agent会清理本地资源或自动向远端发送资源释放的请求，开发者无需感知。
以下代码展示了建立连接、申请本地和远端内存，通过RDMA WRITE操作通信和最终资源释放的过程。

```rust
/// 模拟客户端链接服务端
async fn client(addr: SocketAddrV4) -> io::Result<()> {
    // 通过TCP连接远端，与远端交换用于建立稳定连接的元数据并启动Agent
    // 连接建立后，后续元数据交换通过RDMA SEND RECV操作进行
    let rdma = Rdma::connect(addr, 1, 1, 512).await?;
    // 申请一块本地用于RDMA操作的内存(MR)
    let mut lmr = rdma.alloc_local_mr(Layout::new::<i32>())?;
    // 申请一块远端的内存(MR)
    let mut rmr = rdma.request_remote_mr(Layout::new::<i32>()).await?;
    // 将要发送的数据填入本地MR
    unsafe { *(lmr.as_ptr() as *mut i32) = 666 };
    // 通过RDMA WRITE操作将本地内存所存数据写入远端内存，远端不感知也无需处理本次请求
    rdma.write(&lmr, &mut rmr).await?;
    // rmr会在生命周期结束时向远端发送释放请求, lmr释放逻辑只需在本地执行。
    Ok(())
}
```

### 异步I/O

如本项目名中的`async`，本库提供的涉及I/O的接口都是异步的。
使用Verbs API时，每一次RDMA SEND、WRITE等请求后，都需要使用`poll()`方法轮询接收对端应答，才能确定请求是否正确完成。
首先忙等肯定是低效的，当然也可以由开发者实现异步逻辑，但轮询到的响应是前面哪次请求的也不确定，需要额外的逻辑去判断。

为了避免所有开发者都实现一遍上述逻辑，我们借助tokio实现了异步的RDMA请求处理逻辑。
当前task发出RDMA请求后即让出，当对端响应事件返回本机后，由后台服务程序唤醒与响应信息对应的task。
这避免了忙等开销，也无需开发者处理请求与响应间的对应关系，详细实现可见仓库中的[文档](https://github.com/datenlord/async-rdma/blob/master/doc/event_listener_arch.md)。
当然，事件驱动的异步逻辑在提高机器利用效率的同时可能会带来一定时延。对低时延有极致要求的场景可以牺牲效率换取低时延，有相关需求的朋友可以参与贡献忙等接口。

## 结语

总结一下，async-rdma主要提供了以下接口和功能：

* 用于主机之间建立RDMA稳定连接的异步接口，如`connect()`。

* 高抽象层级的异步RDMA数据收发操作接口，如`write()`。

* 高抽象层级的异步远端RDMA内存管理接口和本地RDMA内存管理接口，如`alloc_remote_mr()`。

* 用于支持上述异步接口，以及辅助管理远端内存的后台服务，如`Agent`。

除此之外，async-rdma还在不断完善错误的自动处理和提供尽可能详细的错误提示信息。
Verbs API在进行MR的管理或数据传输操作时遇到错误只返回系统错误代码，包含的信息很少。
有的错误处理逻辑会出现上层错误代码覆盖底层错误代码，返回对开发者具有误导性的错误代码。
一些错误在自己的应用程序中很难发现，要到用户态驱动甚至内核代码中去找。
这显然是极为低效和不友好的，也是async-rdma想要解决的问题。

当前async-rdma尚处于早期开发阶段，提供了常用功能接口，但项目在稳定性等方面仍有待加强。
DatenLord的目标是将其打造成产品级软件，使之能够广泛应用于生产环境中。

我们近期的主要开发目标如下：

1. 提高内存管理效率，改造通用内存分配器jemalloc使其能够分配MR，减少MR注册等慢速路径操作数量。
2. 完善错误处理，使后台服务尝试自动处理错误，并在尝试失败后返回详细错误信息和操作建议。
3. 完善测试，包括单元测试，压力测试，性能测试等，以发现潜在问题和辅助性能调优。
4. 提供更多配置接口，当前一些配置参数为默认值且尚未给出用户自定义接口，缺乏灵活性。

其中目标3、4不需要对RDMA或者本项目有非常深入的了解，只需了解对外暴露的上层接口，或熟悉Rust语言即可。
因此非常适合想要入门的朋友参与贡献，社区也会提供引导和帮助。
我们为每一个接口都提供了说明文档和使用样例，同时提供了在虚拟机中搭建RDMA应用运行环境的配置指南(无需特殊硬件支持)，此前未接触过RDMA的朋友也可以尝试。
欢迎大家提出问题和建议，欢迎对本项目感兴趣的朋友加入DatenLord社区一起参与贡献。

DatenLord社区的主项目为高性能分布式存储系统：datenlord，async-rdma是其子项目之一，更多项目见[社区github仓库](https://github.com/datenlord)。

## 相关链接

* async-rdma
  * Github: <https://github.com/datenlord/async-rdma>
  * Crate: <https://crates.io/crates/async-rdma>
  * Docs: <https://docs.rs/async-rdma/0.1.0/async_rdma>
* rdma-sys: <https://github.com/datenlord/rdma-sys>
* DatenLord: <https://github.com/datenlord>
* RDMA专栏文章: <https://zhuanlan.zhihu.com/p/164908617>
* RDMA协议规范: <https://cw.infinibandta.org/document/dl/8566>
