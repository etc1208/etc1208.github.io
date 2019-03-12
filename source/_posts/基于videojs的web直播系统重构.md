---
title: React+Mobx+videojs重构web直播系统
date: 2017-08-01 12:00:00
tags: [直播, React, Mobx, VideoJs, 原创]
categories: 项目
top: true
---

在正式入职之后接手的第一个重点项目便是web直播系统的重构。因为在实习期间一直参与旧版直播系统的迭代开发，同时因旧版本技术栈相对陈旧、代码维护成本过高、扩展能力较弱等原因，决定于2018年进行彻底重构，针对UI做出重新设计。整体系统比较简单，主体由视频和讨论区两大元素构成，在技术选型上采用React作为UI层框架，Mobx作为状态管理库，videoJs作为视频播放器类库。本文针对重构过程中涉及的主要技术点进行记录，留作今后查缺补漏。

## 技术选型

![](/images/zhibo/2.png)

## Mobx

> 无论是Redux / MobX ，均是开源的状态管理库，用状态描述 UI 界面，与 React 都不具有强绑定关系

![](/images/zhibo/3.png)

### store

![](/images/zhibo/4.png)

### Mobx-react 绑定库
> 主要提供API：Provider & inject & observer

- redux <-> react-redux
- mobx <-> mobx-react

![](/images/zhibo/5.png)

### 装饰器

- @表示装饰器，可以在 ES7 或者 TypeScript 类属性中使用。Mobx中装饰器的使用并非必须，只是语法稍有区别。
- Babel默认情况下是不支持装饰器的，需配置相应plugin:

![](/images/zhibo/6.png)

> !!注意， plugins 属性中: transform-decorators-legacy 应放在最前面。当使用 react native 时，可用下面的预设来代替 transform-decorators-legacy:

![](/images/zhibo/7.png)

## H5的video标签

### 基本使用

H5中的video标签可以进行视频播放，写一个video设置一个src属性指定媒体源地址就可以实现媒体播放了。针对低版本浏览器video的HTML结构也提供了友好的兼容方案，只需要在video标签内写上不兼容时要展示的HTML内容即可。

![](/images/zhibo/8.png)

### video的属性

![](/images/zhibo/9.png)

- src: 视频地址controls: 控制条；
- poster: 视频下载时显示的图像；
- preload: 预加载；
- playsinline: 防止ios用户视频播放自动全屏。
- x-webkit-airplay=“allow” : 支持ios的AirPlay功能；
- x5-video-orientation: 声明播放器支持的方向；
- x5­-video­-player­-fullscreen:全屏设置；

### 媒体格式

目前原生H5支持的媒体格式主要有MP4、OGG、WebM、M3U8等，但各大浏览器厂商之间对媒体格式的支持各不相同：

![](/images/zhibo/10.png)

### canPlayType API

可以通过HTMLVideoElement的canPlayType API进行当前环境的格式兼容判断:
- 'probably': The specified media type appears to be playable.
- 'maybe': Cannot tell if the media type is playable without playing it.
- ' ' :The specified media type definitely cannot be played.

![](/images/zhibo/11.png)

### 环境差异

- 1. UI不一致
- 2. API实现与支持程度不一致（标准API里的controls、poster、autoplay…未必有效）
- 3. 事件交互行为不一致（播放进度变化、事件触发频率不同、部分事件触发时相应状态值未必可靠、部分场景缺少事件..）
- 4. 媒体格式的支持无法保证

![](/images/zhibo/12.png)

## Video.js

### 简介

Video.js 是一个通用的在网页中嵌入视频播放器的 JS 库，自动检测浏览器对 HTML5 的支持情况，如果不支持则自动使用 Flash 播放器。（目前版本：v6.5.0）

> 优点：
- 1. 开源免费（https://github.com/videojs/video.js）。
- 2.接入简单（几分钟即可完成基础配置，搭建视频页面）。
- 3.几乎兼容所有主流浏览器，并优先使用html5；在不支持的浏览器中，会自动使用flash进行播放。
- 4. 界面可定制、插件可扩展。

> 注意：
- v6.0.0版本之前：默认将flash build在sdk中;
- v6.0.0版本之后：需手动引入官方提供的flash tech（https://github.com/videojs/videojs-flash）;

### Skins & Icons & Plugins

- Skinning

播放器样式完全通过html+css构建，可以通过覆盖基础css样式进行调整；

- Icons

![](/images/zhibo/13.png)

除此之外，videoJS本身固有的几个class如果灵活运用，可以减少部分细节处理工作量

- vjs-live-control // 展示直播状态
- vjs-poster // 展示默认图
- vjs-seeking // 展示加载中loading
- vjs-controls-enabled // 展示控制条
- vjs-controls-disabled //隐藏所有的控制组件
- vjs-ended // 播放结束样式 
- vjs-error // 视频异常展示错误信息
- …

