# 01-NumPy简介

## 1 数学上的操作

```python
from numpy import *

a = [1, 2, 3, 4]
b = array([2, 3, 4, 5])

# 对应元素相加
# print(a+1) # 报错
a = array(a)
print(a + 1)

# 对应元素相乘
print(a*b)

# 对应元素乘方
print(a ** b)
```

## 2 提取数组中的元素

```python
from numpy import *

a = [1, 2, 3, 4]
b = array([2, 3, 4, 5])

a = array(a)
# 前两个元素
print(a[:2])
# 后两个元素
print(a[-2:])
```

## 3 修改数组形状

```python
# 查看array形状
a.shape
>> (4,)

# 修改array形状
a.shape = 2,2
>> [[1 2]
 [3 4]]
```

## 4 画图



