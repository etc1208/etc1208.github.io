---
title: Chrome的断点调试
date: 2019-03-22 13:31:29
tags: [Chrome, 调试]
categories: 技术
---

前端开发离不开浏览器环境的debug，chrome为我们提供了断点调试的诸多便利，利用断点调试我们可以触达代码逻辑细微之处、DOM元素变化的瞬间、XHR请求的发生、事件监听的触发等等；有助于我们对bug的追踪排查。这篇笔记记录chrome断点调试js代码的种种功能，在开发过程中提高我们的debug效率。

# 各类断点使用时间概览

| 断点类型 | 使用情况 |
| :-: | :-: |
| 代码行|在确切的代码区域中。|
| 条件代码行|在确切的代码区域中，且仅当其他一些条件成立时。|
| DOM|在更改或移除特定 DOM 节点或其子级的代码中。|
| XHR|当 XHR 网址包含字符串模式时。|
| 事件侦听器|在触发 click 等事件后运行的代码中。|
| 异常|在引发已捕获或未捕获异常的代码行中。|
| 函数|任何时候调用特定函数时。|

# 代码行断点

## 在 DevTools 中设置代码行断点

在知道需要调查的确切代码区域时，可以使用代码行断点。 DevTools 始终会在执行此代码行之前暂停。

在 DevTools 中设置代码行断点：

- 点击 Sources 标签。
- 打开包含您想要中断的代码行的文件。
- 转至代码行。
- 代码行的左侧是行号列。 点击行号列。 行号列顶部将显示一个蓝色图标。

## 代码中的代码行断点

在代码中调用 debugger 可在该行暂停。 此操作相当于使用代码行断点，只是此断点是在代码中设置，而不是在 DevTools 界面中设置。

```
console.log('a');
console.log('b');
debugger;
console.log('c');
```

## 条件代码行断点

如果知道需要调查的确切代码区域，但只想在其他一些条件成立时进行暂停，则可使用条件代码行断点。

若要设置条件代码行断点：

- 点击 Sources 标签。
- 打开包含您想要中断的代码行的文件。
- 转至代码行。
- 代码行的左侧是行号列。 右键点击行号列。
- 选择 Add conditional breakpoint。 代码行下方将显示一个对话框。
- 在对话框中输入条件。
- 按 Enter 键激活断点。 行号列顶部将显示一个橙色图标。

## 管理代码行断点

使用 Breakpoints 窗格可以从单个位置停用或移除代码行断点。

- 勾选条目旁的复选框可以停用相应的断点。
- 右键点击条目可以移除相应的断点。
- 右键点击 Breakpoints 窗格中的任意位置可以取消激活所有断点、停用所有断点，或移除所有断点。 停用所有断点相当于取消选中每个断点。 取消激活所有断点可让 DevTools 忽略所有代码行断点，但同时会继续保持其启用状态，以使这些断点的状态与取消激活之前相同。

# DOM 更改断点

如果想要暂停更改 DOM 节点或其子级的代码，可以使用 DOM 更改断点。

若要设置 DOM 更改断点：

- 点击 Elements 标签。
- 转至要设置断点的元素。
- 右键点击此元素。
- 将鼠标指针悬停在 Break on 上，然后选择 Subtree modifications、Attribute modifications 或 Node removal。

# XHR/Fetch 断点

如果想在 XHR 的请求网址包含指定字符串时中断，可以使用 XHR 断点。 DevTools 会在 XHR 调用 send() 的代码行暂停。

> 注：此功能还可用于 Fetch 请求。

例如，在您发现您的页面请求的是错误网址，并且您想要快速找到导致错误请求的 AJAX 或 Fetch 源代码时，这类断点很有用。

若要设置 XHR 断点：

- 点击 Sources 标签。
- 展开 XHR Breakpoints 窗格。
- 点击 Add breakpoint。
- 输入要对其设置断点的字符串。 DevTools 会在 XHR 的请求网址的任意位置显示此字符串时暂停。
- 按 Enter 键以确认。

# 事件侦听器断点

如果想要暂停触发事件后运行的事件侦听器代码，可以使用事件侦听器断点。 您可以选择 click 等特定事件或所有鼠标事件等事件类别。

- 点击 Sources 标签。
- 展开 Event Listener Breakpoints 窗格。 DevTools 会显示 Animation 等事件类别列表。
- 勾选这些类别之一以在触发该类别的任何事件时暂停，或者展开类别并勾选特定事件。

# 异常断点

如果想要在引发已捕获或未捕获异常的代码行暂停，可以使用异常断点。

- 点击 Sources 标签。
- 点击 Pause on exceptions。 启用后，此按钮变为蓝色。
- （可选）如果除未捕获异常以外，还想在引发已捕获异常时暂停，则勾选 Pause On Caught Exceptions 复选框。

# 函数断点

函数断点

如果想要在调用特定函数时暂停，可以调用 debug(functionName)，其中 functionName 是要调试的函数。 您可以将 debug() 插入您的代码（如 console.log() 语句），也可以从 DevTools 控制台中进行调用。 debug() 相当于在第一行函数中设置代码行断点。

```
function sum(a, b) {
  let result = a + b; // DevTools pauses on this line.
  return result;
}
debug(sum); // Pass the function object, not a string.
sum();
```
