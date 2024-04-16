---
title: Tire字典树
date: 2022-09-15 11:57:00
tags: 
categories:
- 数据结构
---

### Bundling

https://codingcompetitions.withgoogle.com/kickstart/round/000000000019ffc7/00000000001d3ff3

题意

> 将给定的n个字符串划分为大小为k的组,每组的权值为这些字符串的最长公共前缀
>
> $\sum n \leq 10^6,k|n$

思路

> 构建一棵字典树,每一个节点对应一个前缀,对于每个前缀,如果它出现的次数大于k,那么它对答案有贡献,加上即可

代码: https://xlorpaste.cn/0ve9u4
<!--more-->
