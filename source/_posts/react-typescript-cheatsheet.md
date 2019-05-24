---
title: react-typescript-cheatsheet
date: 2019-05-24 13:19:43
tags: [TypeScript]
categories: 前端
---

最近在新项目里开始尝试ts,说实话对于写惯了js的人来讲，最初上手ts是会遇到一些阻碍的，有各种不适应，以及在与框架、lint等配合使用过程中会遇到各种问题。本片笔记记录一些在react中使用ts的代码段。

[参考](https://github.com/fi3ework/blog/tree/master/react-typescript-cheatsheet-cn)


# 无状态组件

方式一：

// 将 props 解构的时候指定它们的类型

```js
const App = ({ message }: { message: string }) => <div>{message}</div>;
```

方式二：

// 使用 @types/react 提供的无状态组件的通用类型：

```js
const App: React.SFC<{ message: string }> = ({ message }) => <div>{message}</div>;
```

> 补充：
//如果你想在函数体内正确的使用 children 的话，在第一种模式下你需要显示的声明它。SFC<T> 已经正确的包含了 children 属性，所以不需要你再声明它的类型了。

```
const Title: React.SFC<{ title: string }> = ({ children, title }) => (
    <div title={title}>{children}</div>
);
```

# 基于 Class 的有状态组件

// 在使用 Typescript 时，React.Component 是一个通用类型 （也被写作 React.Component<PropType, StateType>），所以你实际上需要给它提供 prop 和 state（可选）的类型：

```js
class App extends React.Component<{
  message: string,
}, {
  count: number,
}> {
  state = {
    count: 0
  }
  render() {
    return (
      <div onClick={() => this.increment(1)}>{this.props.message} {this.state.count}</div>
    );
  }
  increment = (amt: number) => { // like this
    this.setState(state => ({
      count: state.count + amt
    }));
  }
}
```


// 如果你想先声明一个之后用到的变量，那么声明它的类型即可：

```js
class App extends React.Component<{
  message: string,
}> {
  pointer: number // like this
  componentDidMount() {
    this.pointer = 3;
  }
  render() {
    return (
      <div>{this.props.message} and {this.pointer}</div>
    );
  }
}
```

# 定义 DefaultProps

// 这种模式使用了TypeScript 的 Partial type 特性，这意味着当前的接口只会实现被包裹的接口的一部分，这样我们可以随意拓展 defaultProps 而不需要改其他任何地方

```js
interface IMyComponentProps {
  firstProp: string;
  secondProp: IPerson[];
}

export class MyComponent extends React.Component<IMyComponentProps, {}> {
  static defaultProps: Partial<IMyComponentProps> = {
    firstProp: "default",
  };
}
```

# 提取 Prop Types

```js
type AppProps = { message: string }
const App: React.SFC<AppProps> = ({ message }) => <div>{message}</div>;
```

// 你也可以在有状态组件中使用（真的，任何类型都可以）：

```js
type AppProps = { // like this
  message: string,
}

type AppState = { // and this
  count: number,
}
class App extends React.Component<AppProps, AppState> {
  state = {
    count: 0
  }
  render() {
    return (
      <div>{this.props.message} {this.state.count}</div>
    );
  }
}
```

# 基本的 Prop Types 

```js
type AppProps = {
  message: string,
  count: number,
  disabled: boolean,
  names: string[], // array of a type!
  obj: object, // any object as long as you dont use it in your typescript code
  obj2: {}, // same
  object: {
   id: string,
   title: string
  }, // an object with defined properties
  objects: {
   id: string,
   title: string
  }[], // array of objects!
  onSomething: Function, // not recommended
  onClick: () => void, // function that doesn't return anything
  onChange: (id: number) => void, // function with named prop
  optional?: OptionalType, // an optional prop
}
```

# 表单与事件

```js
class App extends React.Component<{}, { // no props
    text: string,
  }> {
  state = {
    text: ''
  }
  render() {
    return (
      <div>
        <input
          type="text"
          value={this.state.text}
          onChange={this.onChange}
        />
      </div>
    );
  }
  onChange = (e: React.FormEvent<HTMLInputElement>): void => {
    this.setState({text: e.currentTarget.value})
  }
}
```

# 有用的 React Type 

```js
export declare interface AppProps {
  children1: JSX.Element; // bad
  children2: JSX.Element | JSX.Element[]; // meh
  children3: React.ReactChild | React.ReactChildren; // better
  children: React.ReactNode; // best
  style?: React.CSSProperties; // for style
  onChange?: (e: React.FormEvent<HTMLInputElement>) => void; // form events!
  props: Props & React.HTMLProps<HTMLButtonElement> // to impersonate all the props of a HTML element
}
```

# 高阶组件 / render props

// 有时你想写一个接受 React 元素或者字符串或者其他的类型的 prop，这种情况下最好的 Type 方式是使用 React.ReactNode，React Node 可以匹配任何合适的类型：

```js
import * as React from 'react';
export interface Props {
  label?: React.ReactNode;
  children: React.ReactNode;
}

export const Card = (props: Props) => {
  return (
    <div>
      {props.label && <div>{props.label}</div>}
      {props.children}
    </div>
  );
};
```

// 如果你使用函数作为渲染的 prop

```js
export interface Props {
  children: (foo: string) => React.ReactNode;
}
```

# Context

// 使用新的 context API React.createContext：

```js
class Provider extends React.Component<{}, ProviderState> {
  public readonly state = {
    themeColor: 'red'
  }

  private update = ({ key, value }: UpdateStateArg) => {
    this.setState({ [key]: value })
  }

  public render() {
    const store: ProviderStore = {
      state: this.state,
      update: this.update
    }

    return (
      <Context.Provider value={store}>
        {this.props.children}
      </Context.Provider>
    )
  }
}

const Consumer = Context.Consumer
```

# ref

// 使用 React.RefObject：

```js
class CssThemeProvider extends React.PureComponent<Props> {
  private rootRef: React.RefObject<HTMLDivElement> = React.createRef();
  render() {
    return <div ref={this.rootRef}>{this.props.children}</div>;
  }
}
```
