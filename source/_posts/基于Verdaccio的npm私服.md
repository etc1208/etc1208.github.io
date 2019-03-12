---
title: 基于Verdaccio的npm私服
date: 2018-06-12 13:20:00
tags: [npm, 原创]
categories: 笔记
---

在日常开发过程中，我们经常会使用到npm包，也就是出现在我们的package.json中的种种依赖，亦是node_modules中的一个个文件夹。这些包大多来自[npm公网仓库](https://www.npmjs.com/)。
随着公司的业务越来越复杂，项目迭代速度也越来越快，我们可能会去开发公司内部的组件库或类似的东东。在没有npm私有仓库之前，我们都是手动复制一些开发完的公共组件代码到相关的项目中，这样操作比较繁琐也不利于更新维护，因此我们需要一个npm私有仓库存放相关公用的组件及模块。同时这些组件库或者代码可能涉及公司内部信息或其他原因，导致其是不能对外开放的，因此我们有必要搭建一个类似于npmjs的私有平台来管理公司业务相关的组件及代码。换句话说，我们需要在公司内部搭建一个npm仓库来管理我们自己的npm包。

# Verdaccio 是什么

Verdaccio是一个轻量级私有NPM的Registry（fork自Sinopia，但Sinopia已基本停止更新维护）

![Verdaccio](/images/verdaccio/Verdaccio.png)

# npm私服搭建的必要性

- 1.如果公司处于隐私保护的需要，不想将内部生产的包推到npm社区，但又急需要一套完整的包管理工具来管理越来越多的组件、模块等。对于前端，npm是前端包管理的不二选择

- 2.从npmjs上下载包非常慢；利用私服，项目中使用的所有包可以缓存在上面，然后大家下包的时候走私服，不用走npmjs了，速度提升明显。 

- 3.对于下载和发布npm包都有了相应的权限管理


# 搭建&发布过程

> verdaccio must be installed globaly

## 全局安装verdaccio包

```
	npm install -g verdaccio
	或者：
	yarn global add verdaccio
```

## 修改配置文件

verdaccio的特点是:你在哪个目录运行，它就会在对应的目录下创建有关文件。目录下默认有两个文件：config.yaml和storage，另外htpasswd是添加用户之后自动创建的； 
第一次启动默认的config文件位置在 `/Users/xxx/.config/verdaccio/config.yaml`(该配置文件将在下方详述。)

![Verdaccio](/images/verdaccio/verdaccioDir.png)

在配置文件最后添加监听端口: 
```
	listen: 0.0.0.0:4873 	# listen on all addresses 
```



## 启动verdaccio

### verdaccio直接启动

```
	verdaccio
	或者：
	verdaccio --listen xxxx --config ~./config.yaml  // 指定端口和配置文件
```


![Verdaccio](/images/verdaccio/verdaccioStart.png)

![Verdaccio](/images/verdaccio/verdaccioPage.png)

### pm2守护verdaccio进程

利用第一种方法虽然可以正常启动和使用verdaccio，但不建议用这种方式启动verdaccio，我们可以用pm2来使用pm2对verdaccio进程进行托管启动。 
使用pm2托管的进程可以保证进程永远是活着的,方便查看进程状态、日志等。

#### 安装pm2

```
	npm install -g pm2 --unsafe-perm
```

#### 使用pm2启动verdaccio

```
	pm2 start verdaccio
```

#### 查看pm2下的verdaccio信息

```
	pm2 show verdaccio
```

![Verdaccio](/images/verdaccio/pm2Log.png)


#### 实时日志：

```
	tail -f /xxx/.pm2/logs/verdaccio-out-0.log
```

更过pm2的内容看参考[pm2](https://github.com/Unitech/PM2/)

## 添加用户

```
	npm adduser --registry http://xxx.xxx.com/      //后面是我们的私服地址
	类似如下：
	Username: yanghe
	Password: xxxxxx
	Email: (this IS public) xxxx@qq.com
	Logged in as rong on http://xxx.xxx.com/.
```

> 然后在verdaccion启动页面尝试登录即可，默认登录后有发布包的权限。(这里可以通过修改config.yaml配置文件来对权限进行设置)

## 发布&删除npm包

```
	npm publish           #已经切换到我们私服地址的情况下
	npm publish --registry http://xxx.xxx.com/   #未切换到我们的私服时，直接--registry指定私服地址

	删除发布的包
	npm unpublish --force xxxx
```

# 配置文件详解

```
	// 配置文件原文：
	storage: ./storage
	auth:
	htpasswd:
	   file: /.htpasswd
	uplinks:
	npmjs:
	   url: http://registry.npmjs.org/
	packages:
	'@*/*':
	   access: $all
	   publish: $authenticated
	'*':
	   access: $all
	   publish: $authenticated
	   proxy: npmjs
	logs:
	- {type: stdout, format: pretty, level: http}
	listen: 0.0.0.0:4873
	http_proxy: http://代理服务器ip:8080
	https_proxy: http://代理服务器ip:8080
```

 常用配置详解：
  ● storage： 仓库保存的地址，publish时仓库保存的地址。
  ● auth： htpasswd file：账号密码的文件地址，初始化时不存在，可指定需要手工创建。 
max_users：默认1000，为允许用户注册的数量。 
为-1时，不允许用户通过npm adduser注册。 
但是，当为-1时，可以通过直接编写htpasswd file内容的方式添加用户。
  ● 语法：用户名:{SHA}哈希加密的字符=:autocreated 时间
  ● 加密算法：SHA1哈稀之后再转换成 Base64 输出就好
  ● uplinks: 配置上游的npm服务器，主要用于请求的仓库不存在时到上游服务器去拉取。
  ● packages: 配置模块。access访问下载权限,publish包的发布权限。 
格式如下： 
scope: 
权限：操作 
scope:两种模式 
      ○ 一种是 @/ 表示某下属的某项目
      ○ 另一种是 * 匹配项目名称(名称在package.json中有定义)
  ● 权限： 
      ○ l access: 表示哪一类用户可以对匹配的项目进行安装(install)
      ○ l publish: 表示哪一类用户可以对匹配的项目进行发布(publish)
      ○ l proxy: 如其名，这里的值是对应于 uplinks 的名称，如果本地不存在，允许去对应的uplinks去取。
  ● 操作：
      ○ l $all 表示所有人(已注册、未注册)都可以执行对应的操作
      ○ l $authenticated 表示只有通过验证的人(已注册)可以执行对应操作，注意，任何人都可以去注册账户。
      ○ l $anonymous 表示只有匿名者可以进行对应操作（通常无用）
      ○ l 或者也可以指定对应于之前我们配置的用户表 htpasswd 中的一个或多个用户，这样就明确地指定哪些用户可以执行匹配的操作 
听端口和主机名。 
          ■ localhost:4873 　　　　 #默认
          ■ 0.0.0.0:4873　　　　　　#在所有网卡监听
  ● 代理
#http_proxy: http://something.local/  #http代理
#https_proxy: https://something.local/  #https代理
#no_proxy: localhost,127.0.0.1  #不适用代理的iP

> 修改了配置文件后，运行命令: verdaccio -c config.yml

# nrm

nrm是npm registry管理工具, 能够查看和切换当前使用的registry。不安装也可以，安装会更高效。

## 安装nrm

```
	yarn global add nrm
```

## 添加私服地址到nrm管理工具

这里的`xxx`是我们给自己的私服地址起的别名，为了切换和使用方便。

```
	nrm  add  xxx  http://xxx.xxx.com/   #添加本地私服地址
```

## 将npm包的下载地址改到xxx私服。

```
	nrm use xxx  
```

使用`nrm ls`可查到我们可以使用的所有镜像源地址，* 后面是当前使用的; `nrm del`可删除设置的registry。

# npm配置

## 配置方式

- 1.通过npm的config指令；（用来管理npm的配置文件，通过config命令配置的项目实际上是通过更新配置文件npmrc来配置的）

- 2.通过修改npm命令的配置文件npmrc文件；

## 配置文件的位置：

- 1.用户配置文件：npm config get userconfig查看文件路径(~/.npmrc)

- 2.全局配置文件：npm config get globalconfig查看文件路径(/etc/npmrc)

- 3.内置配置文件:  安装npm的目录下的npmrc文件(/usr/local/lib/node_modules/npm/npmrc)

## 命令行操作

```
	npm config set <key> <value> [-g|--global]  //给配置参数key设置值为value；
	npm config get <key>          //获取配置参数key的值；
	npm config delete <key>       //删除置参数key及其值；
	npm config list [-l]      //显示npm的所有配置参数的信息；
	npm config edit     //编辑配置文件
	npm get <key>     //获取配置参数key的值；
	npm set <key> <value> [-g|--global]    //给配置参数key设置值为value；
```

## 配置npm registry的方式

- 1.通过给npm 命令添加注册源选项: `npm --registry=https://registry.npm.taobao.org [npm命令]`

- 2.通过npm的config命令配置指向国内镜像源: `npm config set registry https://registry.npm.taobao.org  //配置源为淘宝的源`

- 3.在配置文件.npmrc 文件写入源地址：`registry=https://registry.npm.taobao.org   //写入.npmrc配置文件`