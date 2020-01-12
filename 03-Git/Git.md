# Git

## 1 版本创建与回退

### 1.1 使用

1. 现在若想回到某一个版本，可以使用如下命令：

```bash
        git reset --hard HEAD^
```

其中HEAD表示当前最新版本，HEAD^表示当前版本的前一个版本,HEAD^^表示当前版本的前前个版本，也可以使用HEAD~1表示当前版本的前一个版本,HEAD~100表示当前版本的前100版本。

现在若觉得想回到版本1，可以使用如下命令：

![image-20191107183330280](https://tva1.sinaimg.cn/large/006y8mN6ly1g8pnoc3643j30mm08yqj2.jpg)

执行命令后使用git log查看版本记录，发现现在只能看到版本1的记录，cat code.txt查看文件内容，现在只有一行，也就是第一个版本中code.txt的内容。

2.  假如我们现在又想回到版本2，这个时候怎么办？

可以使用如下命令：

```
        git reset --hard 版本号
```

从上面可以看到版本2的版本号为：

![image-20191107185006500](https://tva1.sinaimg.cn/large/006y8mN6ly1g8po5kb6bwj30l809y4dq.jpg)

3. 终端关闭回退

```
git reflog
```

### 1.2 撤销修改

```
git checkout -- <文件>
```

![image-20191107192351000](https://tva1.sinaimg.cn/large/006y8mN6ly1g8pp4nyp98j30kq096qis.jpg)

![image-20191107192405424](https://tva1.sinaimg.cn/large/006y8mN6ly1g8pp4x7p0rj30n208g17v.jpg)

### 1.3 对比文件的不同

## 2 分支管理

### 2.1 创建与合并分支

查看分支：git branch

创建分支：git branch <name>

切换分支：git checkout <name>

创建+切换分支：git checkout -b <name>

合并某分支到当前分支：git merge <name>

删除分支：git branch -d <name>

### 2.2 解决冲突

