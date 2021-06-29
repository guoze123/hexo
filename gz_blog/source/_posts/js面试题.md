---
title: js面试题
date: 2020-10-29 11:05:28
tags: js面试题
categories: js面试题
---
# html

# css

# javascript

## == 操作符的强制类型转换规则

* 字符串和数字之间的相等比较，将字符串转换为数字之后再进行比较
* 其他类型和布尔类型之间的相等比较，先将布尔值转换为数字后，再应用其他规则进行比较
* null 和 undefined 之间的相等比较，结果为真。其他值和它们进行比较都返回假值。
* 对象和非对象之间的相等比较，对象先调用 ToPrimitive 抽象操作后，再进行比较
* 如果一个操作值为 NaN ，则相等比较返回 false（ NaN 本身也不等于 NaN ）
* 如果两个操作值都是对象，则比较它们是不是指向同一个对象。如果两个操作数都指向同一个对象，则相等操作符返回 true，否则，返回 false。

## ===、Object.is()、Set去重的区别

* 这几个的差异是在-0与+0；NaN上
* === -0与+0是相等的 NaN与NaN是不相等的
* Object.is()  与===相反，-0与+0是不相等的，NaN与NaN是相等的
* Set 认为两组都是相等的
* includes 和 Set 相同

## this指向 当前方法执行的主体(谁执行的这个方法,那么THIS就是谁,所以THIS和当前方法在哪创建的或者在哪执行的都没有必然的关系)

* 给元素的某个事件绑定方法，方法中的THIS都是当前操作的元素本身
* 函数执行，看函数前面是否有点，有的话，点前面是谁THIS就是谁，没有点，THIS是WINDOW（在JS的严格模式下，没有点THIS是UNDEFINED）
* 构造函数执行，方法中的this一般都是当前类的实例
* 箭头函数中没有自己的THIS,THIS是上下文中的THIS
* 在小括号表达式中，会影响THIS的指向
* 使用call/apply/bind可以改变this指向
<!-- more -->

## case语句是使用恒等（===）来判断的

## 正则 test 方法的参数如果不是字符串，会经过抽象 ToString操作强制转成字符串

## JSON.stringify 拷贝时obj 的缺陷

![JSON.stringify](json.stringify.png)

* 拷贝的对象的值中如果有函数、undefined、symbol 这几种类型，经过 JSON.stringify 序列化之后的字符串中这个键值对会消失
* 拷贝 Date 引用类型会变成字符串
* 无法拷贝不可枚举的属性
* 无法拷贝对象的原型链
* 拷贝正则会变成空对象
* 对象中含有 NaN、Infinity 以及 -Infinity，JSON 序列化的结果会变成 null
* 无法拷贝对象的循环应用，即对象成环 (obj[key] = obj)

## js 常见的6中继承方式

 ![JavaScript继承](继承.png)

* 原型链继承
    介绍：子类的原型指向父类构造的实例
    缺点： 原型属性共享问题

    ``` javascript
    function Parent1() {
    this.name = "parent1";
    this.play = [1, 2, 3];
    }
    function Child1() {
    this.type = "child2";
    }
    Child1.prototype = new Parent1();
    console.log(new Child1());
    var s1 = new Child2();
    var s2 = new Child2();
    s1.play.push(4);
    console.log(s1.play, s2.play);
    ```

* 构造函数继承
    缺点：只能继承父类的实例属性和方法，不能继承原型属性或者方法

    ``` javascript
    function Parent1(){
    this.name = 'parent1';
    }

    Parent1.prototype.getName = function () {
    return this.name;
    }

    function Child1(){
    Parent1.call(this);
    this.type = 'child1'
    }

    let child = new Child1();
    console.log(child);  // 没问题
    // Child1 { name: 'parent1', type: 'child1' }
    console.log(child.getName());  // 会报错 child.getName is not a function
    ```

* 组合继承
    缺点：父类函数会多次执行

    ``` javascript
    function Parent3 () {

        this.name = 'parent3';

        this.play = [1, 2, 3];

    }



    Parent3.prototype.getName = function () {
        return this.name;
    }

    function Child3() {
        // 第二次调用 Parent3()
        Parent3.call(this);
        this.type = 'child3';
    }
    // 第一次调用 Parent3()

    Child3.prototype = new Parent3();

    // 手动挂上构造器，指向自己的构造函数

    Child3.prototype.constructor = Child3;

    var s3 = new Child3();

    var s4 = new Child3();

    s3.play.push(4);

    console.log(s3.play, s4.play);  // 不互相影响

    console.log(s3.getName()); // 正常输出'parent3'

    console.log(s4.getName()); // 正常输出'parent3'

    ```

