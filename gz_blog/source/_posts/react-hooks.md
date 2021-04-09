---
title: react-hooks
date: 2020-10-25 10:08:09
tags: 
    - react-hooks
categories: 
    - react
---

## 认识hooks

* Hook 是⼀一个特殊的函数，它可以让你“钩⼊入” React 的特性。例例如， useState 是允许你在 React 函数组件中添加 state 的 Hook
* 如果你在编写函数组件并意识到需要向其添加⼀一些 state，以前的做法是必须将其它转化为 class。现在你可以在现有的函数组件中使⽤用 Hook

  ``` js
    import React, { useState } from "react";
    export default function HookPage(props) {
    // 声明⼀一个叫 “count” 的 state 变量量，初始化为0
    const [count, setCount] = useState(0);
    return (
    <div>
        <h3>HookPage</h3>
        <p>{count}</p>
        <button onClick={() => setCount(count + 1)}>add</button>
    </div>
    );
    }
  
  ```

## 使⽤ Effect Hook

* Effect Hook 可以让你在函数组件中执⾏行行副作⽤用操作。
 数据获取，设置订阅以及⼿手动更更改 React 组件中的 DOM 都属于副作⽤用。不不管你知不不知道这些操作，或
 是“副作⽤用”这个名字，应该都在组件中使⽤用过它们。

``` js
 import React, { useState, useEffect } from "react";
 export default function HookPage(props) {
 // 声明⼀一个叫 “count” 的 state 变量量，初始化为0
 const [count, setCount] = useState(0);
 // 与 componentDidMount 和 componentDidUpdate相似
 useEffect(() => {
 // 更更新 title
 document.title = `You clicked ${count} times`;
 });
 return (
 <div>
 <h3>HookPage</h3>
 <p>{count}</p>
 <button onClick={() => setCount(count + 1)}>add</button>
 </div>
 );
 }
 
```

 在函数组件主体内（这⾥里里指在 React 渲染阶段）改变 DOM、添加订阅、设置定时器器、记录⽇日志以及执
 ⾏行行其他包含副作⽤用的操作都是不不被允许的，因为这可能会产⽣生莫名其妙的 bug 并破坏 UI 的⼀一致性。
 使⽤用  useEffect 完成副作⽤用操作。赋值给  useEffect 的函数会在组件渲染到屏幕之后执⾏行行。你可以
 把 effect 看作从 React 的纯函数式世界通往命令式世界的逃⽣生通道。

## effect 的条件执⾏

默认情况下，effect 会在每轮组件渲染完成后执⾏行行。这样的话，⼀一旦 effect 的依赖发⽣生变化，它就会被
重新创建。
然⽽而，在某些场景下这么做可能会矫枉过正。⽐比如，在上⼀一章节的订阅示例例中，我们不不需要在每次组件
更更新时都创建新的订阅，⽽而是仅需要在  source props 改变时重新创建。
要实现这⼀一点，可以给  useEffect 传递第⼆二个参数，它是 effect 所依赖的值数组。更更新后的示例例如
下：

``` js
import React, { useState, useEffect } from "react";
export default function HookPage(props) {
// 声明⼀一个叫 “count” 的 state 变量量，初始化为0
const [count, setCount] = useState(0);
const [date, setDate] = useState(new Date());
// 与 componentDidMount 和 componentDidUpdate相似
useEffect(() => {
// 更更新 title
 document.title = `You clicked ${count} times`;
 }, [count]);
 useEffect(() => {
 const timer = setInterval(() => {
  setDate(new Date());
  }, 1000);
 }, []);
 return (
 <div>
 <h3>HookPage</h3>
 <p>{count}</p>
 <button onClick={() => setCount(count + 1)}>add</button>
 <p>{date.toLocaleTimeString()}</p>
 </div>
 );
}
```

此时，只有当 useEffect第⼆二个参数数组⾥里里的数值 改变后才会重新创建订阅

## 清除 effect

