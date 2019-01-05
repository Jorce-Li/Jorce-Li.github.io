---
layout: post
title: "使用colum进行等宽瀑布流布局"
subtitle: '瀑布流'
author: "Jorce"
header-style: text
tags:
  - css
---

Update: 瀑布流布局

---

瀑布流的思路有很多,今天公司的同事遇到了移动端的这个问题就一起看了一下。
以我之前看过的都是

#### 1、横向布局换行之后谁短往谁那推下一张图
瀑布流布局要求要进行布置的元素等宽，然后计算元素的宽度与浏览器宽度之比，得到需要布置的列数。
创建一个数组，长度为列数，里面的值为已布置元素的总高度（最开始为0）
然后将未布置的元素依次布置到高度最小的那一列，就得到了瀑布流布局。
其他种种基本类似
#### 2、flex布局
使用flex-direction：column纵向排列的属性 + flex-wrap:wrap超过高度换行的属性来实现。
这个问题点在于必须要设置最大高度 否则会一直往下排不换列
#### 3、column+break-inside
今天主要用的是这个布局方法
先上代码

```html
<div class="waterfall">
    <div class="item">
        测试测试测试测试测试测试测试测试测试测试测
    </div>
    <div class="item">
        测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试
    </div>
    <div class="item">
        测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试
    </div>
    <div class="item">
        测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试
    </div>
</div> 
```
```css
.waterfall {
    column-count: 2;
    column-gap: 0;
}

.item {
    break-inside: avoid;
    padding: 10px;
    font-size: 20px;
    color: #686868;
    border: 1px solid #ccc;
    margin: 10px;
}
```
结果如图一
![auto-focus](/img/in-post/cutcolumn1.png)

这这里发现图上所标注的位置两边的margin不同,这个问题就需要去看实现这个布局的css属性了

column布局就是列布局,通俗的理解就是将一个div的内容直接划分为多少列,一般的布局呢都是横向排版而这个属性呢就是竖向排版,这个位置就是marin不同的原因所在？？？？
这里有一个重点：右侧的div的margin是去了左侧最底下？并不是。这里又引出了一个经典的边距塌陷的问题。

break-inside属性呢通俗的讲就是是否让div因为布局设置被切断,当不设置这个属性的时候呢,废话少说直接上图二
![auto-focus](/img/in-post/cutcolumn2.png)
左侧底部被切断了切到了右边,理解了吧。
当设置了这个属性之后呢 就像图一一样了
但是如果不设置margin有什么好的解决办法呢？最简单的套个div上个padding不就结束了？
OK上代码
```html
<div class="waterfall">
    <div class="item">
      <div class="item-content">
        测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试试测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试测试测
      </div>
    </div>
    <div class="item">
      <div class="item-content">
        测试测试测试测试测试测试
      </div>
    </div>
    <div class="item">
      <div class="item-content">
        测试测试测试
      </div>
    </div>
    <div class="item">
      <div class="item-content">
        测试测试测试测试测试测试
      </div>
    </div>
  </div>
```

```css
<style>
  .waterfall {
    column-count: 2;
    column-gap: 0;
  }

  .item {
    break-inside: avoid;
    padding: 10px;
  }

  .item-content {
    /* padding: 10px; */
    font-size: 20px;
    color: #686868;
    border: 1px solid #ccc;
  }
</style>
```
好了成功解决
