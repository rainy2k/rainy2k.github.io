```yaml
title: "nn-Meter: towards accurate latency prediction of deep-learning model inference on diverse edge devices."
date: 2023-06-01 01:08:00
tags: 
- MLSys 
categories:
- 科研
- 论文
```

Link:[GitHub - microsoft/nn-Meter: A DNN inference latency prediction toolkit for accurately modeling and predicting the latency on diverse edge devices.](https://github.com/microsoft/nn-Meter)

Paper: https://air.tsinghua.edu.cn/pdf/nn-Meter-Towards-Accurate-Latency-Prediction-of-Deep-Learning-Model-Inference-on-Diverse-Edge-Devices.pdf

解读：

- https://zhuanlan.zhihu.com/p/402436426





是什么？

- 面向深度学习的移动端硬件延迟预测器

- Tiresias

- 



Q1 该论文解决了什么问题？

解决的问题是how to measure the inference latency。在很多任务中，测量真实设备上的延迟成本过高或不太可行。

Q2 该论文的解决方案是什么？

该论文开发了一个预测不同edge device上的DNN模型的inference latency。

- 将模型推理划分为多个内核，即设备上的执行单元(execution unit)，通过测试用例自动检测unit。
- nn-Meter为内核构建等待时间预测因子，并通过模型所有内核的等待时间总和预测模型的总等待时间。
- 亮点1：自动内核检测以捕获边缘设备上的各种操作员融合行为
- 亮点2：自适应数据采样以降低构建内核级延迟预测的成本

Q3 该论文的实验是如何设计的？

采用了新的数据集，包含26000个具有代表性的CNN模型，具有各种模型图、配置和延迟范围。

from：https://zhuanlan.zhihu.com/p/576053444
