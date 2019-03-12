---
title: 移动端视频autoplay+playsinline小结
date: 2017-09-06 16:30:00
tags: [video]
categories: 笔记
---

在移动端开发中我们会遇到音视频相关的功能需求，而针对不同系统、不同厂商的移动设备、不同浏览器环境，音视频在移动端的表现存在不少差异，这些差异体现在UI、交互、默认属性表现等方面。这里我针对移动端的video元素在自动播放和内联播放两方面进行相关实验，比较不同环境的差异并作出记录，以备后用。

## 自动播放

### 仅设置autoPlay

```javaScript
	<video width="320" height="240" controls src="http://www.w3school.com.cn/i/movie.mp4" autoplay="autoplay"></video>
```

|  | 原生 | 微信 | 微博 | QQ | UC | 搜狗 | 360 | 360极速 |
| - | :-: | :-: | :-: | :-: | :-: | :-: | -: | 
| IOS|N|N|N|N|N|N|N|N| 
| Android |Y|N|N|Y|N|N|Y|N|


### 设置autoPlay + muted

```javaScript
	<video width="320" height="240" controls src="http://www.w3school.com.cn/i/movie.mp4" autoplay="autoplay" muted></video>
```

|  | 原生 | 微信 | 微博 | QQ | UC | 搜狗 | 360 | 360极速 |
| - | :-: | :-: | :-: | :-: | :-: | :-: | -: | 
| IOS|N|N|N|N|N|N|N|N| 
| Android |Y|N|不定|Y|N|N|Y|N|


### video.play()主动触发

```javaScript
	<video width="320" height="240" controls src="http://www.w3school.com.cn/i/movie.mp4" id="v1"></video>

	 setTimeout(() => {
	    document.getElementById('v1').play()
	  }, 1000);
```

|  | 原生 | 微信 | 微博 | QQ | UC | 搜狗 | 360 | 360极速 |
| - | :-: | :-: | :-: | :-: | :-: | :-: | -: | 
| IOS|N|N|N|Y|N|N|N|N| 
| Android |Y|N|N|Y|N|N|Y|N|

### WeixinJSBridgeReady事件触发play()

```javaScript
	<video width="320" height="240" controls src="http://www.w3school.com.cn/i/movie.mp4" id="v1"></video>

		document.addEventListener(
      'WeixinJSBridgeReady',
      function() {
        document.getElementById('v1').play()
      },
      false
    )
```
> IOS微信支持，Android微信不支持

## 取消全屏播放

### 设置playsinline

```javaScript
	<video width="320" height="240" controls src="http://www.w3school.com.cn/i/movie.mp4" webkit-playsinline="true" playsinline="true"></video>
```

|  | 原生 | 微信 | 微博 | QQ | UC | 搜狗 | 360 | 360极速 |
| - | :-: | :-: | :-: | :-: | :-: | :-: | -: | 
| IOS|Y|Y|N|Y|N|Y|诡异|Y| 
| Android |Y|Y|Y|Y|Y|Y|Y|Y|

> ios微博取消全屏，可以用 https://www.npmjs.com/package/iphone-inline-video 