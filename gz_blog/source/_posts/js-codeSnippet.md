---
title: js 代码片段实现
date: 2020-10-29 11:05:28
tags: js 代码片段实现
categories: js 代码片段实现
---

## 做一个PC端的网页，设计图是1920X1080，要在常见屏上显示正常 ，比如：1280X720 1366X768 1440X900 1920X1080。就要使用REM，width、height、margin、padding、left、top都采用了REM

``` javascript
(function(win) {
  var tid;
  function refreshRem() {
    let designSize = 1920; // 设计图尺寸
    let html = document.documentElement;
    let wW = html.clientWidth; // 窗口宽度
    let rem = (wW * 100) / designSize;
    document.documentElement.style.fontSize = rem + "px";
  }

  win.addEventListener(
    "resize",
    function() {
      clearTimeout(tid);
      tid = setTimeout(refreshRem, 300);
    },
    false
  );
  win.addEventListener(
    "pageshow",
    function(e) {
      if (e.persisted) {
        clearTimeout(tid);
        tid = setTimeout(refreshRem, 300);
      }
    },
    false
  );

  refreshRem();
})(window);
```

计算font-size的逻辑是：
当设计图是1920时,规定HTML的FONT-SIZE的值是100. 也就是,当浏览器窗口调整到1920PX时,1REM=100PX,如果要设定一个160PX(1920设计图时)的margin-top,那么REM设置值是1.6rem.

## 深拷贝

``` javascript
function deepClone(origin, hashMap = new WeakMap()) {
    if (origin == undefined || typeof origin !== 'object') {
        return origin
    }

    if (origin instanceof Date) {
        return new Date(origin)
    }
    if (origin instanceof RegExp) {
        return new RegExp(origin)
    }
    let hashKey = hashMap.get(origin);
    if (hashKey) {
        return hashKey
    }
    let target = new origin.constructor()
    hashMap.set(origin, target)
    for (const k in origin) {
        if (origin.hasOwnProperty(k)) {
            target[k] = deepClone(origin[k])
        }
    }
}
```

## js 原有的 toFixed 函数得到结果并非 想要的 需要自己重新定义

``` javascript
function toFixed(number, m) {
    if (typeof number !== 'number') {
        throw new TypeError("number不是数字");
    }
    let result = Math.round(Math.pow(10, m) * number) / Math.pow(10, m);
    result = String(result);
    if (result.indexOf(".") == -1) {
        if(m != 0){
            result += ".";
            result += new Array(m + 1).join('0');
        }
    } else {
        let arr = result.split('.');
        if (arr[1].length < m) {
            arr[1] += new Array(m - arr[1].length + 1).join('0')
        }
        result = arr.join('.')
    }
    return result
}
```
<!-- more -->
## bind 实现

* 箭头函数的 this 永远指向它所在的作用域，函数作为构造函数用 new 关键字调用时，不应该改变其 this 指向，因为 new绑定 的优先级高于 显示绑定 和 硬绑定
* 返回⼀个函数，绑定this，传递预置参数

  ``` javascript
  Function.prototype.mybind = function(thisArg) {
    if (typeof this !== 'function') {
      throw new TypeError("不是一个函数");
    }
    // 拿到参数，为了传给调用者
    const args = Array.prototype.slice.call(arguments, 1);
     let  self = this;
      let nop = function() {};  // 构建一个干净的函数，用于保存原函数的原型
      // 绑定的函数
      let bound = function() {
        // this instanceof nop, 判断是否使用 new 来调用 bound
        // 如果是 new 来调用的话，this的指向就是其实例，
        // 如果不是 new 调用的话，就改变 this 指向到指定的对象 o
        return self.apply(
          this instanceof nop ? this : thisArg,
          args.concat(Array.prototype.slice.call(arguments))
        );
      };

    // 箭头函数没有 prototype，箭头函数this永远指向它所在的作用域
    if (this.prototype) {
      nop.prototype = this.prototype;
    }
    // 修改绑定函数的原型指向
    bound.prototype = new nop();

    return bound;
  }
  
  ```

## call实现

``` javascript
Function.prototype.mycall = function(thisArg) {
  // this指向调用call的对象
  if (typeof this !== 'function') {
    // 调用call的若不是函数则报错
    throw new TypeError("不是一个函数");
  }

  const args = [...arguments].slice(1);
  thisArg = thisArg || window;
  // 将调用call函数的对象添加到thisArg的属性中
  thisArg.fn = this;
  // 执行该属性
  const result = thisArg.fn(...arg);
  // 删除该属性
  delete thisArg.fn;
  // 返回函数执行结果
  return result;
};

```

## apply 实现

``` javascript
Function.prototype.myapply = function(thisArg) {
  if (typeof this !== 'function') {
    throw new TypeError("不是一个函数");
  }

  const args = arguments[1];

  thisArg.fn = this;

  const result = thisArg.fn(...arg);

  delete thisArg.fn;

  return result;
};
```
