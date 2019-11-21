# 1简介

## 1.1 字符参数

表示颜色的字符参数有：

| 字符  | 颜色          |
| ----- | ------------- |
| `‘b’` | 蓝色，blue    |
| `‘g’` | 绿色，green   |
| `‘r’` | 红色，red     |
| `‘c’` | 青色，cyan    |
| `‘m’` | 品红，magenta |
| `‘y’` | 黄色，yellow  |
| `‘k’` | 黑色，black   |
| `‘w’` | 白色，white   |

表示类型的字符参数有：

| 字符        | 类型       | 字符   | 类型      |
| ----------- | ---------- | ------ | --------- |
| `  '-'	` | 实线       | `'--'` | 虚线      |
| `'-.'`      | 虚点线     | `':'`  | 点线      |
| `'.'`       | 点         | `','`  | 像素点    |
| `'o'`       | 圆点       | `'v'`  | 下三角点  |
| `'^'`       | 上三角点   | `'<'`  | 左三角点  |
| `'>'`       | 右三角点   | `'1'`  | 下三叉点  |
| `'2'`       | 上三叉点   | `'3'`  | 左三叉点  |
| `'4'`       | 右三叉点   | `'s'`  | 正方点    |
| `'p'`       | 五角点     | `'*'`  | 星形点    |
| `'h'`       | 六边形点1  | `'H'`  | 六边形点2 |
| `'+'`       | 加号点     | `'x'`  | 乘号点    |
| `'D'`       | 实心菱形点 | `'d'`  | 瘦菱形点  |
| `'_'`       | 横线点     |        |           |

## 1.2 显示范围

与 **`MATLAB`** 类似，这里可以使用 `axis` 函数指定坐标轴显示的范围：

    plt.axis([xmin, xmax, ymin, ymax])

```python
plt.plot([1,2,3,4], [1,4,9,16], 'ro')
# 指定 x 轴显示区域为 0-6，y 轴为 0-20
plt.axis([0,6,0,20])
plt.show()
```

- 使用 plt.plot() 的返回值来设置线条属性

`plot` 函数返回一个 `Line2D` 对象组成的列表，每个对象代表输入的一对组合，例如：

​		- line1, line2 为两个 Line2D 对象

`line1, line2 = plt.plot(x1, y1, x2, y2)`

​		- 返回 3 个 Line2D 对象组成的列表

`lines = plt.plot(x1, y1, x2, y2, x3, y3)`

我们可以使用这个返回值来对线条属性进行设置：

```python
# 加逗号 line 中得到的是 line2D 对象，不加逗号得到的是只有一个 line2D 对象的列表
line, = plt.plot(x, y, 'r-')

# 将抗锯齿关闭
line.set_antialiased(False)

plt.show()
```

- plt.setp() 修改线条性质

  ```python
  lines = plt.plot(x, y)
  
  # 使用键值对
  plt.setp(lines, color='r', linewidth=2.0)
  
  # 或者使用 MATLAB 风格的字符串对
  plt.setp(lines, 'color', 'r', 'linewidth', 2.0)
  
  plt.show()
  ```

## 1.3 子图

`figure()` 函数会产生一个指定编号为 `num` 的图：

    plt.figure(num)

这里，`figure(1)` 其实是可以省略的，因为默认情况下 `plt` 会自动产生一幅图像。

使用 `subplot` 可以在一副图中生成多个子图，其参数为：

    plt.subplot(numrows, numcols, fignum)

当 `numrows * numcols < 10` 时，中间的逗号可以省略，因此 `plt.subplot(211)` 就相当于 `plt.subplot(2,1,1)`。

```python
def f(t):
    return np.exp(-t) * np.cos(2*np.pi*t)

t1 = np.arange(0.0, 5.0, 0.1)
t2 = np.arange(0.0, 5.0, 0.02)

plt.figure(1)
plt.subplot(212)
plt.plot( t1, f(t1), 'bo',t2, f(t2), 'k')

plt.subplot(211)
plt.plot(t2, np.cos(2*np.pi*t2), 'r--')
plt.show()
```

------

# 2 使用 style 来配置 pyplot 风格

