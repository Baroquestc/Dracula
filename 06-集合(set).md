# 06-集合(set)

## 1 概念

 - 无序的，不可随机访问的，不可重复的元素集合
 - 与数学集合概念类似，可进行交、并、差、补等逻辑运算
### 1.1 可变
`set`:可做增、删、改
### 1.2 不可变
`frozenset`:创建后无法增删改

## 2 定义
### 2.1 可变集合
```python
s = {1, 2, 3}
s = set(iterable)，参数可以是
 * str
 * list
 * tuple
 * dict - 只会将生成又 key 组成的集合

eg.s2 = set("abc")
> {'a', 'b', 'c'}
s2 = set([1,2,3)]/set((1,2,3))
> {1, 2 ,3}
s2 = set({"name":"sz", "Age":18})
> {'name', 'sz'}

```
 * 生成一个1到9的集合
推导式：表达式 + 循环 + 表达式，参考列表的推导式生成方式
```python
函数推导式
s = set(x for x in range(1, 10))

定义推导式
s = {x for x in range(1, 10)}
```

### 2.2 不可变集合
```python
fs = frozenset(iterable)，参数类型同可变类型
```

 - 生成一个1到9的集合
```python
推导式
fs = frozenset(x for x in range(1, 10)
```
### 2.3 注意点

 - 创建一个空的集合，只能使用`set()` 或 `frozenset()`，不能使用`s = {}`，因为会默认成字典
 - 集合中的元素必须是可哈希的值
  *  如果一个对象在自己的生命周期中有一个哈希值是不可变的，那么它就是可哈希的 
   *  可暂时理解为不可变类型

```python
# s = {1, 2, [1, 2]}
s = {1, 2, {"name": "sz"}}
# s = {1, 2, 1}
print(s, type(s))
```

 - 如果集合中的元素出现重复，则会被合并成一个。可用于列表去重操作
## 3 操作
### 3.1 单一集合操作
#### 3.1.1 单一集合操作
 - 增

```python
s.add（element）#返回值为none
```

 - 删

```python
s.remove(element)
# 指定删除set对象中的一个元素
# 如果集合中没有这个元素，则返回一个错误，有的话返回为none

s.discard(element)
# 指定删除集合中的一个元素
# 若没有这个元素，则do nothing；返回值为none

s.pop(element)
# 随机删除并返回一个集合中的元素
# 若集合为空，则返回一个错误

s.clear()
# 清空一个集合中的所有元素

del 语句
# 删除集合
```

 - 改

```python
元素为不可变类型，不能修改
```

 - 查

```python
* 因为集合是无序的，不能通过索引进行查询
* for in 遍历
* 迭代器访问
```

#### 3.1.2 不可变集合

 - 查
```python
* 通过 for in 进行遍历
    s = frozenset([1, 2, 3])
    print(s, type(s))
    for v in s:
        print(v)
 * 通过迭代进行访问
    s = {1, 2, 3}
    # 1. 生成一个迭代器
    its = iter(s)
    # 2. 使用这个迭代器去访问(next(), for in)
    print(next(its))
    print(next(its))
```

### 3.2 集合间操作

 - 交

```python
# .intersection(Iterable)，方法是先将 iterable 参数值转为集合，然后再与另一个集合进行计算的
s1.intersection(s2)

iterable 参数类型可以使如下：
1. 字符串
  * 只判定字符串中的非数字
  * 即 集合 {1, 2, 3} 与 intersection(“123”) 运算结果是 》set() 空集合
  * 因为会先将“123” 转成 集合 {“1”, “2”, “3”}, 此时与数值集合{1, 2, 3} 元素类型不匹配，即无交集
2. 列表
3. 元组
4. 字典
  * 使用 key判定
5. 集合
```

```python
# 逻辑与 ‘&'，作用相同
s1 & s2

# .intersection_update(…)
s1.intersection_update(s2)
  * 交集计算完毕后, 会再次赋值给原对象
  * 会更改原对象
  * 所以, 只适用于可变集合
s1 = {"1", "2", "3", "4", "5"}
s2 = {4, "5", 6}
result = s2.intersection_update(s1)
print(s1, s2) #s1: {"1", "2", "3", "4", "5"} s2:{"5"}

```

 - 并

```python
# union()，返回并集
s1.union(s2)

# 逻辑或 ‘|'，返回并集
s1 | s2

# update()，更新原集合为并集
s1.update(s2)
```

 - 查

```python
# difference()
s1.difference(s2)

# 算术运算符减 ‘-‘
s1 - s2

# difference_update()
s1.difference_update(s2)
```

 - 判定

```python
# isdisjoint() 两个集合不相交
s1.isdisjoint(s2)

# issuperset() 一个集合包含另一个集合
s1.issuperset(s2)

# issubset() 一个集合包含于另一个集合
s1.issubset(s2)
```

### 3.3 注意

 - 当可变与不可变集合混合运算时，返回的结果类型，以运算符左侧为主
