---
layout: post
title: "Vue updated生命周期函数"
subtitle: ''
author: "周欢"
header-style: text
tags:
  - Vue.js学习
---

**由于数据更改导致的虚拟 DOM 重新渲染和打补丁，在这之后会调用该钩子。**

无论是组件本身的数据变更，还是从父组件接收到的 `props` 或者从vuex里面拿到的数据有变更，都会触发虚拟 DOM 重新渲染和打补丁，并在之后调用 `updated`。

_有个值得注意的地方，举例说明：_

*例子1*

```html
<template>
    <div>
        <div v-for="(item, index) in list" :key="index">{{item}}</div>
    </div>
</template>
```
```js
<script>
export default {
    data () {
        return {
            list: [1, 1, 1]
        }
    },
    created () {
        setTimeout(_ => {
            this.list = [2, 2, 2]
        }, 1000)
    },
    updated () {
        console.log('App.vue finish re-render')
    }
}
</script>
```

*运行结果*

页面首先渲染了3个1的列表，1s后页面重绘为3个2，并打出'App.vue finish re-render'的日志。

*注意*

将`created`生命周期函数的`setTimeout`函数里的代码改为`this.list = [1, 1, 1]`，
会发现还是打出了'App.vue finish re-render'的日志，虽然`list`的值没变，但页面仍然重新渲染了，**这是因为 `this.list` 的指针指向发生了改变。**

*例子2*

```html
<template>
    <div>
        {{box}}
    </div>
</template>
```
```js
<script>
export default {
    data () {
        return {
            box: 1
        }
    },
    created () {
        setTimeout(_ => {
            this.box = 1
        }, 1000)
    },
    updated () {
        console.log('App.vue finish re-render')
    }
}
</script>
```

这时`updated`生命周期函数没有被调用，如果`this.box=2`则会被调用。

**对于数字、字符串这样的简单值，只有前后值不相等时，才会触发 Virtual Dom re-render 和 patch 并在之后调用 updated。**