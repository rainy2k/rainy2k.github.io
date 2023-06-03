---
title: "REEF 论文笔记"
date: 2023-04-25 14:01:00
tags: 
- MLSys 
categories:
- 科研
- 论文
---

## 动机

“However, commodity GPUs lack efficient preemptive scheduling support and state-of-the-art approaches either have to monopolize GPU or let the realtime tasks to wait for best-effort tasks to complete, which causes low utilization or high latency, or both.” 

商业 GPU 缺乏有效的抢占式调度支持，最先进的方法要么必须独占 GPU，要么让实时任务等待尽力而为的任务完成，这会导致低利用率或高延迟，或两者兼而有之。

通常，DNN 推理有两个可能相互冲突的 GPU 调度目标。

首先，实时任务应该被视为 GPU 上的“first-class citizens” ，不受其他任务的干扰，以实现低端到端延迟。

其次，实时任务和best-effort的任务都应该在 GPU 上同时提供服务，以实现高整体吞吐量（工作守恒work-conserving.）。

## REEF

“REEF, the first GPU-accelerated DNN inference serving system that enables microsecond-scale kernel preemption and controlled concurrent execution in GPU scheduling.”

REEF，第一个 GPU 加速的 DNN 推理服务系统，可在 GPU 调度中实现微秒级内核抢占和受控并发执行。

### 抢占

“First, based on the observation that DNN inference kernels as mostly idempotent, REEF devises a reset-based preemption scheme that launches a real-time kernel on the GPU by proactively killing and restoring best-effort kernels at microsecond-scale” 

首先，基于对 DNN 推理内核大多是幂等的观察，REEF 设计了一种基于重置的抢占方案，通过在微秒级主动杀死和恢复best-effort的内核，在 GPU 上启动实时内核

### 内核填充

“Second, since DNN inference kernels have varied parallelism and predictable latency, REEF proposes a dynamic kernel padding mechanism that dynamically pads the real-time kernel with appropriate best-effort kernels to fully utilize the GPU with negligible overhead.” (Han 等, p. 539) 

其次，由于 DNN 推理内核具有不同的并行性和可预测的延迟，REEF 提出了一种动态内核填充机制，该机制使用适当的best-effort内核动态填充实时内核，以充分利用 GPU，开销可忽略不计。

### 定义

“REEF, the first DNN inference serving system for commodity GPUs with microsecond-scale kernel preemption and controlled concurrent execution in GPU scheduling to achieve both real time and work conserving” 

REEF，第一个用于商品 GPU 的 DNN 推理服务系统，具有微秒级内核抢占和 GPU 调度中的受控并发执行，以实现实时和工作守恒

具体来说，到达的实时任务应该立即从正在运行的尽力而为的内核中抢占 GPU，而无需等待它们完成。同时，best-effort的内核应该使用实时内核剩余的 GPU 资源并发执行。

### Key Insight

REEF 的一个关键见解是 DNN 推理中的每个内核大多是幂等的。这意味着可以在不保存上下文的情况下主动终止和恢复正在运行的尽力而为的内核。

### Implementaion

“We have implemented REEF by extending Apache TVM [73] (a compiler for deep learning) and AMD ROCm [3] (an open-source GPU computing platform). We evaluate REEF using a new DNN Inference Serving Benchmark (DISB) with diverse workloads and models, as well as a real-world trace from Apollo [7] (an open autonomous driving platform).” 