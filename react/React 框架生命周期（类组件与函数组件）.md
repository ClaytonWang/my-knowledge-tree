---
created: 2022-07-02T22:10:09 (UTC +08:00)
tags: [React.js]
source: [https://juejin.cn/post/6871728918643081230](https://juejin.cn/post/6871728918643081230)
author: Lion
permalink: /react/React 框架生命周期（类组件与函数组件）.html
---

# React 框架生命周期（类组件与函数组件）

## 前言

在学习任何一个框架原理之前，学习它的生命周期是非常必要的，至少可以让我们明白在什么时候该做什么事情。不至于犯一些低级错误。

`React 16.8` 版本正式发布了 `Hook` 机制，因此本文会分别讲解 `Class Component` 生命周期与 `Function Component` 生命周期。

## Class Component 生命周期

React 生命周期指的是组件从创建到卸载的整个过程，每个过程都有对应的钩子函数会被调用，它主要有以下几个阶段：

-   挂载阶段  -  组件实例被创建和插入 DOM 树的过程
-   更新阶段  -  组件被重新渲染的过程
-   卸载阶段  -  组件从 DOM 树中被删除的过程

### 挂载阶段

这个阶段是做初始化操作，主要有这几个钩子函数：

#### static defaultProps

设置 props 的默认值

    static defaultProps = {
      name: '子组件设置的默认props'
    }

#### static propTypes 

props 数据类型检查

    import PropTypes from 'prop-types';

    static propsTypes = {
      name: PropTypes.string // 限定name为string类型
    }

#### constructor(props) 

构造函数的作用：

-   初始化 `props and state`
-   绑定事件处理函数


    constructor(props) {
      super(props);
      this.state = {number: 0};
      this.handlexxx = this.handlexxx.bind(this)
    }

#### componentWillMount() 

组件挂载前钩子\*\*(17 版本后不推荐使用)\*\*

由于 React 未来的版本中推出了异步渲染，DOM 被挂载之前的阶段都可以被打断重来，导致 `componentWillMount` 、 `componentWillUpdate` 、 `componentWillReceiveProps` 在一次更新中可能会被触发多次，因此那些只希望触发一次的副作用应该放在 `componentDidMount` 中。

这也就是为什么要把异步请求放在 `componentDidMount` 中，而不是放在 `componentWillMount` 中的原因，是为了向后兼容。

    componentWillMount() {
    // componentWillMount在渲染过程中可能会执行多次
    }

#### componentDidMount() 

组件挂载成功钩子，该过程组件已经成功挂载到了真实 DOM 上。

由于在渲染过程中只执行一次，因此常用来：

-   监听事件；
-   获取到真实 DOM；
-   请求后台接口。


    componentDidMount(){
      fetch('https://api.github.com/users').then(res=>res.json()).then(users=>{
        console.log(users);
        this.setState({users});
      });
    }

### 更新阶段

这个阶段主要是做状态更新操作，主要有这几个钩子函数：

#### componentWillReceiveProps(newProps) 

父组件更新 props 钩子\*\*(17 版本后不推荐使用)\*\*

#### shouldComponentUpdate(nextProps, nextState)  

组件是否更新钩子

由于 React 父组件更新，必然会导致子组件更新，因此我们可以在子组件中通过手动对比 `props` 与 `nextProps`，`state` 与 `nextState` 来确定是否需要重新渲染子组件，如果需要则返回`true`，不需要则返回 `false`。该函数默认返回 `true`。

     shouldComponentUpdate(nextProps, nextState) {
       console.log('Counter', nextProps, nextState);
       console.log('5. shouldComponentUpdate 询问组件是否需要更新');
       return true;
     }

#### componentWillUpdate()

组件更新前钩子\*\*(17 版本后不推荐使用)\*\*

#### componentDidUpdate() 

此生命周期方法在组件更新完后被调用。

-   因为组件已经重新渲染了所以这里可以对组件中的 DOM 进行操作；
-   在比较了 `this.props` 和 `nextProps` 的前提下可以发送网络请求。


    componentDidUpdate(prevProps, prevState, snapshot) {
    if (this.props.userID !== prevProps.userID) {
        this.fetchData(this.props.userID);
      }
    }

### 卸载阶段

这个阶段主要是从 DOM 树中删除组件的操作，它的钩子只有一个 `componentWillUnmount` 。

#### componentWillUnmount() 

这是 `unmount` 阶段唯一的生命周期，在这里进行的是善后工作：清理计时器、取消网络请求或者取消事件监听等。

### 老版本执行顺序图谱

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0fb3cd2923f04e4c8dc58243522ff666~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

### 17 版本生命周期

新增了两个生命周期函数：

1.  `static getDerivedStateFromProps(nextProps, prevState)`
2.  `getSnapshotBeforeUpdate(prevProps, prevState)`

删除了以下生命周期函数：

1.  `componentWillMount`
2.  `componentWillReceiveProps`
3.  `componentWillUpdate`

从这个版本开始，只有新的 `UNSAFE_` 生命周期名称将起作用：

1.  `UNSAFE_componentWillMount`
2.  `UNSAFE_componentWillReceiveProps`
3.  `UNSAFE_componentWillUpdate`

#### static getDerivedStateFromProps(nextProps, prevState)

在 `render` 前调用，在初始挂载以及后续更新时都会被调用。

他应该返回一个对象来更新 `state`。如果返回 `null` 则不更新任何内容。

它接收两个参数，一个是传进来的 `nextProps` 和之前的 `prevState`。

    static getDerivedStateFromProps(nextProps, prevState){
      console.log('getDerivedStateFromProps',nextProps,prevState);
      return null;
    }

#### getSnapshotBeforeUpdate(prevProps, prevState)

在更新阶段 render 后挂载到真实 DOM 前进行的操作，它使得组件能在发生更改之前从 DOM 中捕获一些信息。此组件返回的任何值将作为 `componentDidUpdate` 的第三个参数。

      getSnapshotBeforeUpdate(prevProps, prevState){
        return "getSnapshotBeforeUpdate";
      }

      // 组件更新成功钩子
      componentDidUpdate(prevProps, prevState, snapshot) {
        console.log(snapshot); // "getSnapshotBeforeUpdate"
      }

17 版本执行顺序图谱： ![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/77843a0d241c4e8f9e70af2c8946f563~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

[生命周期网站查询链接](https://link.juejin.cn/?target=https%3A%2F%2Fprojects.wojtekmaj.pl%2Freact-lifecycle-methods-diagram%2F "https&#x3A;//projects.wojtekmaj.pl/react-lifecycle-methods-diagram/")

以上就是 Class 组件的生命周期函数以及使用方法，然而不论 Class 组件多好用，我们已经拥抱 Hooks。

## Function Component 生命周期

`Function Component` 是更彻底的状态驱动抽象，甚至没有 `Class Component` 生命周期的概念，只有一个状态，而 React 负责同步到 DOM。

回顾下在 `Class Component` 的数据请求：

1.  在 `componentDidMount` 初始化发请求；
2.  在 `componentDidUpdate` 判断参数是否变化，变化就调用请求函数重新请求数据；
3.  在 `componentWillUnmount` 生命周期取消发送请求。

那么在函数组件中我们该如何做呢？答案是 `useEffect` 。

### useEffect

`useEffect` 就是一个 `Effect Hook` ，给函数组件增加了操作副作用的能力。它跟 class 组件中的 `componentDidMount` 、 `componentDidUpdate`  和 `componentWillUnmount` 具有相同的用途，只不过被合并成了一个 API。

**useEffect 做了什么：**

-   使用 `useEffect` 相当于告诉 React 组件需要在渲染后执行某些操作，React 将在执行 DOM 更新之后调用它。
-   React 保证了每次运行 `useEffect` 的时候，DOM 已经更新完毕。

Class 组件 Demo：

    class Example extends React.Component {
      constructor(props) {
        super(props);
        this.state = {
          count: 0
        };
      }

      componentDidMount() {
        document.title = `You clicked ${this.state.count} times`;
      }
      componentDidUpdate() {
        document.title = `You clicked ${this.state.count} times`;
      }

      render() {
        return (
          <div>
            <p>You clicked {this.state.count} times</p>
            <button onClick={() => this.setState({ count: this.state.count + 1 })}>
              Click me
            </button>
          </div>
        );
      }
    }

Function Component 重写该案例：

    import React, { useState, useEffect } from 'react';

    function Example() {
      const [count, setCount] = useState(0);

      // 相当于 componentDidMount 和 componentDidUpdate:
      useEffect(() => {
        // 使用浏览器的 API 更新页面标题
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

### 需要清除的 effect

在 class 组件中，我们去监听原生 DOM 事件时，会在 `componentDidMount` 这个生命周期中去做，因为在这里可以获取到已经挂载的真实 DOM。我们也会在组件卸载的时候去取消事件监听避免内存泄露。那么在 `useEffect` 中该如何实现呢？

    useEffect(() => {
      function handleClick(status) {
        document.title = `You clicked ${count} times`;
      }
      
      document.body.addEventListener("click",handleClick,false);
     
      return function cleanup() {
        document.body.removeEventListener("click",handleClick,false);
      };
    });

通过在 `useEffect` 中返回一个函数，它便可以清理副作用。

清理规则是：

-   **首次渲染不会进行清理，会在下一次渲染，清除上一次的副作用；**
-   **卸载阶段也会执行清除操作。**

### Dependencies

在 `useEffect` 中我们会去请求后台数据，通过前面的学习我们也了解到每次更新组件时我们都会再次去执行 `useEffect` ，但其实我们并不需要每次更新组件都发送请求。那么碰到这样的问题如何处理呢？

回顾上面是不是类似于 `componentDidUpdate` 中发送请求呢？直觉是对的，在`componentDidUpdate` 中我们是通过判断参数是否变化来避免每次都发送请求，那么在 `useEffect hook` 中我们也是异曲同工，通过第二个参数是否发生变换来决定是否重复执行，如果第二参数为空数组，则表示只在初始化执行一次，后续更新不会再次调用。

    useEffect(() => {
      fetchData(instanceId){...}
      
      fetchData(instanceId);
    }, [instanceId]);

上面例子是通过 `fetchData` 函数去请求后台数据，具体函数体我们就省略了，然后你会发现`useEffect` 的第二个参数添加了一个数组，其中添加了一个参数 `instanceId`，它表示只有当`instanceId` 变化时，我们才去再次执行 `useEffect`。这样就可以避免我们多次请求后台数据。

当然我们的依赖项还可以传入一个空数组，就表示只在初始化时执行一次：

    useEffect(() => {
      fetchData(instanceId){...}
      
      fetchData(instanceId);
    }, []);

### useCallback

    const memoizedCallback = useCallback(
      () => {
        doSomething(a);
      },
      [a],
    );

把内联回调函数及依赖项数组作为参数传入 `useCallback` ，它将返回该回调函数的 `memoized` 版本，该回调函数仅在某个依赖项改变时才会更新。

通俗来讲当参数 a 发生变化时，会返回一个新的函数引用赋值给 `memoizedCallback` 变量，因此这个变量就可以当做 `useEffect` 第二个参数。这样就有效的将逻辑分离出来。

    function Parent(){
    const [query,setQuery] = useState('q');
      const fetchData = useCallback(()=>{
      ...省略函数体的具体实现
      },[query]);
      return <Child fetchData={fetchData} />
    }
      
    function Child({fetchData}){
      const [data,setData] = useState(null);
    useEffect(()=>{
      fetchData().then(setData);
      },[fetchData])
    }

经过 `useCallback` 包装过的函数可以当作普通变量作为 `useEffect` 的依赖。 `useCallback`做的事情，就是在其依赖变化时，返回一个新的函数引用，触发 `useEffect` 的依赖变化，并激活其重新执行。

现在我们不需要在 `useEffect` 依赖中直接对比 `query` 参数了，而可以直接对比 `fetchData` 函数。useEffect 只要关心 `fetchData` 函数是否变化，而 `fetchData` 参数的变化在 `useCallback` 时关心，能做到 依赖不丢、逻辑内聚，从而容易维护。

## 小结

本文通过讲解 `Class Component` 与 `Function Component`分别的生命周期函数，让我们可以更加深入的理解 React 的运行原理。并且生命周期也是面试中常见问题，因此我们需要熟练掌握。

 [https://juejin.cn/post/6871728918643081230](https://juejin.cn/post/6871728918643081230)
