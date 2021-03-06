---
title: react 基础
date: 2020-10-21 10:07:09
tags: 
    - react 基础
categories: 
    - react
---

## 什么是React?

- React 是一个用于构建用户界面的JavaScript库
- 核心专注于视图,目的实现组件化开发

## 组件化的概念

我们可以很直观的将一个复杂的页面分割成若干个独立组件,每个组件包含自己的逻辑和样式 再将这些独立组件组合完成一个复杂的页面。 这样既减少了逻辑复杂度，又实现了代码的重用

- 可组合：一个组件可以和其他的组件一起使用或者可以直接嵌套在另一个组件内部
- 可重用：每个组件都是具有独立功能的，它可以被使用在多个场景中
- 可维护：每个小的组件仅仅包含自身的逻辑，更容易被理解和维护

*<https://pan.baidu.com/s/1hsivfN2>*

## 跑通react开发环境

```
npm install create-react-app -g
create-react-app <project-name>
cd <project-name> && npm start
```
<!-- more -->
> 默认会自动安装React,react由两部分组成,分别是:

- react.js 是 React 的核心库
- react-dom.js 是提供与DOM相关的功能,会在window下增加ReactDOM属性,内部比较重要的方法是render,将react元素或者react组件插入到页面中。

## 简介JSX

- 是一种JS和HTML混合的语法,将组件的结构、数据甚至样式都聚合在一起定义组件,会编译成普通的Javascript。

> 需要注意的是JSX并不是html,在JSX中属性不能包含关键字，像class需要写成className,for需要写成htmlFor,并且属性名需要采用驼峰命名法！

## createElement

