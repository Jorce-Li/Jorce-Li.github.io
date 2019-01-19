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
let arr = [1,2,3]
let obj = {}
Object.defineProperty(obj, 'arr', {
  get () {
    console.log('get arr')
    return arr
  },
  set (newVal) {
    console.log('set', newVal)
    arr = newVal
  }
})
obj.arr.push(4) // 只会打印 get arr, 不会打印 set
obj.arr = [1,2,3,4] // 这个能正常 set

```
数组的以下几个方法不会触发 set：
`push
`pop
`shift
`unshift
`splice
`sort
`reverse
Vue 把这些方法定义为变异方法 (mutation method)，指的是会修改原来数组的方法。与之对应则是非变异方法 (non-mutating method)，例如 filter, concat, slice 等，它们都不会修改原始数组，而会返回一个新的数组。Vue 官网有相关文档讲述这个问题。

使用 Object.defineProperty() 多数要配合 Object.keys() 和遍历，于是多了一层嵌套。如：
```js
let obj = {
  info: {
    name: 'eason'
  }
}
Object.keys(obj).forEach(key => {
  Object.defineProperty(obj, key, {
    // ...
  })
})
```
如果是这一类嵌套对象，那就必须逐层遍历，直到把每个对象的每个属性都调用 Object.defineProperty() 为止。 Vue 的源码中就能找到这样的逻辑 (叫做 walk 方法)。

# Proxy

Proxy是针对整个对象,而不是对象的某个属性,不需要对 keys 进行遍历。

```js
let obj = {
  name: 'Eason',
  age: 30
}
let handler = {
  get (target, key, receiver) {
    console.log('get', key)
    return Reflect.get(target, key, receiver)
  },
  set (target, key, value, receiver) {
    console.log('set', key, value)
    return Reflect.set(target, key, value, receiver)
  }
}
let proxy = new Proxy(obj, handler)
proxy.name = 'Zoe' // set name Zoe
proxy.age = 18 // set age 18
```
