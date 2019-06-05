---
layout: post
title: "storybook使用记录"
subtitle: '踩坑记录'
author: "Jorce"
header-style: text
tags:
  - storybook
---

Update:新公司项目使用storybook进行组件展示

---

凭借着过人的理解能力和超乎常人的水平踏入了一家外企。
目前接手的项目是基于react的官网,要求使用storybook进行组件测试和管理。之前没用过,所以来踩踩坑
#####. 首先在项目根目录创建.storybook文件夹
其中包含了addons.js config.js webpack.config.js

1. addons.js 用于管理插件 目前我引入了以下三个包
```js
import '@storybook/addon-storysource/register';
import '@storybook/addon-actions/register';
import '@storybook/addon-links/register';
```

2. config.js 用于引入需要展示的组件。
```js
import { configure } from '@storybook/react';
function loadStories() {
  require('../src/js/components/stories/lis.js');
  require('../src/js/components/stories/buttons.js');
  // require('../src/js/components/stories/heroSlider.js');
  require('../src/js/components/stories/sectionTitle.js');
  // You can require as many stories as you need.
  // 这里的路径需要注意
}
configure(loadStories, module);
```
3. webpack.config.js 用于配置一些路径等问题

   遇到了两个问题
   1.需要将组件代码展示在页面中，使用了addon-storysoure插件仍然需要添加代码
   2.需要展示的组件中使用了alias,storybook默认不读取相关配置。
   ```js
    const path = require('path');
    const storysource = require.resolve('@storybook/addon-storysource/loader')
    const PATH_SRC = path.resolve(__dirname, '../src'); //这里的路径需要注意一下
    const PATH_JS = `${PATH_SRC}/js`;
    const resolve = {
      extensions: ['.js', '.jsx', '.css', '.scss', '.json', '.html', '.ico'],
      alias: {
        store: `${PATH_JS}/store`,
        controllers: `${PATH_JS}/controllers`,
        '@constants': `${PATH_JS}/constants`,
        '@pages': `${PATH_JS}/components/pages`,
        '@layouts': `${PATH_JS}/components/layouts`,
        routes: `${PATH_JS}/routes`,
        scss: `${PATH_SRC}/scss`,
        '@api': `${PATH_JS}/api`,
        '@components': `${PATH_JS}/components`,
        '@bootstrap': `${PATH_JS}/bootstrap`,
        '@utils': `${PATH_JS}/utils`,
        '@propTypes': `${PATH_JS}/propTypes`,
      }
    }
    module.exports = function({ config }) {
      config.module.rules.push({
        test: /\.jsx?$/,
        loaders: [storysource],
        include: path.resolve(__dirname, '../'), // 这里是解决问题1的地方
        enforce: 'pre',
      });
      config.resolve = resolve

      return config;
    }
    ```
##### 在components目录下创建storyies文件夹
其中包含 sectionTitle.js等 这个就是在conifg中引入的文件

一般的普通组件代码如下
```js
import React from 'react';
import { storiesOf } from '@storybook/react';
import { Button } from '@storybook/react/demo';
import { action } from '@storybook/addon-actions';
storiesOf('Buttons', module)
  .add('with text', () => (
    <Button onClick={action('clicked')}>Hello Button111</Button>
  ));

```

但是遇到的问题是组件嵌套过深,同时使用了redux
那么就需要在同级目录下先创建provider
```jsx
import React from 'react'
import { Provider } from 'react-redux'

const ProviderWrapper = ({ children, store }) => (
  <Provider store={store}>
      { children }
  </Provider>
)

export default ProviderWrapper

```
然后在sectionTitle.js中引入
```js
import React from 'react';
import { storiesOf } from '@storybook/react';
import SectionTitle from '../paragraphs/SectionTitle';//此处是我使用的组件
import { createStore } from '../../store/index';//此处是本项目使用的redux
import ProviderWrapper from './ProviderWrapper';//此处是同级的provider
import { sectionTitle } from './data.js';//此处是该组件需要使用的data
/* eslint-disable */
const store = createStore();

const withProvider = (story) => (
  <ProviderWrapper store={store}>
    { story() }
  </ProviderWrapper>
)

storiesOf('sectionTitle', module)
  .addDecorator(withProvider)
  .add('normal', () => (
    <SectionTitle paragraph={sectionTitle}></SectionTitle>
  ));
```

其他的配置官方文档还是比较全的

这次坑先踩到这