---
title: KMP
date: 2022-09-12 11:57:00
tags: 
categories: 
- 算法
---
# prefix function 前缀函数
```cpp
vector<int> prefix_function(int n){
    vector<int> pi(n);
    for (int i = 1; i < n;i++){
        int j = pi[i - 1];
        while(j>0&&t[i]!=t[j])j = pi[j - 1];
        if(t[i]==t[j])j++;
        pi[i] = j;
    }
    return pi;
}
```
<!--more-->
# 例题
## [CF204E]
题意
> 给定一主串,再查询某字符串是否可以由主串的两个子串拼接而成.
思路
> 正反向各kmp一次
> 记录查询的字符串的前缀和后缀在主串中的位置

代码:先空着,之前的代码写的不太对

### [CCPC秦皇岛]
题意
> 跟找循环节有关
思路
> 正&&反
代码 : http://xlorpaste.cn/kwgp23

