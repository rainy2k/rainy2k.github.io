```yaml
title: "Pollux: Co-adaptive Cluster Scheduling for Goodput-Optimized Deep Learning"
date: 2023-06-01 16:21:00
tags: 
- MLSys 
- 深度学习调度
categories:
- 科研
- 论文
```

OSDI‘21

之前的大多数工作都需要用户预先指定每个 Training Job 所需要集群资源或者自动为每个 Job 静态分配集群资源，本项工作提出了调度器 Pollux，根据**每个 Job 当前运行的状态动态调整它们的资源**（例如 GPU 的数量）以及它们的**训练参数**（例如 learning rate 以及 batch size）来最大化整个集群的资源利用效率。



**Distributed DL Training**

这里介绍本文考虑的两个核心的 Metric：**System Throughput** 以及 **Statistical Efficiency**。

System Throughput 的定义为单位时间所训练的样本数量，这里考虑主要的影响因素：

1. **分配的资源（例如 GPU），资源越多 throughput 越高。**
2. batch size 的大小，batch size 越大，throughput 越高。这主要因为大 batch size 的计算时间长，可以减少分布式训练中 <mark>synchronization</mark> 时间的占比。
   - synchronization 同步

因此，在不更换训练算法的前提下，增加计算资源以及提高 batch size 可以有效地提高 throughput。



Statistical Efficiency 的定义为每个训练的样本对整个训练过程的贡献度，这里也主要考虑两个影响因素：

1. batch size 的大小。一般地，batch size 越大，statistical efficiency 越低；但在不同的训练阶段，batch size 对 statistical efficiency 的影响程度也不同，例如在训练初期的影响较大，而在后期影响较小。
2. learning rate。一般地，learning rate 越大，statistical efficiency 越高。

因此，增大 batch size 反而会降低 statistical efficiency。

- statistical efficiency 统计有效性

如下图，对于 batch size 的调整需要综合考虑 throughput 和 statistical efficiency。

![](https://pic4.zhimg.com/80/v2-5c0c90b7c963a43328dfc38ced1f428f_1440w.webp)

**Key Idea: Goodput，not Throughput**

本文核心的 idea 就是用一个新的指标：Goodput 来指导对集群资源的调度。

Goodput 综合考虑了 throughput 和 efficiency，下图给出了本文对 Goodput 的定义。本项工作就是通过调整 a, m, s 三个参数来最大化 Goodput。

![](https://pic3.zhimg.com/80/v2-f21d78948649474def65e87b497fe09a_1440w.webp)

**Modeling System Throughput**

为了最优化 Goodput，需要对 Throughput 和 Efficiency 两个指标进行建模并做实时的评估。下面简单介绍本文对两个指标的建模方式。

对于 Throughput，根据定义，可以通过计算 batch size / 计算每个 iteration 的时间： THROUGHPUT(a,m,s) = M(a,m,s)/T_{iter}(a,m,s)

而其中 T_{iter} 可以通过以下方式来计算：

![](https://pic4.zhimg.com/80/v2-a77b63fac9a69cd0889d02ea1c4d538f_1440w.webp)

下图给出了验证上述模型准确性的测试，可以看出这个模型基本上可以很准确地对 Throughput 进行评估。

![](https://pic4.zhimg.com/80/v2-f64437f539d72af15416a5a0ae83475b_1440w.webp)

**Modeling Statistical Efficiency**

Pollux 将 Efficiency 建模为 EFFICIENCY_t(M_0) 的相对值，并且在调整 batch size 的过程中只考虑 M \ge M_0，因此 Efficiency 的取值范围是 (0, 1]，例如如果某个时刻采用 batch size 为 M 时的 Efficiency 是 E，表达的含义是需要用 1/E 的样本来训练才能够达到初始 batch size M_0 用一个样本所能够达到的 progress。

![](https://pic3.zhimg.com/80/v2-9616f2599c151ebedf06f78be8e6d766_1440w.webp)

**Pollux Architecture**

Pollux 是一个深度学习训练任务调度器，负责在一个集群中为每个 DL Job 分配资源，并且调整他们的 batch size 以及 learning rate 来提高 Goodput。

Pollux 主要包含两个组件，全局的 PolluxSched，以及 per Job 的 PolluxAgent。

![](https://pic3.zhimg.com/80/v2-38d8d1fcfd88ee57d651007d674af0a2_1440w.webp)

PolluxAgent 负责收集当前 Job 的 efficiency 和 throughput，并实时地调整 batch size 以最大化 Goodput，且将 Goodput 汇报给 PolluxSched。

PolluxSched 负责为每个 Job 动态地分配 GPU 资源，PolluxSched 的调度目标是最大化 fitness：

![](https://pic4.zhimg.com/80/v2-6a38f49877fc828e7598013e28751753_1440w.webp)

**Evaluation**

作者在 16个节点的集群（每个节点 4 个 T4 GPU）用 8 小时训练了 160 个 DL Job，从下面的表格可以看出，相比其他调度器最优的配置，平均任务完成时间降低了 37% ~ 50%。

![](https://pic3.zhimg.com/80/v2-4ccc0c78b9286fb6a539c9931971595e_1440w.webp)

**Conclusion**

本项工作提出的 Pollux 是一个 DL Job 的调度器。Pollux 使用 Goodput 来衡量模型训练的效率，Goodput 同时考虑了 System Throughput 和 Statistical Efficiency；Pollux 并且在 cluster 和 job 两个层次同时对资源的利用进行优化，可以降低 37% - 50% 的平均任务完成时间。

Pollux 开源地址：[https://github.com/petuum/adapt](https://link.zhihu.com/?target=https%3A//github.com/petuum/adaptdl)
