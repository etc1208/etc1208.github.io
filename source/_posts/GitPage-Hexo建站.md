---
title: GitPage+Hexo建站
date: 2017-04-01 10:09:14
tags: [GitPage, Hexo, 原创]
categories: Doc
---

这里简要记录 [GitPage](https://pages.github.com/) + [Hexo](https://hexo.io/zh-cn/) 快速建立个人站点的过程。建站方式很多，依托的平台不尽相同，我选择GitPage+Hexo，其具有操作简单、维护成本低、文档详细等优势。网上已经有很多此类教程，此文并无过多创新，仅在记录自己建站中的操作步骤、相关参考文档等，力求简明扼要。

## 环境

- mac os 10.11.6
- node v8.14.0
- yarn v1.13.0
- git v2.10.1
- hexo v3.8.0
- next v7.0.1

## GitHub

### 创建Pages项目
创建一个名称为{yourusername}.github.io的新仓库即可。其中的 `yourusername` 填写自己的用户名，Github会识别并自动将该仓库设为Github Pages。用户主页是唯一的，填其他名称只会被当成普通项目。

### 分支管理

Github Pages部署分支设置中，一般分为：

- master 分支
- gh-pages 分支（需要手动创建）

我的做法：

- `blog-src` 分支用于存放源码
- `master` 分支用于部署

## Hexo

### 安装

```
$ yarn global add hexo-cli
```

### 创建hexo项目

```
$ hexo init [projectname]
```

### 拉取Github项目到本地

```
$ git clone https://github.com/yourusername/yourprojectname.git
```
把之前生成的hexo项目文件夹下的内容全部复制过来。

### 常用命令

- hexo init [folder]: 新建项目
- hexo new [post_title]: 新建文章
- hexo generate [-d]: 生成静态文件
- hexo serve [-p port]: 启动本地服务器。默认访问网址为： http://localhost:4000/
- hexo deploy [-g]: 部署
- hexo clean: 清除缓存

> 具体参考：[hexo命令](https://hexo.io/zh-cn/docs/commands.html)

### 更文步骤

`启本地服务` - `新建&编写文章` - `生成静态文件` - `部署`

> 具体参考：[hexo写作](https://hexo.io/zh-cn/docs/writing.html)

### 配置_config.yml

主要是针对网站的基本信息配置，比如网站(副)标题、描述等。

> 具体参考：[hexo配置](https://hexo.io/zh-cn/docs/configuration)

## 更换主题

这里我采用hexo的next主题，另外还有非常多主题可供选择，详见[hexo-themes](https://hexo.io/themes/index.html)


> 具体参考： [theme-next](https://theme-next.org/)

### 下载

```
$ git clone https://github.com/theme-next/hexo-theme-next themes/next
```
### 修改项目配置

根目录下的_config.yml中，theme：next

### 修改主题配置

这里罗列出我进行的主要个性化配置:（themes/next下的_config.yml）

- favicon
- footer
- menu
- scheme
- avatar
- auto_excerpt
- sidebar
- back2top
- social
- third party services

> 具体参考：[theme-next doc](https://theme-next.org/docs/)

## 备注

- github添加公钥

如果是首次在一台机器进行部署（或更换了环境），记得在github添加公钥。
具体参考：[使用Github SSH Key以免去Hexo部署时输入密码](https://xuanwo.io/2015/02/07/generate-a-ssh-key/)
