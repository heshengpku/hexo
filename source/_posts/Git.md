---
title: Git命令整理
catagory: Tools
tags: 
 - Git 
 - GitHub 
 - GitLab
date: 2017-01-01 13:08
---

<!--more-->

## Git初始化

Command line instructions
本地Git全局设置（Git global setup）

```shell
git config --global user.name "yourname"
git config --global user.email "your@email"
```

克隆新仓库（Create a new repository）

```shell
git clone http://your.repository.address.git
cd repo
touch README.md
git add README.md
git commit -m "add README"
git push -u origin master
```

对于已经存在的文件夹（Existing folder）

```shell
cd existing_folder
git init
git remote add origin http://your.repository.address.git
git add .
git commit -m "Initial commit"
git push -u origin master
```

对于本地已经存在的仓库（Existing Git repository）

```shell
cd existing_repo
git remote add origin http://your.repository.address.git
git push -u origin --all
git push -u origin --tags
```

## Git忽略的文件

### 1. 查看所有被Git忽略的文件

Git 1.6+:

```shell
git ls-files --others -i --exclude-standard
```

Git 1.4, 1.5:

```shell
git ls-files --others -i \
--exclude-from="`git rev-parse --git-dir`/info/exclude" \
--exclude-per-directory=.gitignore
```

### 2. 清除所有被Git忽略的文件或文件夹(小心)

- 查看在清理之前会做的操作

```shell
git clean -Xn
```

- 清除文件或文件夹， -f 选项强制删除，-d删除目录（小心）

```shell
git clean -Xdf
```

## Git变更远程仓库地址

### 1. 通过命令直接修改远程地址

```shell
git remote // 查看所有远程仓库
git remote set-url origin http://your.repository.address.git
```

### 2. 通过命令先删除再添加远程仓库

```shell
git remote // 查看所有远程仓库
git remote rm origin
git remote add origin http://your.repository.address.git
```

## Git撤销已同步的远程提交

先在本地回退到指定版本：

```shell
git reflog // 查看commits记录
git reset --hard <版本号> // 注意使用 --hard 参数会抛弃当前工作区的修改
// git reset HEAD~1 // 撤回至上一个commit
git push origin <分支名> --force
```
