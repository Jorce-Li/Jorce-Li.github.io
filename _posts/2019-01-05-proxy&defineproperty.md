---
layout: post
title: "vue的双向绑定与proxy"
subtitle: 'proxy与Object.defineProperty'
author: "Jorce"
header-style: text
tags:
  - vue
---

Update: vue源码中双向绑定的实现

---

本篇文章是在vue源码略读之后开始仔细看vue最著名的双向绑定的具体实现原理,从而看到了proxy和defineProperty
下面开始具体讲一下个人的理解

vue的双向绑定其实就是使用了所谓的数据劫持
>数据劫持:指的是在访问或者修改对象的某个属性时，通过一段代码拦截这个行为，进行额外的操作或者修改返回结果

目前典型的两种方式就是Object.defineProperty和proxy对象

先看一下vue使用的defineProperty

## Object.defineProperty

每次去面试基本必问的东西

```js

```
