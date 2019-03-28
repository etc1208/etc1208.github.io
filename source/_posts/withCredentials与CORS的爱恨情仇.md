---
title: withCredentials与CORS的爱恨情仇
date: 2019-03-28 12:26:24
tags: [跨域, 原创]
categories: 技术
---

在日常开发中，跨域是我们老生常谈的话题。为什么存在跨域呢？因为浏览器有[同源策略](https://developer.mozilla.org/zh-CN/docs/Web/Security/Same-origin_policy)，
而我们实际的开发过程中时长存在哪种场景呢：从a域向b域发请求，浏览器就开始搞事情了。总之一句话，一切为了安全。这里不再去赘述跨域的一个个解决办法，比如我们常说的JSONP、domian、代理、postMessage等方式，此文仅简单记录CORS的一些琐碎。

# cookie

- 一般用于身份验证
- 服务端通过在响应头加入Set-Cookie字段向浏览器写cookie
- 浏览器向服务端发送请求时可以将cookie附加在HTTP请求的头字段Cookie中
- 服务端可以设置httpOnly，使得前端无法操作cookie
- 服务端可以设置secure，保证在https的加密通信中传输以防截获


# CORS

> 跨域资源共享,它允许浏览器向跨源服务器发出XMLHttpRequest请求，从而克服了AJAX只能同源使用的限制

- 需要浏览器和服务器同时支持

- JSONP只支持GET请求，CORS支持所有类型的HTTP请求

引用阮一峰老师的一段话：“整个CORS通信过程，都是浏览器自动完成，不需要用户参与。对于开发者来说，CORS通信与同源的AJAX通信没有差别，代码完全一样。浏览器一旦发现AJAX请求跨源，就会自动添加一些附加的头信息，有时还会多出一次附加的请求，但用户不会有感觉。
因此，实现CORS通信的关键是服务器。只要服务器实现了CORS接口，就可以跨源通信。”

## 两种请求

- 简单请求
- 非简单请求

### 简单请求

> 浏览器发现这次跨源AJAX请求是简单请求，就自动在头信息之中，添加一个Origin字段。

`Origin` 字段表示请求源，服务器根据这个值决定是否同意这次请求。

- `Origin` 不在许可范围内

响应头信息没有包含Access-Control-Allow-Origin字段，被XMLHttpRequest的onerror回调函数捕获。

> 注意，这种错误无法通过状态码识别，因为HTTP回应的状态码有可能是200。

- `Origin` 在许可范围内

服务器返回的响应，会多出几个头信息字段:

- Access-Control-Allow-Origin: 该字段是必须的。它的值要么是请求时Origin字段的值，要么是一个*，表示接受任意域名的请求
- Access-Control-Allow-Credentials: 该字段可选。它的值是一个布尔值，表示是否允许发送Cookie
- Access-Control-Expose-Headers

### 非简单请求

- 对服务器有特殊要求的请求，比如请求方法是PUT或DELETE，或者Content-Type字段的类型是application/json

- 正式通信之前，增加一次预检请求（preflight）。

  - 请求方法是OPTIONS
  - 头信息里面，关键字段是Origin，表示请求来自哪个源

浏览器先询问服务器，当前网页所在的域名是否在服务器的许可名单之中，以及可以使用哪些HTTP动词和头信息字段。只有得到肯定答复，浏览器才会发出正式的XMLHttpRequest请求，否则就报错。

- 通过了预检请求后，就会发出正常的CORS请求，会有一个Origin头信息字段。服务器的回应，也都会有一个 `Access-Control-Allow-Origin` 头信息字段。

## withCredentials

> CORS请求默认不发送Cookie和HTTP认证信息。

如果要把Cookie发到服务器,需要client + server 共同努力：

- 服务端：设置Access-Control-Allow-Credentials: true
- 客户端：设置withCredentials属性

```
var xhr = new XMLHttpRequest();
xhr.withCredentials = true;
```

> 永远不会影响到同源请求

> 如果要发送Cookie，Access-Control-Allow-Origin就不能设为星号!!!!!必须指定明确的、与请求网页一致的域名！！！！！
> 具体参见[Reason: Credential is not supported if the CORS header ‘Access-Control-Allow-Origin’ is ‘*’](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CORS/Errors/CORSNotSupportingCredentials)
