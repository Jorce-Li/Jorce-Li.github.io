---
layout: post
title: "兼容iphonex以及11以上底部被盖住的问题"
subtitle: 'iphonex兼容'
author: "Jorce"
header-style: text
tags:
  - 样式兼容
  - 
---

Update: 新方案safe-area-inset-bottom

---

## iphoneX 以及11以上兼容

最近一直不管是做小程序还是h5遇到需要兼容iphoneX以及11以上底部按钮的问题，应该很多人都遇到过，如果使用fixed或者sticky bottom:0的话 会被iphone底部那条线盖住，所以需要一个好的解决办法
1. 判断是否是iphonex或者11以上，然后单独进行处理
```js
 isIphoneX() {
    // iPhone X、iPhone XS
    const isIPhoneX = /iphone/gi.test(window.navigator.userAgent) && window.devicePixelRatio && window.devicePixelRatio === 3 && window.screen.width === 375 && window.screen.height === 812
    // iPhone XS Max iphone11 Pro
    const isIPhoneXSMax = /iphone/gi.test(window.navigator.userAgent) && window.devicePixelRatio && window.devicePixelRatio === 3 && window.screen.width === 414 && window.screen.height === 896
    // iPhone XR iphone11
    const isIPhoneXR = /iphone/gi.test(window.navigator.userAgent) && window.devicePixelRatio && window.devicePixelRatio === 2 && window.screen.width === 414 && window.screen.height === 896
    if (isIPhoneX || isIPhoneXSMax || isIPhoneXR) return true
    return false
  },
```
2. 使用safe-area-inset-bottom
这个属性很早之前就有了，但是没有遇到过，现在发现很实用，可以兼容各端
```js
<meta name="viewport" content="viewport-fit=cover" />
body {
  padding:
    env(safe-area-inset-top, 20px)
    env(safe-area-inset-right, 20px)
    env(safe-area-inset-bottom, 20px)
    env(safe-area-inset-left, 20px);
}
// 第二个参数可以不设置就是默认底部安全位置上方
```