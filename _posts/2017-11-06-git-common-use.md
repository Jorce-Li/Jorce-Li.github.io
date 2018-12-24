---
layout: post
title: "Git常用命令"
subtitle: ''
author: "周欢"
header-style: text
tags:
  - Git
---

`git remote` 查看已经配置的远程仓库服务器

`git remote -v` 显示需要读写远程仓库使用的 Git 保存的简写与其对应的 URL

`git remote add <shortname> <url>` 添加一个新的远程 Git 仓库，同时指定一个别名

`git branch -vv` 查看所有本地分支的跟踪分支

`git checkout -b [branch] [remotename]/[branch]` 创建本地分支跟踪远程分支

`git tag` 查看所有的标签

`git tag -d tagName` 删除某个标签

`git tag -a newName -m"annotate"` 创建一个带注释的标签

`git tag newName` 创建一个轻量级的标签

`git checkout tagName` 切换到某个标签

可以按照特定的表达式搜索某些标签：

```bash
$ git tag -l v1.4.2.*
v1.4.2.1
v1.4.2.2
v1.4.2.3
v1.4.2.4
```

`git push --tags` 默认情况下，`git push`不会将标签上传到远程服务器，为了共享这些标签，须在`git push`后加`--tags`选项

`git stash` 暂存区和工作区的状态都会被保存

`git show tagName` 查看该标签的信息

**tag实际上指向的是一次commit，show tagName命令展示出来的diff也是该次commit的diff**

`git config user.name` 查看本地用户名

`git config user.email` 查看本地用户邮箱地址

`git config --global user.name "username"` 修改本地用户名

`git config --global user.email "email-address"` 修改本地用户邮箱地址

如果发现将某些文件加入 gitignore 的忽略规则之后未生效，原因可能是：.gitignore只能忽略那些原来没有被track的文件，如果某些文件已经被纳入了版本管理中，则修改.gitignore文件是无效的，解决方法就是把本地缓存删除（改变成未track状态），然后再提交：
```bash
git rm -r --cached .
git add .
git commit -m "update .gitignore"
```