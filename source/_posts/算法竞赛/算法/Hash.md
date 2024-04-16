---
title: Hash
date: 2022-09-15 11:57:00
tags: 
categories: 
---
# 模板
http://xlorpaste.cn/y0xia1
```cpp
struct Hash() {
    const int mod = 1e9 + 7;
    const int SEED = 13331;
    long long p[maxn], f[maxn], ff[maxn];
    int n;
    void Hash(string s) {
        n = s.size();
        f[0] = s[0] - 'a' + 1;
        p[0] = 1;
        for (int i = 1; i < n; i++) {
            f[i] = (f[i - 1] * SEED % mod + (s[i] - 'a' + 1)) % mod;
            p[i] = p[i - 1] * SEED % mod;
        }
        ff[n - 1] = s[n - 1] - 'a' + 1;
        for (int i = n - 2; i >= 0; i--) {
            ff[i] = (ff[i + 1] * SEED % mod + (s[i] - 'a' + 1)) % mod;
        }
    }
    long long h(int l, int r) {
        if (l == 0)
            return f[r];
        return (f[r] - f[l - 1] * p[r - l + 1] % mod + mod) % mod;
    }
    long long hh(int l, int r)
    {
        if (r == n - 1)
            return ff[l];
        return (ff[l] - ff[r + 1] * p[r - l + 1] % mod + mod) % mod;
    }
};
```
<!--more-->

# 例题
## Google Kick Start Round A

题意

> 有L个单词,现在给定一个字符串S,长度为N.
> 1<L<20000,2<N<1e6。且所有单词的长度总和小于$10^5$
> 一个单词和S串匹配的方法是: 这个单词和S的一个子串的首字符和尾字符相同,中间的字母种类个数相同,但是顺序随意,求有多少个单词是匹配的?

思路

> 首先匹配的思路是：确定首尾之后，只要中间的字符出现的次数相同即可，那么用一个num[26]来记录即可
> 由于所以单词长度总和小于1e5，则可以知道不同长度有$\sqrt{10^5}$个,因为1+2+3+...+n = n*(n+1)/2
> 以这个长度枚举

代码: http://xlorpaste.cn/sm4tac
