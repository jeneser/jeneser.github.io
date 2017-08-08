---
layout: post
title: "Vue.js路由懒加载[译]"
description: "当你的SPA（单页应用程序）变得复杂时，打包构建后的Javascript包会变得非常大，以至于严重影响页面的加载时间。幸运的是：`vue-router`支持WebPack内置的异步模块加载系统。"
categories: [vue, translation]
tags: [vue, vue-router, translation]
redirect_from:
  - /2017/06/16/
---

作者：[Joshua Bemenderfer](https://alligator.io/info/#joshua_bemenderfer)

原文地址: [lazy-loading-routes](https://alligator.io/vuejs/lazy-loading-routes/)

译者：[jeneser](https://github.com/jeneser)

* Kramdown table of contents
{:toc .toc}

> 当你的SPA（单页应用程序）变得复杂时，打包构建后的Javascript包会变得非常大，以至于严重影响页面的加载时间。幸运的是：`vue-router`支持WebPack内置的异步模块加载系统。所以，那些使用较少的路由组件不必打包进bundles里，只需要在路由被访问时按需加载。

## 使用

假设你的路由配置是这样的：

```
import MainPage from './routes/MainPage.vue'
import OtherMassivePage from './routes/OtherMassivePage.vue'

const routes = [
  { path: '/main', component: MainPage },
  { path: '/other', component: OtherMassivePage }
]
```

简单来说，你可以使用`require.ensure`来替代`import`。它能帮你将`OtherMassivePage`组件以及该组件的所有依赖分割到一个单独的chunk中去。

现在重启你的应用，你会发现并没有什么改变。但，当你打开开发人员工具，选择检查网络，再一次访问`/other`路径时，你会看到一个新的文件被加载进来。

```
import MainPage from './routes/MainPage.vue'
const OtherMassivePage = r => require.ensure([], () => r(require('./routes/OtherMassivePage.vue')))

const routes = [
  { path: '/main', component: MainPage },
  { path: '/other', component: OtherMassivePage }
]
```

是的，这看起来是有点奇怪，相信我，它并不是那么糟糕。

还有一种方法是将路由对应的组件定义成异步组件。写起来像这样：

```
const OtherMassivePage = resolve => {
  // 空数组用来指定该路由组件需要加载的依赖
  require.ensure([], () => {
    resolve(require('./routes/OtherMassivePage.vue'))
  })
}
```

不过，你最好不要使用这种包裹起来的写法，因为WebPack会使用静态分析来检测和分割块。比较好的做法是，将他们写成一行以减少空间的占用。

## 按组分块

有时候我们想把某个路由下的所有组件都打包在同个异步 chunk 中。只需要 [给 chunk 命名](https://webpack.js.org/guides/code-splitting-require/#chunkname)，提供`require.ensure`第三个参数作为 chunk 的名称：

```
// 这两条路由被打包在相同的块中，访问任一路由都会延迟加载该路由组件
const OtherMassivePage = r => require.ensure([], () => r(require('./routes/OtherMassivePage.vue')), 'big-pages')
const WeightLossPage = r => require.ensure([], () => r(require('./routes/WeightLossPage.vue')), 'big-pages')
```

不像许多其他的WebPack任务，这个方法出乎意料的简单，并且能产生意想不到的有用结果。如果你正在维护那些变得臃肿不堪的大型单页应用，我会毫不犹豫的将这种方法推荐给你。

End

作者：[Joshua Bemenderfer](https://alligator.io/info/#joshua_bemenderfer)

原文地址: [lazy-loading-routes](https://alligator.io/vuejs/lazy-loading-routes/)

译者：[jeneser](https://github.com/jeneser)

译者GitHub：[https://github.com/jeneser](https://github.com/jeneser)

版权声明：自由转载-非商用-非衍生-保持署名（[创意共享3.0许可证](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)）

勘误&讨论： [New issue](https://github.com/jeneser/jeneser.github.io/issues/new)