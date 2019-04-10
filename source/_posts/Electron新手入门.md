---
title: Electron新手入门
date: 2018-02-11 14:40:00
tags: [Electron, 原创]
categories: 前端
---

![Electron](/images/electron/electron.png)

这篇分享旨在初步介绍Electron，重点介绍其中的几个主要概念，让没有接触过Electron的开发者有一个入门的基础认知。

如果你还未接触过Electron，请看之前某乎上一篇装逼的Title：`感谢 Electron，我现在有了两个身份：前端开发 和 桌面客户端开发`。

# 基本概念

## 是什么

> 官方概念：Electron是由Github开发，用HTML，CSS和JavaScript来构建跨平台桌面应用程序的一个开源库。 Electron通过将Chromium和Node.js合并到同一个运行时环境中，并将其打包为Mac，Windows和Linux系统下的应用来实现这一目的。
 
>> 通俗理解：让你用纯 `JavaScript` 调用丰富的`原生 APIs` 创造桌面应用。使用 web 页面作为 GUI，你能把它看作一个被 JavaScript 控制的，`精简版的 Chromium 浏览器`。

## 依赖项更新（Chromium & Node）

- Electron中Chromium的版本通常会在Chromium发行新的稳定版后的一到两周之内更新，具体时间根据升级所需的工作量而定。

- 为了使版本更加稳定，Electron通常会在Node.js发布了新版本的一个月之后再更新。

> 补充：为了保持Electron的小巧，Electron只用了`Chromium的渲染库`而不是其全部组件。 这使得升级Chromium更加容易，但也意味着Electron缺少了Google Chrome里的一些浏览器相关的特性。

## 特点

- Web 技术
  - 基于 Chromium 和 Node.js, 让你可以使用 HTML, CSS 和 JavaScript 构建应用。

- 跨平台
  - 兼容 Mac, Windows 和 Linux， 它构建的应用可在这三个操作系统上面运行。

## 解决哪些困难

