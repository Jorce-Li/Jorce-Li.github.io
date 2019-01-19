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
#### 嵌套支持
本质上，Proxy 也是不支持嵌套的，这点和 Object.defineProperty() 是一样的。因此也需要通过逐层遍历来解决。Proxy 的写法是在 get 里面递归调用 Proxy 并返回，代码如下：
```js
let obj = {
  info: {
    name: 'eason',
    blogs: ['webpack', 'babel', 'cache']
  }
}
let handler = {
  get (target, key, receiver) {
    console.log('get', key)
    // 递归创建并返回
    if (typeof target[key] === 'object' && target[key] !== null) {
      return new Proxy(target[key], handler)
    }
    return Reflect.get(target, key, receiver)
  },
  set (target, key, value, receiver) {
    console.log('set', key, value)
    return Reflect.set(target, key, value, receiver)
  }
}
let proxy = new Proxy(obj, handler)
// 以下两句都能够进入 set
proxy.info.name = 'Zoe'
proxy.info.blogs.push('proxy')
```
# 面试题
看一下经常遇到的面试题

`什么样的 a 可以满足 (a === 1 && a === 2 && a === 3) === true 呢？(注意是 3 个 =，也就是严格相等)`

原来的方法
```js
let current = 0
Object.defineProperty(window, 'a', {
  get () {
    current++
    return current
  }
})
console.log(a === 1 && a === 2 && a === 3) // true
```
使用Proxy
```js
let foo = {
  foo () {
    console.log('foo')
  }
}
let bar = {
  bar () {
    console.log('bar')
  }
}
// 正常状态下，对象只能继承一个对象，要么有 foo()，要么有 bar()
let sonOfFoo = Object.create(foo);
sonOfFoo.foo();     // foo
let sonOfBar = Object.create(bar);
sonOfBar.bar();     // bar
// 黑科技开始
let sonOfFooBar = new Proxy({}, {
  get (target, key) {
    return target[key] || foo[key] || bar[key];
  }
})
// 我们创造了一个对象同时继承了两个对象，foo() 和 bar() 同时拥有
sonOfFooBar.foo();   // foo 有foo方法，继承自对象foo
sonOfFooBar.bar();   // bar 也有bar方法，继承自对象bar
```
