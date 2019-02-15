---
layout: post
title: "js中的盒子宽高"
subtitle: '诸如offsetWidth ClientX等'
author: "Jorce"
header-style: text
tags:
  - js
---

Update:js中的盒子宽高

---

# JS

### offsetWidth 

padding + width + border 只读

### offsetHeight 

padding + height + border 只读

### clientWidth 

padding + width 只读

### clientHeight

padding + height 只读

### style.width

width + 'px' 可写 (必须在内联样式定义,外联css无效)

### style.height

height + 'px' 可写 (必须在内联样式定义,外联css无效)

### scrollWidth

width + padding + overWidth 可写

### scrollHeight

width + padding + overHeight 可写

### offsetTop

顶部距离父元素的距离  只读

### offsetTop

左部距离父元素的距离 只读

### scrollLeft

当前元素在容器内X的滚动距离 可写

### scrollLeft

当前元素在容器内Y的滚动距离 可写

### e.clientX

鼠标相对于浏览器的X距离 不跟随滚动条

### e.clientY

鼠标相对于浏览器的X距离 不跟随滚动条

### e.pageX

鼠标相对于浏览器的X距离 跟随滚动条

### e.pageY

鼠标相对于浏览器的X距离 跟随滚动条

### e.screenX

鼠标相对于显示器的X距离

### e.screenY

鼠标相对于显示器的Y距离

### e.offsetX

鼠标相对于事件源的容器的X距离

### e.offsetY

鼠标相对于事件源的容器的Y距离
