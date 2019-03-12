---
title: 那些年我们一起追过的mpvue
date: 2019-01-10 15:35:00
tags: [Vue, 小程序, mpvue, 原创]
categories: 笔记
---

> 这里我们并不会深入讲述小程序开发;
> 这次分享刚刚走到小程序开发的大门口，门口有你、有我、有坑;
> 鲁迅说过：世上本没有坑，踩的人多了就有了坑;

# what is mpvue ?

mpvue 是一个使用 Vue.js 开发小程序的前端框架。框架基于 Vue.js 核心，mpvue 修改了 Vue.js 的 runtime 和 compiler 实现，使其可以运行在小程序环境中，从而为小程序开发引入了整套 Vue.js 开发体验。

## 全局安装 vue-cli
```
$ npm install --global vue-cli
```
> vue cli 之于 vue  <->  creact-react-app 之于 react 

* 题外话：vue-cli & @vue/cli *

- 两者其实是一个东西
- 从3.0开始：从 vue-cli 更名为 @vue/cli
- 如果你使用vue cli 3，官方要求：“ Vue CLI requires Node.js version 8.9 or above (8.11.0+ recommended) ”

```
// cli 2
npm install -g vue-cli 

// cli 3
npm install -g @vue/cli
```

两个版本的详细区别这里不做重点，我们只说创建项目

```
// cli 2
vue init <template-name> <project-name>    

// cli 3
vue create <project-name>
```

![](/images/mpvue/1.png)

因为mpvue官方提供的是基于模板(template)初始化项目，而vue cli3不再直接支持；同时官方也为我们提供了在3的基础上使用template的方式：安装一个全局桥

## 初始化mpvue

- 1. 创建一个基于 mpvue-quickstart 模板的新项目

```
// 新手一路回车选择默认就可以了
$ vue init mpvue/mpvue-quickstart my-project
```

- 2. 安装依赖

```
$ cd my-project
$ npm install
$ npm run dev
```

![](/images/mpvue/2.png)

* 如果你和我一样使用nvm管理本地node版本，必须手动nvm use xx切换一次 *（e.g.哪怕当前就是在node 8版本下也要手动执行一次nvm use 8），否则初始化mpvue项目时就会失败：报错 vue-cli · [.eslintrc.js] Missing helper: "if_eq"

## 生成目录结构

![](/images/mpvue/3.png)

## 导入微信开发者工具

![](/images/mpvue/4.png)

>如果我们按照官方指引一步步导入，是不能正常跑起来项目的，会报错：
>未找到入口 app.json 文件，或者文件读取失败，请检查后重新编译。

* 原因在：project.config.json *

- 这个文件是用来存储项目配置的，每次微信开发者工具导入mpvue项目后会依据这个配置文件去加载项目；
- 那么我们看这个文件里有这样一条配置项："miniprogramRoot": “dist",
- 它就是告诉微信开发者工具：这个目录下有我打包好的微信小程序，你快去加载

* 补充：app.json是什么 *

![](/images/mpvue/5.png)

- app.json 是当前小程序的全局配置，包括了小程序的所有页面路径、界面表现、网络超时时间、底部 tab 等；

- 它是必须的！（而我们去看打包好的dist目录下哪里有app.json？？只有一个wx目录，自然会报错找不到）

![](/images/mpvue/6.png)
![](/images/mpvue/7.png)

解决办法：更改project.config.json中的miniprogramRoot项为”dist/wx"，这样微信开发者工具就会自动去这个目录下加载小程序所需资源；亦或者直接导入dist/wx目录自然也是可以的

![](/images/mpvue/8.png)

> 补充：只有直接导入dist/wx目录才能使用预览功能，原因未知

# 页面配置

![](/images/mpvue/9.png)

- app.json中的pages字段设定了当前小程序包含的页面（相当于路由），其中以^开头的会作为小程序首页对待，否则默认出现在pages数组的第一项为首页；
- 我们看到对应pages目录下分布着一个个页面，每个里面都有一个main.js，并且内容极其相似甚至完全一样；

另外，官方Q&A中关于页面配置提到一点：

![](/images/mpvue/10.png)

mpvue本身页面配置的缺陷：

  •	mpvue新增页面或者模块的时候必须重新npm run dev才可以进行更新，不支持热更新;（官方告诉我们要忍）
	
  •	mpvue所有页面模块除了.vue文件外，都需要写一个很类似的main.js，重复工作;

推荐一个第三方的插件：mpvue-entry

该插件使得我们新建页面时只需要一个.vue文件，并且将页面路由通过一个js文件统一配置

优点：集中式页面配置，自动生成各页面的入口文件，优化目录结构

# mpvue-entry

根据官方的readme，我们一步步来：

- 安装：npm i mpvue-entry -D
- 使用
  - 修改webpack.base.conf.js
  - 新增pages.js配置文件
  - 调整原来的pages目录结构（去掉冗余的main.js）
  - 对应修改app.json的pages配置！！！

在官方readme里我们注意到：
> v1.5.0 版本开始支持 mpvue-loader@^1.1.0 版本，新版 src 目录下需存在 app.json 文件，预计 v2.0 版本不再兼容旧版 mpvue-loader

编译通过，导入微信开发者工具,报错了：

* 未找到入口 app.json 文件，或者文件读取失败，请检查后重新编译。*

仔细查看：dist/wx中的app.json怎么搬家跑到上层去了？！

原因有二：
- 官方指引中让务必删去的配置部分导致本该复制到dist/wx目录下的app.json没有被复制；
- mpvue-entry帮我们在dist目录下生成了一个app.json

* 怎么办呢？ *

