---
title: 扩展GCD
date: 2022-09-15 11:57:00
tags: 
categories: 
---

# 扩展GCD
给定a,b

求$ax=1(mod \ b)$ 的最小整数解
```cpp
#include<bits/stdc++.h>
using namespace std;
#define ll long long
ll x, y;
ll exgcd(ll a, ll b, ll &x, ll &y){
    if(!b){
        x = 1, y = 0;
        return a;
    }
    ll d = exgcd(b, a % b, x, y);
    ll t = x;
    x = y, y = t - y * (a / b);
    return d;
}

int main() {
    ll a,b;
    cin >> a >> b;
    exgcd(a, b, x, y);
    ll ans = (x + b) % b;//最小整数解
    
    cout << ans << endl;
}
```

```cpp
bool liEu(ll a, ll b, ll c, ll &x, ll &y) {
  ll d = exgcd(a, b, x, y);
  if (c % d != 0) return 0;
  ll k = c / d;
  x *= k;
  y *= k;
  return 1;
}
```
<!--more-->

## 例题
### ax+by+cz == k
https://ac.nowcoder.com/acm/contest/4853/D

题意

> 求x,y,z,使得ax+by+cz ==k
>
> 保证给出的a,b,c,k有解

思路

> 因为ax+by == gcd(a,b)有解
>
> 只要让gcd(a,b)==1,那么肯定能得到akx+bky == k
>
> 本来只想用a,b来找答案的,但是发现不行,枚举了一下ab,bc,ac就过了

代码 :  https://xlorpaste.cn/vp0m4u
