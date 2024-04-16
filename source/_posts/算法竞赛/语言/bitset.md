---
title: bitset
date: 2022-09-13 11:57:00
tags: 
categories: 
---


```cpp
#include<bits/stdc++.h>
using namespace std;

bitset<5> b, a;
int main() {
    cout << b << endl; //00000

    b.set(2);
    cout << b << endl;//00100

    a.set(3);
    cout << a << endl;//01000

    b ^= a;//(00100)^(01000)
    cout << b << endl;//01100

    b.reset(3);//00100
    cout << b << endl;

    b.flip();//11011
    cout << b << endl;
}
```

<!--more-->