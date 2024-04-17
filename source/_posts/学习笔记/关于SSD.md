---
title: 关于SSD
date: 2024-2-28 18:20:00
tags: 
- 存储
categories:
- 学习笔记
---

# 关于SSD

https://mp.weixin.qq.com/s/T1l177Sneiyni_QCmi4wBA

<!--more-->
**引言**

在当前分布式工作流程中，对于边缘、数据中心和云端生成的数据，通过网络或云进行聚合和共享的工作流程、AI和ML引擎以及应用程序变得至关重要。随着云计算和边缘设备的广泛应用，以及大量数据驱动型员工在偏远地区工作，这种需求变得更加迫切。

随着数据集的不断增长和数据驱动型洞察应用程序的增加，我们需要持续关注并消除阻碍数据有效利用的障碍。尽管大多数创新都是单一供应商技术的渐进性进步，但偶尔也会出现一次巨大的飞跃，多种技术汇聚在一起解决一个问题。

我们的行业正处于数据路径性能进步的关键时刻。随着AI、ML和数据分析应用程序的兴起，时机已经成熟。现在，我们将见证通信、存储和计算技术融合如何催生一种新型存储架构，为数据密集型、分布式应用程序的发展扫清障碍，铺就未来之路。

**NAS性能的演进**

回顾过去，RAID控制器曾是性能提升的障碍。但随着NVMe和PCIe的引入，基于NVMe的直接连接存储系统成功地将RAID控制器从数据路径中剔除。

然而，在共享存储环境中，要充分利用底层SSD的性能仍面临较大挑战。对于共享存储的使用场景，高性能的纠错编码至关重要，它能有效保护数据安全。此外，数据路径中还需要额外的物理层，以便文件系统能将文件映射到块，进而将块映射到闪存地址。为了完成这些操作，系统需要内部网络来连接和扩展（scale-out）多个节点，形成一个统一的系统。

值得注意的是，在高性能环境中，文件系统软件的效率和物理架构同样重要。接下来，我们深入探讨物理架构的问题。在使用InfiniBand或以太网背板的Scale-out NAS解决方案中，每个高速串行总线传输都需要进行至少九次数据重传，无论是芯片间传输还是通过线路传输。每次数据重传都会降低底层NVMe硬件的性能，导致客户端无法充分发挥NVMe的全部潜能。

![图片](%E5%85%B3%E4%BA%8ESSD/640)

面对性能工作负载的Scale-out NAS解决方案提供商，他们看到了这些挑战，并基于NVMe over Fabrics（NVMe-oF）设计了相应的技术。这些解决方案将CPU从存储服务器的数据路径中移除，并使用PCIe路由到网络适配器。这种数据路径优化虽然取得了巨大的进步，但仍然需要大量的数据重传，并带来不必要的硬件成本。此外，由于每个NVMe设备都需要一个网络端口，以充分利用设备的性能，这也导致了功耗效率低下。

![图片](%E5%85%B3%E4%BA%8ESSD/640-20240415185507104)

随着NFSv4.2的推出，Linux社区引入了一个基于标准的软件解决方案，旨在进一步提升速度和效率。NFSv4.2允许工作负载从数据路径中移除文件服务器和目录映射，使NFSv3数据路径能够保持与存储的不间断连接。

这一进步显著减少了数据重传次数，从九次减少到五次，降低了44%。通过NFSv4.2，NAS环境具备了以前仅在并行文件系统中才有的优势，即元数据服务器不再位于数据路径中，从而消除了额外的内部存储网络成本和功耗。这种方法显著简化了从小规模到大规模的高效扩展能力，因为存储可以直接连接到现有的以太网网络。

![图片](%E5%85%B3%E4%BA%8ESSD/640-20240415185517646)

另外，GPUDirect架构的利用也可以提高数据路径效率。它通过主机CPU和内存之间的单一PCIe跳跃和复制，提供了与NVMe-oF相似的数据路径效率。

![图片](%E5%85%B3%E4%BA%8ESSD/640-20240415185530644)

**下一个重大机遇在哪里？**

下一个巨大的文件存储性能创新将出现在当现有以太网连接的固态硬盘（eSSD）能够直接与客户端通过NFS进行连接时。这将允许客户端几乎完全获得底层NVMe架构的性能，并彻底消除额外的基础设施成本。

这一创新最终推动了技术的演进，使得SSD本身能够直接连接到以太网（一种NFS-eSSD）。SSD将能够与NFSv3进行原生通信，而且块到闪存地址的映射可以简化为单一级别的映射。这种架构的结果是数据重传的次数进一步减少，从传统NAS中的九次，到并行文件系统中的五次，再到利用NFSv4.2和NFS-eSSD时仅为三次。

