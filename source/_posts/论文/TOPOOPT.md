---
title: "TOPOOPT: Co-optimizing Network Topology and Parallelization Strategy for Distributed Training Jobs"
date: 2023-04-20 19:40:00
tags: 
- MLSys
- Network
categories: 
- 科研
- 论文
---



## Summary

《TOPOOPT: Co-optimizing Network Topology and Parallelization Strategy for Distributed Training Jobs》

这篇论文提出了一种新的直连网络拓扑结构，名为TOPOOPT，用于深度神经网络（DNN）的分布式训练。

TOPOOPT通过协同优化计算、通信和网络拓扑三个维度，实现了分布式训练过程的协同优化。

TOPOOPT利用AllReduce流量的可变性，构建了高效的DNN训练作业网络拓扑结构。

- AllReduce流量的可变性是指**AllReduce流量**的大小和分布在不同节点之间的方式可以根据不同的**并行化策略**进行调整，以优化网络拓扑结构。这种可变性是TOPOOPT直接连接深度神经网络（DNN）训练负载的一个关键特征。

TOPOOPT使用交替优化技术和一种名为TotientPerms的群论算法来找到最佳的网络拓扑结构、路由计划和并行化策略。TOPOOPT使用全局优化技术来解决这个问题，该技术将并行化策略和网络拓扑结构作为一个整体进行优化。



TOPOOPT在100 Gbps下使用远程直接内存访问（RDMA）转发构建了一个完全功能的12节点直连原型。

实际分布式训练模型上的大规模模拟表明，与类似成本的Fat-tree互连相比，TOPOOPT可以将DNN训练时间缩短多达3.4倍。

: https://www.usenix.org/conference/nsdi23/presentation/wang-weiyang





## Introduction

“We demonstrate that training workloads do not satisfy common assumptions about datacenter traffic that underlie the design of Fat-tree interconnects” (Wang 等, p. 739) 🔤我们证明训练工作负载不满足关于数据中心流量的常见假设，这些假设是 Fat-tree 互连设计的基础🔤

“(i) the communication overhead of large DNN training jobs increases dramatically as we increase the number of workers; and 

(ii) the traffic pattern of a DNN training job depends on its parallelization strategies.” (Wang 等, p. 739) 

🔤(i) 随着我们增加worker数量，大型 DNN 训练工作的通信开销急剧增加； 

(ii) DNN 训练作业的流量模式取决于其并行化策略。🔤

受这些观察的启发，我们提出了 TOPOOPT，这是一种**直接连接(direct-connect)的 DNN 训练系统，可共同优化网络拓扑和并行化策略。** 

TOPOOPT 使用可重构光开关(reconfigurable optical switches)和接线板(patch panels)为每个训练作业创建专用分区，并联合优化每个分区内的拓扑和并行化策略。

为了实现我们的目标，我们努力应对寻找最佳拓扑的算法挑战，例如如何在计算、通信和拓扑维度上导航大型搜索空间，以及各种操作挑战，例如哪种光开关技术匹配得很好与 DNN 模型的流量模式。



我们将拓扑和并行化策略协同优化问题视为离线交替优化框架（“off-line alternating optimization framework” ）。

我们的优化技术在优化并行化策略和优化网络拓扑之间交替进行。它在假定固定拓扑的并行化策略空间中进行搜索，并将流量需求提供给 TOPOLOGYFINDER 算法。然后将更新后的拓扑反馈到并行化策略搜索算法中。这个交替过程不断重复，直到系统收敛到优化的并行化策略和拓扑结构。



> **？这里的网络拓扑指的是什么呢？**

我们证明，为 DNN 寻找优化的网络拓扑具有挑战性，因为理想的网络拓扑需要同时满足两个目标：

理想的网络拓扑需要同时满足两个目标

**(i) 高效完成大型 AllReduce 传输**

**(ii) 确保模型并行传输的小跳数**

为了实现这些目标，我们提出了一种基于群论的新型技术，称为 TotientPerms，它利用了 AllReduce 传输的可变性。

我们的 TotientPerms 方法构建了一系列 AllReduce 排列，这些排列不仅可以有效地进行 AllReduce 传输，而且还可以很好地进行模型并行传输，从而提高整体训练性能。



## Background

### Training iteration

训练 DNN 的一种常见方法是随机梯度下降 (SGD，“stochastic gradient descent” ) 。

每次 SGD 迭代都涉及随机选择一批标记训练数据，计算模型相对于标记数据的误差，并通过反向传播计算模型权重的梯度。

使用新的一批数据重复训练迭代，直到模型收敛到目标精度。

重点就是随机选择一批标记数据。



### Data parallelism

#### AllReduce

“Each accelerator holds a replica of the DNN model and executes the forward and backpropagation steps locally. In data parallelism, all accelerators synchronize their model weights during each training iteration. This step is commonly referred to as AllReduce and can be performed using various techniques” (Wang 等, p. 740) 🔤每个加速器都持有 DNN 模型的副本，并在本地执行前向和反向传播步骤。在数据并行中，所有加速器在每次训练迭代期间同步它们的模型权重。此步骤通常称为 AllReduce，可以使用各种技术执行🔤



### Hybrid data and model parallelism

“As a result, large DNNs are distributed using a hybrid of data and model parallelism, where different parts of a DNN and its dataset are processed on different accelerators in parallel.” (Wang 等, p. 740) 🔤因此，大型 DNN 使用数据和模型并行的混合分布，其中 DNN 的不同部分及其数据集在不同的加速器上并行处理。🔤



由单纯的数据并行，转换为模型并行+数据并行。



### Types of data dependencies in DNN training

类型 (1) 是指在前向传播和反向传播步骤中计算的激活和梯度。每个输入样本都需要这种数据依赖性。

类型 (2) 是指一旦处理了一批样本，就通过 AllReduce 步骤在加速器之间同步模型权重。根据并行化策略，这些数据依赖性可能会导致本地内存访问或跨加速器流量。

例如，在混合数据和模型并行化策略中，类型 (1) 和 (2) 都会导致跨加速器流量，具体取决于模型如何跨加速器分布。

鉴于类型（1）与模型并行性相关，我们将类型（1）创建的网络流量称为 MP 传输。同样，我们将类型 (2) 创建的网络流量称为 AllReduce 传输。

请注意，**AllReduce 传输并不严格意味着数据并行性流量，因为模型并行还可以跨节点子集创建 AllReduce 传输**。



