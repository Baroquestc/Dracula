# 42-虚拟环境(创建/激活/操作/删除-virtualenv、Pycharm、virtualenvwrapper、pipenv)

标签（空格分隔）： python

---

## 1 安装
安装命令：pip install virtualenv

## 2 使用
### 2.1 创建一个局部的隔离的虚拟环境
#### 2.1.1 创建命令：virtualenv 虚拟环境名称
```python
virtualenv ENV
```

 - cd 到存放虚拟环境的的地址
 - 执行命令virtualenv ENV，创建名为ENV 的虚拟环境
```python

```

#### 2.1.2 指定 Python 版本创建虚拟环境
可选参数：-p
指定 Python2.x 版本创建虚拟环境
```python
virtualenv -p /usr/local/bin/python2.7 ENV
```

#### 2.1.3 是否继承系统三方库 `--system-site-packages`
添加参数--system-site-packages，项目检索库的时候，也会到系统的三方库中找
不添加时，默认只到虚拟环境中查找库
```python
virtualenv --system-site-packages ENV
```

### 2.2 激活/退出 虚拟环境

 - cd 到虚拟环境文件夹目录
 - 执行命令 source bin/activate，shell 会打印出已激活的虚拟环境名称
 - 验证当前确实在已激活的虚拟环境中，执行命令 pip --version 和 pip list
 - 退出虚拟环境，在激活的虚拟环境中，执行命令 deactivate
 - 验证已经退出，执行命令 pip --version 和 pip list
 
### 2.3 项目交接
 - 冻结项目需求文本 pip freeze > requirements.txt
 - 根据需求文本，安装项目依赖库 pip install -r requirements.txt （在激活的虚拟环境中）
 
## 3 虚拟环境进阶使用
### 3.1  集中式虚拟环境管理
 1. 通过virtualenvwrapper创建的虚拟环境都会统一存放到指定位置统一管理
 2. 方便在多个虚拟环境间切换
 3. 更加方便的去使用 virtualenv
 
* 更加方便的去使用 virtualenv
mkvirtualenv venv1
* 快速在多个虚拟环境中切换
workon venv2
* 快速关闭虚拟环境
deactivate
* 快速删除虚拟环境
rmvirtualenv env1
* 查看所有创建的虚拟环境
lsvirtualenv

### 3.2  更加基于项目的虚拟环境管理
#### 3.2.1 pipenv 使用优点

 - pipenv，是 pip + virtualenv 结合体，解决了virtualenvwrapper弊端
 - 自动帮你创建虚拟环境，以及安装三方库
 - 自动的记录你的项目依赖的所有三方库
 - 使用 pipfile 和 pipfile.lock取代了 requirements.txt

#### 3.2.2 使用步骤


 1. 创建虚拟环境
```python
 1. cd 到项目文件夹目录下
 2. 在该目录下执行命令
pipenv --two # 指定参照Python版本
或
pipenv --three

 3. 查看相关信息
查看位置：pipenv --where
查看虚拟环境位置：pipenv --venv
查看解析器信息：pipenv --py
```

 2. 激活虚拟环境

```python
pipenv shell
```
 1. 在激活状态下操作
 安装包
pipenv install requests
注意：不要使用 pip install 命令，使用的话虽然可以在虚拟环境中安装对应的包，但是不会更新 PIPfile 和 PIPfile.lock
 
 2. 列表项