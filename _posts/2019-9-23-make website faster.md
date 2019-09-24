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
    在webpack4之前使用过的包是extract-text-webpack-plugin,作用是将css样式提取到单独的文件当中来，即使用import进行引入，支持SourceMaps按需加载。
```js
    const MiniCssExtractPlugin = require('mini-css-extract-plugin');
    // 在loader中使用， 此处注意，此插件默认不支持hmr,所以需要手动开启
    {
      test: /\.scss$/,
      use: [
        // extract-text-webpack-plugin has bug in webpack4
        {
          loader: MiniCssExtractPlugin.loader,
          // 默认情况下，使用的是webpackOptions.output中publicPath
          publicPath: './',
          options: {
            hmr: process.env.NODE_ENV === 'development'
          }
        },
        // MiniCssExtractPlugin.loader,
        'css-loader?sourceMap',
        'postcss-loader?sourceMap',
        'sass-loader?sourceMap',
      ],
    },
    // 在plugins中 初始化
    new MiniCssExtractPlugin({
      filename: 'css/style.[chunkhash].css',
      chunkFilename: 'css/style.[id].[chunkhash].css',
    }),
```

### CopyWebpackPlugin
    此插件不是用于优化，而是因为是SSR项目，便于更换IP等设置，所以将打包文件复制一份打包方便操作。
```js
    const CopyWebpackPlugin = require('copy-webpack-plugin');
    // flatten的意思是忽略文件层级,全部展平
    new CopyWebpackPlugin([
      { from: `${PATH_SRC}/vendor/*`, to: `${PATH_PUBLIC}/vendor/`, flatten: true },
      { from: 'src/assets', to: `${PATH_PUBLIC}/assets/` },
      {
        from: `${PATH_SRC}/*.txt`,
        to: `${PATH_PUBLIC}/[name].[ext]`,
      },
      { from: `${PATH_SRC}/index.hbs`, to: `${PATH_DIST}` }
    ]),
```

### DefinePlugin
    此插件不是用于优化，而是重新定义环境变量，这样就可以忽略构建时所定义的内容
```js
    const CopyWebpackPlugin = require('copy-webpack-plugin');
    new webpack.DefinePlugin({
      'process.env': {
        IS_BROWSER: JSON.stringify(true),
      },
    }),
```

### IgnorePlugin
    这个插件的作用是打包时忽略第三包的指定目录，即按需打包～
```js
    // 例如moment包很大，我们只用了计算时间的问题，并不需要计算时区等，所以要减小这个包的大小。
    new webpack.IgnorePlugin(/^\.\/locale$/, /moment$/),
```

### CleanWebpackPlugin
    这个插件的作用是清除打包时生成的bundle文件，重新打包的过程中不会清除上一次打包的bundle文件
```js
    const CleanWebpackPlugin = require('clean-webpack-plugin');
    // 新版本不需要传参数
    new CleanWebpackPlugin([PATH_PUBLIC], { exclude: ['.gitignore'] }),
```

### SourceMapDevToolPlugin
    用于打包后报错
```js
    new webpack.SourceMapDevToolPlugin(),
```

### OptimizeCssAssetsPlugin
    用于压缩css
```js
    const OptimizeCssAssetsPlugin = require('optimize-css-assets-webpack-plugin');
    new OptimizeCssAssetsPlugin(),
```

### CompressionPlugin
    用于压缩成gzip或其他格式上线
```js
    const CompressionPlugin = require('compression-webpack-plugin');
    new CompressionPlugin({
      filename: '[path].gz[query]',
      test: /\.(js|css|html|svg)$/,
      threshold: 1024, // 只处理大于此大小的文件
      minRatio: 0.8 // 最小压缩比例
    }),
    new CompressionPlugin({
      filename: '[path].br[query]',
      test: /\.(js|css|html|svg)$/,
      threshold: 1024,
      minRatio: 0.8,
      algorithm: 'brotliCompress', //压缩格式
      compressionOptions: { level: 11 }, // 压缩等级
    }),
```
<!-- ### NodemonPlugin -->

### optimization
    用于优化打包策略
```js
    optimization: {
      noEmitOnErrors: true, // 编译错误时不生成资源
      runtimeChunk: 'single', // manifest
      splitChunks: {
        // 缓存打包分离
        cacheGroups: {
          vendor: {
            test: /[\\/]node_modules[\\/]/,
            name: 'vendors',
            chunks: 'initial',
          }
        }
      }
    },
```