* 原型式继承
    缺点：多个实例的引用类型属性指向相同的内存，存在篡改的可能

    ``` js
        let parent4 = {
            name: "parent4",

            friends: ["p1", "p2", "p3"],

            getName: function () {
                return this.name;
            },
        };

        let person4 = Object.create(parent4);

        person4.name = "tom";

        person4.friends.push("jerry");

        let person5 = Object.create(parent4);

        person5.friends.push("lucy");

        console.log(person4.name);
        // tom
        console.log(person4.name === person4.getName());
        // true
        console.log(person5.name);
        // parent4
        console.log(person4.friends);
        // ["p1", "p2", "p3","jerry","lucy"]
        console.log(person5.friends);
        // ["p1", "p2", "p3","jerry","lucy"]
    ```

* 寄生式继承
* 寄生组合继承
    寄生组合式继承方式，基本可以解决前几种继承方式的缺点，较好地实现了继承想要的结果，同时也减少了构造次数，减少了性能的开销

    ``` JavaScript
    function clone(parent, child) {
        // 这里改用 Object.create 就可以减少组合继承中多进行一次构造的过程
        child.prototype = Object.create(parent.prototype);
        child.prototype.constructor = child;
    }
    function Parent6() {
        this.name = "parent6";
        this.play = [1, 2, 3];
    }

    Parent6.prototype.getName = function () {
     return this.name;
    };
    function Child6() {
        Parent6.call(this);
        this.friends = "child5";
    }

    clone(Parent6, Child6);
    Child6.prototype.getFriends = function () {
        return this.friends;
    };
    let person6 = new Child6();
    console.log(person6);
    console.log(person6.getName());
    console.log(person6.getFriends());
    ```

## js 原型链

![js原型链](js原型链.png)

* 每个函数都有  `prototype` 属性，除了  `Function.prototype.bind()` 该属性指向原型。该属性的值是一个堆内存，堆内存中默认自带一个属性`constructor`,值是函数本身。
* 每个对象都有 `__proto__`属性，指向了创建该对象的构造函数的原型。
* 对象可以通过 `__proto__`来寻找不属于该对象的属性， `__proto__` 将对象连接起来组成了原型链。

## 定时器为什么不是精确的

## JS 的各种位置，比如 clientHeight,scrollHeight,offsetHeight ,以及 scrollTop, offsetTop,clientTop 的区别？

* clientHeight：表示的是可视区域的高度，不包含 border 和滚动条
* offsetHeight：表示可视区域的高度，包含了 border 和滚动条
* scrollHeight：表示了所有区域的高度，包含了因为滚动被隐藏的部分
* clientTop：表示边框 border 的厚度，在未指定的情况下一般为 0
* scrollTop：滚动后被隐藏的高度，获取对象相对于由 offsetParent 属性指定的父坐标(css定位的元素或 body 元素)距离顶端的高度。

## 箭头函数和普通函数的区别?

* 箭头函数没有自己的this，只能通过作用域链来向上查找离自己最近的那个函数的this

* 箭头函数不能作为constructor，因此不能通过new 来调用，所以它并没用new.target这个属性

* 箭头函数没有argument属性，可以通过rest可以获取
  
* 箭头函数不能直接使用call和apply，bind来改变this
  
* 箭头函数不能使用yield，不能作为generator函数
  
* 箭头函数语法比普通函数更加简洁
  
* ES6 为 new 命令引入了一个 new.target 属性，该属性一般用在构造函数之中，返回new命令作用于的那个构造函数或构造方法。如果构造函数不是通过new命令或Reflect.construct()调用的，new.target会返回undefined，因此这个属性可以用来确定构造函数时怎样调用的。包括super也不存在以及原型prototype ---- 因为在执行new的时候需要将函数的原型赋值给实力对象的原型属性。

## TypeScript里面有哪些JavaScript没有的类型?

``` javascript
相比较JavaScript，TypeScript独有的类型
any
声明为any的变量可以赋予任意类型的值

tuple
元组类型用来表示已知元素数量和类型的数组，个元素的类型不必相同，对应位置的类型需要一样

let x: [string, number];
x = [‘string’, 0]; // 正常
x = [0, ‘string’]; // 报错
enum
枚举类型用于定义值集合

enum Color {
    Red,
    Green,
    Blue,
}
let c: Color = Color.Green;
console.log©; // 1
void 标识方法返回值的类型，表示方法没有返回值。
function hello(): void {}
never
never是其它类型(包括null和undefined)的子类型，是不会发生的类型。例如，never总是抛出异常或永不返回的异常的函数表达式的返回类型

// 返回 never 的函数终点不可达
function error(message: string): never {
throw new Error(message);
}

// 推断的返回类型是 never
function fail() {
return error(‘Something failed’);
}

// 返回 never 的函数终点不可达
function infiniteLoop(): never {
while (true) {}
}
unknown 未知类型，一般在使用后再手动转具体的类型

union

联合类型，多种类型之一

string | number; // string 或 number
intersection
交叉类型，多种类型合并

{ a: string; } & { b: number; } // => { a: string; b: number }
Generics
泛型

interface Backpack {
add: (obj: T) => void;
get: () => T;
}
```

