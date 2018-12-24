---
layout: post
title: "Vue vm.$nextTick() 方法使用"
subtitle: ''
author: "周欢"
header-style: text
tags:
  - Vue.js学习
---

## 定义

将回调延迟到下次 DOM 更新循环之后执行。在修改数据之后立即使用它，然后等待 DOM 更新。它跟全局方法 `Vue.nextTick` 一样，不同的是该方法回调的 `this` 自动绑定到调用它的实例上。

## 用法

**示例**

    <template>
        <div id="box">{{box}}</div>
    </template>

    <script>
        export default {
            data () {
                return {
                    box: 'foo'
                }
            },
            mounted () {
                this.box = 'bar'
                console.log(1, document.querySelector('#box').innerHTML)
                // 1 "foo"
                setTimeout(_ => {
                    console.log(2, document.querySelector('#box').innerHTML)
                    // 2 "bar"
                }, 500)
                this.$nextTick(function () {
                    console.log(3, document.querySelector('#box').innerHTML)
                    // 3 "bar"
                })
            }
        }
    </script>

`mounted` 函数里，首先修改了 `this.box` 的值，再分别在三个地方打印日志来展示 DOM 中 id 为 box 的 div 的 `innerHTML`。

- 第一处 `console.log` 执行时，虽然 `this.box` 的值已经更新了，但 DOM 还没有更新，打印出 `1 "foo"`

- 第二处将 `console.log` 延迟了 500ms 执行，拿到的是更新之后的 DOM，打印出 `2 "bar"`

- 第三处在 `this.$nextTick` 方法里执行，该方法执行时 DOM 已更新完成，打印出 `3 "bar"`

**注意**

如果将打印的内容换成 `document.querySelector('#box')`，会发现三个 `console.log()` 打印出的内容均为 `<div id="box">bar</div>`，这是因为这时候拿到的是一个 DOM 对象，`this.box = 'bar'` 的行为相当于修改了该对象的一个 `key`，并没有改变对象的内存地址。这类似于下面的例子：

    data () {
      return {
        test: {
          name: 'kobe'
        }
      }
    },
    created () {
      console.log(this.test)
      this.test.name = 'jordan'
    }

在修改 `this.test.name` 之前打印 `this.test`，打印出的对象 `name` 对应的 `value` 为 `'jordan'`。