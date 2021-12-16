# Git

## 1 版本创建与回退

### 1.1 版本回退

1. 现在若想回到某一个版本，可以使用如下命令：

```bash
        git reset --hard HEAD^
```

其中HEAD表示当前最新版本，HEAD^表示当前版本的前一个版本,HEAD^^表示当前版本的前前个版本，也可以使用HEAD~1表示当前版本的前一个版本,HEAD~100表示当前版本的前100版本。

2.  回退

可以使用如下命令：

```bash
        git reset --hard 版本号
```

3. 终端关闭回退

```bash
git reflog
```

### 1.2 撤销修改

- 当你改乱了`工作区`某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。

- 当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD `，就回到了场景1，第二步按场景1操作。

- 已经提交了不合适的修改到版本库时，想要撤销本次提交，参考[版本回退](https://www.liaoxuefeng.com/wiki/896043488029600/897013573512192)一节，不过前提是没有推送到远程库。

### 1.3 删除文件

- `rm 文件名` -> `git rm 文件名` -> `git commit -m xxx`
- 误删：`git checkout --文件名`

## 2 分支管理

### 2.1 创建与合并分支

查看分支：git branch

```bash
# 查看远程分支
git branch -r
# 查看本地和远程分支
git branch -a
```

创建分支：git branch <name>

切换分支：git checkout <name>

创建+切换分支：git checkout -b <name>

合并某分支到当前分支：git merge <name>

删除分支：git branch -d <name>

### 2.2 解决冲突

当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。

解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。

用`git log --graph`命令可以看到分支合并图

### 2.3 分支管理策略

通常，合并分支时，如果可能，Git会用`Fast forward`模式，但这种模式下，删除分支后，会丢掉分支信息。

如果要强制禁用`Fast forward`模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。

```bash
git merge --no-ff -m "merge with no-ff" dev
```

`-m`参数，把commit描述写进去。

### 2.4 Bug分支

多次stash，恢复的时候，先用`git stash list`查看，然后恢复指定的stash，用命令：

```bash
git stash apply stash@{0}
```

修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；

当手头工作没有完成时，先把工作现场`git stash`一下，然后去修复bug，修复后，再`git stash pop`，回到工作现场；

在master分支上修复的bug，想要合并到当前dev分支，可以用`git cherry-pick `命令，把bug提交的修改“复制”到当前分支，避免重复劳动。

### 2.5 Feature分支

软件开发中，总有无穷无尽的新的功能要不断添加进来。

添加一个新功能时，你肯定不希望因为一些实验性质的代码，把主分支搞乱了，所以，每添加一个新功能，最好新建一个feature分支，在上面开发，完成后，合并，最后，删除该feature分支。

现在，你终于接到了一个新任务：开发代号为Vulcan的新功能，该功能计划用于下一代星际飞船。

于是准备开发：

```bash
git switch -c feature-vulcan
Switched to a new branch 'feature-vulcan'
```

5分钟后，开发完毕：

```bash
git add vulcan.py

git status
On branch feature-vulcan
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   vulcan.py

$ git commit -m "add feature vulcan"
[feature-vulcan 287773e] add feature vulcan
 1 file changed, 2 insertions(+)
 create mode 100644 vulcan.py
```

切回`dev`，准备合并：

```bash
git switch dev
```

一切顺利的话，feature分支和bug分支是类似的，合并，然后删除。

但是！

就在此时，接到上级命令，因经费不足，新功能必须取消！

虽然白干了，但是这个包含机密资料的分支还是必须就地销毁：

```bash
git branch -d feature-vulcan
error: The branch 'feature-vulcan' is not fully merged.
If you are sure you want to delete it, run 'git branch -D feature-vulcan'.
```

销毁失败。Git友情提醒，`feature-vulcan`分支还没有被合并，如果删除，将丢失掉修改，如果要强行删除，需要使用大写的`-D`参数。。

现在我们强行删除：

```bash
git branch -D feature-vulcan
Deleted branch feature-vulcan (was 287773e).
```

### 2.6 多人协作

默认远程仓库`origin`。

查看远程库信息，用`git remote`：

```bash
git remote
origin
```

`git remote -v`显示更详细信息：

上面显示了可以抓取和推送的`origin`的地址。如果没有推送权限，就看不到push的地址。

- `推送分支`

推送分支，就是把该分支上的所有本地提交推送到远程库。推送时，要指定本地分支，这样，Git就会把该分支推送到远程库对应的远程分支上：

```bash
git push origin master
```

如果要推送其他分支，比如`dev`，就改成：

```bash
git push origin dev
```

- `抓取分支`

从远程库clone时,默认只能看到`master`分支

在dev分支开发

```bash
git checkout -b dev origin/dev
```

分开开发冲突时，先用`git pull`把最新的提交从`origin/dev`抓下来，然后，在本地合并，解决冲突，再推送：

```bash
$ git pull
There is no tracking information for the current branch.
Please specify which branch you want to merge with.
See git-pull(1) for details.

    git pull <remote> <branch>

