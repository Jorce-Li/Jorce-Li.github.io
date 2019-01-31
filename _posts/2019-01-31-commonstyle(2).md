---
layout: post
title: "React代码风格统一规范"
subtitle: 'css-tricks.com/react-code-style-guide'
author: "Jorce"
header-style: text
tags:
  - react
  - style
---

Update: 风格规范

---

# React

### 解构取值

```js
  const {prop1,prop2,pro3} = this.props
  let {state1,state2,state3} = this.state
```
### 简单组件的构建
```js
class Dog extends Component {
  render () {
    let { breed, color, goodOrBad } = this.props;
    return <div className={breed}>My {color} dog is {goodOrBad}</div>;
  }
}
// 对比
let Dog = (breed, color, goodOrBad) => <div className={breed}>My {color} dog is {goodOrBad}</div>;

```
同时对一个组件中属性的传递可以更改为...扩展运算符的传递 注意:当该组件需要动态render的时候不推荐...
```js
let propertiesList = {
  className: "my-favorite-component",
  id: "myFav",
  content: "Hello world!"
};
let SimpleDiv = props => <div {... props} />;

let jsxVersion = <SimpleDiv props={propertiesList} />;
```

### render尽量避免少的判断
推荐下列的判断写法
```js
class SearchResult extends Component {
  render () {
    const { results } = this.props

    const Results = () => <Fragment>
      {results.map(index => <Result key={index} {...results[index]} />)}
    </Fragment>

    const NoResults = () => No results

    return <section className="search-results">
      {
        results.length
          ? Results()
          : NoResults()
      }
    </section>
  }
}
```

### css js命名规范
css需要加上scoped或者module
css和js起名为了避免与引入引出的命名产生冲突,最好有特殊的前缀

### 组件起名规范
1. 同Vue 组件属性最好分行写,增加易读性

```js
  <MyComponent
    foo="a"
    bar="b"
    baz="c"
  />
```

### 状态管理
推荐使用redux进行全局状态管理而不是window.status进行管理
注:redux刷新会消失。
