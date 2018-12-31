---
layout: post
title: "ES6 find 和 filter 的区别"
subtitle: '前端过滤函数'
author: "Jorce"
header-style: text
tags:
  - intro
---

Update: 遇到个功能是要分类就想说在前端过滤，不要从查数据库的时候过滤了。

---

发现有个find，测试一番之后发现
```js
const list = [{'name':'1',index:1},{'name':'2'},{'name':'1'}]
let list2 = list.find(i=>i.name==='1')

let list3 = list.filter(i=>i.name==='1')

console.log(list);[{ name: '1', index: 1 }, { name: '2' }, { name: '1' } ]
console.log(list2);{ name: '1', index: 1 }

console.log(list3);[{ name: '1', index: 1 }, { name: '1' } ]
```
find 和 filter 都是不改变原数组的方法

但是find只查出第一个符合条件的结果像例子里是直接返回了一个对象而不是数组！

，而filter返回全部结果仍然是数组。