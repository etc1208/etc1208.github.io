---
title: 探索React与Three.js
date: 2020-02-28 10:05:10
tags: [React, Three.js, react-three-fiber]
categories: 前端
---

在最近的工作中，逐渐增多了对用户体验、交互的需求：比如3D渲染与手势交互。WebGL是一种JavaScript API，用于在不使用插件的情况下在任何兼容的网页浏览器中呈现交互式2D和3D图形，但开发复杂，代码量巨大，这时候three.js应运而生，它是WebGL的JavaScript 3D库，其对WebGL提供的接口进行了非常好的封装，简化了很多细节，大大降低了学习成本。

今天我们针对在react技术栈中接入three.js进行3D图形绘制，打开three.js的大门，并探索这其中的技术选型。

# Three.js是什么?

Three.js is the world's most popular JavaScript framework for displaying 3D content on the web, providing you with the power to display incredible models, games, music videos, scientific and data visualizations, or pretty much anything else you can imagine, right in your browser and on your smartphone!

![](/images/threejs/1.jpg)

# 技术选型

- [Three.js](https://github.com/mrdoob/three.js/)（其实基本的需求他就够了）
- [whs.js](https://github.com/WhitestormJS/whs.js)（面向对象）
- [react-three](https://github.com/Izzimach/react-three)（react 15）
- [react-three-fiber](https://github.com/react-spring/react-three-fiber) ✅ （react 16）
- [vue-threejs](https://github.com/fritx/vue-threejs) （vue技术栈）

# three的核心概念

## 误区: Three.js ≠ WebGL

- WebGL非常底层，使用WebGL需要大量代码
- Three.js可以处理诸如场景，灯光，阴影，材质，纹理…

## Three三大件

### Renderer

- WebGLRenderer
- CanvasRenderer

![](/images/threejs/2.jpg)
![](/images/threejs/3.jpg)

- DOMRenderer
- SVGRenderer

### Scene

右手直角坐标系
![](/images/threejs/6.jpg)
![](/images/threejs/7.jpg)

### Camera

- OrthographicCamera（正交相机）
- PerspectiveCamera (透视相机)

![](/images/threejs/4.jpg)
![](/images/threejs/5.jpg)

- CubeCamera（全景相机）
- StereoCamera（3D相机）

## Three.js 主要概念
![](/images/threejs/8.jpg)

Mesh=Geometry+Material

### Geometry

- EdgesGeometry
- CylinderGeometry
- BoxGeometry
- ConeGeometry
- CircleGeometry

### Material

- MeshBasicMaterial
- MeshLambertMaterial
- MeshPhongMaterial

# 画一个正六面体

![](/images/threejs/9.jpg)
![](/images/threejs/10.jpg)
![](/images/threejs/11.jpg)
![](/images/threejs/12.jpg)

# 对比 whs & react-three-fiber

## whs.js

一切皆module

## react-three-fiber

- 封装好的Canvas
- 灵活的写法
- 丰富的事件
- Hooks

# 辅助工具

![](/images/threejs/13.jpg)

## stats.js 性能监控

![](/images/threejs/14.jpg)
![](/images/threejs/15.jpg)

# 坑

## 锯齿&模糊

three.js默认绘制效果锯齿化明显、模糊

![](/images/threejs/18.jpg)

- three.js
![](/images/threejs/16.jpg)

- react-three-fiber
![](/images/threejs/17.jpg)

## 如何画带边框的立方体

> three.js并没原生提供带边框的几何体

- 法一：edgesGeometry + boxGeometry

![](/images/threejs/19.jpg)

> 问题：线宽度无法调整

- 法二：texture (纹理)

![](/images/threejs/20.jpg)
