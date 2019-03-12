---
title: next.js初探
date: 2018-12-30 17:50:00
tags: [React, SSR, next.js, 原创]
categories: 笔记
---

![](/images/next/1.png)

next.js 是一个轻量级的react同构框架，使用它可以快速的开发出基于服务端渲染的react应用。它在支持ssr的同时支持快速导出静态站点，相比与常规的SPA页面，利用ssr我们能够得到更好的SEO和首屏响应时间等。这次恰好赶上从零开始的海外wap主站项目，借此机会一探next.js的究竟。之前组内使用的前后端同构脚手架是react-starter-kit,它很不错但总感觉有点重；一直想找机会试一试React官方推荐的Next,so~~机会来啦！

# 是什么

- a lightweight framework for static and server‑rendered applications built with React. 
- includes styling and routing solutions out of the box

# 特点

- 轻量
- 默认支持ssr
- 自动 code splitting
- 简洁的路由 (page based)
- HMR & 明确的错误提示
- 自由组合Express等Node.js HTTP server
- Babel 、Webpack等零配置&可扩展

![](/images/next/2.jpg)

# 开始一个next项目

```
mkdir hello-next
cd hello-next
npm init -y
npm install --save react react-dom next
mkdir pages
```

package.json:

```
"scripts": {
  "dev": "next",
  "build": "next build",
  "start": "next start"
}
```

# 路由

> pages即路由:根据pages目录默认生成路由配置

![](/images/next/3.png)

路由：
- /        : pages/index.js
- /about   : pages/about.js
- /a       : pages/a/index.js
- /b/c     : pages/b/c/index.js


## 问题1：a/index.js 与a.js 谁的优先级高

![](/images/next/4.png)

## 问题2：动态路由（稍后解决）

## client side Routing

### with <Link>

```
import Link from 'next/link'

export default () => (
  <div>
    Click{' '}
    <Link href=“/a/b“ as=“/ab”>
      <a>here</a>
    </Link>{' '}
    to read more
  </div>
)
```

常用属性：

- prefetch 
- URL object
- Replace

### Imperatively

```
import Router from 'next/router'

const clickHandler = () => {
  Router.push('/about')
}

export default () => (
    <span onClick={() => clickHandler()}> Click here</span>  
)
```

- Router包含以下属性&API : route/pathname/query/asPath/push/replace等
- Router内包含事件 : routeChangeStart/routeChangeComplete/routeChangeError/beforeHistoryChange/hashChangeStart/hashChangeComplete

```
e.g.
Router.events.on('routeChangeStart', handleRouteChange)
```

### Higher Order Component

```
import { withRouter } from 'next/router'

const ActiveLink = ({ children, router, href }) => {
  return (
    <a href={href}>{children}</a>
  )
}

export default withRouter(ActiveLink)
```

组件被注入router props，其包含属性同 `next/router`.

## Custom server and routing

- client side：利用asPath属性
- server side：自定义server.js
- package.json：修改scripts

### asPath

```
<Link href="/a/b" as="/ab">
  <a>here</a>
</Link>

or:

Router.push('/a/b', '/ab')
```

> Next称之为：Clean URLs with Route Masking
> 但这就存在个问题，这样可以做到client 端路由跳转
> 当手动刷新如/course/detail/2这种路由，server端认识吗？

### server.js

```
const express = require('express')
const next = require('next')

const port = parseInt(process.env.PORT, 10) || 3000
const dev = process.env.NODE_ENV !== 'production'
const app = next({ dev })
const handle = app.getRequestHandler()

app.prepare()
  .then(() => {
    const server = express()

    // a页
    server.get('/a', (req, res) => renderAndCache(req, res, '/a', req.query))

    // b页
    server.get('/b/:id?', (req, res) => renderAndCache(req, res, '/b', { ...req.query, ...req.params }))

    // c页
    server.get('/c', (req, res) => app.render(req, res, '/c', { ...req.query, ...req.params }))

    server.get('*', (req, res) => handle(req, res))

    server.listen(port, (err) => {
      if (err) throw err
      console.log(`> Ready on http://localhost:${port}`)
    })
  })
  .catch((ex) => {
    console.error(ex.stack)
    process.exit(1)
  })
