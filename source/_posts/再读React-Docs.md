---
title: 再读React Docs
date: 2019-04-01 13:28:25
tags: [React]
categories: 技术
---

平时会看到各种各样关于React的文章，其实React官方的doc足够精彩、精炼且准确，我们不妨通过读一读[官方doc](https://react.docschina.org/docs/jsx-in-depth.html)来加深对React诸多概念的理解掌握，亦或者查缺补漏、温故知新。在这里记录阅读官方文档时的一些知识点，以备之后查询。

# JSX

- React 组件也可以返回包含多个元素的一个数组：

```
render() {
  // 不需要使用额外的元素包裹数组中的元素！
  return [
    // 不要忘记 key :)
    <li key="A">First item</li>,
    <li key="B">Second item</li>,
    <li key="C">Third item</li>,
  ];
}
```

- JavaScript中的一些 “falsy” 值(比如数字0)，它们依然会被React渲染。例如，下面的代码不会像你预期的那样运行，因为当 props.message 为空数组时，它会打印0:

```
<div>
  {props.messages.length &&
    <MessageList messages={props.messages} />
  }
</div>
```

要解决这个问题，请确保 && 前面的表达式始终为布尔值：

```
<div>
  {props.messages.length > 0 &&
    <MessageList messages={props.messages} />
  }
</div>
```

相反，如果你想让类似 false、true、null 或 undefined 出现在输出中，你必须先把它转换成字符串 :

```
<div>
  My JavaScript variable is {String(myVariable)}.
</div>
```

# Refs

React v16.3 引入的 React.createRef() API 更新

- 创建 Refs

```
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();
  }
  render() {
    return <div ref={this.myRef} />;
  }
}
```

- 访问 Refs

当一个 ref 属性被传递给一个 render 函数中的元素时，可以使用 ref 中的 `current` 属性对节点的引用进行访问。

```
const node = this.myRef.current;
```

ref的值取决于节点的类型:

  - 当 ref 属性被用于一个普通的 HTML 元素时，React.createRef() 将接收底层 DOM 元素作为它的 current 属性以创建 ref 。
  - 当 ref 属性被用于一个自定义类组件时，ref 对象将接收该组件已挂载的实例作为它的 current 。

> 你不能在函数式组件上使用 ref 属性，因为它们没有实例

> 需要注意的是，这种方法仅对 class 声明的 CustomTextInput 有效

- 回调 Refs

```
<input
  type="text"
  ref={textInput => this.textInput = textInput}
/>
```

- 旧版 API：String 类型的 Refs

如果你之前使用过 React ，你可能了解过之前的API中的 string 类型的 ref 属性，比如 “textInput” ，你可以通过 this.refs.textInput 访问DOM节点。我们不建议使用它，因为 String 类型的 refs 存在问题。它已过时并可能会在未来的版本被移除。如果你目前还在使用 this.refs.textInput 这种方式访问 refs ，我们建议用回调函数的方式代替。

- 注意

如果 ref 回调以内联函数的方式定义，在更新期间它会被调用两次，第一次参数是 null ，之后参数是 DOM 元素。这是因为在每次渲染中都会创建一个新的函数实例。因此，React 需要清理旧的 ref 并且设置新的。通过将 ref 的回调函数定义成类的绑定函数的方式可以避免上述问题，但是大多数情况下无关紧要。

# Fragments

- `<></>` 是 `<React.Fragment/>` 的语法糖。
- `<></>` 语法不能接受键值或属性

```
function Glossary(props) {
  return (
    <dl>
      {props.items.map(item => (
        // 没有`key`，将会触发一个key警告
        <React.Fragment key={item.id}>
          <dt>{item.term}</dt>
          <dd>{item.description}</dd>
        </React.Fragment>
      ))}
    </dl>
  );
}
```

> key 是唯一可以传递给 Fragment 的属性。在将来，我们可能增加额外的属性支持，比如事件处理。

# Portals

```
render() {
  // React does *not* create a new div. It renders the children into `domNode`.
  // `domNode` is any valid DOM node, regardless of its location in the DOM.
  return ReactDOM.createPortal(
    this.props.children,
    domNode,
  );
}
```

# Error Boundaries

> 注意: 无法捕获如下错误:
> - 事件处理 （了解更多）
> - 异步代码 （例如 setTimeout 或 requestAnimationFrame 回调函数）
> - 服务端渲染
> - 错误边界自身抛出来的错误 （而不是其子组件）

一般：使用 `static getDerivedStateFromError()` 渲染一个退路UI。使用 `componentDidCatch()` 去记录错误信息。

```
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // Update state so the next render will show the fallback UI.
    return { hasError: true };
  }

  componentDidCatch(error, info) {
    // You can also log the error to an error reporting service
    logErrorToMyService(error, info);
  }

  render() {
    if (this.state.hasError) {
      // You can render any custom fallback UI
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children; 
  }
}
```

> 注意: Error Boundaries仅可以捕获组件在树中比他低的组件的错误, 无法捕获其自身的错误。如果一个Error Boundaries无法渲染错误信息，则错误会向上冒泡至最接近的Error Boundaries。

# Hooks

> 让你在class以外使用state和其他React特性

## State Hook

```
import { useState } from 'react';

function Example() {
  // 声明一个名为“count”的新状态变量
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>你点击了{count}次</p>
      <button onClick={() => setCount(count + 1)}>
        点我
      </button>
    </div>
  );
}
```

## Effect Hook

```
import { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  // 与 componentDidMount 和 componentDidUpdate 类似:
  useEffect(() => {
    // 通过浏览器自带的 API 更新页面标题
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

> 可以把 useEffect Hooks 视作 componentDidMount、componentDidUpdate 和 componentWillUnmount 的结合

> 默认情况下，useEffect会在第一次 render 和 之后的每次 update 后运行

### 需要清理的 Effect

```
useEffect(() => {
  ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
  // 明确在这个 effect 之后如何清理它
  return function cleanup() {
    ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
  };
});
```

> 每个 effect 都可以返回一个用来在每次组件 `unmount` 的时候清理它的函数

### Tips

- 使用多个 Effect 以实现关注点分离
- 通过跳过 Effect 以优化性能

```
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]); // Only re-run the effect if count changes
```

## Hooks 规范

- 顶层调用：不要在循环，条件或嵌套函数中调用Hook
- 在React函数组件 或者 自定义Hook中调用Hooks

## 自定义Hook

- 以 `use` 开头的 function
- 调用了其他hooks

```
import { useState, useEffect } from 'react';

function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }

  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(friendID, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(friendID, handleStatusChange);
    };
  });

  return isOnline;
}
```
