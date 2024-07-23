---
created: 2022-05-30 23:58
modified: 2023-11-18 13:03
tags:
  - Git
cssclasses:
  - embed-strict
---

> [!link]- Document
> [Git 官方文档](https://git-scm.com/book/zh/v2/%E8%B5%B7%E6%AD%A5-%E5%85%B3%E4%BA%8E%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6)
> 
> [一张脑图带你掌握 Git 命令](https://juejin.cn/post/6869519303864123399)
> 
[Git 的一些常用概念](https://mp.weixin.qq.com/s?__biz=MzUzMzQ2MDIyMA==&mid=2247494116&idx=1&sn=2c1a20a694ebe596954b791ac594e27c&sharer_shareinfo=28d5c7d0c557735324d30a1e950711d4&sharer_shareinfo_first=28d5c7d0c557735324d30a1e950711d4#rd)

### Git 工作流

- **工作区**（Working Directory）：在 `git init` 之后的本地的文件目录下，也就是大家写代码的地方
- **暂存区**（Staging Area）：修改了代码之后，需要先将改动 `add` 到暂存区，表示将要提交的改动
- **本地仓库**（Local Repository）：本地 Git 仓库，通俗讲就是本地隐藏文件.git 目录下，存储着你的所有改动
- **远程仓库**（Remote Repository）：远程 Git 仓库，理论上和本地仓库地位平等，但是主要是用于多个开发者之间 `pull`/`push` 代码的仓库

![[Git 常用命令-2.png]]

### Git 文件状态

![[Git 常用命令.png]]

- **UnTracked**: 未跟踪，此文件在文件夹中，但并没有加入到 git 库，不参与版本控制。通过 `git add` 状态变为 Staged。
- **UnModify**: 文件已经入库，未修改， 即版本库中的文件快照内容与文件夹中完全一致。这种类型的文件有两种去处，如果它被修改，而变为 Modified。如果使用 `git rm` 移出版本库，则成为 UnTracked 文件。
- **Modified**: 文件已修改，仅仅是修改，并没有进行其他的操作。这个文件也有两个去处，通过 `git add` 可进入暂存 Staged 状态，使用 `git checkout` 则丢弃修改过，返回到 Unmodify 状态。 `git checkout` 即从库中取出文件，覆盖当前修改。
- **Staged**: 暂存状态，执行 `git commit` 则将修改同步到库中，这时库中的文件和本地文件又变为一致，文件为 UnModify 状态。执行 `git reset` 取消暂存，文件状态为 Modified。

### clone 克隆远程项目

```shell
git clone url
```

### remote 远程项目

查看远程项目名称

```shell
git remote -v
```

将一个新的远程仓库与你的本地仓库关联起来

```shell
git remote add <远程仓库> <远程仓库URL>
```

重命名远程仓库

```shell
git remote rename <旧远程仓库名称> <新远程仓库名称>
```

从本地仓库中移除已配置的远程仓库

```shell
git remote remove <远程仓库>
```

### 查看提交记录

```shell
git log
```

- `回车` 显示下一行
- `q` 退出

查看最近 n 条记录

```shell
git log -n <数量>
```

以简洁的方式显示提交记录

```shell
git log --oneline
```

显示更详细的信息

```shell
git log --stat
```

### 查看项目配置

```shell
git config --local --list
```

### 修改当前项目的 username 和 email

```shell
git config user.name [用户名]
git config user.email [邮箱]
```

打开当前项目配置文件 `.git/config`

![[Git 常用命令-3.png]]

### 修改全局的 username 和 email

```shell
git config --global user.name [用户名]
git config --global user.email [邮箱]
```

打开全局配置文件

```shell
vim ~/.gitconfig
```

### status 查看文件修改状态

```shell
git status
```

### branch 分支管理

查看分支列表

```shell
git branch
```

创建新分支

```shell
git branch <分支名称>
```

删除分支，`-D` 强行删除

```shell
git branch -d <分支名称>
```

查看分支的最后一次提交

```shell
git branch -v
```

### 分支重命名

先切换到其他分支，再重命名

```shell
git branch -m <旧分支名称> <新分支名称>
```

推送到远程仓库

```shell
git push origin -u <新分支名称>
```

如果你在远程仓库中的分支已经存在，则需要使用 `-f` 参数进行强制推送

```shell
git push -f origin <新分支名称>
```

### checkout 切换/恢复文件

切换分支

```shell
git checkout <分支名称>
```

创建新分支并切换

```shell
git checkout -b <新分支名称>
```

切换到指定的提交

如果提供的哈希值足够唯一，即使只提供了提交的**部分哈希值**，Git 也可以识别并切换到相应的提交。

```shell
git checkout <提交哈希值>
```

恢复文件

```shell
git checkout <文件名称>
```

恢复特定提交或分支中的文件版本

```shell
git checkout <提交标识符/分支名称> -- <文件名称>
```

### add 添加所有修改到暂存区

```shell
git add -A
```

> [!link] [git add .，git add -A，git add -u，git add \* 的区别与联系](https://juejin.cn/post/7053831273277554696)

- `git add .`
  - `Git Version 1.x` 提交**当前目录(包括子目录)的**新文件 (new)和已修改 (modified)文件，不包括已删除 (deleted)文件
  - `Git Version 2.x` 提交**当前目录的 (包括子目录)**所有改动
    - `git add --ignore-removal .` 不提交已删除的文件
- `git add *` 添加当前目录(包括子目录)的所有文件改动，不包括以 `.` 开头的文件
- `git add -u` 提交已修改(modified)和已删除(deleted)文件，不包括新文件(new)
- `git add -A` 提交**当前整个工作区的**所有变化

### commit 提交修改到本地仓库

```shell
git commit -m "message"
```

如果没有新文件，可以直接使用 `-a` 参数自动将已追踪文件的修改添加(add)到暂存区(staging)

```shell
git commit -a -m "message"
```

**--amend**

```shell
git commit --amend
```

作用：

- 修改最近一次提交的提交信息
- 合并最新的修改到最近一次提交，不会产生新的提交信息
  1.  使用 `git add` 添加修改在暂存区
  2.  运行 `git commit --amend` 命令

### push 推送

提交修改到【origin】远程仓库的【master】分支

```shell
git push <远程仓库> <本地分支>:<远程分支>
```

将本地分支与远程分支进行关联，并设置将来的推送和拉取操作的默认远程分支

```shell
git push -u <远程仓库> <远程分支>
```

强制 push 到远程，**会覆盖远程仓库中的历史提交记录，慎用!!!**

```shell
git push --force
```

### fetch 获取

从远程仓库获取最新的提交和分支信息，下载到本地仓库，但不会自动合并或修改你的当前工作目录

```shell
git fetch <远程仓库> <远程分支>
```

获取指定分支的最新提交信息

```shell
git fetch <远程仓库> <远程分支>:<本地分支>
```

如果远程仓库中删除了分支，但本地仓库仍然存在相应的远程分支引用，使用  `--prune`  可以将这些无效的分支删除

```shell
git fetch --prune origin
```

### merge 合并

`merge` 用于将不同分支的提交合并到当前分支中

在执行**合并**操作之前，请确保你处于目标分支（通常是接收合并更改的分支）上。合并将会将指定分支的更改应用到当前分支，并在需要时自动解决冲突。

```shell
git merge -m "合并分支feature-branch" <分支名称>
```

**生成**一个新的合并提交（即使没有冲突），保留了合并操作的历史记录，而不仅仅是移动指针

```shell
git merge --no-ff <分支名称>
```

将指定分支的所有提交**压缩**成一个大的提交，并将其应用到当前分支

```shell
git merge --squash <分支名称>
```

**取消**正在进行的合并操作，并返回到合并前的状态

- 任何未完成的合并操作将被取消。
- 未解决的合并冲突将被恢复为合并前的状态。
- 如果有合并提交已经创建，它将被撤销。

```shell
git merge --abort
```

![[ChatGPT#git merge 有哪些策略，分别讲一下|seamless]]

### pull 拉取

执行  `git fetch`  后立即执行合并操作，将远程分支的更改自动合并到当前分支

```shell
git pull <远程仓库> <远程分支>
```

拉取指定分支的最新提交信息

```shell
git pull <远程仓库> <远程分支>:<本地分支>
```

使用 Git 的变基操作（[[git rebase]]）来合并远程分支的更改。它会将本地未提交的更改“移动”到远程分支的顶部，以保持整洁的提交历史。使用  `--rebase`  可以避免产生合并提交（merge commit），使提交历史更加线性和清晰。

```shell
git pull --rebase <远程仓库> <远程分支>
```

### stash 暂存

将保存当前的修改，并将其存储为一个临时的存储区

```shell
git stash
```

将之前保存的修改应用到当前分支上。如果有多个 stash，可以通过指定 stash 的索引来选择应用的 stash

越新的 stash 具有较大的索引值：`stash@{0}`, `stash@{1}`, `stash@{2}`

```shell
git stash apply
```

将之前保存的修改应用到当前分支，并从 stash 列表中移除该 stash

```shell
git stash pop
```

查看当前存储的 stash 列表

```shell
git stash list
```

删除 stash

```shell
git stash drop <stash索引>
```

清空 stash

```shell
git stash clear
```

### tag 标签

列出标签

```shell
git tag
```

创建轻量标签

```shell
git tag <tag名称>
```

创建附注标签

```shell
git tag -a <tag名称> -m "标签说明"
```

切换到标签所指向的提交

```shell
git checkout <tag名称>
```

创建带有签名的标签

使用 `-s` 标志可以创建一个带有 GPG 签名的标签。这种类型的标签提供了更高的安全性，可以用于验证标签的真实性和完整性。

```shell
git tag -s <tag名称> -m "标签说明"
```

删除标签

```shell
git tag -d <tag名称>
```

### reset

### revert

### diff