- Plugins

Video.js 本身很简单，支持基础的播放功能和特性。任何复杂或高级特性将以插件形式提供，比如：播放列表、分析、广告和支持先进的格式如HLS等。(http://videojs.com/plugins/) 

![](/images/zhibo/14.png)

Video.js 提供强大而丰富的插件同时，你也可以自己创建插件并发布。

- 1.插件方法中的this指向所创建plugin依附的videojs实例；

- 2.插件方法中可以使用任何videojs API进行相关处理；

- 3.例如在直播系统中：有关视频的日志上报、各种播放器事件监听处理等，均可放到单独的插件中，方便组织代码；

> Step 1: Write Some Javascript

![](/images/zhibo/15.png)

> Step 2: Registering A Plugin

![](/images/zhibo/16.png)

> Step 3: Using A Plugin

![](/images/zhibo/17.png)

### 初始化

- 方式一：

![](/images/zhibo/18.png)

- 方式二：（推荐）

![](/images/zhibo/19.png)

###  配置参数

一般，对于播放器的初始化，除了传入必备的视频源地址以及视频格式之外，videoJS支持配置额外的options。如：

- 直播type：'rtmp/flv'
- 录播type：'video/mp4'

![](/images/zhibo/20.png)

### 常用API

![](/images/zhibo/21.png)

### 常用事件

![](/images/zhibo/22.png)

### 主要事件的处理

- loadstart：标识播放器开始加载视频，在这里可以展示加载中状态、设置定时器判定首次加载是否成功;

- error：标识播放器异常，一般原因有网络异常、视频解码异常等，此时进行主动拉流操作，并在多次尝试拉流失败后给出用户提示；

- canplaythrough、 play、playing、 seeked：都可能标识视频已正常播放，此时应对错误提示、加载中提示、切换线路提示等UI进行隐藏调整；

- waiting：标识视频发生卡顿较为准确的事件，可以在此处进行视频卡顿次数的记录并展示卡顿提示等信息；

- pause：标识视频暂停，可以做一些UI（如：预览、控制条等）方面的显隐操作;

- timeupdate：标识视频进行中位置发生改变，可在此完成协同播放时间的其他界面操作;

- ended：标识视频播放结束，可做一系列资源回收、清理工作，或者结束提示、循环播放等操作；

- stalled：标识视频资源不可用，这时应进行主动拉流或者提示用户切换线路等操作（但要注意：在部分浏览器中，视频长时间处于暂停状态后可能也会触发此事件，这是应该忽略）；

## 其他

### 视频播放监控

![](/images/zhibo/23.png)

### 视频异常

播放器提供的error事件回调基本涵盖了绝大多数异常情况，包括视频格式无法解析、视频源错误、网络异常、拉流超时等；

![](/images/zhibo/24.png)

### 视频首次加载过程

当音频/视频处于首次加载过程中时，大致会发生以下事件：

![](/images/zhibo/25.png)

- 1.play、playing、canplaythrough三个事件不一定都会触发，顺序也并不固定；
- 2.不同浏览器中（如Edge）可能直接触发到seeked事件即加载完成开始播放，并不会有play、playing、canplaythrough这些；
- 3. durationchange有时候会在loadstart事件之前触发；

### 视频首次加载&卡顿

> 这里的关键在于定时器到期后判断视频是否处于正常状态

在web直播系统中,我们针对视频首次加载是否成功，视频播放中产生的卡顿进行监测记录：

- 1.首次加载：从loadstart事件开始若干秒内视频是否加载成功；

- 2.卡顿：从waiting事件触发后若干秒内是否恢复正常播放；

![](/images/zhibo/26.png)

![](/images/zhibo/27.png)

## 其他问题小结

> Video.networkState() 判断网络状态	

- 0 = NETWORK_EMPTY - 音频/视频尚未初始化	
- 1 = NETWORK_IDLE - 音频/视频是活动的且已选取资源，但并未使用网络	
- 2 = NETWORK_LOADING - 浏览器正在下载数据	
- 3 = NETWORK_NO_SOURCE - 未找到音频/视频来源

在开发中遇到这种情况：直播时，如果web端直接断开网络连接，视频播放器是不会抛出任何异常事件的，这时如果我们想为用户提供主动拉流，需要监测网络状态变化。Video.networkState经试验并不可取，无论网络异常与否，都会从0或2最终变化到1。（最后通过监听offline & online事件获取网络状态变化）

> video.addTextTrack()

- addTextTrack方法创建和返回新的文本轨道。
- 新的TextTrack对象会被添加到视频/音频元素的文本轨道列表中。

浏览器支持程度：所有主流浏览器都不支持addTextTrack方法。

> IE浏览器，每次暂停再开始或快进/退视频，视频倍速会被自动重置为1倍速

![](/images/zhibo/28.png)