## 对 URL 进行编码/解码的实现方式

* ：escape和unescape:
escape()不能直接用于URL编码，它的真正作用是返回一个字符的Unicode编码值
除了ASCII字母、数字、标点符号"@ * _ + - . /"以外，对其他所有字符进行编码。在u0000到u00ff之间的符号被转成%xx的形式，其余符号被转成%uxxxx的形式。对应的解码函数是unescape()。
* ：encodeURL和decodeURL：
encodeURI()是Javascript中真正用来对URL编码的函数。
它用于对URL的组成部分进行个别编码，除了常见的符号以外，对其他一些在网址中有特殊含义的符号"; / ? : @ & = + $ , #"，也不进行编码。编码后，它输出符号的utf-8形式，并且在每个字节前加上%。
它对应的解码函数是decodeURI()
* ：encodeURLComponent和decodeURLComponent:
与encodeURI()的区别是，它用于对整个URL进行编码。"; / ? : @ & = + $ , #"，这些在encodeURI()中不被编码的符号，在encodeURIComponent()中统统会被编码。
它对应的解码函数是decodeURIComponent()

# DOM

# BOM

# vue

## vuex的原理和理解

# react

## state如何注入组件，从redux到组件经历的过程

## react 最新的生命周期

React 16之后有三个⽣命周期被废弃(但并未删除)

* componentWillMount
* componentWillReceiveProps
* componentWillUpdate
计划在17版本完全删除这三个函数，只保留UNSAVE_前缀的三个函数，⽬的是为了向下兼容，但是对于开发者⽽⾔应该尽量避免使⽤他们，⽽是使⽤新增的⽣命周期函数替代它们

最新的⽣命周期分为三个阶段,分别是挂载阶段、更新阶段、卸载阶段

* 挂载阶段
  * constructor: 构造函数，最先被执⾏,我们通常在构造函数⾥初始化state对象或者给⾃定义⽅法绑定this
  * getDerivedStateFromProps: static getDerivedStateFromProps(nextProps, prevState) ,这是个静态⽅法,当我们接收到新的属性想去修改我们state，可以使⽤getDerivedStateFromProps
  * render: render函数是纯函数，只返回需要渲染的东⻄，不应该包含其它的业务逻辑,可以返回原⽣的DOM、React组件、Fragment、Portals、字符串和数字、Boolean和null等内容
  * componentDidMount: 组件装载之后调⽤，此时我们可以获取到DOM节点并操作，⽐如对canvas，svg的操作，服务器请求，订阅都可以写在这个⾥⾯，但是记得在componentWillUnmount中取消订阅
* 更新阶段
  * getDerivedStateFromProps: 此⽅法在更新个挂载阶段都可能会调⽤
  * shouldComponentUpdate: shouldComponentUpdate(nextProps, nextState) ,有两个参数nextProps和nextState，表示新的属性和变化之后的state，返回⼀个布尔值，true表示会触发重新渲染，false表示不会触发重新渲染，默认返回true,我们通常利⽤此⽣命周期来优化React程序性能
  * render: 更新阶段也会触发此⽣命周期
  * getSnapshotBeforeUpdate: getSnapshotBeforeUpdate(prevProps, prevState) , 这 个 ⽅ 法 在 render 之 后 ，componentDidUpdate之前调⽤，有两个参数prevProps和prevState，表示之前的属性和之前的state，这个函数有⼀个返回值，会作为第三个参数传给componentDidUpdate，如果你不想要返回值，可以返回null，此⽣命周期必须与componentDidUpdate搭配使⽤
  * componentDidUpdate: componentDidUpdate(prevProps, prevState, snapshot) ,该⽅法在getSnapshotBeforeUpdate⽅法之后被调⽤，有三个参数prevProps，prevState，snapshot，表示之前的props，之前的state，和snapshot。第三个参数是getSnapshotBeforeUpdate返回的,如果触发某些回调函数时需要⽤到 DOM 元素的状态，则将对⽐或计算的过程迁移⾄ getSnapshotBeforeUpdate，然后在 componentDidUpdate 中统⼀触发回调或更新状态。
