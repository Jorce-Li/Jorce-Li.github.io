---
layout:     post
title:      "一篇带有代码块的测试"
subtitle:   "测试"
date:       2018-12-22
author:     "Jorce"
header-img: "img/post-bg-js-module.jpg"
tags:
    - 前端开发
    - JavaScript
---



## Foreword

> Here comes Module!

随着网站逐渐变成「互联网应用程序」，嵌入网页的 JavaScript 代码越来越庞大，越来越复杂。网页越来越像桌面程序，需要一个团队分工协作、进度管理、单元测试……我们不得不使用软件工程的方法，来管理网页的业务逻辑。

于是，JavaScript 的模块化成为迫切需求。在 ES6 Module 来临之前，JavaScript 社区提供了强大支持，尝试在现有的运行环境下，实现模块的效果。


---

## Catalog


1.  [CommonJS & Node](#commonjs--node)



## CommonJS & Node

> Javascript: not just for browsers any more! —— CommonJS Slogen

前端模块化的事实标准之一，2009 年 8 月，[CommonJS](http://wiki.commonjs.org/wiki/CommonJS) 诞生。

CommonJS 本质上只是一套规范（API 定义），而 Node.js 采用并实现了部分规范，CommonJS Module 的写法也因此广泛流行。


让我们看看 Node 中的实现：

```js
// 由于 Node 原生支持模块的作用域，并不需要额外的 wrapper
// "as though the module was wrapped in a function"

var a = require('./a')  // 加载模块（同步加载）
a.doSomething()         // 等上一句执行完才会执行

exports.b = function(){ // 暴露 b 函数接口
  // do something
}
```

`exports`是一个内置对象，就像`require`是一个内置加载函数一样。如果你希望直接赋值一个完整的对象或者构造函数，覆写`module.exports`就可以了。

CommonJS 前身叫 ServerJS ，**后来希望能更加 COMMON，成为通吃各种环境的模块规范，改名为 CommonJS** 。CommonJS 最初只专注于 Server-side 而非浏览器环境，因此它采用了同步加载的机制，这对服务器环境（硬盘 I/O 速度）不是问题，而对浏览器环境（网速）来说并不合适。


因此，各种适用于浏览器环境的模块框架与标准逐个诞生，他们的共同点是：

* 采用异步加载（预先加载所有依赖的模块后回调执行，符合浏览器的网络环境）
* 虽然代码风格不同，但其实都可以看作 CommonJS Modules 语法的变体。
* 都在向着 **COMMON** 的方向进化：**兼容不同风格，兼容浏览器和服务器两种环境**

本文接下来要讨论的典例是：

* RequireJS & AMD（异步加载，预执行，依赖前置。默认推荐 AMD 写法）
* SeaJS & CMD（异步加载，懒执行，依赖就近，默认推荐 CommonJS 写法）
