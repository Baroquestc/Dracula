# advanced

## 1 深拷贝、浅拷贝

### 1.1 浅拷贝

拷贝了引用，并没有拷贝内容

- `=` 使用

![img](https://tva1.sinaimg.cn/large/006tNbRwly1g9vy8213wkj317r0u0gwh.jpg)

![img](https://tva1.sinaimg.cn/large/006tNbRwly1g9vy87a04vj311j0u00yd.jpg)

### 1.2 深拷贝

深拷贝是对于一个对象所有层次的拷贝(递归)

- `copy.deepcopy()`

![img](https://tva1.sinaimg.cn/large/006tNbRwly1g9vytutvwjj31d10u0tdf.jpg)

![img](https://tva1.sinaimg.cn/large/006tNbRwly1g9vyu2qqa9j31ji0toju7.jpg)

- `copy.copy()`对于可变类型，浅拷贝；对于不可变类型，仅仅是指向

![image-20191214103850791](https://tva1.sinaimg.cn/large/006tNbRwly1g9w1vt5s0sj30ys0ps76n.jpg)

![image-20191214104925671](https://tva1.sinaimg.cn/large/006tNbRwly1g9w26tuj61j319q0sknhn.jpg)

1）当浅复制的值是**不可变对象（数值，字符串，元组**）时和“等于赋值”的情况一样，对象的id值与浅复制原来的值相同。（copy.copy拷贝的是元组，不会进行浅拷贝，仅仅是指向）

2）当浅复制的值是**可变对象（列表和元组）**时会产生一个“不是那么独立的对象”存在。有两种情况：

- 复制的 对象中无 复杂 子对象，原来值的改变并不会影响浅复制的值，同时浅复制的值改变也并不会影响原来的值。原来值的id值与浅复制原来的值不同。
- 复制的对象中有 复杂 子对象 （例如列表中的一个子元素是一个列表），如果不改变其中复杂子对象，浅复制的值改变并不会影响原来的值。 但是改变原来的值 中的复杂子对象的值  会影响浅复制的值。

### 1.3 拷贝其他方式

- 分片表达式可以赋值一个序列：

![img](https://tva1.sinaimg.cn/large/006tNbRwly1gajbdir0atj30xh0u0jxh.jpg)

- 字典的copy方法可以拷贝一个字典

![img](https://tva1.sinaimg.cn/large/006tNbRwly1gajbe87kjij30xt0u07bo.jpg)

### 1.4 混淆点

#### 1.4.1 浅拷贝对不可变类型和可变类型的copy不同

1. copy.copy对于可变类型，会进行浅拷贝(id不一样)
2. copy.copy对于不可变类型，不会拷贝，仅仅是指向

![img](https://tva1.sinaimg.cn/large/006tNbRwly1gajcorfxepj31a80u0n3o.jpg)

#### 1.4.2 copy.copy和copy.deepcopy的区别

- copy.copy()

![img](https://tva1.sinaimg.cn/large/006tNbRwly1gajctl0j5aj30u00v5dnn.jpg)

![img](https://tva1.sinaimg.cn/large/006tNbRwly1gajcttlvjlj30xk0u0gqr.jpg)

- copy.deepcopy()

![img](https://tva1.sinaimg.cn/large/006tNbRwly1gajcsphaa4j30zo0u0jvd.jpg)

![img](https://tva1.sinaimg.cn/large/006tNbRwly1gajcszcnn1j31ec0kymzs.jpg)

![img](https://tva1.sinaimg.cn/large/006tNbRwly1gajct7xajtj30u00u4tf1.jpg)

----

## 2 私有化、import、封装继承多态

### 2.1 私有化

- xx: 公有变量
- _x: 单前置下划线,私有化属性或方法，from somemodule import *禁止导入,类对象和子类可以访问
- __xx：双前置下划线,避免与子类中的属性命名冲突，无法在外部直接访问(名字重整所以访问不到)
- __xx__:双前后下划线,用户名字空间的魔法对象或属性。例如:`__init__` , __ 不要自己发明这样的名字
- xx_:单后置下划线,用于避免与Python关键词的冲突

### 2.2 import导入模块

- import搜索路径

![image-20191214110350006](https://tva1.sinaimg.cn/large/006tNbRwly1g9w2lt62blj30oc0d8abp.jpg)

**程序执行时添加新的模块路径**

```python
sys.path.append('/home/itcast/xxx')
sys.path.insert(0, '/home/itcast/xxx')  # 可以确保先搜索这个路径
In [37]: sys.path.insert(0,"/home/python/xxxx")
In [38]: sys.path
Out[38]: 
['/home/python/xxxx',
 '',
 '/usr/bin',
 '/usr/lib/python35.zip',
 '/usr/lib/python3.5',
 '/usr/lib/python3.5/plat-x86_64-linux-gnu',
 '/usr/lib/python3.5/lib-dynload',
 '/usr/local/lib/python3.5/dist-packages',
 '/usr/lib/python3/dist-packages',
 '/usr/lib/python3/dist-packages/IPython/extensions',
 '/home/python/.ipython']
```

- 重新导入模块

模块被导入后，`import module`不能重新导入模块，重新导入需用`reload`

![img](https://tva1.sinaimg.cn/large/006tNbRwly1g9w3bpvu3xj31b603gaa7.jpg)

![img](https://tva1.sinaimg.cn/large/006tNbRwly1g9w3bwdt6aj31b80bwq3j.jpg)

![img](https://tva1.sinaimg.cn/large/006tNbRwly1g9w3c2fjbvj31b202y0sv.jpg)

![img](https://tva1.sinaimg.cn/large/006tNbRwly1g9w3c923rbj31eg0u0gqe.jpg)

- 多模块开发



### 2.3 封装、继承、多态

### 2.4 多继承以及MRO顺序

#### 2.4.1 单独调用父类方法

```python
# coding=utf-8

print("******多继承使用类名.__init__ 发生的状态******")
class Parent(object):
    def __init__(self, name):
        print('parent的init开始被调用')
        self.name = name
        print('parent的init结束被调用')

class Son1(Parent):
    def __init__(self, name, age):
        print('Son1的init开始被调用')
        self.age = age
        Parent.__init__(self, name)
        print('Son1的init结束被调用')

class Son2(Parent):
    def __init__(self, name, gender):
        print('Son2的init开始被调用')
        self.gender = gender
        Parent.__init__(self, name)
        print('Son2的init结束被调用')

class Grandson(Son1, Son2):
    def __init__(self, name, age, gender):
        print('Grandson的init开始被调用')
        Son1.__init__(self, name, age)  # 单独调用父类的初始化方法
        Son2.__init__(self, name, gender)
        print('Grandson的init结束被调用')

gs = Grandson('grandson', 12, '男')
print('姓名：', gs.name)
print('年龄：', gs.age)
print('性别：', gs.gender)

print("******多继承使用类名.__init__ 发生的状态******\n\n")
```

运行结果：

```python
******多继承使用类名.__init__ 发生的状态******
Grandson的init开始被调用
Son1的init开始被调用
parent的init开始被调用
parent的init结束被调用
Son1的init结束被调用
Son2的init开始被调用
parent的init开始被调用
parent的init结束被调用
Son2的init结束被调用
Grandson的init结束被调用
姓名： grandson
年龄： 12
性别： 男
******多继承使用类名.__init__ 发生的状态******
```

#### 2.4.2 super调用父类方法

```python
print("******多继承使用super().__init__ 发生的状态******")
class Parent(object):
    def __init__(self, name, *args, **kwargs):  # 为避免多继承报错，使用不定长参数，接受参数
        print('parent的init开始被调用')
        self.name = name
        print('parent的init结束被调用')

class Son1(Parent):
    def __init__(self, name, age, *args, **kwargs):  # 为避免多继承报错，使用不定长参数，接受参数
        print('Son1的init开始被调用')
        self.age = age
        super().__init__(name, *args, **kwargs)  # 为避免多继承报错，使用不定长参数，接受参数
        print('Son1的init结束被调用')

class Son2(Parent):
    def __init__(self, name, gender, *args, **kwargs):  # 为避免多继承报错，使用不定长参数，接受参数
        print('Son2的init开始被调用')
        self.gender = gender
        super().__init__(name, *args, **kwargs)  # 为避免多继承报错，使用不定长参数，接受参数
        print('Son2的init结束被调用')

class Grandson(Son1, Son2):
    def __init__(self, name, age, gender):
        print('Grandson的init开始被调用')
        # 多继承时，相对于使用类名.__init__方法，要把每个父类全部写一遍
        # 而super只用一句话，执行了全部父类的方法，这也是为何多继承需要全部传参的一个原因
        # super(Grandson, self).__init__(name, age, gender)
        super().__init__(name, age, gender)
        print('Grandson的init结束被调用')

print(Grandson.__mro__)

gs = Grandson('grandson', 12, '男')
print('姓名：', gs.name)
print('年龄：', gs.age)
print('性别：', gs.gender)
print("******多继承使用super().__init__ 发生的状态******\n\n")
```

运行结果：

```python
******多继承使用super().__init__ 发生的状态******
(<class '__main__.Grandson'>, <class '__main__.Son1'>, <class '__main__.Son2'>, <class '__main__.Parent'>, <class 'object'>)
Grandson的init开始被调用
Son1的init开始被调用
Son2的init开始被调用
parent的init开始被调用
parent的init结束被调用
Son2的init结束被调用
Son1的init结束被调用
Grandson的init结束被调用
姓名： grandson
年龄： 12
性别： 男
******多继承使用super().__init__ 发生的状态******
```

## 3 上下文管理器、魔法属性

### 3.1 上下文管理器

任何实现了 __enter__() 和 __exit__() 方法的对象都可称之为上下文管理器，上下文管理器对象可以使用 with 关键字。显然，文件（file）对象也实现了上下文管理器。

```python
class File():

    def __init__(self, filename, mode):
        self.filename = filename
        self.mode = mode

    def __enter__(self):
        print("entering")
        self.f = open(self.filename, self.mode)
        return self.f

    def __exit__(self, *args):
        print("will exit")
        self.f.close()
```

__enter__() 方法返回资源对象，这里就是你将要打开的那个文件对象，__exit__() 方法处理一些清除工作。

因为 File 类实现了上下文管理器，现在就可以使用 with 语句了。

```python
with File('out.txt', 'w') as f:
    print("writing")
    f.write('hello, python')
```

这样，你就无需显示地调用 close 方法了，由系统自动去调用，哪怕中间遇到异常 close 方法也会被调用。

实现上下文管理器的另外方式

Python 还提供了一个 contextmanager 的装饰器，更进一步简化了上下文管理器的实现方式。通过 yield 将函数分割成两部分，yield 之前的语句在 __enter__ 方法中执行，yield 之后的语句在 __exit__ 方法中执行。紧跟在 yield 后面的值是函数的返回值。

```python
from contextlib import contextmanager

@contextmanager
def my_open(path, mode):
    f = open(path, mode)
    yield f
    f.close()
```

调用

```
with my_open('out.txt', 'w') as f:
    f.write("hello , the simplest context manager")
```

### 3.2 魔法属性

#### 3.2.1 __doc__

- 表示类的描述信息

```python
class Foo:
    """ 描述类信息，这是用于看片的神奇 """
    def func(self):
        pass

print(Foo.__doc__)
#输出：类的描述信息
```

#### 3.2.2  __module__ 和 __class__

- __module__ 表示当前操作的对象在那个模块
- __class__ 表示当前操作的对象的类是什么

`test.py`

```python
# -*- coding:utf-8 -*-

class Person(object):
    def __init__(self):
        self.name = 'laowang'
```

`main.py`

```python
from test import Person

obj = Person()
print(obj.__module__)  # 输出 test 即：输出模块
print(obj.__class__)  # 输出 test.Person 即：输出类
```

#### 3.2.3 __del__

- 当对象在内存中被释放时，自动触发执行。

注：此方法一般无须定义，因为Python是一门高级语言，程序员在使用时无需关心内存的分配和释放，因为此工作都是交给Python解释器来执行，所以，__del__的调用是由解释器在进行垃圾回收时自动触发执行的。

```python
class Foo:
    def __del__(self):
        pass
```

#### 3.2.4 __call__

- 对象后面加括号，触发执行。

注：__init__方法的执行是由创建对象触发的，即：`对象 = 类名()` ；而对于 __call__ 方法的执行是由对象后加括号触发的，即：`对象()` 或者 `类()()`

```python
class Foo:
    def __init__(self):
        pass

    def __call__(self, *args, **kwargs):
        print('__call__')


obj = Foo()  # 执行 __init__
obj()  # 执行 __call__
```

#### 3.2.5 __dict__

- 类或对象中的所有属性

类的实例属性属于对象；类中的类属性和方法等属于类，即：

```python
class Province(object):
    country = 'China'

    def __init__(self, name, count):
        self.name = name
        self.count = count

    def func(self, *args, **kwargs):
        print('func')

# 获取类的属性，即：类属性、方法、
print(Province.__dict__)
# 输出：{'__dict__': <attribute '__dict__' of 'Province' objects>, '__module__': '__main__', 'country': 'China', '__doc__': None, '__weakref__': <attribute '__weakref__' of 'Province' objects>, 'func': <function Province.func at 0x101897950>, '__init__': <function Province.__init__ at 0x1018978c8>}

obj1 = Province('山东', 10000)
print(obj1.__dict__)
# 获取 对象obj1 的属性
# 输出：{'count': 10000, 'name': '山东'}

obj2 = Province('山西', 20000)
print(obj2.__dict__)
# 获取 对象obj1 的属性
# 输出：{'count': 20000, 'name': '山西'}
```

#### 3.2.6 __str__

- 如果一个类中定义了__str__方法，那么在打印 对象 时，默认输出该方法的返回值。

```python
class Foo:
    def __str__(self):
        return 'laowang'


obj = Foo()
print(obj)
# 输出：laowang
```

#### 3.2.7 __getitem__、__setitem__、__delitem__

- 用于索引操作，如字典。以上分别表示获取、设置、删除数据

```python
# -*- coding:utf-8 -*-

class Foo(object):

    def __getitem__(self, key):
        print('__getitem__', key)

    def __setitem__(self, key, value):
        print('__setitem__', key, value)

    def __delitem__(self, key):
        print('__delitem__', key)


obj = Foo()

result = obj['k1']      # 自动触发执行 __getitem__
obj['k2'] = 'laowang'   # 自动触发执行 __setitem__
del obj['k1']           # 自动触发执行 __delitem__
```

#### 3.2.8 __getslice__、__setslice__、__delslice__

- 该三个方法用于分片操作，如：列表

```python
# -*- coding:utf-8 -*-

class Foo(object):

    def __getslice__(self, i, j):
        print('__getslice__', i, j)

    def __setslice__(self, i, j, sequence):
        print('__setslice__', i, j)

    def __delslice__(self, i, j):
        print('__delslice__', i, j)

obj = Foo()

obj[-1:1]                   # 自动触发执行 __getslice__
obj[0:1] = [11,22,33,44]    # 自动触发执行 __setslice__
del obj[0:2]                # 自动触发执行 __delslice__
```

## 4 GIL锁

> 描述Python GIL的概念， 以及它对python多线程的影响？编写一个多线程抓取网页的程序，并阐明多线程抓取程序是否可比单线程性能有提升，并解释原因。

解答：

> 1. Python语言和GIL没有半毛钱关系。仅仅是由于历史原因在Cpython虚拟机(解释器)，难以移除GIL。
> 2. GIL：全局解释器锁。每个线程在执行的过程都需要先获取GIL，保证同一时刻只有一个线程可以执行代码。
> 3. 线程释放GIL锁的情况： 在IO操作等可能会引起阻塞的system call之前,可以暂时释放GIL,但在执行完毕后,必须重新获取GIL Python 3.x使用计时器（执行时间达到阈值后，当前线程释放GIL）或Python 2.x，tickets计数达到100
> 4. Python使用多进程是可以利用多核的CPU资源的。
> 5. 多线程爬取比单线程性能有提升，因为遇到IO阻塞会自动释放GIL锁

计算密集型：进程

I/O密集型：线程、协程