* 卸载阶段

## setState 是同步还是异步

## react 组件的通信

## react 优化手段

# node

# webpack

# 数据结构

# http 请求

## 请求在客户端报413是什么错误,怎么解决呢?

* HTTP 413 错误(Request entity too large 请求实体太大)，就是客户端发送的实体主体部分比服务器能够或者希望处理的要大时，会出现这样的错误。一般上传文件时会出现这样的错误概率比较大。
  解决方案可以修改服务器的配置文件。配置客户端请求大小和缓存大小

# 前端安全和工程化

# 前端性能优化

# 计算机基础

# 面试题集

* [面试题1](https://mp.weixin.qq.com/s?__biz=MzAxODE4MTEzMA==&mid=2650081252&idx=1&sn=1fedc422a3806fa1f9c3faf31bb2a20b&chksm=83db9a81b4ac1397132de99ebdbdbdad57dcc6785d0b8fe1a5ee2b57dbb960b0fbf65015c3ca&scene=126&sessionid=1603760808&key=54ce6b15dc70fa94e4cee849718a95dcb45463880bfbf73a52f6e49f4e4a65fb8adec9e1c54df8bf81bfa1d78626a8537229cc36083224e425c795f892103475ca5f06542d47eec5dabc5d55c77dc7f9fabc4524bbc83cf94060d9236d1061a0fa026db04b47ae38fdfd65662df5549a11d6cd60ff371f5492081a022254d0e7&ascene=1&uin=MjQ4OTg5MDk4MQ%3D%3D&devicetype=Windows+10+x64&version=6300002f&lang=zh_CN&exportkey=AXrZ8Ft8M%2FkmfXMdRQOHyYs%3D&pass_ticket=Kkp6C7aNRW%2BSS3CyH29rTpuzIryrfuzR2BkuJOMPRmZ73lUqRYKqbJR1nz5SlRhp&wx_header=0)
* [面试题2](https://mp.weixin.qq.com/s?__biz=MzUyNDYxNDAyMg==&mid=2247486750&idx=1&sn=d7e13a8393b83ac330d9b48690428c0e&chksm=fa2bedf7cd5c64e19fcafbe4dab742b65cfe168ad567f3f799b5fc229a35710eb4164084897c&scene=126&sessionid=1602725812&key=6664ac14267ba66883c13581e1d9e62b3ffc7ddfc44d1984c762bde82d19131986d5d9af50595ab1d798e16e45eddd68ded75929bfc6217a87ec0dcacb393b0aa10b53bcd066f65c7865905a425d129f9f1f110464e3a8faa5601a1b7a192f46240134dd033c0bacd43e93b0b51701140f106a0a52acfaabf76e8fee9f2cae06&ascene=1&uin=MjQ4OTg5MDk4MQ%3D%3D&devicetype=Windows+10+x64&version=6300002f&lang=zh_CN&exportkey=Adc0WAca8bFpyYT3RtaxAjo%3D&pass_ticket=gNZw604QfgMyZ5MfqQB17Zb9G0KO%2Fy%2FGpe3%2BUhEBieJBkyQwt1xU8LnZyQLLT598&wx_header=0)
* [源码面试题](https://mp.weixin.qq.com/s?__biz=MzI2NTk2NzUxNg==&mid=2247488674&idx=1&sn=3f5c6af2c52365525aa84ff92b9f865b&chksm=ea941651dde39f4790e96e2d8f2530fa23257afb50de8d40d6d318507b873f9d870c0f507863&mpshare=1&scene=1&srcid=1026yMwvhU6WsEBstdZUyIgl&sharer_sharetime=1603682665969&sharer_shareid=1b2206d548f7c54418de346a0102e46f&key=041bb01ba83758f9c012f304255f853e521afbe7bbf65555a0e068f76f2c433eea39d0413b426b59a870039c71945328b288292bbbbac9811706f2f09f6716c482684831e94eab0b6935f37a6a5c8892d4ca9ecd897e139bf608b85a18e8ee5339e931c56cc60e39443738eeb63253718488c0322710c61a17510cbfa97910cb&ascene=1&uin=MjQ4OTg5MDk4MQ%3D%3D&devicetype=Windows+10+x64&version=6300002f&lang=zh_CN&exportkey=AQ%2BcZHmGMZ8MBH%2FqQ1l2YVc%3D&pass_ticket=L%2BjndQVDhQl1X8R7c%2BwxUxrwQN%2FfivdCt7LVG0oUoik5qA1Gx2ZTiVGm%2B4shiHQn&wx_header=0)
