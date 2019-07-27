---
title: Git Submodule
date: 2019-07-26 11:52:43
tags: [Git]
categories: 技术
---

Git是程序员在日常开发中不可分割的强有力工具，有时候我们需要在两个甚至多个仓库间进行同步和公用，需要提取一个公共的类库提供给多个项目使用，或者说某个项目需要包含并使用另一（/多)个项目，这时候可以使用Git Submodule 解决。

子模块允许你将一个 Git 仓库作为另一个 Git 仓库的子目录。 它能让你将另一个仓库克隆到自己的项目中，同时还保持提交的独立。

# 解决问题

- 如何在git项目中导入子库?

- 子库在其他的项目中被修改了可以更新到远程的代码库中?

- 其他项目如何获取到子库最新的提交?

- 如何在clone的时候能够自动导入子库?

# 开始使用Submodule

## 添加Submodule

```bash
git submodule add https://github.com/xxx/XXXX [XXXX位置]
```

> 默认，子模块会将子项目放到一个与仓库同名的目录中。 如果你想要放到其他地方，可以在命令结尾添加一个不同的路径。

```bash
git status

On branch master
Changes to be committed:

    new file:   .gitmodules
    new file:   XXXX
```

### .gitmodules 文件

该配置文件保存了项目 URL 与已经拉取的本地目录之间的映射：

```bash
$ cat .gitmodules
[submodule "XXXX"]
	path = XXXX
	url = https://github.com/xxx/XXXX
```

> 如果有多个子模块，该文件中就会有多条记录。该文件一样受到版本控制

## 修改Submodule

> 首先需要有对Submodule的commit权限。

- 进入Submodule目录：`cd XXXX`
  - 修改内容
  - 查看变动：`git status`
  - 提交更改内容：`git commit`
  - 推送远程：`git push`

- 回到父目录,提交Submodule在父项目中的变动
  - git commit -m'update submodule'
  - git push

## 更新Submodule

- 方式一：在父项目的目录下直接更新

```
git submodule foreach git pull
```

- 方式一：在Submodule的目录下面更新

```
cd xxxx
git pull
```

> 在Submodule目录中使用git如同在一个独立的项目,更新Submodule时如果有新的commit id产生，需要在父项目产生一个新的提交

## 克隆含有Submodule的项目

- 方式一：递归clone整个项目

```
git clone git@github.com:xxx/xxxx.git --recursive
```

- 方式二：先clone父项目，再更新子项目

```
git clone git@github.com:xxx/xxxx.git
cd xxxsubmodule
git submodule init
git submodule update
```

## 更新Submodule

`git submodule update --remote`: Git 将会进入子模块然后抓取并更新

> 此命令默认会假定你想要更新并检出子模块仓库的 master 分支
> 当运行 git submodule update --remote 时，Git 默认会尝试更新所有子模块，所以如果有很多子模块的话，你可以传递想要更新的子模块的名字。

## 更改Submodule的追踪分支

```
// 更改为追踪XXX子模块的xxxBranch分支
git config -f .gitmodules submodule.XXX.branch xxxBranch
```

## 删除Submodule

> git不支持直接删除Submodule,需手动删除:

```
cd xxxsubmodule

git rm --cached xxxsubmodule
rm -rf xxxsubmodule
rm .gitmodules

// 更改git的配置文件config:
vim .git/config
```