If you wish to set tracking information for this branch you can do so with:

    git branch --set-upstream-to=origin/<branch> dev
```

`git pull`也失败了，原因是没有指定本地`dev`分支与远程`origin/dev`分支的链接，根据提示，设置`dev`和`origin/dev`的链接：

```bash
$ git branch --set-upstream-to=origin/dev dev
```

再pull

因此，多人协作的工作模式通常是这样：

1. 首先，可以试图用`git push origin `推送自己的修改；
2. 如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；
3. 如果合并有冲突，则解决冲突，并在本地提交；
4. 没有冲突或者解决掉冲突后，再用`git push origin `推送就能成功！

如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to  origin/`。

这就是多人协作的工作模式，一旦熟悉了，就非常简单。

- 查看远程库信息，使用`git remote -v`；
- 本地新建的分支如果不推送到远程，对其他人就是不可见的；
- 从本地推送分支，使用`git push origin branch-name`，如果推送失败，先用`git pull`抓取远程的新提交；
- 在本地创建和远程分支对应的分支，使用`git checkout -b branch-name origin/branch-name`，本地和远程分支的名称最好一致；
- 建立本地分支和远程分支的关联，使用`git branch --set-upstream branch-name origin/branch-name`；
- 从远程抓取分支，使用`git pull`，如果有冲突，要先处理冲突。

### 2.7 rebase

- rebase操作可以把本地未push的分叉提交历史整理成直线；
- rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。

## 3 标签管理

### 3.1 创建标签

- 命令`git tag `用于新建一个标签(默认标签是打在最新提交的commit)，默认为`HEAD`，也可以指定一个commit id；
- 命令`git tag -a  -m "blablabla..." commit id`可以指定标签信息；
- 命令`git tag`可以查看所有标签。
- 命令`git show tag号`查看tag详细信息

### 3.2 操作标签

- 命令`git push origin v1.0`可以推送一个本地标签；
- 命令`git push origin --tags`可以推送全部未推送过的本地标签；
- 命令`git tag -d `可以删除一个本地标签；
- 命令`git push origin :refs/tags/`可以删除一个远程标签。

## 4 [git submodule添加、更新和删除 ](https://www.cnblogs.com/jyroy/p/14367776.html)

### 4.1 添加

`git submodule add <url> <path>` 

-   url：替换为自己要引入的子模块仓库地址
-   path：要存放的本地路径

执行添加命令成功后，可以在当前路径中看到一个.gitsubmodule文件，里面的内容就是我们刚刚add的内容

如果在添加子模块的时候想要指定分支，可以利用 -b 参数

`git submodule add -b <branch> <url> <path>` 

例子

未指定分支

`git submodule add https://github.com/tensorflow/benchmarks.git 3rdparty/benchmarks` 

.gitsubmodule内容

```bash
[submodule "3rdparty/benchmarks"]
	path = 3rdparty/benchmarks
	url = https://github.com/tensorflow/benchmarks.git
```

指定分支

```bash
git submodule add -b cnn_tf_v1.10_compatible https://github.com/tensorflow/benchmarks.git 3rdparty/benchmarks
```

.gitsubmodule内容

```bash
[submodule "3rdparty/benchmarks"]
	path = 3rdparty/benchmarks
	url = https://github.com/tensorflow/benchmarks.git
	branch = cnn_tf_v1.10_compatible
```

使用

当我们add子模块之后，会发现文件夹下没有任何内容。这个时候我们需要再执行下面的指令添加源码。

```bash
git submodule update --init --recursive
```

这个命令是下面两条命令的合并版本

```bash
git submodule init
git submodule update
```

### 4.2 更新

我们引入了别人的仓库之后，如果该仓库作者进行了更新，我们需要手动进行更新。即进入子模块后，执行

`git pull` 

进行更新。

### 4.3 删除

1.  删除子模块目录及源码

`rm -rf 子模块目录` 

2.  删除.gitmodules中的对应子模块内容

`vi .gitmodules` 

3.  删除.git/config配置中的对应子模块内容

`vi .git/config` 

4.  删除.git/modules/下对应子模块目录

`rm -rf .git/modules/子模块目录` 

5.  删除git索引中的对应子模块

`git rm --cached 子模块目录`