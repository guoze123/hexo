---
title: js 代码片段
date: 2020-10-29 11:05:28
tags: js 代码片段
categories: js 代码片段
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
        throw new Error("number不是数字");
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
