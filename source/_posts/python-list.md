---
title: Python的列表推导式
date: 2016-12-19 21:19:28
tags: python
---
#### 1.列表推导式书写形式
[表达式 for 变量 in 列表]    或者  [表达式 for 变量 in 列表 if 条件]

#### 2.举例说明
```python
#!/usr/bin/python
# -*- coding: utf-8 -*-

li = [1,2,3,4,5,6,7,8,9]
print [x**2 for x in li]
print [x**2 for x in li if x>5]
print dict([(x,x*10) for x in li])
print  [ (x, y) for x in range(10) if x % 2 if x > 3 for y in range(10) if y > 7 if y != 8 ]
vec=[2,4,6]
vec2=[4,3,-9]
sq = [vec[i]+vec2[i] for i in range(len(vec))]
print sq
print [x*y for x in [1,2,3] for y in  [1,2,3]]
testList = [1,2,3,4]
def mul2(x):
    return x*2
print [mul2(i) for i in testList]
```
#### 3.结果
[1, 4, 9, 16, 25, 36, 49, 64, 81]

[36, 49, 64, 81]

{1: 10, 2: 20, 3: 30, 4: 40, 5: 50, 6: 60, 7: 70, 8: 80, 9: 90}

[(5, 9), (7, 9), (9, 9)]

[6, 7, -3]

[1, 2, 3, 2, 4, 6, 3, 6, 9]

[2, 4, 6, 8]