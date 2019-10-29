# Git和Github

### 前提（基础）

- GitHub注册和基本的GitHub操作（创建仓库, Fellow, Stars等）
- 会用git clone http://xxx.git 克隆项目到本地

### 基本操作

- 克隆项目(对于新注册的GitHub用户可能会遇到无法clone问题，这就需要添加一个SSH Keys)

  `git clone https://github.com/Prime-Number/whm.github.io.git`

- 查看当前文件夹状态

  `git status`

![1572360399862](C:\Users\Henry.Wu\AppData\Roaming\Typora\typora-user-images\1572360399862.png)

上图说明，文件到“暂存区”，还没有commit, 经过commit 后就可以管理了。

- 推送到远程仓库

  `git push [alias] [branch]`

  example:

  `touch runoob-text.txt 		# 添加文件
  git add runoob-text.txt
  git commit -m "添加到远程"
  git push origin master 		# 推送到Github`
  

  