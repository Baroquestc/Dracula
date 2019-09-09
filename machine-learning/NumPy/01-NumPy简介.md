# 01-NumPy简介

## 1 数学基础操作

### 1.1 元素按位加、减、乘、除

```python
>>> a = np.array( [20,30,40,50] )
>>> b = np.arange( 4 )
>>> b
array([0, 1, 2, 3])
>>> c = a-b # 减
>>> c
array([20, 29, 38, 47])
>>> b**2 # 平方
array([0, 1, 4, 9])
>>> 10*np.sin(a) # 正弦函数
array([ 9.12945251, -9.88031624,  7.4511316 , -2.62374854])
>>> a<35 # 布尔
array([ True, True, False, False])
```

### 1.2 矩阵乘法

用 dot 函数 或 @ 操作符（python>3.5）

```python
>>> A = np.array( [[1,1],
...             [0,1]] )
>>> B = np.array( [[2,0],
...             [3,4]] )
>>> A * B                       # elementwise product
array([[2, 0],
       [0, 4]])
>>> A @ B                       # matrix product
array([[5, 4],
       [3, 4]])
>>> A.dot(B)                    # another matrix product
array([[5, 4],
       [3, 4]])
```

### 1.3 sum/min/max函数

```python
>>> b = np.arange(12).reshape(3,4)
>>> b
array([[ 0,  1,  2,  3],
       [ 4,  5,  6,  7],
       [ 8,  9, 10, 11]])
>>>
>>> b.sum(axis=0)                            # sum of each column
array([12, 15, 18, 21])
>>>
>>> b.min(axis=1)                            # min of each row
array([0, 4, 8])
>>>
>>> b.cumsum(axis=1)                         # cumulative sum along each row
array([[ 0,  1,  3,  6],
       [ 4,  9, 15, 22],
       [ 8, 17, 27, 38]])
```

### 1.4 常用函数

***注意这些函数都是按位操作的***

#### 1.4.1 exp()/sqrt()/add()

```python
>>> B = np.arange(3)
>>> B
array([0, 1, 2])
>>> np.exp(B)
array([ 1.        ,  2.71828183,  7.3890561 ])
>>> np.sqrt(B)
array([ 0.        ,  1.        ,  1.41421356])
>>> C = np.array([2., -1., 4.])
>>> np.add(B, C)
array([ 2.,  0.,  6.])
```

#### 1.4.2 numpy.all(a, axis=None, out=None, keepdims=<no value>)

沿着轴测试数组中所有元素是否为True。 如果存在一个为False,就返回False

注意： NaN / 正无穷 / 负无穷都是True,因为它们都不等于0

```python
>>> np.all([[True,False],[True,True]])
False

>>> np.all([[True,False],[True,True]], axis=0)
array([ True, False])

>>> np.all([-1, 4, 5])
True

>>> np.all([1.0, np.nan])
True

>>> o=np.array([False])
>>> z=np.all([-1, 4, 5], out=o)
>>> id(z), id(o), z                             
(28293632, 28293632, array([ True]))
```

#### 1.4.3 numpy.any(a, axis=None, out=None, keepdims=<no value>)

跟all函数类似，不过它是如果有一个元素为True,则返回True

```python
>>> np.any([[True, False], [True, True]])
True
>>>
>>> np.any([[True, False], [False, False]], axis=0)
array([ True, False])
>>>
>>> np.any([-1, 0, 5])
True
>>>
>>> np.any(np.nan)
True
>>>
>>> o=np.array([False])
>>> z=np.any([-1, 4, 5], out=o)
>>> z, o
(array([ True]), array([ True]))
>>> # Check now that z is a reference to o
>>> z is o
True
>>> id(z), id(o) # identity of z and o              
(191614240, 191614240)
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



