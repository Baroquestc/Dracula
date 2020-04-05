# 07-列表(list)

## 1 增、删、改、查

### 1.1 增

 - a.insert（1，56):返回值为none
 - a.extend（）添加一个列表b的元素到另一个列表的结尾（往列表中,扩展另外一个可迭代序列。和append之间的区别：extend可以算是两个集合的拼接，append是把一个元素, 追加到一个集合中)
 - '+'只能列表类型和列表类型相加；append和extend返回值都是none
 - 列表生成式创建列表($\color{#FF0000}{用这种语法创建列表之后元素已经准备就绪所以需要耗费较多的内存空间}$)

```python
[x for x in range(1,19)]
>> [1, 2, 3, 4, 5, 6, 7, 8, 9]

F = [x + y for x in 'ABCDE' for y in '1234567']
>> ['A1', 'A2', 'A3', 'A4', 'A5', 'A6', 'A7', 'B1', 'B2', 'B3', 'B4', 'B5', 'B6', 'B7', 'C1', 'C2', 'C3', 'C4', 'C5', 'C6', 'C7', 'D1', 'D2', 'D3', 'D4', 'D5', 'D6', 'D7', 'E1', 'E2', 'E3', 'E4', 'E5', 'E6', 'E7']
```

 - sys.getsizeof(f) # 查看对象占用内存的字节数

### 1.2 删

- a.remove（234）删除元素234(元素不存在会报错;存在多个元素会删除最左边的一个)
- del a[-1]删除指定元素的列表元素

```python

```

- a.pop(index=-1)移除并返回列表中指定索引对应元素(默认最后一个)，返回被删除的元素

```python

```

### 1.3 改

### 1.4 查
```python
nums = [3, 4, 5, 6, 5, 7, 55, 5, 8, 9]
idx = nums.index(5, 3)# 从索引3之后找对象5，返回索引值
print(idx)# 输出为4
```
## 2 进阶

### 1.5 遍历

 - 普通对象遍历
 - 自造索引遍历
```python
for index in range(len(list))：
#方便获取索引，通过索引获取元素 nums[index]t
```
 - 通过枚举对象
```python
 - enumerate(sequence[, start = 0]), 拆成多个元祖的集合，然后对单个元祖解包
 - for idx, val in enumerate(values):
 - for idx, val in enumerate(values, 3):
```
 - 使用迭代器遍历
 ```python
 
 ```
### 1.6 排序
 - sorted(iterable, key = None, reverse = False)
```python
 - 内建函数，可以对所有可迭代对象进行排序
 - 返回值时一个排序好的列表
 - 原对象不改变
      * s = [("sz", 18),  ("sz2", 16), ("sz1", 17), ("sz3", 15)
      * def getKey(x):
      * return x[1]
      * result = sorted(s, key=getKey, reverse=True)：以元组第一个值为 key，降序排列
      * print(result)
```
 - 列表对象方法：list.sort(key = None, reverse = False)
```python
 * 原对象被改变，方法没有返回值
```

### 1.7 乱序
```python
    * 无返回值，直接修改 list
    * import random
    * list = [1, 2, 3, 4, 5]
    * random.shuffle(l)
```

### 1.8 反转
 - list.reverse（）
```python
 * 无返回值，直接修改 list
 * 使用，切片反转
```
 - list[::-1]
```python
 * 返回值，原列表不变
```

### 1.9 过滤

- filter（）

  - ***描述：***

    <font color=red>filter()</font>函数用于过滤序列，过滤掉不符合条件的元素，返回由符合条件元素组成的新列表。

    该接收两个参数，第一个为函数，第二个为序列，序列的每个元素作为参数传递给函数进行判，然后返回 True 或 False，最后将返回 True 的元素放到新列表中

    ```
    filter(function, iterable)
    
    - function --判断函数
    - iterable -可迭代对象
    ```

  - ***注意：***

    Python2:直接返回列表

    Python3:返回一个迭代对象，若转化为列表可以使用`list`转换

  ```python
  #!/usr/bin/python3
   
  def is_odd(n):
      return n % 2 == 1
   
  tmplist = filter(is_odd, [1, 2, 3, 4, 5, 6, 7, 8, 9, 10])
  newlist = list(tmplist)
  print(newlist)
  ```