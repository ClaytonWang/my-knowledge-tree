# React hook使用详解
因为篇幅原因，React hook 的由来和影响这里不做介绍。本文主要介绍的是 hook 的基本 API，还有从 class 编程迁移到 hook 编程过程中的细节记录和心得体会

## 目录

-   useState
-   useEffect
-   useCallback
-   useMemo
-   useRef

## Ⅰ.**useState**

**1.useState 的基本用法**

useState 是 hook 提供的一个最基本的 API，通过调用 useState 方法，能返回一个数组：

-   数组的第一项是返回的 state 数值
-   数组的第二项是修改这个 state 数值的函数
-   传入 useState 的参数就是这个 state 的初始值

例如有以下代码，设计一个点击时显示数字递增的按钮

    import React, { useState } from "react";

    export default function Example() {
      const [count, setCount] = useState(0);
      return <button onClick={() => setCount(count + 1)}>{count}</button>;
    } 

![](https://upload-images.jianshu.io/upload_images/23680806-bbd41ba7e93ebebc.png)

image

上面的代码在 Class 组件中相当于

    export default class Example extends React.Component {
      state = {
        count: 0
      };
      setCount = count => {
        this.setState({
          count: count + 1
        });
      };
      render() {
        const { count } = this.state;
        return <button onClick={() => this.setCount(count)}>{count}</button>;
      }
    } 

**2. 以回调的方式修改 state**

set 方法除了直接接收新值修改 state 外，还可以通过回调的方式修改 state，例如下面：setCount 接收的回调里，回调的参数就是当前的 state.count，而回调的返回值是更新后的 state.count

    import React, { useState } from "react";
    export default function Example() {
      const [count, setCount] = useState(0);
      return <button onClick={() => setCount(count => count + 1)}>{count}</button>;
    } 

**3.useState 的回调参数：节约 state 初始化带来的性能损耗**

useState 初始化 state 时一般比较简单，对于它带来的性能损耗可以忽略不计，但如果遇到 state 创建时有较大计算量的情况的话，重复渲染的过程中就可能带来比较昂贵的性能损耗，这时我们可以把一个回调传给 useState，计算后返回值会作为 state 的初始值。 这个回调只在函数组件入栈的时候调用一次，就可以节约重复计算的性能损耗。

    import React, { useState } from "react";
    export default function Example() {
      const [count, setCount] = useState(() => {
        
        const v = 1 + 1 * 1 - 2;
        return v;
      });
      return <button onClick={() => setCount(count => count + 1)}>{count}</button>;
    } 

**4. 模拟 setState 更新完成后的异步回调**

在 class 组件的编程当中，我们有时会遇到这样的需求：在 setState 完成后执行某项异步回调，但函数组件的 set 方法是没有第二个参数的，那我们应该怎么处理呢？

实际上可以结合 useEffect 和 useRef 来实现（下文会详细介绍这两个 API）

-   **useEffect**: 可监听某个依赖 state 的变化并异步执行响应函数
-   **useRef**：因为 useEffect 除了依赖参数变化会调用外，组件入栈时也会调用，useRef 主要是通过标记排除组件入栈的情况


    import React, { useState, useEffect, useRef } from "react";
    export default function Example() {
      const [count, setCount] = useState(0);
      const isMountedRef = useRef(true);
      useEffect(() => {
        if (isMountedRef.current) {
          isMountedRef.current = false;
          return;
        }
        
        console.log("count被改变了，当前值为" + count);
      }, [count]);

      return <button onClick={() => setCount(count + 1)}>{count}</button>;
    } 

运行结果

\[图片上传失败...(image-b96fdc-1610348773478)]

## Ⅱ. **useEffect**

> 使用 useEffect 完成副作用操作。赋值给 useEffect 的函数会在组件渲染到屏幕之后执行。你可以把 effect 看作从 React 的纯函数式世界通往命令式世界的逃生通道。

**1.useEffect 不同写法的执行差别**

**(1) 不写第二个参数**

会在函数组件初次渲染和每次重渲染的时候调用，包括 props 改变和 state 改变导致的更新。

效果相当于**componentDidMount** + **componentDidUpdate** + **componentWillUnmount**

**(2) 第二个参数为空数组**

只在入栈的时候运行一次

效果相当于**componentDidMount**

**(3)useEffect 使用返回值**

返回值是一个函数，将会在组件销毁时候调用

如果 useEffect 按照上面 1 中编写方式不写第二个参数，也就是只在入栈时运行一次的话，那么此时返回函数效果相当于**componentWillUnmount**

    useEffect(() => {
       let id = setInterval(() => { ... }, 1000);
       return () => clearInterval(id);
    },[]); 

**(4)useEffect 在第二个参数中写入数据属性**

这种写法的效果是：除了初次入栈被以外，将只在数据属性改变的时候才运行 useEffect 内的函数, 如下面代码中 useEffect 内匿名函数将会伴随 count 的变化而调用 (初次入栈时 count 也会被识别为是 "变化" 的)

    const [count, setCount] = useState(0);
    useEffect(() => {
      
    }, [count]); 

某种程度上说，它相当于 class 组件中的以下写法

    componentDidUpdate(prevProps, prevState) {
      if (prevState.count !== this.state.count) {
        
      }
    } 

**2.useEffect 不写第二个参数进行调用时和函数直接调用的区别**

两者在执行次数上是一样的，区别在于 useEffect 是异步的，而函数内调用是同步的

例如有以下代码

    export default function Example() {
      useEffect(() => {
        console.log("useEffect调用");
      });
      console.log("函数调用");
      return <div />;
    } 

![](https://upload-images.jianshu.io/upload_images/23680806-e868eb059444092e.jpg)

image

但你不能因此就把副作用直接写在组件函数内部

> 在函数组件主体内 (这里指在 React 渲染阶段) 改变 DOM、添加订阅、设置定时器、记录⽇志以及执 ⾏其他包含副作⽤用的操作都是不不被允许的，因为这可能会产生莫名其妙的 bug 并破坏 UI 的一致性。

**3. 函数组件内多个 useEffect 的执行次序**

函数组件内部是可以写入多个 useEffect 的，如果这几个 useEffect 内的函数都是同步代码且执行条件相同的话 (useEffect 第二个参数相同)，理论上多个 useEffect 内部函数是会按照编写时从上到下的次序执行的。

从源码上看， 组件加载时会依次执行各个 useEffect，然后根据先后次序建立链表，而在 effect 执行时遍历链表，依次判断条件并执行 effect 函数

    参考资料： https://www.cnblogs.com/vvjiang/p/12160791.html 

**（当然最好还是不要在逻辑上依赖于这个顺序，毕竟官方文档并没有特别陈述这一点）**

    export default function Example() {
      useEffect(() => {
        console.log(1);
      }, []);
      useEffect(() => {
        console.log(2);
      }, []);
      useEffect(() => {
        console.log(3);
      }, []);
      return <div />;
    } 

输出

![](https://upload-images.jianshu.io/upload_images/23680806-3b6ac6cee9148ebf.png)

image

**4.useEffect 访问外部依赖的限制**

下面用一个例子加以说明，我们来实现这样一个功能：**在页面中显示一个从 0 开始每隔 1 秒增加 1 的变化数字。** 

1\. 我们可能想要在 1 中的回调里面更新 state, 然而这却可能遇到问题，例如以下代码中，我们在 useEffect 中先访问了外部的 count 变量，然后在原来的 count 值的基础上去 setCount

    export default function Example() {
      const [count, setCount] = useState(0);
      useEffect(() => {
        let id = setInterval(() => {
          setCount(count + 1);
        }, 1000);
        return () => clearInterval(id);
      }, []);
      return <div>{count}</div>;
    } 

**问题来了**: React Hook 的 eslint 检查器会提醒你以下警告，说必须要声明依赖

    React Hook useEffect has a missing dependency: 'count'. 

What?! ! 这里就形成一个矛盾了：

-   我之所以不声明 count 依赖，就是为了只在入栈的时候执行一次模拟 componentDidMount 的效果
-   而如果声明了 count 依赖，上面的方法就会在每次 setCount 的时候重新执行一遍 Effect 回调, 那就不是我要的 componentDidMount 了呀

**解决办法**：通过 setState 接收回调参数的方式更新 state，这样就不用访问 useEffect 外部的 state 了

    setCount(count => count + 1); 

具体代码如下

    export default function Example() {
      const [count, setCount] = useState(0);
      useEffect(() => {
        let id = setInterval(() => {
          setCount(count => count + 1);
        }, 1000);
        return () => clearInterval(id);
      }, []);
      return <div>{count}</div>;
    } 

**总结**： 使用 useEffect 访问外部数据的时候要小心，如果可以的话尽量使逻辑闭合在 useEffect 内部

## Ⅲ. **useMemo**

useMemo 这个 API 的作用是用来优化渲染性能的，它接收两个参数，

-   第一个参数是一个计算某个值的函数
-   第二个参数是一个依赖数组。组件重渲染的时候会根据依赖数组是否变化决定是否重新计算

根据接收参数的不同 useMemo 的执行情况如下：

-   **不传第二个参数时**：每次组件渲染 useMemo 接收函数都会调用并返回计算值
-   **第二个参数为空数组时**：只有组件首次加载时候 useMemo 接收函数才会调用返回计算值，后续重渲染都返回第一次计算的缓存值
-   **第二个参数为依赖数组时**：当依赖发生改变时 useMemo 调用接收函数并返回值，如果依赖相比前一次渲染没有改变就返回缓存值

例如下面这个例子，

1.  我们每隔一秒就通过 setCount 使组件重渲染,
2.  但全过程中只调用 setText 了 1 次，也即只改变了 text 一次
3.  然后以 text 为依赖调用 useMemo 计算函数。


    export default function Example() {
      let [text, setText] = useState("默认文本");
      let [count, setCount] = useState(0);
      useEffect(() => {
        
        const id = setInterval(() => setCount(count => count + 1), 1000);
        
        setTimeout(() => setText("修改后文本"), 1000);
        return () => clearInterval(id);
      }, []);
      
      let t = useMemo(() => {
        console.log("memo调用");
        return "当前文本:" + text;
      }, [text]);
      
      console.log("组件渲染");
      return (
        <div>  <p>{t}</p>  <p>统计数:{count}</p>  </div>
      );
    } 

通过控制台观察到 useMemo 的函数只在 text 变化时候才会进行实际的调用

\[图片上传失败...(image-87dc0-1610348773477)]

**React.memo**

React.memo 这个顶层 API 可以实现类似于 PureComponent 的功能

    const NewComponent = React.memo(function MyComponent(props) {
     
    })

    class  NewComponent extends React.PureComponent {  
      
    } 

React.memo 还可以接收一个比较函数作为第二个参数，当返回 true 时会阻止组件重渲染，返回 false 则不阻止

    function isEqual(preProps, nextProps) {
      return preProps.index === nextProps.index;
    }
    function Item({ index }) {
      return <div>{index}</div>;
    }
    export default React.memo(Item, isEqual); 

## Ⅳ. **useCallback**

useCallback 的作用规律也是和 useEffect, useMemo 相似的

-   **不传第二个依赖参数时**：每次渲染都把传入的函数原样返回，每次返回的都是新的函数引用
-   **第二个参数为空数组时**：每次渲染都返回缓存的第一次传入的函数引用
-   **第二个参数为一个依赖数组时**，只有依赖改变时才返回接收到的新函数引用，如果依赖没有改变就返回之前缓存的函数引用

**1.useCallback 和 useMemo 的异同**

useMemo 和 useCallback 也具有缓存作用，并可以用于优化渲染性能。但两者也有区别：

**(1) 执行逻辑不同**

-   useMemo 缓存的是**计算结果**，而 useCallback 缓存的是**函数引用**。
-   useMemo 是会对传入函数做计算的，而 useCallback 不会运行传入的函数，它只会选择性地返回函数引用

**(2) 使用目的不同**

-   useMemo 的性能优化是**针对当前组件**的
-   useCallback 的性能优化**不是针对当前组件的，而是针对当前组件的子组件的**

（这句话将在下文将着重解释，详见下文\[useCallback 的语义陷阱]一节）

“针对子组件” 是什么意思？ 让我们先从一段既有性能代码的问题开始讲起吧。

有以下代码：在 Example 组件中写入一个子组件 Item, 子组件 Item 被设计为一个 PureComponent，也就是只有在 props 发生变化时才会重新渲染。我们定义一个 onClick 方法传递给 pure 子组件 Item。

    import React, { useEffect, useState } from "react";

    let Item = React.memo(function({ onClick }) {
      console.log(`item组件渲染`);
      return <div onClick={onClick}>item</div>;
    });

    export default function Example() {
      let [count, setCount] = useState(0);
      useEffect(() => {
        
        const id = setInterval(() => setCount(count => count + 1), 1000);
        return () => clearInterval(id);
      }, []);
      
      let f = () => {};
      return (
        <div>  <div>重渲染次数：{count}</div>  <Item onClick={f} />  </div>
      );
    } 

一切看起来都很正常，但其实这段代码是有性能问题的，请看下控制台：控制台显示作为 pureComponet 的 item 每一次都被重渲染了！

![](https://upload-images.jianshu.io/upload_images/23680806-9421107908837cb6.jpg)

image

在这里，我们遇到了一个 Class 组件编程中不会遇到的问题：因为事件函数 onClick 的赋值在组件渲染函数的内部，**所以每次重渲染的时都会重新创建并赋值, 从而使传入 Item 子组件的 props 是一个新的函数引用，最后导致 Item 重复进行不必要的重渲染**，React.memo 的优化失效。

显然，我们希望 onClick 只要开始的时候创建一次就好，最好缓存起来，后面直接获取之前缓存的 onClick 就好了，这正是 useCallback 给我们起到的作用。

**我们只要稍微改一改就能解决上面的问题**

    let Item = React.memo(function({ onClick }) {
      console.log(`item组件渲染`);
      return <div onClick={onClick}>item</div>;
    });

    export default function Example() {
      let [count, setCount] = useState(0);
      useEffect(() => {
        
        const id = setInterval(() => setCount(count => count + 1), 1000);
        return () => clearInterval(id);
      }, []);
      
      let f = useCallback(() => {}, []);
      return (
        <div>  <div>重渲染次数：{count}</div>  <Item onClick={f} />  </div>
      );
    } 

输出如下，可以看到多次渲染的时候，子组件只渲染了一次

![](https://upload-images.jianshu.io/upload_images/23680806-5dbf03343f6f9c0b.jpg)

image

**2.useCallback 的语义陷阱**

前文讲过一句话：

> useCallback 的性能优化不是针对当前组件的，而是针对当前组件的子组件的”

也就是说如果当前组件没有需要优化的子组件的话，useCallback 其实是派不上用场的。并不能起到优化性能的作用，反而还会增加性能损耗。

因为 useMemo 这个方法的影响，我们可能会误以为下面这段代码里 useCallback 也能够优化性能

    export default function Example() {
      let f = useCallback(() => { ... }, []);
      return <div onClick={f}></div>;
    } 

但实际上是不能的，因为它等效于于：

    export default function Example() {
      
      let fn = () => {};
      
      let f = useCallback(fn, []);
      return <div onClick={f}></div>;
    } 

这样看就很清晰了：这里使用 useCallback 不但不能节约性能，反而还会因为 useCallback 的比较逻辑增加性能损耗。

## Ⅴ. **useRef**

React hook 中的 useRef 有两个作用

-   作为存值对象使用，起到类似 class 组件中 this 的作用
-   读取到当前最新值而非旧的 “快照”
-   获取上一轮次渲染的 state 或 props

**作为存值对象使用**

在函数式组件中你是不能使用 this 的，当你想用 this 又找不到用法的时候，也许 useRef 就是你想要的东西。

useRef 就是在函数式组件中能够 “替代”class 中 this 的一个 api（也许这里用等效一词更合适一些）。

useRef 返回的 ref 对象，自创建后会在函数组件的整个生命周期中一直留存。也就是说，当次渲染时写入 ref 的数据能在下次渲染时读取出来。

useRef 调用后会返回一个含有 current 属性的对象， 这个对象的. current 属性被初始化为传入的参数，并且可以在后续进行修改。

例如有以下代码：我们通过对 ref.current 的读写实现根据是否为首次渲染返回不同文本

    import React, { useEffect, useState, useRef } from "react";
    export default function Example() {
      const [count, setCount] = useState(0);
      
      useEffect(
        () =>
          setTimeout(() => {
            setCount(1);
          }, 1000),
        []
      );
      
      let isMountedRef = useRef(true);
      if (isMountedRef.current) {
        isMountedRef.current = false;
        return <div>首次渲染</div>;
      }
      return <div>非首次渲染</div>;
    } 

UI 变化

**读取到当前最新值而非旧的 “快照”**

useRef 的另外一个作用是通过引用取值的方式，读取到当前的 props 的最新值而非旧的 “快照”

函数式组件带来的一个可能的问题是获取数据的滞后性，当前的 state 在使用的时候可能是旧的而不是最新的。

以下面官方文档提供的 demo 为例：页面上有一个按钮和一个弹框，点击按钮后在页面上同步\[点击次数]，同时点击弹框的时候能把当前\[点击次数]以弹框的形式弹出。

    import React, { useCallback, useEffect, useState, useRef } from "react";
    function Example() {
      const [count, setCount] = useState(0);

      function handleAlertClick() {
        setTimeout(() => {
          alert('你点击了: ' + count);
        }, 3000);
      }

      return (
        <div>  <p>You clicked {count} times</p>  <button onClick={() => setCount(count + 1)}> 按钮 </button>  <button onClick={handleAlertClick}> 弹框 </button>  </div>
      );
    } 

运行上面的代码

如果你是先点按钮再点弹框，那么页面显示是同步的，你点击了按钮多少次，弹框就会提示你已经点击的次数

![](https://upload-images.jianshu.io/upload_images/23680806-23559dc12d05fd80.jpg)

image

但如果你是先点弹框再点按钮，结果就可能是滞后的，例如下面那样，如果先点弹框，我们明明接下来连续点了按钮 8 次了，但是弹框还是显示 0 次

![](https://upload-images.jianshu.io/upload_images/23680806-5bacac169ecba2d9.jpg)

image

为什么会出现这两种截然不同的结果？我们来分析一下。

**1. 先点按钮再点弹框**

这会触发 setCount 并使页面重渲染，handleAlertClick 会被重新声明，重新声明时它获取到的 count 是最新的，这时候当然页面显示是同步的

**2. 先点弹框再点按钮**

点击弹框的瞬间就发起了一个异步调用，这个时候读取的 count 是一个基本类型的数值而不是一个引用，所以它的值就被 “固定” 下来了，这就是导致弹框内的弹出次数滞后于按钮点击次数的原因

     const [count, setCount] = useState(0);
      function handleAlertClick() {
        setTimeout(() => {
          alert('你点击了: ' + count);
        }, 3000);
      } 

**使用 useRef 解决这个问题**

useRef 创建的是一个在函数组件生命周期内一直存续的对象引用，能够帮助解决这种 “旧值” 问题

如下所示，我们通过 useRef 进行改造

    import React, { useCallback, useEffect, useState, useRef } from "react";

    export default function Example() {
      const [count, setCount] = useState(0);
      const countRef = useRef(0);
      function handleAlertClick() {
        
        setTimeout(() => {
          alert("你点击了: " + countRef.current);
        }, 3000);
      }

      return (
        <div>  <p>你点击了按钮 {count} 次</p>  <button
            onClick={() => {
              setCount(count + 1);
              
              countRef.current = count + 1;
            }}
          > 按钮 </button>  <button onClick={handleAlertClick}>弹框</button>  </div>
      );
    } 

运行结果如下，现在先点弹框再点按钮也可以显示正常了

\[图片上传失败...(image-37a943-1610348773477)]

**获取上一次渲染的 state 或 props**

这一点，官方文档的例子说的很明白了

    function Counter() {
      const [count, setCount] = useState(0);
      const prevCount = usePrevious(count);  
      return <h1>当前: {count}, 上一次: {prevCount}</h1>;
    }

    function usePrevious(value) {  
      const ref = useRef();
      useEffect(() => {
        ref.current = value;
      });
      return ref.current;
    } 

 [https://www.jianshu.com/p/309ae83fe57e](https://www.jianshu.com/p/309ae83fe57e)