![图片](%E5%85%B3%E4%BA%8ESSD/640-20240415185543374)

**使用NFS-eSSD的优势**

这种新型架构带来了显著的优势。它提供了更低的延迟、减少的数据重传和较低的功耗，因为跨芯片到芯片连接或通过线路进行的数据串行传输占据了数据中心大部分的功耗。此外，这种方法还降低了运营和资本成本，因为它消除了许多额外的硬件需求。

对于NFS-eSSD来说，写放大问题得到了缓解。因为SSD更加了解其空闲空间，可以避免覆盖写入，从而提高SSD的耐久性。这减少了过度配置（OP）的需求，使得在不牺牲实际可用性能的情况下实现更高的容量密度和更高的访问密度成为可能。与传统的NVMe SSD相比，整体可靠性、可用性和可服务性通过减少硬件和数据重传得到了优化。因此，更广泛的动态扩展能力得以实现，允许存储系统直接在现有的以太网网络上进行放大或缩小，并利用基于标准的存储连接。

**为何是现在？**

现代数据分析和AI工具让我们能从数据中获取巨大的价值，但要实现这一价值，前提是能快速访问和处理数据。这些数据分析工作负载需要庞大的数据集，这些数据集必须存储在高效、高性能的存储设备上，并且需要能智能地编排数据。

所需的高速数据编排管道必须为所有系统提供一个共享的数据企业视图。它需要针对小型、随机的I/O模式进行优化，并提供卓越的系统性能峰值以及出色的文件系统性能聚合，以满足企业可能遇到的各种训练工作负载需求。数据管道需要由基于标准的解决方案驱动，这将便于在不同安全性和构建环境的机器上进行部署。同时，存储系统需要将SSD的全部性能提供给工作流程，以最大限度地利用机会，同时控制基础设施成本。

随着高速以太网、64位处理器IP的普及、IPv6、Linux中的NFSv4以及高性能轻量级文件系统的可用性，所有相关技术已经成熟，足以实现这一目标。现在，业界只需将这些要素巧妙地组合起来！

**参考资料**

1. P. Goodwin, “SSDs and RAID: What’s the right strategy, JEDEC at the CES,” JEDEC, Arlington, VA, USA, 2011. Accessed: Jun. 22, 2023. [Online]. Available: https://www.jedec.org/ sites/default/files/1330-1400%20 Paul%20Goodwin%20RAID%20 and%20SSDs.pdf
2. J. L. Hafner, V. Deenadhayalan, T. Kanungo, and K. K. Rao, “Performance metrics for erasure codes in storage systems,” IBM, San Jose, CA, USA, Tech. Rep. RJ 10321 (A0408003), Aug. 2004.
3. G. F. Pfister, “An introduction to the infiniband architecture,” in High Performance Mass Storage and Parallel I/O: Technologies and Applications, H. Jin, T. Cortes, and R. Buyya, Eds. Piscataway, NJ, USA: IEEE, 2001, ch. 42, pp. 617–632.
4. E. Kim and F. Zhang, “Optimizing NVMe® over fabrics (NVMe-oFTM),” SNIA, Santa Clara, CA, USA, White Paper, Apr. 2021. [Online]. Available: https://www.snia.org/ sites/default/files/education/ snia-optimizing-nvme -over-fabrics-nvme-of.pdf
5. S. Dickson. Linux Support of NFS v4.1 and v4.2. (Mar. 2017). RedHat. [Online]. Available: https://events.static. linuxfound.org/sites/events/files/ slides/Vault2017.pdf
6. T. Vojnovich, M. Carlson, R. Davis, and J. F. Kim, “Ethernet-attached SSDs, brilliant idea or storage silliness?” SNIA, Santa Clara, CA, USA, SNIA Webcast Presentation, Mar. 2020. Accessed: Jun. 22, 2023. [Online]. Available: https:// www.snia.org/sites/default/files/ ESF/SNIA-Ethernet-SSDs-Final. pdf
7. D. Flynn, “The case for NFS-eSSDs,” in Proc. IEEE Mass Storage Conf., May 2023. [Online]. Available: https://storageconference.us/2023/ FlynnPresentation2.pdf
8. “Google collects statistics about IPv6 adoption in the internet on an ongoing basis, continuously updated.” Google. Accessed: Jun. 22, 2023. [Online]. Available: https://www.google. com/intl/en/ipv6/statistics.html

\-----

Source: David Flynn, Thomas Coughlin; Overcoming Performance Bottlenecks with a Network File System in Solid State Drives; 23 August 2023