JSX其实只是一种语法糖,最终会通过[babel](https://babeljs.io/repl/)转译成createElement语法,以下代码等价

```
ReactDOM.render(<div>姜,<span>帅哥</span></div>);
ReactDOM.render(React.createElement("div",null,"姜,",React.createElement("span",null,"帅哥")));
```

> 我们一般使用React.createElement来创建一个虚拟dom元素。

## react元素/JSX元素

``` javascript
function ReactElement(type,props) {
    this.type = type;
    this.props = props;
}
let React = {
    createElement(type,props={},...childrens){
        childrens.length===1?childrens = childrens[0]:void 0
        return new ReactElement(type,{...props,children:childrens})
    }
};
```

> ReactElement就是虚拟dom的概念，具有一个type属性代表当前的节点类型，还有节点的属性props

## 模拟render实现

``` javascript
let render = (eleObj,container)=>{
    // 先取出第一层 进行创建真实dom
    let {type,props} = eleObj;
    let elementNode = document.createElement(type); // 创建第一个元素
    for(let attr in props){ // 循环所有属性
        if(attr === 'children'){ // 如果是children表示有嵌套关系
            if(typeof props[attr] == 'object'){ // 看是否是只有一个文本节点
                props[attr].forEach(item=>{ // 多个的话循环判断 如果是对象再次调用render方法
                    if(typeof item === 'object'){
                        render(item,elementNode)
                    }else{ //是文本节点 直接创建即可
                        elementNode.appendChild(document.createTextNode(item));
                    }
                })
            }else{ // 只有一个文本节点直接创建即可
                elementNode.appendChild(document.createTextNode(props[attr]));
            }
        }else if(attr === 'className'){ // 是不是class属性 class 属性特殊处理
            elementNode.setAttribute('class',props[attr]);
        }else{
            elementNode.setAttribute(attr,props[attr]);
        }
    }
    container.appendChild(elementNode)
};
```

## JSX表达式的用法

- 1) 可以放JS的执行结果
- 2) 如果换行需要用()包裹jsx代码
- 3) 可以把JSX元素当作函数的返回值
- 4) <{来判断是表达式还是js

``` javascript
import React from 'react';
import ReactDOM from 'react-dom';

function toResult({name,age}) {
    return <span>今年{name},{age}岁了!</span>
}
let arrs =  [{name:'zfpx',age:8},,{name:'姜文',age:28}];
ReactDOM.render(<div>
    {arrs.map(((item,index)=>(
        typeof item==='object'?<li key={index}>{toResult(item)}</li>:null
    )))}
</div>,document.getElementById('root'));
```

> null也是合法元素,循环时需要带key属性

## JSX属性

- 在JSX中分为普通属性和特殊属性，像class要写成className,for要写成htmlFor
- style要采用对象的方式
- dangerouslyInnerHTML插入html

## 组件的特点声明方式

react元素是是组件组成的基本单位

- 首字母必须大写,目的是为了和JSX元素进行区分
- 组件定义后可以像JSX元素一样进行使用
- 每个组件必须返回唯一的顶级JSX元素
- 可以通过render方法将组件渲染成真实DOM

## 组件的两种定义方式

react怎么区分是组件还是jsx元素？组件名需要开头大写，react组件当作jsx来进行使用

- 第一种方式是函数声明

``` javascript
function Build(props) {
    return <p>{props.name} {props.age}</p>
}
render(<div>
    <Build name={school1.name} age={school1.age}/>
    <Build {...school2} />
</div>,window.root);
```

- 第二种方式是类声明

``` javascript
class Build extends Component{
    render(){
        let {name,age} = this.props;
        return <p>{name} {age}</p>
    }
}
```

> 类声明有状态，this，和声明周期

## 组件中属性和状态的区别

- 组件的数据来源有两个地方
  - props 外界传递过来的(默认属性，属性校验)
  - state 状态是自己的,改变状态唯一的方式就是setState

> 属性和状态的变化都会影响视图更新

## setState 使用

``` javascript
setState(partialState, callback)
1. partialState : object|function
⽤于产⽣与当前state合并的⼦集。
2. callback : function
state更新之后被调⽤。
```

``` javascript
import React, { Component } from "react";
export default class SetStatePage extends Component {
 constructor(props) {
 super(props);
 this.state = {
 counter: 0
 };
 }
 changeValue = v => {
 this.setState({
 counter: this.state.counter + v
 });
 console.log("counter", this.state.counter);
 };
 setCounter = () => {
 this.changeValue(1);
 this.changeValue(2);
 console.log("counter", this.state.counter);
 };
 render() {
 const { counter } = this.state;
 return (
 <div>
 <h3>SetStatePage</h3>
 <button onClick={this.setCounter}>{counter}</button>
 </div>
 );
 }
}
```

- setState只有在合成事件和⽣命周期函数中是异步的，在原⽣事件和setTimeout中都是同步
的，这⾥的异步其实是批量更新。
  要获取到最新状态值有以下⽅式
  - 在回调中获取状态值

    ``` javascript
    changeValue = v => {
      this.setState(
      {
      counter: this.state.counter + v
      },
      () => {
      console.log("counter", this.state.counter);
      }
      );
    };
    ```

  - 使⽤定时器：

    ``` javascript
    setTimeout(() => {

    this.setCounter();
    }, 0);
    ```

  - 原⽣事件中修改状态

    ``` javascript
    componentDidMount(){

      document.body.addEventListener('click', this.changeValue, false)
      }
    ```
  
- 传入对象 setState 的更新会被合并 执行最后一次
  
  ``` javascript
  changeValue = v => {
    this.setState({
    counter: this.state.counter + v
    });
  };
  setCounter = () => {
    this.changeValue(1);
    this.changeValue(2);
  };
  ```

- 传入函数实现链式更新state

  ``` javascript
  changeValue = v => {
  this.setState(state => ({ counter: state.counter + v }));
  };
  setCounter = () => {
    this.changeValue(1);
    this.changeValue(2);
  };
  ```

## 绑定事件

- 给元素绑定事件，事件绑定方式

``` javascript
class Clock extends Component {
    constructor(){
        super();
        this.state = {date:new Date().toLocaleString()}
    }
    componentDidMount(){ //组件渲染完成，当渲染后会自动触发此函数
        this.timer = setInterval(()=>{ // 箭头函数 否则this 指向的是window
            this.setState({date:new Date().toLocaleString()})
        },1000);
    }
    componentWillUnmount(){ //组件将要卸载，当组件移除时会调用
        clearInterval(this.timer); //一般在这个方法中 清除定时器和绑定的事件
    }
    destroy=()=>{ //es7 箭头函数
        // 删除某个组件
        ReactDOM.unmountComponentAtNode(window.root);
    }
    render(){
        // 给react元素绑定事件默认this是undefined,bind方式 在就是箭头函数
        return <h1 onClick={this.destroy}>{this.state.date}</h1>
    }
}
// 执行顺序 constructor -> render -> componentDidMount -> setState-> render - onClick-> unmountComponentAtNode -> componentWillUnmount -> clearInterval
ReactDOM.render(<Clock/>,window.root);

```

> 给jsx元素绑定事件要注意事件中的this指向，事件名采用 on+"开头大写事件名"的方式

## 属性校验,默认属性

``` javascript
import React from 'react';
import ReactDOM from 'react-dom';
import PropTypes from 'prop-types'; //引入属性校验的模块
class School extends React.Component{ // 类上的属性就叫静态属性
    static propTypes = { // 校验属性的类型和是否必填
        age:PropTypes.number.isRequired, // 支持的类型可以参考prop-types的readme文件
    };
    static defaultProps = { // 先默认调用defaultProps
        name:'珠峰',
        age:1
    }; // 默认属性
    constructor(props){ //如果想在构造函数中拿到属性需要通过参数的方式
         //不能在组件中更改属性 不能修改属性*
        super();
    }
    render(){
        return <h1>{this.props.name} {this.props.age}</h1>
    }
}
```

> propTypes和defaultProps名字不能更改，这是react规定好的名称

## 状态的使用

``` javascript
class Counter extends Component{
    constructor(){
        super();
        this.state = {count:0}
    };
    handleClick = ()=>{
        // setState方法会进行合并 setState有两种写法 一种是对象一种是函数
        /*this.setState({count:this.state.count+1});
          this.setState({count:this.state.count+1});*/
        //this.setState((prevState)=>({count:prevState.count+1})); //如果返回的就是一个对象可以用小括号包裹
        //this.setState((prevState)=>({count:prevState.count+1}));
        // 下一个状态是依赖于上一个状态时需要写成函数的方式
        this.setState({count:this.state.count+1},function () {
            this.setState({count:this.state.count+1});
        }); // 这个写法等同于 this.setState((prevState)=>({count:prevState.count+1}));
    };
    render(){
        console.log('render');
        return (
            <p>
                {this.state.count}
                <button onClick={this.handleClick}>+</button>
            </p>
        )
    }
}
ReactDOM.render(<Counter/>,window.root);
```

> 如果设置多个状态setState会合并，如果下一个状态依赖于上一个状态，需要写成函数的方式

## 复合组件

复合组件就是将多个组件进行组合，结构非常复杂时可以把组件分离开

### 不具名

``` javascript
import React, { Component } from "react";
import TopBar from "../components/TopBar";
import BottomBar from "../components/BottomBar";
export default class Layout extends Component {
  componentDidMount() {
    const { title = "商城" } = this.props;
    document.title = title;
  }
  render() {
    const { children, showTopBar, showBottomBar } = this.props;
    console.log("children", children);
    return (
      <div>
        {showTopBar && <TopBar />}
        {children.content}
        {children.txt}
        <button onClick={children.btnClick}>button</button>
        {showBottomBar && <BottomBar />}
      </div>
    );
  }
}

import React, { Component } from "react";
import Layout from "./Layout";
export default class UserPage extends Component {
  render() {
    return (
      <Layout showTopBar={true} showBottomBar={true} title="⽤用户中⼼心">
        <div>
          <h3>UserPage</h3>
        </div>
      </Layout>
    );
  }
}

```

### 具名（传个对象进去）

``` js
import React, { Component } from "react";
import Layout from "./Layout";
export default class HomePage extends Component {
  render() {
    return (
      <Layout showTopBar={false} showBottomBar={true} title="商城⾸首⻚页">
        {{
          content: (
            <div>
              <h3>HomePage</h3>
            </div>
          ),
          txt: "这是个⽂文本",
          btnClick: () => {
            console.log("btnClick");
          },
        }}
      </Layout>
    );
  }
}

```

### 父子组件的通信

``` javascript
class Panel extends Component{
    render(){
        let {header,body} = this.props;
        return (
            <div className="container">
                <div className="panel-default panel">
                    <Header head={header}></Header>
                    <Body b={body}/>
                </div>
            </div>
        )
    }
} // react种需要将属性一层层向下传递 单向数据流
class Body extends Component{
    render(){return (<div className="panel-body">{this.props.b}</div>)}
}
class Header extends Component{
    render(){return (<div className="panel-heading">{this.props.head}</div>)}
}
let data = {header:'我非常帅',body:'长的帅'};
ReactDOM.render(<Panel {...data}/>,window.root);
```

### 子父组件的通信

通过父亲传递给儿子一个函数，儿子调用父亲的函数将值传递给父亲,父亲更新值，刷新视图

``` javascript
class Panel extends Component{
    constructor(){
        super();
        this.state = {color:'primary'}
    }
    changeColor=(color)=>{ //到时候儿子传递一个颜色
        this.setState({color});
    };
    render(){
        return (
            <div className="container">
                <div className={"panel-"+this.state.color+" panel"}>
                    <Header head={this.props.header}
                            change={this.changeColor}
                    ></Header>
                </div>
            </div>
        )
    }
}
class Header extends Component{
    handleClick = ()=>{
        this.props.change('danger'); //调用父亲的方法
    };
    render(){return (
        <div className="panel-heading">
        {this.props.head} <button className="btn btn-danger" onClick={this.handleClick}>改颜色</button>
        </div>)}
}
```

## 受控组件和非受控组件

- 受状态控制的组件，必须要有onChange方法，否则不能使用
- 受控组件可以赋予默认值（官方推荐使用 受控组件）

### 实现双向数据绑定

``` javascript
class Input extends Component{
    constructor(){
        super();
        this.state = {val:'100'}
    }
    handleChange = (e) =>{ //e是事件源
        let val = e.target.value;
        this.setState({val});
    };
    render(){
        return (<div>
            <input type="text" value={this.state.val} onChange={this.handleChange}/>
            {this.state.val}
        </div>)
    }
}
```

### 受控组件

``` javascript
class Sum extends Component{
    constructor(){
        super();
        this.state = {a:1,b:1}
    }
    // key表示的就是当前状态改的是哪一个
    // e表示的是事件源
    handleChange(key,e){ //处理多个输入框的值映射到状态的方法
        this.setState({
            [key]:parseInt(e.target.value) || 0
        })
    }
    render(){
        return (
            <div>
                <input type="number" value={this.state.a} onChange={e=>{this.handleChange('a',e)}}/>
                <input type="number" value={this.state.b} onChange={e=>{this.handleChange('b',e)}}/>
                {this.state.a+this.state.b}
            </div>
        )
    }
}
```

### 非受控组件

``` javascript
class Sum extends Component{
    constructor(){
        super();
        this.state =  {result:''}
    }
    //通过ref设置的属性 可以通过this.refs获取到对应的dom元素
    handleChange = () =>{
        let result = this.refs.a.value + this.b.value;
        this.setState({result});
    };
    render(){
        return (
            <div onChange={this.handleChange}>
                <input type="number" ref="a"/>
                {/*x代表的真实的dom,把元素挂载在了当前实例上*/}
                <input type="number" ref={(x)=>{
                    this.b = x;
                }}/>
                {this.state.result}
            </div>
        )
    }
}
```

## 生命周期

``` javascript
class Counter extends React.Component{ // 他会比较两个状态相等就不会刷新视图 PureComponent是浅比较
  static defaultProps = {
    name:'珠峰培训'
  };
  static propTypes = {
    name: String,
  }
  constructor(props){
    super();
    this.state = {number:0}
    console.log('1.constructor构造函数')
  }
  componentWillMount(){ // 取本地的数据 同步的方式：采用渲染之前获取数据，只渲染一次
    console.log('2.组件将要加载 componentWillMount');
  }
  componentDidMount(){
    console.log('4.组件挂载完成 componentDidMount');
  }
  handleClick=()=>{
    this.setState({number:this.state.number+1});
  };
  // react可以shouldComponentUpdate方法中优化 PureComponent 可以帮我们做这件事
  shouldComponentUpdate(nextProps,nextState){ // 代表的是下一次的属性 和 下一次的状态
    console.log('5.组件是否更新 shouldComponentUpdate');
    return nextState.number%2;
    // return nextState.number!==this.state.number; //如果此函数种返回了false 就不会调用render方法了
  } //不要随便用setState 可能会死循环
  componentWillUpdate(){
    console.log('6.组件将要更新 componentWillUpdate');
  }
  componentDidUpdate(){
    console.log('7.组件完成更新 componentDidUpdate');
  }
  render(){
    console.log('3.render');
    return (
      <div>
        <p>{this.state.number}</p>
        {this.state.number>3?null:<ChildCounter n={this.state.number}/>}
        <button onClick={this.handleClick}>+</button>
      </div>
    )
  }
}
class ChildCounter extends Component{
  componentWillUnmount(){
    console.log('组件将要卸载componentWillUnmount')
  }
  componentWillMount(){
    console.log('child componentWillMount')
  }
  render(){
    console.log('child-render')
    return (<div>
      {this.props.n}
    </div>)
  }
  componentDidMount(){
    console.log('child componentDidMount')
  }
  componentWillReceiveProps(newProps){ // 第一次不会执行，之后属性更新时才会执行
    console.log('child componentWillReceiveProps')
  }
  shouldComponentUpdate(nextProps,nextState){
    return nextProps.n%3; //子组件判断接收的属性 是否满足更新条件 为true则更新
  }
}
// defaultProps
// constructor
// componentWillMount
// render
// componentDidMount
// 状态更新会触发的
// shouldComponentUpdate nextProps,nextState=>boolean
// componentWillUpdate
// componentDidUpdate
// 属性更新
// componentWillReceiveProps newProps
// 卸载
// componentWillUnmount
```

### 16.3 之前的

![lifeCycle](react15.png)

### 16.3 之后的

![lifeCycle](react16.3.png)
V17可能会废弃的三个⽣生命周期函数⽤用getDerivedStateFromProps替代，⽬目前使⽤用的话加上UNSAFE_：

- componentWillMount
- componentWillReceiveProps
- componentWillUpdate
引⼊入两个新的⽣生命周期函数：

- static getDerivedStateFromProps
    getDerivedStateFromProps 会在调⽤用 render ⽅方法之前调⽤用，并且在初始挂载及后续更更新时都会被
    调⽤用。它应返回⼀一个对象来更更新 state，如果返回 null 则不不更更新任何内容。
    请注意，不不管原因是什什么，都会在 每次 渲染前触发此⽅方法。这与UNSAFE_componentWillReceiveProps 形成对⽐比，后者仅在⽗父组件重新渲染时触发，而不不是在内部调⽤用  setState 时。

- getSnapshotBeforeUpdate
    getSnapshotBeforeUpdate(prevProps, prevState)
    在render之后，在componentDidUpdate之前
    getSnapshotBeforeUpdate() 在最近⼀一次渲染输出（提交到 DOM 节点）之前调⽤用。它使得组件能
在发⽣生更更改之前从 DOM 中捕获⼀一些信息（例例如，滚动位置）。此⽣生命周期的任何返回值将作为参数传
递给  componentDidUpdate(prevProps, prevState, snapshot)

如果不不想⼿手动给将要废弃的⽣生命周期添加  UNSAFE_ 前缀，可以⽤用下⾯面的命令。

``` cmd
npx react-codemod rename-unsafe-lifecycles <path>
```