```

### package.json

```
"scripts": {
  "dev": "node server.js",
  "build": "next build",
  "start": "NODE_ENV=production node server.js"
}
```

# styled-jsx

```
function HelloWorld() {
  return (
    <div>
      Hello world
      <p>scoped!</p>
      <style jsx>{`
        p {
          color: blue;
        }
        div {
          background: red;
        }
        @media (max-width: 600px) {
          div {
            background: blue;
          }
        }
      `}</style>
      <style global jsx>{`
        body {
          background: black;
        }
      `}</style>
    </div>
  )
}
```

其他css解决方案：

- @zeit/next-css
- @zeit/next-sass
- @zeit/next-less
- @zeit/next-stylus

# Fetching data & component lifecycle

## getInitialProps

```
class HelloUA extends React.Component {
  static async getInitialProps({ req }) {
    const userAgent = req ? req.headers['user-agent'] : navigator.userAgent
    return { userAgent }
  }

  render() {
    return (
      <div>
        Hello World {this.props.userAgent}
      </div>
    )
  }
}
```

- Next提供了一个新的生命周期：getInitialProps
- 只允许用于pages，不支持子组件中使用
- 在页面加载时执行，并将返回的Object作为props
- 页面首次加载只会在服务端执行，之后通过路由API或者Link组件跳转，才会在客户端执行
- 接收一个context对象，包含：
  - pathname - path section of URL
  - query - query string section of URL parsed as an object
  - asPath - String of the actual path (including the query) shows in the browser
  - req - HTTP request object (server only)
  - res - HTTP response object (server only)
  - err - Error object if any error is encountered during the rendering

# Static file serving

- 根目录下创建static文件夹
- 通过’/static/xxx’引用静态文件
- 建议按照这个约定来，后面方便使用官方提供的配置项配置cdn

> Note: Don't name the static directory anything else. The name is required and is the only directory that Next.js uses for serving static assets.

# next.config.js

```
const withCSS = require('@zeit/next-css')
const withImages = require('next-images')
const pkg = require('./package.json')

const isDev = process.env.NODE_ENV !== 'production' // node环境

module.exports = withCSS(withImages({
  distDir: isDev ? 'build' : '_next', // Setting a custom build directory
  generateBuildId: async () => `v${pkg.version}`, // Configuring the build ID
  assetPrefix: !isDev ? 'https://xxx' : '', // add CDN assetPrefix in the production.
  cssModules: true,
  publicRuntimeConfig: { // config Will be available on both server and client
    ...
  },
  webpack: config => {
    // Fixes npm packages that depend on `fs` module
    config.node = {
      fs: 'empty',
    }

    return config
  },
}))
```

# head 、document、error

## head

```
import Head from 'next/head'

const Layout = ({ title, children }) => (
  <div style={{ fontSize: '0.3rem' }}>
    <Head>
      <title>{title}</title>
    </Head>
    {children}
  </div>
)
```

> 为避免标签重复，可以使用key属性

## _document.js

```
import Document, { Html, Head, Main, NextScript } from 'next/document'

class MyDocument extends Document {
  static async getInitialProps(ctx) {
    const initialProps = await Document.getInitialProps(ctx)
    return { ...initialProps }
  }

  render() {
    return (
      <Html>
        <Head>
          <style>{`body { margin: 0 } /* custom! */`}</style>
        </Head>
        <body className="custom_class">
          <Main />
          <NextScript />
        </body>
      </Html>
    )
  }
}

export default MyDocument
```

- Is rendered on the server side
- Is used to change the initial server side rendered document markup
- Commonly used to implement server side rendering for css-in-js libraries like styled-components or emotion. styled-jsx is included with Next.js by default.

## _error.js

404 或 500 错误 next会自动渲染到内置的error.js. 若希望自定义error页面，创建_error.js即可

```
class Error extends React.Component {
  static getInitialProps({ res, err }) {
    const statusCode = res ? res.statusCode : err ? err.statusCode : null;
    return { statusCode }
  }

  render() {
    return (
      <p>
        {this.props.statusCode
          ? `An error ${this.props.statusCode} occurred on server`
          : 'An error occurred on client'}
      </p>
    )
  }
}
```

# next-routes

- 为了简化next原生路由系统在动态路由的规划上复杂的部分
- 符合Express风格的路由参数匹配
- 方便为请求处理添加中间件
- 主要还是看着清爽、用着也简单

```
const routes = require('next-routes')

module.exports = routes()
  .add({ name: 'a', pattern: '/a', page: 'a' })
  .add({ name: 'b', pattern: '/b', page: 'b' })
  .add({ name: 'c', pattern: 'c/:id', page: 'c' })
```

# lru-cache

> 基于“最近最少使用”的服务端缓存

## ssr过程

![](/images/next/5.png)