- 查看预设风格

  ```python
  plt.style.available
  ```

- 实用某种风格

  ```python
  plt.style.use('ggplot')
  ```

- 不改变全剧风格，暂时改变下分隔

  ```python
  with plt.style.context(('dark_background')):
  ```

- 混搭风格，最后边的风格覆盖最左边的

  ```python
  plt.style.use(['dark_background', 'ggplot'])
  
  plt.plot(x, y, 'r-o')
  plt.show()
  ```

- 自定义

  自定义文件需要放在 matplotlib 的配置文件夹 mpl_configdir 的子文件夹 mpl_configdir/stylelib/ 下，以 .mplstyle 结尾

  查看`mpl_configdir`位置

  ```python
  import matplotlib
  matplotlib.get_configdir()
  ```

------

# 3 处理文本

## 3.1 基础

## 3.2 数学表达式

### 3.2.1 字符串

```python
plt.title(r'$\alpha > \beta$')
```

### 3.2.2 上下标

使用 `_` 和 `^` 表示上下标：

$\alpha_i > \beta_i$：

    r'$\alpha_i > \beta_i$'

$\sum\limits_{i=0}^\infty x_i$：

    r'$\sum_{i=0}^\infty x_i$'

注：

- 希腊字母和特殊符号可以用 '\ + 对应的名字' 来显示
- `{}` 中的内容属于一个部分；要打出花括号是需要使用 `\{\}`

### 3.2.3 分数，二项式系数，stacked numbers

$\frac{3}{4}, \binom{3}{4}, \stackrel{3}{4}$：

    r'$\frac{3}{4}, \binom{3}{4}, \stackrel{3}{4}$'

$\frac{5 - \frac{1}{x}}{4}$：

    r'$\frac{5 - \frac{1}{x}}{4}$'

在 Tex 语言中，括号始终是默认的大小，如果要使括号大小与括号内部的大小对应，可以使用 `\left` 和 `\right` 选项：

$(\frac{5 - \frac{1}{x}}{4})$

    r'$(\frac{5 - \frac{1}{x}}{4})$'

$\left(\frac{5 - \frac{1}{x}}{4}\right)$：

    r'$\left(\frac{5 - \frac{1}{x}}{4}\right)$'

### 3.2.4 根号

$\sqrt{2}$：

    r'$\sqrt{2}$'

$\sqrt[3]{x}$：

    r'$\sqrt[3]{x}$'

### 3.2.5 特殊字体

默认显示的字体是斜体，不过可以使用以下方法显示不同的字体：

| 命令                  | 显示                    |
| --------------------- | ----------------------- |
| \mathrm{Roman}        | $\mathrm{Roman}$        |
| \mathit{Italic}       | $\mathit{Italic}$       |
| \mathtt{Typewriter}   | $\mathtt{Typewriter}$   |
| \mathcal{CALLIGRAPHY} | $\mathcal{CALLIGRAPHY}$ |
| \mathbb{blackboard}   | $\mathbb{blackboard}$   |
| \mathfrak{Fraktur}    | $\mathfrak{Fraktur}$    |
| \mathsf{sansserif}    | $\mathsf{sansserif}$    |

$s(t) = \mathcal{A}\ \sin(2 \omega t)$：

    s(t) = \mathcal{A}\ \sin(2 \omega t)

注：

- Tex 语法默认忽略空格，要打出空格使用 `'\ '`
- \sin 默认显示为 Roman 字体

### 3.2.6 音调

| 命令              | 结果              |
| ----------------- | ----------------- |
| `\acute a`        | $\acute a$        |
| `\bar a`          | $\bar a$          |
| `\breve a`        | $\breve a$        |
| `\ddot a`         | $\ddot a$         |
| `\dot a`          | $\dot a$          |
| `\grave a`        | $\grave a$        |
| `\hat a`          | $\hat a$          |
| `\tilde a`        | $\tilde a$        |
| `\4vec a`         | $\vec a$          |
| `\overline{abc}`  | $\overline{abc}$  |
| `\widehat{xyz}`   | $\widehat{xyz}$   |
| `\widetilde{xyz}` | $\widetilde{xyz}$ |

------

## 4 注释

