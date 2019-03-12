---
title: Cocos2dx 记录
date: 2018-05-12 12:30:00
tags: [Cocos2dx]
categories: 转载
---

Creator编辑器
* AnySDK多渠道打包工具

起源自Cocos2D-iPhone，创始人Ricardo Quesada。
Cocos2D-X创始人王哲。

背后公司为触控科技，起步于2008年初创建的CocoaChina社区，专注于苹果产品和iOS系统开发，后推出开源2D 游戏引擎 Cocos2d。

[官方的关于我们](http://www.cocos.com/docs/native/v3/about/about-us/zh.html)

![](/images/cocos2dx/15119227265813.jpg)

![](/images/cocos2dx/15119228044624.jpg)
所有分支游戏引擎均是MIT许可证下发布。


## Cocos2dx 
X代表着Cross，即交叉。因为Cocos2D-X为开发者提供了跨平台支持，通过C++语言把游戏逻辑一次编写即可编译到iOS、Android以及更多手机平台上运行。Cocos2D-X在中国拥有70%的开发者认可和使用比例。此外，Cocos2D-X在全球199个国家地区有40万开发者使用，而Cocos2D-X已经成为全球使用率最高的手机游戏引擎之一，在中国前10名收入最高的手机游戏产品中有8款产品是由Cocos2D-X引擎及工具开发的。

现在看到的都是 v3.x 版本。3.x 和2.x相比，改动非常大。

### Cocos2d-html5
Cocos2d-JS的一个重要模块，是一个面向Web的游戏引擎，采用Canvas或者WebGL渲染，并完全兼容HTML5规范，所以基于Cocos2d-JS开发的游戏天然可运行在所有支持HTML5规范的浏览器

### Cocos2dx-js FullVersion 全平台编译发布

[官网：1.1 Cocos2d-JS介绍](http://www.cocos.com/docs/js/1-about-cocos2d-js/1-1-a-brief-history/zh.html)

**简单粗暴说就是用 Js 写，full version 可以js 写完后编译到全部平台：web 版翻译到 WebGl 或者 Canvas，使用的是 Cocos2dx-h5渲染；其他安卓、iOS、window等等均等于使用 js 调用底层 API, 使用的解析器是 SpiderMonkey。**

>JavaScript代码由Mozilla的JavaScript虚拟机SpiderMonkey进行解析和运行，同时这个虚拟机被改造为支持Cocos2d-x的类型，数据结构和对象。SpiderMonkey本身是由C/C++开发的，不仅可嵌在浏览器中，也嵌入到任何C++的程序中使用。
>SpiderMonkey负责脚本的解析运行，创建和检查JavaScript的数据结构，处理各种错误，提供安全检查，调试脚本的功能，并通过JSAPI实现C++代码的调用。通过这项技术，可让开发者能用纯JavaScript来开发原生游戏，游戏的开发更加高效，原型构建和验证更加便捷。

注：这种一般我都认为不靠谱，搜了下反馈确实不太靠谱。

### Cocos2dx-js LiteVersion 只做 Web

>如果您只关注于纯Web游戏开发，那么您还可以选择Cocos2d-JS Lite的版本，Lite版本提供Cocos2d-html5单引擎文件，可自由定制功能，并可直接嵌入到HTML页面可以开始游戏开发，提供更加简便的工作流，开发更加便捷，还兼具脚本文件体积小，性能高效等优点，具体的内容见后续的工作流章节的介绍。

## Cocos Creator


[Cocos Creator 用户手册](http://docs.cocos.com/creator/manual/zh/getting-started/)

>cocos2d-x是游戏开发框架，Cocos Creator才称得上是完整的游戏引擎。 --- 王哲

看了一小时 Cocos2dx 的文档我是拒绝的。看文档的时候就发现Cocos出过好几代编辑器， 但之前的槽点都很多。而直接下载引擎代码开发，打开一看简直毛骨悚然。
但是看了下 Cocos Creator，感觉很惊艳。

![](/images/cocos2dx/15119233874329.jpg)

[Cocos Creator 官方介绍](http://docs.cocos.com/creator/manual/zh/getting-started/introduction.html)

>Q: Cocos Creator 是游戏引擎吗？
A: 它是一个完整的游戏开发解决方案，包括了 cocos2d-x 引擎的 JavaScript 实现（不需要学习一个新的引擎），以及能让你更快速开发游戏所需要的各种图形界面工具
Q: Cocos Creator 的编辑器是什么样的？
A: 完全为引擎定制打造，包含从设计、开发、预览、调试到发布的整个工作流所需的全功能一体化编辑器
Q: 我不会写程序，也能使用 Cocos Creator 吗？
A: 当然！Cocos Creator 编辑器提供面向设计和开发的两种工作流，提供简单顺畅的分工合作方式。
Q: 我使用 Cocos Creator 能开发面向哪些平台的游戏？
A: Cocos Creator 目前支持发布游戏到 Web、Android 和 iOS，以及点开即玩原生性能的 Cocos Play 手机页游平台，真正实现一次开发，全平台运行。

![](/images/cocos2dx/15119236683850.jpg)

[知乎上有人 po 出了他们公司的使用感想](https://www.zhihu.com/question/39663501/answer/82576709)

可以直接在 Chrome 中进行调试，即改即见，非常顺畅。

Web平台：canvas 或者 webGL 可选

支持 VS Code 管理和编辑项目脚本代码，可以轻松实现语法高亮、智能代码提示等功能。支持配置脚本一键编译。[配置代码编辑环境](http://docs.cocos.com/creator/manual/zh/getting-started/coding-setup.html) 

### 搜到的各种负面反馈

+ cocos2dx 总在不断大改，持续学习成本高。【确实，cocos2dx 文档非常一般。但 Cocos Creator 文档非常完善。
+ 游戏从业者认为 jsbinding 性能要求绝对不能符合要求【但我们不是做游戏哇感觉 web 就够了
+ 2016年才发布，应该是存在了很多坑，具体可参见[如何评价 Cocos2d-x 的新编辑器 Cocos Creator？](https://www.zhihu.com/question/39663501)

### 场景开发

#### 节点 & 组件
Cocos Creator 的工作流程是以组件式开发为核心的，组件式架构也称作 组件-实体系统（或 Entity-Component System），简单的说，就是以组合而非继承的方式进行实体的构建。

在 Cocos Creator 中，节点（Node）是承载组件的实体，我们通过将具有各种功能的 组件（Component） 挂载到节点上，来让节点具有各式各样的表现和功能。接下来我们看看如何在场景中创建节点和添加组件。

#### 坐标系 & 变换

笛卡尔坐标系

世界坐标系（World Coordinate）和本地坐标系（Local Coordinate）； 每个节点以父节点来算本地坐标系；本地坐标系会最后转成世界坐标系。

锚点（Anchor） 是节点的另一个重要属性，它决定了节点以自身约束框中的哪一个点作为整个节点的位置。我们选中节点后看到变换工具出现的位置就是节点的锚点位置。
    
### 脚本开发

```
cc.Class({
    extends: cc.Component,

    properties: {
    },

    // use this for initialization
    onLoad: function () {
    },

    // called every frame, uncomment this function to activate update callback
    update: function (dt) {
    },
});
```

#### 生命周期

onLoad
start
update
lateUpdate
onDestroy
onEnable
onDisable

#### 动作系统

>动作系统并不能取代动画系统，动作系统提供的是面向程序员的 API 接口，而动画系统则是提供在编辑器中来设计的。同时，他们服务于不同的使用场景，动作系统比较适合来制作简单的形变和位移动画，而动画系统则强大许多，美术可以用编辑器制作支持各种属性，包含运动轨迹和缓动的复杂动画。

[动作列表](http://docs.cocos.com/creator/manual/zh/scripting/action-list.html)

#### 网络接口 
#### 脚本引用 

支持 ES2015 TypeScript

模块化脚本 & 插件脚本

脚本加载顺序如下：

1. Cocos2d 引擎
2. 插件脚本（有多个的话按项目中的路径字母顺序依次加载）[可以用于配置
3. 普通脚本（打包后只有一个文件，内部按 require 的依赖顺序依次初始化）

>目标平台兼容性
插件发布后将直接被目标平台加载，所以请检查插件的目标平台兼容性，否则项目发布后插件有可能不能运行。
目标平台不提供原生 node.js 支持
例如很多 npm 模块都直接或间接依赖于 node.js，这样的话发布到原生或网页平台后是不能用的。
依赖 DOM API 的插件将无法发布到原生平台
网页中可以使用大量的前端插件，例如 jQuery，不过它们有可能依赖于浏览器的 DOM API。依赖这些 API 的插件不能用于原生平台中。


## 子系统

前几个系统应该是设计师重点要看的。

### 图像和渲染

自带的各种组件，绘图系统、外部资源家在，还有摄像头。

### UI系统
[多分辨率适配](http://docs.cocos.com/creator/manual/zh/ui/multi-resolution.html)

### 动画

这个动画编辑器感觉忒难用。。。

### 物理碰撞

## summary

使用 Cocos Creator 做教研配套目前来看的优势 & 劣势。

### 还需要确定的点：

1. 是否能够插入我们自己的统计和用户代码等？如果编译到原生，是否丢失？【目前尝试 js 应该是可以的，但原生那边不知道怎么处理，此外存在兼容问题
2. 如何放到视频上进行交互？【视频上的是否直接使用 其他js 库更好。
3. 进一步的需求有哪些？

### 优势： 

+ 工作流非常顺畅，且可以设计和程序分工合作，都可以直接看到效果。
+ 配套文档很赞。可以考虑通过培训，建立设计师和程序都可使用的完整开发流程。
+ 可以直接打包生成资源包上线。
+ 花了几个小时跟着文档写了下demo，感觉上手度不错。


### 劣势

+ 需要培训。设计师需要学会游戏设计，如果运营来做的话也需要一定的基础。此外脚本方面也需要学习游戏脚本开发设计，需要熟悉游戏开发术语、流程、Cocos2dx-js 的 API 等。
+ 流程上的磨合可能会耗费一定时间和精力，这边的人基本都没有做过游戏开发。
+ 目前发布不到两年，在持续迭代，需要踩坑。
+ 太复杂的成本可能会比较大，例如踩到内存的坑
+ 目前技术积累为0。 有组件库等可能会好一些。c



## Reference 

+ [cocos 官网](http://www.cocos.com/)