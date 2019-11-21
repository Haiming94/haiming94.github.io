# Git和Github

### 前提（基础）

- GitHub注册和基本的GitHub操作（创建仓库, Fellow, Stars等）
- 会用git clone http://xxx.git 克隆项目到本地

### 基本操作

- 克隆项目(对于新注册的GitHub用户可能会遇到无法clone问题，这就需要添加一个SSH Keys)

  `git clone https://github.com/Prime-Number/whm.github.io.git`

- 查看当前文件夹状态

  `git status`

![1572360399862](.\images\1572360399862.png)

上图说明，文件到“暂存区”，还没有commit, 经过commit 后就可以管理了。

- 推送到远程仓库

  `git push [alias] [branch]`

  example:

  `touch runoob-text.txt 		# 添加文件
  
  git add runoob-text.txt
  
  git commit -m "添加到远程"
  
  git push origin master 		# 推送到Github`

### 有效的一次操作

查看当前的远程库

`git remote`

`git remote -v`

提取远程仓库（Git有两个命令用来提取远程仓库的更新）

1、从远程仓库下载新分支与数据：

`git fetch`

该命令执行完后需要执行 `git merge` 远程分支到自己所在的分支。

2、从远端仓库提取数据并尝试合并到当前分支：

`git merge`

该命令就是在执行 `git fetch` 之后紧接着执行 `git merge` 远程分支到你所在的任意分支。

假设配置好了一个远程仓库，并且想要提取更新的数据，可以首先执行 `git fetch [alias]` 告诉 Git 去获取 *它有而自己没有* 的数据，然后可以执行 `git merge [alias]/[branch]` 以将服务器上的任何更新（假设有人这时候推送到服务器了）合并到你的当前分支。

然后在本地进行数据更新。用如下命令更新 master 分支：

`git fetch origin`

然后使用以下命令将更新同步到本地：

`git merge origin/master`

推送到远程仓库，推送自己的新分支与数据到远程端仓库：

`git push [alias] [branch]`

![1572369326665](.\images\1572369326665.png)

  ![1572369272547](.\images\1572369272547.png)


### 参考链接

https://www.runoob.com/git/git-remote-repo.html
