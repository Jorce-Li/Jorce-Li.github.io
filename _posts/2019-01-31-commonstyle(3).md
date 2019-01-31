---
layout: post
title: "js代码风格统一规范"
subtitle: 'https://github.com/airbnb/javascript'
author: "Jorce"
header-style: text
tags:
  - js
  - style
---

Update: 风格规范

---
注:这里目前只记录本人遇到的自己可能会书写不规范的地方,已经规范使用的地方暂不记录

# JS

##Objects 
1. objct method命名

```js
let obj = {
  <!-- 不推荐 -->
  a:function(){

  }
  <!-- 推荐 -->
  a(){

  }
}
```

2. 同名传值
新手在vue引入组件肯定接触得到 components

```js
const a = 1
let obj = {
  a
}

```
## Arrays
1. 使用...扩展运算符进行复制
很久以前会使用map 和 使用一个temp数组进行push

```js
const a = [1]
const b = [...a]
```

2. 将一些伪数组或对象转为数组

```js
const foo = document.querySelectorAll('.foo');
// good
const nodes = Array.from(foo);
// best
const nodes = [...foo];

const arrLike = { 0: 'foo', 1: 'bar', 2: 'baz', length: 3 };
// best
const arr = Array.from(arrLike);
```

##Destructuring

1. 传入简单对象的时候直接解构

```js
// best
function getFullName({ firstName, lastName }) {
  return `${firstName} ${lastName}`;
}
```

##String

1. string 推荐使用''包裹,因为Html使用"",方便嵌套

##Functions

1. 方法中有参数却没有传入时,直接在参数加默认值,同时将赋默认值的参数放在末尾

```js
// good
function handleThings(name, opts = {}) {
  // ...
}
```
##Arrow Functions

##Classes & Constructors

##Iterators and Generators

##Properties

1. 次方计算

```js
// bad
const binary = Math.pow(2, 10);

// good
const binary = 2 ** 10;
```

##Hoisting
