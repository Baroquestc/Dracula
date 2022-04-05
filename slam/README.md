
<div align=center>
<img width="500" src="img/cover.png" alt="封面"/>
</div>

[本项目](https://tangshusen.me/Dive-into-DL-PyTorch)将[《动手学深度学习》](http://zh.d2l.ai/) 原书中MXNet代码实现改为PyTorch实现。原书作者：阿斯顿·张、李沐、扎卡里 C. 立顿、亚历山大 J. 斯莫拉以及其他社区贡献者，GitHub地址：https://github.com/d2l-ai/d2l-zh

## 简介
本仓库主要包含code和docs两个文件夹（外加一些数据存放在data中）。其中code文件夹就是每章相关jupyter notebook代码（基于PyTorch）；docs文件夹就是markdown格式的《动手学深度学习》书中的相关内容，然后利用[docsify](https://docsify.js.org/#/zh-cn/)将网页文档部署到GitHub Pages上，由于原书使用的是MXNet框架，所以docs内容可能与原书略有不同，但是整体内容是一样的。欢迎对本项目做出贡献或提出issue。

## 食用方法 
### 方法一
本仓库包含一些latex公式，但github的markdown原生是不支持公式显示的，而docs文件夹已经利用[docsify](https://docsify.js.org/#/zh-cn/)被部署到了GitHub Pages上，所以查看文档最简便的方法就是直接访问[本项目网页版](https://baroquestc.github.io/Dive-into-DL-PyTorch/)。当然如果你还想跑一下运行相关代码的话还是得把本项目clone下来，然后运行code文件夹下相关代码。

### 方法二
你还可以在本地访问文档，先安装`docsify-cli`工具:
``` shell
npm i docsify-cli -g
```
然后将本项目clone到本地:
``` shell
git clone https://github.com/Baroquestc/Dive-into-DL-PyTorch
cd Dive-into-DL-PyTorch
```
然后运行一个本地服务器，这样就可以很方便的在`http://localhost:3000`实时访问文档网页渲染效果。
``` shell
docsify serve docs
```


## 目录
* [简介]()
* 2\. 预备知识
   * [2.1 环境配置](chapter02_prerequisite/2.1_install.md)
   * [2.2 数据操作](chapter02_prerequisite/2.2_tensor.md)
   * [2.3 自动求梯度](chapter02_prerequisite/2.3_autograd.md)