- [自动更新](https://electronjs.org/docs/api/auto-updater): autoUpdater
- [原生的菜单和通知](https://electronjs.org/docs/api/menu): menu 
- [崩溃报告](https://electronjs.org/docs/api/crash-reporter): crashReporter
- [调试和性能分析](https://electronjs.org/docs/api/content-tracing): contentTracing
- ...

## 与web项目的主要区别

electron核心可以分成2个部分:`主进程`和`渲染进程`。
- 主进程连接着操作系统和渲染进程；
- 渲染进程就是我们所熟悉前端环境；
- 传统的web环境不能对系统进行操作，而electron相当于node环境，我们可以在项目里使用所有的node api；

## 目录结构抽象

your-app/
|── package.json
|── main.js
|── index.html

# 主进程 & 渲染进程 

![主进程&渲染进程](/images/electron/electronProcess.png)

- 运行 package.json 的 main 脚本的进程被称为主进程。 在主进程中运行的脚本通过创建web页面来展示用户界面。 一个 Electron 应用总是有且只有一个主进程；

- Electron 中的每个 web 页面运行在它自己的渲染进程中。

- 在普通的浏览器中，web页面通常在一个沙盒环境中运行，不被允许去接触原生的资源。 然而 Electron 的用户在 Node.js 的 API 支持下可以在页面中和操作系统进行一些底层交互。

## 区别

- 主进程使用 BrowserWindow 实例创建页面。 每个 BrowserWindow 实例都在自己的渲染进程里运行页面；

- 主进程管理所有的web页面和它们对应的渲染进程；

- 页面中调用与 GUI 相关的原生 API 是不被允许的，对应的渲染进程必须与主进程进行通讯，请求主进程进行相关的 GUI 操作；

## 举例

1.主进程操作

```javascript
const {BrowserWindow} = require('electron')
// 主进程创建web页面
let someWindow = new BrowserWindow(winOpts)
// 加载本地的文件
someWindow.loadURL('file://' + __dirname + '/index.html')
```

2.渲染进程操作
```javascript
  const {BrowserWindow} = require('electron').remote

  const modalPath = 'xxxxxx'
  let win = new BrowserWindow({ width: 400, height: 320 })

  win.on('close', () => { win = null })
  win.loadURL(modalPath)
  win.show()
```

# 进程间通信

`在web页面，不允许调用原生GUI相关的API，因为在web页面管理原生GUI资源是很危险的，会很容易泄露资源。如果你想在web页面施行GUI操作，web页面的渲染进程必须要与主进程通信，请求主进程来完成这些操作。`

## ipc模块（渲染进程 -> 主进程）

使用`ipcMain`和`ipcRenderer`模块，在渲染进程中使用ipcRender模块向主进程发送消息，主进程中ipcMain接收消息，进行操作;如果需要反馈，则通知渲染进程，渲染进程根据接收的内容执行相应的操作。

1.主进程
```javascript
const {ipcMain} = require('electron')

ipcMain.on('close-window', (evt, data) => {
    console.log(data)
    evt.sender.send('replymsg', otherData);
});
```

2.渲染进程
```javascript
const {ipcRenderer} = require('electron')

ipcRender.send('close-window', data);
ipcRender.on('replaymsg', (evt, otherData) => {
    console.log(otherData)
})
```
> 不过切忌用 `ipc` 传递大量的数据，会有很大的性能问题，严重会让你整个应用卡住。

## remote模块

在渲染进程中使用主进程模块。为渲染进程和主进程通信提供了一种简单方法。

使用 remote 模块, 你可以调用 main 进程对象的方法, 而不必显式发送进程间消息。

![remote模块](/images/electron/remote.png)

```javascript
  // 渲染进程中
  const {BrowserWindow} = require('electron').remote

  let win = new BrowserWindow({width: 800, height: 600})
  win.loadURL('https://ke.youdao.com')
```

## webContents（主进程 -> 渲染进程）
主进程主动向渲染进程发送消息。
```javascript
this.webviewWindow.webContents.send('main-process-messages');
```

## ipcRenderer.sendTo（渲染进程 -> 渲染进程）
渲染进程互相发送通信。
```javascript
ipcRenderer.sendTo(windowId, 'xxx-message', 'data')
```

# 菜单（Menu & MenuItem）

对桌面应用来说，另一个重要的概念就是菜单栏。分为上下文菜单（右击菜单），托盘菜单（绑定到托盘图标），应用菜单（在OS X上）等多种。

1.主进程中
```javascript
const {app, Menu} = require('electron')
  
  const template = [
    {
      label: 'Edit',
      submenu: [
        {role: 'undo'},
        {role: 'redo'},
        {type: 'separator'},
        {role: 'cut'},
        {role: 'copy'}
      ]
    },
    {
      role: 'window',
      submenu: [
        {role: 'minimize'},
        {role: 'close'}
      ]
    },
    {
      role: 'help',
      submenu: [
        {
          label: 'Learn More',
          click () { require('electron').shell.openExternal('https://electronjs.org') }
        }
      ]
    }
  ]
  
  const menu = Menu.buildFromTemplate(template)
  Menu.setApplicationMenu(menu)
```

2.渲染进程中
```javascript
  const {remote} = require('electron')
  const {Menu, MenuItem} = remote
  
  const menu = new Menu()
  menu.append(new MenuItem({label: 'MenuItem1', click() { console.log('item 1 clicked') }}))
  menu.append(new MenuItem({type: 'separator'}))
  menu.append(new MenuItem({label: 'MenuItem2', type: 'checkbox', checked: true}))
```
# Shell

shell 模块提供与桌面集成相关的功能, 使用默认应用程序管理文件和 url。

- 在文件管理器中显示给定的文件
- 将给定的文件移动到垃圾箱
- 播放哔哔的声音
- ...

```javascript
  // 打开系统文件目录
  const {shell} = require('electron')
  const os = require('os')

  shell.showItemInFolder(os.homedir())

  // 使用默认浏览器打开url
  const {shell} = require('electron')

  shell.openExternal('http://electron.atom.io')
```

# 调试

## 渲染进程调试

最广泛使用来调试指定渲染进程的工具是Chromium的开发者工具集，可以通过编程的方式在BrowserWindow的webContents中调用`openDevTool()`API来打开它们或者注册快捷键。
```javascript
  const { BrowserWindow } = require('electron')
  
  let win = new BrowserWindow()
  win.webContents.openDevTools()

  // 或者
  globalShortcut.register('Alt+CommandOrControl+I', () => {
    BrowserWindow.getFocusedWindow().toggleDevTools();
  })
```
![debug-render-process](/images/electron/debug-render.jpg)

## 主进程调试（vscode）

这里我们介绍如何利用vscode来调试Electron主进程。

步骤：
1. 使用vscode打开electron项目
2. 点击debug 添加配置
3. 打开launch.json点击右下角的添加配置，添加“electron 主”
4. 在主进程（main.js）添加断点，点击“启动”进行调试
```javascript
{
  // 修改launch.json文件最终为
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Electron Main",
      "runtimeExecutable": "${workspaceFolder}/node_modules/.bin/electron",
      "program": "${workspaceFolder}/main.js",
      "protocol": "inspector"
    }
  ]
}
```
> 注意：”protocol”: “legacy” //添加默认的协议是legacy，这个协议导致不进入断点，改为：“inspector”

![debug-main-process](/images/electron/debug-main.jpg)

# 打包

## 第三方打包工具
- [electron-packager](https://github.com/electron-userland/electron-packager)
- [electron-forge](https://github.com/electron-userland/electron-forge)
- [electron-builder](https://github.com/electron-userland/electron-builder)

## 举例（electron-packager）
```javascript
  // location of project是你项目文件夹的位置，
  // name of project定义你的项目名，
  // platform决定要构建的平台（*all* 包括Windows，Mac和Linux ），
  // architecture决定构建哪个构架下（x86或x64，all表示两者），
  // electron version让你选择要用的Electron版本

  electron-packager <location of project> <name of project> <platform> <architecture> <electron version> <optional options>
```

> 第一次打包用时比较久，因为要下载平台的二进制文件，随后的打包将会快的多。

# 与 React or Vue 集成

- [electron-react-boilerplate](https://github.com/chentsulin/electron-react-boilerplate)
- [electron-vue](https://github.com/SimulatedGREG/electron-vue)

# 总结

- Electron 并不是很复杂，在写完不多的主进程代码后，其他的业务代码几乎和Web应用没什么区别，甚至可以将一个线上应用迅速的包装成为一个客户端应用，比如[electronic-wechat](https://github.com/geeeeeeeeek/electronic-wechat)。

- 采坑不可避免，比如在打包、集成flash等方面等等；

- 基本不考虑兼容性（只需要兼容 Chromium 浏览器）

- 渲染进程调试和在浏览器中调试完全一致。多个web页面，每个都可以打开对应了调试工具，你可以和浏览器调试一样查看DOM、查看log、监听网络请求等等。

最后多说一句：`虽然Electron 的进程间通信很方便，而且支持多窗口，但我墙裂倾向于使用 Electron 构建单窗口应用`。


# 参考资料

- [官方doc](https://electronjs.org/docs)
- [用Electron开发桌面应用](http://get.ftqq.com/7870.get)
- [electron-quick-start](https://github.com/electron/electron-quick-start)
- [electron-api-demos](https://github.com/electron/electron-api-demos)
- [awesome-electron](https://github.com/sindresorhus/awesome-electron)