办法一（不能完美解决）：先把刚刚“务必删去”的配置部分加回来；
结果：存在两个app.json,而且还稍有不同
① mpvue-entry帮我们在dist目录下生成的app.json多了个subPackages项:分包；稍后会讲到。
② mpvue-loader帮我们在dist/wx下生成了一个app.json,就是src/app.json的复制品；
③ pages.js中关于单页面的配置(标题等)不生效

对于单个页面的配置：官方提供的方法是: 通过在每个page的main.js中export独立的配置：    

```
export default {
  config: {
    navigationBarTitleText: '第二页标题'
  }
}
```

（main.js都给干掉了，pass！并且我测试了一下在不接入mpvue-entry的情况下这个方法也是无效的，需要在每个page目录下新建一个main.json文件进行配置）

但还是有办法的：
通过调用 wx API 动态设置；

```
onLoad() {
  wx.setNavigationBarTitle({
‘title’: ‘xxxx’
  })
}
```

真正的解决办法：mpvue_entry应该支持构建路径可配置啥的，于是往下翻文档，果不其然：

![](/images/mpvue/11.png)

一切似乎在朝着更好的方向发展：只有一个app.json了 & 单页面的配置生效了

但我们仔细看在dist/wx下构建出的app.json是和src/app.json一模一样的（它实际是mpvue-loader复制过去的，覆盖了mpvue-entry构建出来的app.json）

！！这就解释了为何mpvue-entry要求务必删去webpack.base.conf.js中复制json的配置

# 分包

刚刚我们跳过了一个概念：分包。它的功效类比于webpack打包出来的多个chunk

- 每个使用分包小程序必定含有一个主包。主包就是包含启动页面的包。
- 在小程序启动时，默认会下载主包进入首页，当用户进入分包内某个页面时，客户端才会把对应分包下载下来，下载完成后再进行展示。
- 对小程序进行分包，可以优化小程序首屏时间

![](/images/mpvue/12.png)
![](/images/mpvue/13.png)

# 接入vuex

![](/images/mpvue/14.png)

- 1.安装依赖：yarn add vuex
- 2.state & getters & mutations & 
actions等基础配置
- 3.入口处添加store

![](/images/mpvue/15.png)


*补充：Logger & 严格模式*

1.Logger plugin

因为在微信开发者工具中没有 vue-devtools，最好在开发模式下配置vuex的logger插件，方便观察状态变化；

2.严格模式

在严格模式下，无论何时发生了状态变更且不是由 mutation 函数引起的，将会抛出错误。这能保证所有的状态变更都能被调试工具跟踪到。

>不要在发布环境下启用严格模式！
>严格模式会深度监测状态树来检测不合规的状态变更——请确保在发布环境下关闭严格模式，以避免性能损失。

- 如果你并未接入mpvue-entry，最初的项目中接入vuex后，是会遇到问题的：
- 入口处无法通过new Vue({store, …App})这种形式传入store
- 必须在每个page下的main.js中注入才能生效;(也有绕过去的办法，就是在入口处通过Vue.prototype.$store = store绑定store，这样在每个子page中就可以访问到store了

# 网络请求

> 说明：微信小程序的 js运行环境和浏览器不同，页面的脚本逻辑是在JsCore中运行，JsCore是一个没有窗口对象的环境，访问不了window，也没有XmlhttpRequest等对象；所以像jquery 、zepto、axios这些在小程序中都不能直接使用

- 方案一： 封装wx.request（不再赘述，参考微信官方文档）
- 方案二：Flyio

一个支持所有JS运行环境的基于Promise的、支持请求转发、强大的http请求库。可以在多端最大限度的实现代码复用。

目前支持的平台包括：Node.js 、微信小程序 、Weex 、React Native 、Quick App 和浏览器。


# 番外

## 不支持部分复杂的 JavaScript 渲染表达式

官方举例是这样的：

```
  // 这种就不支持，建议写 computed
  <p>{{ message.split('').reverse().join('') }}</p>
  
  // 但写在 @event 里面的表达式是都支持的，因为这部分的计算放在了 vdom 里面
  <ul>
      <li v-for="item in list">
          <div @click="clickHandle(item, index, $event)">{{ item.value }}</p>
      </li>
  </ul>
```

```
比如<h4>当前值：{{number}}</h4>是可以的；
<h4>{{`当前值：${number}`}}</h4>就会报错: number is undefined
```

## 页面关闭后，重进数据状态并未初始化

这个是mpvue已知的问题，开发者正在解决（但其实已经很久了仍未解决）
解决办法就是利用一些生命周期钩子重新初始化一次数据，如：

```
onHide() {
   // 退出页面时候
   this.reset()
}
```

## 生命周期钩子

- created：小程序加载后，所有页面的created钩子都会被调用，而且只调用这一次；可以用小程序的onLoad钩子代替（在每个页面进入后执行一次）

- mounted：page A -> page B -> page A，此时页面A的mounted钩子不会被触发，因为小程序的历史页面不会被销毁；如果有需要每次页面展示都要调用的逻辑，可以使用小程序的onShow代替

## 小心后台页面的js

小程序中可能有n个页面，所有的这些页面，虽然都拥有自己的webview， 但是却共享同一个js运行环境。当你从A跳到B，A的定时器等js操作仍在进行，并且不会被销毁，并且会抢占B页面的资源。

在h5的环境中，当我们跳转到其他页面，老页面的js环境会被自动销毁，定时器什么都被销毁掉了，因此我们不需要关心老页面中还有哪些js代码可能还会执行。但是在小程序中，我们必须手动的“清理”掉这样的代码。

