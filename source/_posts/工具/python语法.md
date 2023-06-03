---
title: python语法
date: 2023-04-26 14:36:00
tags:
- python
- 工具
categories:
- 语言
---



### 在遍历列表的过程中， 删除列表中的元素

```python
a = [1, 2, 3, 4, 5, 6, 7, 8, 9,10]
b = a[:]
for x in b:
    if x % 2 == 0:
        a.remove(x)
    else:
        print(x)
print(a)
```

