---
title: js面试题
date: 2020-10-22 11:05:28
tags: js面试题
categories: js面试题
---
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

* 拷贝的对象的值中如果有函数、undefined、symbol 这几种类型，经过 JSON.stringify 序列化之后的字符串中这个键值对会消失
* 拷贝 Date 引用类型会变成字符串
* 无法拷贝不可枚举的属性
* 无法拷贝对象的原型链
* 拷贝正则会变成空对象
* 对象中含有 NaN、Infinity 以及 -Infinity，JSON 序列化的结果会变成 null
* 无法拷贝对象的循环应用，即对象成环 (obj[key] = obj)
## 面试题集

* [面试题1](https://mp.weixin.qq.com/s?__biz=MzAxODE4MTEzMA==&mid=2650081252&idx=1&sn=1fedc422a3806fa1f9c3faf31bb2a20b&chksm=83db9a81b4ac1397132de99ebdbdbdad57dcc6785d0b8fe1a5ee2b57dbb960b0fbf65015c3ca&scene=126&sessionid=1603760808&key=54ce6b15dc70fa94e4cee849718a95dcb45463880bfbf73a52f6e49f4e4a65fb8adec9e1c54df8bf81bfa1d78626a8537229cc36083224e425c795f892103475ca5f06542d47eec5dabc5d55c77dc7f9fabc4524bbc83cf94060d9236d1061a0fa026db04b47ae38fdfd65662df5549a11d6cd60ff371f5492081a022254d0e7&ascene=1&uin=MjQ4OTg5MDk4MQ%3D%3D&devicetype=Windows+10+x64&version=6300002f&lang=zh_CN&exportkey=AXrZ8Ft8M%2FkmfXMdRQOHyYs%3D&pass_ticket=Kkp6C7aNRW%2BSS3CyH29rTpuzIryrfuzR2BkuJOMPRmZ73lUqRYKqbJR1nz5SlRhp&wx_header=0)
* [面试题2](https://mp.weixin.qq.com/s?__biz=MzUyNDYxNDAyMg==&mid=2247486750&idx=1&sn=d7e13a8393b83ac330d9b48690428c0e&chksm=fa2bedf7cd5c64e19fcafbe4dab742b65cfe168ad567f3f799b5fc229a35710eb4164084897c&scene=126&sessionid=1602725812&key=6664ac14267ba66883c13581e1d9e62b3ffc7ddfc44d1984c762bde82d19131986d5d9af50595ab1d798e16e45eddd68ded75929bfc6217a87ec0dcacb393b0aa10b53bcd066f65c7865905a425d129f9f1f110464e3a8faa5601a1b7a192f46240134dd033c0bacd43e93b0b51701140f106a0a52acfaabf76e8fee9f2cae06&ascene=1&uin=MjQ4OTg5MDk4MQ%3D%3D&devicetype=Windows+10+x64&version=6300002f&lang=zh_CN&exportkey=Adc0WAca8bFpyYT3RtaxAjo%3D&pass_ticket=gNZw604QfgMyZ5MfqQB17Zb9G0KO%2Fy%2FGpe3%2BUhEBieJBkyQwt1xU8LnZyQLLT598&wx_header=0)
* [源码面试题](https://mp.weixin.qq.com/s?__biz=MzI2NTk2NzUxNg==&mid=2247488674&idx=1&sn=3f5c6af2c52365525aa84ff92b9f865b&chksm=ea941651dde39f4790e96e2d8f2530fa23257afb50de8d40d6d318507b873f9d870c0f507863&mpshare=1&scene=1&srcid=1026yMwvhU6WsEBstdZUyIgl&sharer_sharetime=1603682665969&sharer_shareid=1b2206d548f7c54418de346a0102e46f&key=041bb01ba83758f9c012f304255f853e521afbe7bbf65555a0e068f76f2c433eea39d0413b426b59a870039c71945328b288292bbbbac9811706f2f09f6716c482684831e94eab0b6935f37a6a5c8892d4ca9ecd897e139bf608b85a18e8ee5339e931c56cc60e39443738eeb63253718488c0322710c61a17510cbfa97910cb&ascene=1&uin=MjQ4OTg5MDk4MQ%3D%3D&devicetype=Windows+10+x64&version=6300002f&lang=zh_CN&exportkey=AQ%2BcZHmGMZ8MBH%2FqQ1l2YVc%3D&pass_ticket=L%2BjndQVDhQl1X8R7c%2BwxUxrwQN%2FfivdCt7LVG0oUoik5qA1Gx2ZTiVGm%2B4shiHQn&wx_header=0)
