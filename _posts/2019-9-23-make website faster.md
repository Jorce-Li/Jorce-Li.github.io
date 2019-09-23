---
layout: post
title: "提升chrome评分 优化首屏等加载速度"
subtitle: 'webpack等'
author: "Jorce"
header-style: text
tags:
  - webpack
  - 首屏优化
---

Update:首屏优化

---

最近公司项目遇到的问题是要提升网站的首屏加载速度以及chrome的评分。
本项目是二次开发，他们旧网站的评分是40/15(destop/mobile) => 90/75

看到这个分差距的时候，吓了一跳，因为之前没用过这个评分，也没仔细做过首屏优化，所以在公司fullstack的帮助下进行了这次浩瀚的工程。

## index.hbs的所有文件按需加载

```js
export const loadJS = (src, id) => {
  return !document.getElementById(id) && new Promise((resolve) => {
    const mapJs = document.createElement('script');
    mapJs.type = 'text/javascript';
    mapJs.src = src;
    mapJs.onload = resolve;
    mapJs.id = id;
    document.body.appendChild(mapJs);
  });
};

export const loadCss = (src, id) => {
  return !document.getElementById(id) && new Promise((resolve) => {
    const css = document.createElement('link');
    css.rel = 'stylesheet';
    css.href = src;
    css.id = id;
    css.onload = resolve;
    document.head.appendChild(css);
  });
};
```

然后在使用该scirpt或css的地方进行引入就好了，注意的点是不要重复加载 所以使用了id

## 使用loadable进行按需加载

之前使用vue的项目中，vue-router已经有了按需加载的封装，本次使用react，所以另外使用了loadable进行按需加载

```js
  //在需要引入的组件或页面使用loadable进行初始化
  import loadable from '@loadable/component';
  const PopupForm = loadable(() => import('@components/PopupForm'));
  //在 init app 中 使用loadableReady 进行包裹使其生效
  import { loadableReady } from '@loadable/component';
  loadableReady(() => {
    hydrate(
      <App store={store} />,
      document.getElementById('app')
    );
  });
  //在webapck plugin中初始化
  const LoadablePlugin = require('@loadable/webpack-plugin');
  new LoadablePlugin(),
```

## 使用react-lazyload进行图片懒加载

```js 
    //对于footer header等下拉的组件 直接进行包裹
    //对于图片也是一样 直接包裹img标签即可，这要求网站预先要使用相同的img组件 最为快捷
    //offset 指向下滚动的加载偏移量
    import LazyLoad from 'react-lazyload';
    <LazyLoad height={665} offset={200}>
      <Footer />
    </LazyLoad>
```

## webpack 相关打包插件

### MiniCssExtractPlugin
### CopyWebpackPlugin
### DefinePlugin
### IgnorePlugin
### MomentTimezoneDataPlugin
### CleanWebpackPlugin
### SourceMapDevToolPlugin
### OptimizeCssAssetsPlugin
### CompressionPlugin
ssr
### NodemonPlugin


### optimization