---
title: python初探：pyenv&pipenv
date: 2019-04-10 12:08:31
tags: [pyenv, pipenv]
categories: python
---

本着艺多不压身的革命宗旨，计划从今天开始学习下python。之前有粗略的看过两眼，一直没有系统的学习或者实际的使用，希望能够在业余时间接触下这门已经热火朝天口口相传的编程语言，打开新世界的大门，扩充自己的知识面。

作为第一篇python笔记，从开发环境入手，介绍pipenv与pyenv这两个重要的工具。

#pyenv

> pyenv是Python版本管理工具，利用它我们可以在同一台电脑上安装多个版本的Python. (类比于node版本管理工具nvm)

## Mac安装pyenv

```
// 安装
$ brew install pyenv

// 升级
$ brew upgrade pyenv
```

## pyenv安装Python

```
$ pyenv install 3.7.0 (根据自身需要安装不同版本)
$ pyenv rehash
```

### 可能遇到的报错： 

```
BUILD FAILED (OS X 10.14.3 using python-build 20180424)

Inspect or clean up the working tree at /var/folders/db/sh5sm60124j89wcqhdck3rnm0000gn/T/python-build.20190410120716.2695
Results logged to /var/folders/db/sh5sm60124j89wcqhdck3rnm0000gn/T/python-build.20190410120716.2695.log

Last 10 log lines:
  File "/private/var/folders/db/sh5sm60124j89wcqhdck3rnm0000gn/T/python-build.20190410120716.2695/Python-3.7.0/Lib/ensurepip/__main__.py", line 5, in <module>
    sys.exit(ensurepip._main())
  File "/private/var/folders/db/sh5sm60124j89wcqhdck3rnm0000gn/T/python-build.20190410120716.2695/Python-3.7.0/Lib/ensurepip/__init__.py", line 204, in _main
    default_pip=args.default_pip,
  File "/private/var/folders/db/sh5sm60124j89wcqhdck3rnm0000gn/T/python-build.20190410120716.2695/Python-3.7.0/Lib/ensurepip/__init__.py", line 117, in _bootstrap
    return _run_pip(args + [p[0] for p in _PROJECTS], additional_paths)
  File "/private/var/folders/db/sh5sm60124j89wcqhdck3rnm0000gn/T/python-build.20190410120716.2695/Python-3.7.0/Lib/ensurepip/__init__.py", line 27, in _run_pip
    import pip._internal
zipimport.ZipImportError: can't decompress data; zlib not available
make: *** [install] Error 1
```

解决办法：注意最后两行，说明找不到zlib, 同样使用brew安装

```
$ brew install zlib
```

按照提示，还需要设置环境变量, 在 `~/.zshrc` 中添加如下设置：

```
# For compilers to find zlib you may need to set:
export LDFLAGS="${LDFLAGS} -L/usr/local/opt/zlib/lib"
export CPPFLAGS="${CPPFLAGS} -I/usr/local/opt/zlib/include"

#For pkg-config to find zlib you may need to set:
export PKG_CONFIG_PATH="${PKG_CONFIG_PATH} /usr/local/opt/zlib/lib/pkgconfig"
```

然后执行命令安装python即可。(到此为止，python3.7.0安装完成了，当然这里没有将sqlite3编译完成，这个可以通过 `brew install sqlite` 安装即可)


## pyenv切换Python

```
// 版本检测
$ pyenv versions

// 全局切换
$ pyenv global x.x.x

// 局部切换
$ pyenv local x.x.x
```

# pipenv

> pipenv是Python官方推荐的包管理工具。 它综合了 virtualenv , pip 和 pyenv 三者的功能。你可以使用pipenv这一个工具来安装、卸载、跟踪和记录依赖性，并创建、使用和组织你的虚拟环境

> pipenv使用 Pipfile 和 Pipfile.lock 来管理依赖包，并自动维护 Pipfile ，同时生成 Pipfile.lock 来锁定安装包的版本和依赖信息。相比pip需要手动维护requirements.txt 中的安装包和版本，具有很大的进步。

## Mac安装pipenv

```
$ brew install pipenv
```

## pipenv创建虚拟环境

```
$ pipenv --python x.x.x  # 完成后会生成Pipfile文件
```

> 默认地，虚拟环境会创建在~/.local/share/virtualenvs目录里面。
> 如果我们希望在每个项目的根目录下保存虚拟环境目录（.venv），需要在 .bashrc 或 .bash_profile 中配置如下：

```
export PIPENV_VENV_IN_PROJECT=1
```

要想使配置生效，执行下 `source ~/.bashrc` 或者 `source ~/.bash_profile`

## 更新下载源

pipenv安装依赖时默认使用python官方pypi源，可以在创建好Pipfile以后，修改文件中 `source` 的 `url` 字段，设置为国内的pypi源，设置如下：

```
[[source]]
 url = "https://pypi.tuna.tsinghua.edu.cn/simple"
 verify_ssl = true
 name = "pypi"
```

## pipenv安装依赖

```
$ pipenv install xxx

$ pipenv install xxx --dev  # 用于区分需要部署到线上的开发包、只需要在测试环境中执行的包
```

检查一下是否安装成功：

```
$ pipenv graph
```

此时，项目的根目录下多了文件：`Pipfile.lock` ,这个文件记录了此项目的第三方依赖包详情。

## pipenv删除依赖

```
$ pipenv uninstall xxx
```

## 安装项目所有依赖

根据项目的Pipfile和Pipfile.lock

```
$ pipenv install # 安装Pipfile中 [packages] 部分的包
```

```
$ pipenv install -d # 安装[dev-packages]部分的包
```

> 上面的方法都是安装Pipfile中列出来的第三方包的最新版本，如果是想安装Pipfile.lock中固定版本的第三方依赖包，需要执行：

```
$ pipenv install --ignore-pipfile
```
