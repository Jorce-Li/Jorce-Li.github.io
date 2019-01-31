---
layout: post
title: "Vue等代码风格统一规范"
subtitle: 'cn.vue.js等'
author: "Jorce"
header-style: text
tags:
  - vue
  - style
---

Update: 风格规范

---

# Vue

### data 必须是返回一个对象的函数

为了避免data在此组件中所有实例共享,为了做到每个组件实例都管理其自己的数据，每个实例必须生成一个独立的数据对象。在 JavaScript 中，在一个函数中返回这个对象就可以了
```js
  data() {
    return {
      listTitle: '',
      todos: []
    }
  }
```
### props 定义
按照下述代码定义的好处是:
它们写明了组件的 API，所以很容易看懂组件的用法
在开发环境下，如果向一个组件提供格式不正确的 prop，Vue 将会告警，以帮助你捕获潜在的错误来源。
```js
  props: {
    status: {
      type: String,
      required: true,
      validator: function (value) {
        return [
          'syncing',
          'synced',
          'version-conflict',
          'error'
        ].indexOf(value) !== -1
      }
    }
  }
```

### v-for 不要和 v-if一起使用
如果一起使用的话会经过下述步骤
```js
this.users.map(function (user) {
  if (user.isActive) {
    return user.name
  }
})
```
每次users改动的时候都会进行重新遍历,而不会根据isActive是否有所改变而变动。所以好的办法是
将users进行监听过滤取消v-if 或者将-if移动到外层

### css js命名规范
css需要加上scoped或者module
同时起名时js要避免$,_,可能会与vue内置方法产生冲突
css和js起名为了避免与引入引出的命名产生冲突,最好有特殊的前缀

### 组件起名规范

1.始终是单词大写开头CommonCase
2.使用连接符common-case
3.创建通用不包含状态管理的组件时(button table icon等),最好在规定前缀VButton
4.每个页面只使用一次的组件名称:TheCase,加上the前缀表示其一次性
5.父子相关联的特殊组件最好不要用文件夹嵌套,而是用父组件名称对子组件添加前缀的命名方式
6.针对该组件作用,将形容词放在前缀位置:SearchButton
7.引入的组件使用时推荐使用连接符<my-component></my-component>写法
8.组件名没必要为了缩短单词而使用缩写:stu=>student
9.在声明 prop 的时候，其命名应该始终使用 camelCase，而在模板和 JSX 中应该始终使用 kebab-case。
```js
props: {
  greetingText: String
}
<WelcomeMessage greeting-text="hi"/>
```
10.组件属性最好分行写,增加易读性
```js
  <MyComponent
  foo="a"
  bar="b"
  baz="c"
  />
```
### 表达式中的计算

1.尽量避免在v-model中进行大量计算,可以对该属性进行watch
2.将引用类型的监听改为对该引用类型的子简单类型的单一监听

### 状态管理
推荐使用vuex进行全局状态管理而不是window.status进行管理
注:vuex刷新会消失。
