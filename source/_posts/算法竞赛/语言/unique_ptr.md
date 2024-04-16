---
title: 关于unique_ptr的实验
date: 2022-09-10 11:57:00
tags: 
categories: 
- 语言
- C++
---
# code

```CPP
#include<memory>
int main() {
    std::unique_ptr<int> up,up1;
    up = std::unique_ptr<int>(new int(20));
    up1 = std::unique_ptr<int>(std::move(up));

    std::cout<<*up1<<std::endl; //结果：20
}
```
<!--more-->
以上代码如果输出`std::cout<<up<<std::endl` 会报段错误

unique_ptr指针指向的内存只可以被该指针访问
