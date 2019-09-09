# 02-使用 style 来配置 pyplot 风格

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

# 03-处理文本（基础）

