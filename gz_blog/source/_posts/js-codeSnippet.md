---
title: js 代码片段
date: 2020-10-22 11:05:28
tags: js 代码片段
categories: js 代码片段
---

## 做一个PC端的网页，设计图是1920X1080，要在常见屏上显示正常 ，比如：1280X720 1366X768 1440X900 1920X1080。就要使用REM，width、height、margin、padding、left、top都采用了REM,

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