组件卸载时需要清除 effect 创建的诸如订阅或计时器器 ID 等资源。要实现这⼀一点， useEffect
函数需返回⼀一个清除函数，以防⽌止内存泄漏漏，清除函数会在组件卸载前执⾏行行。

``` js
useEffect(() => {
const timer = setInterval(() => {
setDate(new Date());
}, 1000);
return () => clearInterval(timer);
}, []);
```

## 自定义hooks

自定义 Hook 是⼀一个函数，其名称以 “use” 开头，函数内部可以调⽤用其他的 Hook。
`<p>{useClock().toLocaleTimeString()}</p>`

``` javascript
//⾃自定义hook，命名必须以use开头
function useClock() {
  const [date, setDate] = useState(new Date());
  useEffect(() => {
    console.log("date effect");
    //只需要在didMount时候执⾏行行就可以了了
    const timer = setInterval(() => {
    setDate(new Date());
    }, 1000);
    //清除定时器器，类似willUnmount
    return () => clearInterval(timer);
  }, []);
  return date;
}
```

## hooks 使用规则

* 只能在函数最外层调⽤用 Hook。不不要在循环、条件判断或者⼦子函数中调⽤用。
* 只能在 React 的函数组件中调⽤用 Hook。不不要在其他 JavaScript 函数中调⽤用。（还有⼀一个地⽅方可
以调⽤用 Hook —— 就是⾃自定义的 Hook 中。）

## useMemo

把“创建”函数和依赖项数组作为参数传⼊入  useMemo ，它仅会在某个依赖项改变时才重新计算memoized 值。这种优化有助于避免在每次渲染时都进⾏行行⾼高开销的计算

``` javascript
import React, { useState, useMemo } from "react";
export default function UseMemoPage(props) {
  const [count, setCount] = useState(0);
  const expensive = useMemo(() => {
  console.log("compute");
  let sum = 0;
  for (let i = 0; i < count; i++) {
  sum += i;
  }
  return sum;
  //只有count变化，这⾥里里才重新执⾏行行
  }, [count]);
  const [value, setValue] = useState("");
  return (
    <div>
      <h3>UseMemoPage</h3>
      <p>expensive:{expensive}</p>
      <p>{count}</p>
      <button onClick={() => setCount(count + 1)}>add</button>
      <input value={value} onChange={event => setValue(event.target.value)} />
    </div>
  );
}
```

## useCallback

把内联回调函数及依赖项数组作为参数传⼊入  useCallback ，它将返回该回调函数的 memoized 版本，
该回调函数仅在某个依赖项改变时才会更更新。当你把回调函数传递给经过优化的并使⽤用引⽤用相等性去避
免⾮非必要渲染（例例如  shouldComponentUpdate ）的⼦子组件时，它将⾮非常有⽤用。

``` javascript
import React, { useState, useCallback, PureComponent } from "react";
export default function UseCallbackPage(props) {
  const [count, setCount] = useState(0);
  const addClick = useCallback(() => {
  let sum = 0;
  for (let i = 0; i < count; i++) {
  sum += i;
  }
  return sum;
  }, [count]);
  const [value, setValue] = useState("");
  return (
  <div>
  <h3>UseCallbackPage</h3>
  <p>{count}</p>
  <button onClick={() => setCount(count + 1)}>add</button>
  <input value={value} onChange={event => setValue(event.target.value)} />
  <Child addClick={addClick} />
  </div>
  );
}
class Child extends PureComponent {
render() {
  console.log("child render");
  const { addClick } = this.props;
  return (
  <div>
  <h3>Child</h3>
  <button onClick={() => console.log(addClick())}>add</button>
  </div>
  );
}
}

```

`useCallback(fn, deps) 相当于  useMemo(() => fn, deps) 。`
依赖项数组不不会作为参数传给“创建”函数。虽然从概念上来说它表现为：所有“创建”函数中引⽤用的
值都应该出现在依赖项数组中。未来编译器器会更更加智能，届时⾃自动创建数组将成为可能
