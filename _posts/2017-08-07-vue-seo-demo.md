---
layout: post
title: "Vue.js教程: 构建一个预渲染SEO友好的应用示例 [译]"
description: "在本节中，我会告诉你如何使用`Vue 2.0 & Snipcart`建立一个小型的电子商务应用程序。我们还将看到如何确保产品页面被搜索引擎正确“抓取”。"
categories: [vue, seo, translation]
tags: [vue, seo, translation, demo]
redirect_from:
  - /2017/08/07/
---

作者：[Maxime Laboissonniere](https://snipcart.com/blog?author=2765)

原文地址: [Vue.js Tutorial: An Example to Build and Prerender an SEO-Friendly Site](https://snipcart.com/blog/vuejs-tutorial-seo-example)

译者：[jeneser](https://github.com/jeneser)

* Kramdown table of contents
{:toc .toc}

> 快速了解？直接前往教程步骤或[Github仓库](https://github.com/snipcart/vue-prerender-snipcart)&[在线演示](https://vue-snipcart.netlify.com/)

“我受不了了！我们的内部报告面板太烂了”

产品经理很生气。他从这个即将崩溃的应用程序中拉取数据的操作是灾难性的。

“Max，我们需要更好的报告。你能修吗？”

“老实说，我更愿意建立一个全新的应用”，我笑着回答说。

“好，请便。全权委托，老铁”

我笑着，搓了搓手。最后，在一个需要使用JS框架的场景中，大家一致选择了`Vue.js`。

-------

最近，我完成该应用的代码，我对它简直爱不释手。
我花了一些时间为社区写了一个vue.js教程，这些教程的灵感全部来自于我最近对vue的实践。在这里，我主要讨论以下两点：

1. 如何使用Vue.js构建精简的Web应用程序
2. 如何使用[`prerender-spa-plugin`](https://github.com/chrisvfritz/prerender-spa-plugin)来处理Vue.js应用的预渲染与SEO

更具体地说，我将带您创建一个小商店，它将具备SEO友好的产品页面。我会提供在线演示以及相关代码。
在我们开发的最新版[Headless CMS](https://snipcart.com/blog/intro-api-first-headless-cms-directus)中我接触过一些vue，这一次我们会更加的深入，我很兴奋！

> 更新：我们正在将Snipcart的前端从Backbone迁移到Vue.js，[了解更多](https://snipcart.com/blog/progressive-migration-backbone-vuejs-refactoring)

我们先来为那些不熟悉渐进式框架(Vue.js)的同学做一下简单的介绍。

## Vue.js到底是什么？

![Vue.js](/assets/post-images/vue_seo_vuejs.png)

**Vue.js是一套帮助你构建用户界面的轻量级，渐进式的JavaScript框架**

不要被“JS框架”这一定义所愚弄。Vue与目前流行的React.js & Angular.js是截然不同的。对于初学者来说，它不是Google＆Facebook等商业技术巨头的开源副产品。

[Evan You](https://twitter.com/youyuxi)(尤雨溪)在2014年首次发布了它，旨在创建一个“增量开发”的现代JS库。Vue最强大的功能之一是：创建可复用的组件，你可以在其他项目中重用这些组件而不用再次编写。所有开发人员都可以在项目中尝试Vue，而不用担心这会对现有的代码库产生危害或是增加额外的负担。

抛开模式和术语，我觉得Vue有以下提论：

**1. 一开始你不知道整个应用的架构状态**
**2. 应用数据一定会在运行时发生改变**

正是围绕这些提论，vue塑造了自身：它是渐进式，基于组件和响应式的。组件的粒度划分可以让你轻松地分离应用逻辑，同时又保持它们的可重用性。更重要的是，它将您的数据原生绑定到视图，以便在需要时“神奇”地更新(通过watcher)。虽然许多响应式前端框架拥有同样的功能，但是我发现Vue更优雅的实现了它，并且，对于我的大多数用例，它往往表现的更好。

Vue还具有更加平滑的学习曲线，对于React来说，我们需要掌握JSX模板等的相关知识。甚至可以说Vue是React减去了比较复杂的部分。

*Vue官方文档提供了与其他JS框架(React, Angular, Ember, Knockout, Polymer, Riot)更加深入的对比。[查看官方文档](https://vuejs.org/v2/guide/comparison.html)*

最后但同样重要的是：得益于高性能&强大的开发工具，Vue为我们提供了最佳的编码体验。它能[如此流行](https://www.quora.com/How-popular-is-VueJS-in-the-industry/answer/Evan-You-3)也就不足为奇了!

![vuejs流行度](/assets/post-images/vue_seo_vue_hot.png)

从开源项目[Laravel](https://laravel.com/)＆[PageKit](https://snipcart.com/blog/pagekit-cms-ecommerce-demo)，到企业，如[Gitlab](https://about.gitlab.com/2016/10/20/why-we-chose-vue/)＆[Codeship](http://codeship.com/)（更不用说阿里巴巴和百度这些巨头了），许多组织正在使用Vue。

OK，现在是时候来看看我们将如何使用它了。

## Vue.js例子：一个快速的，搜索引擎友好的电子商务应用

在本节中，我会告诉你如何使用`Vue 2.0 & Snipcart`建立一个小型的电子商务应用程序。我们还将看到如何确保产品页面被搜索引擎正确“抓取”。

### 准备

- 了解一些Vue.js相关知识，[还不了解？](https://www.slideshare.net/paddylock/an-introduction-to-vuejs)
- 基本了解[vuex](https://vuex.vuejs.org/en/intro.html) & [vue-router](https://router.vuejs.org/en/)
- [一个Snipcart帐户](https://app.snipcart.com/register)（测试模式永久免费）

如果你想深入了解 Vue 2.0 相关知识，可以查看Laracasts上的[这个系列](https://laracasts.com/series/learn-vue-2-step-by-step)。

### 1. 环境配置

首先，我们将使用vue-cli来构建基本的Vue应用程序。在你喜欢的终端里，输入：

```
npm install -g vue-cli
vue init webpack-simple vue-snipcart
```

这将创建一个新的vue-snipcart文件夹，其中包含使用vue-loader的基本配置，它将能使我们编写单文件组件(template/js/css在同一个`.vue`文件中)。

我们希望这个示例尽可能真实，因此，我们将在本应用中增加两个广泛应用于大型项目的模块：`vuex`和`vue-router`。

- vuex是类Flux架构的状态管理器 - 轻量级，非常强大。它受到了Redux的影响，你可以在这里[了解更多](https://egghead.io/courses/getting-started-with-redux)。
- vue-router允许您定义路由以动态处理应用程序的组件。

要安装这些，请先进入`vue-snipcart`项目文件夹，然后运行以下命令：

```
npm install --save vue-router
npm intsall --save vuex
```

接下来要安装的是`prerender-spa-plugin`，这将使我们能够预渲染“蜘蛛”将要爬行的路径：

```
npm install --save prerender-spa-plugin
```

快要完成了，最后四个包：

- pug - 模板引擎，相对于HTML我更喜欢它。
- vuex-router-sync-to - 轻松保持vue-router和vuex存储同步。
- copy-webpack-plugin-to - 轻松包含我们在dist文件夹中的静态文件。
- babel-polyfill - 在PhantomJS中运行Vue（通过我们的预渲染插件使用）。

运行这些：

```
npm install --save pug
npm install --save vuex-router-sync
npm install --save copy-webpack-plugin
npm install --save babel-polyfill
```

### 2. 架构

安装完成后请检查是否安装正确。之后，便可以处理我们的商店数据了。

先从`vuex`的`store`开始，我们将使用它来存储/访问我们的产品信息。

在本演示中，我们将使用静态数据，如果我们要取而代之，它仍然可以工作。

*注：关于Snipcart，我们使用[基本的JS代码段](https://docs.snipcart.com/getting-started/installation)注入购物车，并使用简单的[HTML属性](https://docs.snipcart.com/configuration/product-definition)定义产品。*

#### 2.1 构建store

在`src`中创建一个`store`文件夹，包含以下3个文件：

- state.js - 定义我们的静态产品数据
- getters.js - 定义`get`函数，通过ID检索产品
- index.js - 组合前两个文件

```
//state.js
export const state = {
    products: [
        {
            id: 1,
            name: 'The Square Pair',
            price: 100.00,
            description: 'Bold & solid.',
            image: 'https://snipcart.com/media/10171/glasses1.jpeg'
        },
        {
            id: 2,
            name: 'The Hip Pair',
            price: 110.00,
            description: 'Stylish & fancy.',
            image: 'https://snipcart.com/media/10172/glasses2.jpeg'
        },
        {
            id: 3,
            name: 'The Science Pair',
            price: 30,
            description: 'Discreet & lightweight.',
            image: 'https://snipcart.com/media/10173/glasses3.jpeg'
        }
    ]
}

//getters.js
    export const getters = {
        getProductById: (state, getters) => (id) => {
            return state.products.find(product => product.id == id)
        }
    }

//index.js
import Vue from 'vue'
import Vuex from 'vuex'
import { state } from './state.js'
import { getters } from './getters.js'

Vue.use(Vuex)

export default new Vuex.Store({
  state,
  getters
})
```

#### 2.2 构建路由器

我们将保持商店尽可能简单：展示产品列表的首页以及每个产品的详细信息页面。我们需要在路由器中注册两条路由来处理这些路由：

```
import VueRouter from 'vue-router'
import Vue from 'vue'
import ProductDetails from './../components/productdetails.vue'
import Home from './../components/home.vue'

Vue.use(VueRouter)

export default new VueRouter({
  mode: 'history',
  routes: [
    { path: '/products/:id', component: ProductDetails },
    { path: '/', component: Home },
  ]
})
```

我们还没有创建这些组件，不用担心，马上就来，;）

请注意，我们在`VueRouter`声明中使用了`mode：'history'`。这一点很重要，否则我们的`prerender`插件将不会工作。其区别在于路由器将使用`history API`而不是`hashbang`来导航。

#### 2.3 把所有东西组合在一起

现在，我们有了数据(store)和路由器，我们需要把他们注册到应用中。更新你的`src/main.js`文件：

```
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import { sync } from 'vuex-router-sync'
import store from './store'

sync(store, router)

new Vue({
  store,
  router,
  render: h => h(App)
}).$mount('#app')
```

很简单吧！正如前面提到的，`vuex-router-sync`中的`sync`方法从我们的`store`中注入状态到当前的路由中。我们稍后再用。

### 3. 书写Vue组件

有数据感觉真棒，但将它显示出来将会更好。我们即将用到的三个组件：

- Home - 展示产品列表
- Product - 单个产品信息，将被用在`Home`组件中
- ProductDetails - 产品详情页

他们将被包含在`src/components`文件夹中。

```
//Home.vue

<template lang="pug">
    div(class="products")
        div(v-for="product in products", class="product")
            product(:product="product")
</template>

<script>
import Product from './../components/Product.vue'

export default {
  name: 'home',
  components: { Product },
  computed: {
    products(){
      return this.$store.state.products
    }
  }
}
</script>
```

以上，我们使用`store`中的状态来获取我们的产品，并对它们进行迭代，来渲染每一个产品。

```
//Product.vue
<template lang="pug">
  div(class="product")
   router-link(v-bind:to="url").product
      img(v-bind:src="product.image" v-bind:alt="product.name" class="thumbnail" height="200")
      p {{ product.name }}
    
    button(class="snipcart-add-item"
      v-bind:data-item-name="product.name"
      v-bind:data-item-id="product.id"
      v-bind:data-item-image="product.image"
      data-item-url="/"
      v-bind:data-item-price="product.price")
        | Buy it for {{ product.price }}$
 
</template>

<script>
export default {
  name: 'Product',
  props: ['product'],
  computed: {
    url(){
      return `/products/${this.product.id}`
    }
  }
}
</script>
```

通过路由器，我们链接到其他页面(`ProductDetails`)，来看看我们的最后一个组件：

```
//ProductDetails.vue
<template lang="pug">
  div(class="product-details")
    
    img(v-bind:src="product.image" v-bind:alt="product.name" class="thumbnail" height="200")
     
    div(class="product-description" v-bind:href="url")
      p {{ product.name }}
      p {{ product. description}}

      button(class="snipcart-add-item"
        v-bind:data-item-name="product.name"
        v-bind:data-item-id="product.id"
        v-bind:data-item-image="product.image"
        data-item-url="/"
        v-bind:data-item-price="product.price")
          | Buy it for {{ product.price }}$

</template>

<script>
export default {
  name: 'ProductDetails',
  computed: {
    id(){
      return this.$store.state.route.params.id
    },
    product(){
      return this.$store.getters.getProductById(this.id)
    }
  }
}
</script>
```

这一节的逻辑要稍微复杂些：我们从路由中获取当前的ID，然后通过之前创建的`getter`获取相关的产品信息。

### 4. 创建App

我们开始使用刚才创建的组件。

打开`App.vue`文件，其内容是脚手架(`vue init webpack-simple`)生成的默认内容。我们来修改它：

```
<template lang="pug">
  div(id="app")
    TopContext
    router-view

</template>

<script>
import TopContext from './components/TopContext.vue'

export default {
  name: 'app',
  components: { TopContext }
}
</script>
```

`TopContext`组件不是很重要，它仅仅是一个`header`。关键部分是`router-view`：它将通过`VueRouter`动态加载组件，而之前与之关联的组件将被替换。

最后我们来更新一下`index.html`。对于我们的用例来说，我们在`src`中创建新的目录`static`，移动`index.html`文件至`static`并将其更新为如下内容：

```
<!DOCTYPE html><html lang="en">
  <head>
    <meta charset="utf-8">
    <title>vue-snipcart</title>
  </head>

  <body>
  
    <div id="app">    
    </div>
  
    <script src="/build.js"></script>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/2.2.2/jquery.min.js"></script>
    <script src="https://cdn.snipcart.com/scripts/2.0/snipcart.js" data-api-key="YjdiNWIyOTUtZTIyMy00MWMwLTkwNDUtMzI1M2M2NTgxYjE0" id="snipcart"></script>
    <link href="https://cdn.snipcart.com/themes/2.0/base/snipcart.min.css" rel="stylesheet" type="text/css" />
  </body>
</html>
```

你可以看到，我们在`index.html`中添加了`Snipcart`的必要脚本。如果将他们精细的划分到各个组件之中代码看起来会更加干净，但，由于我们所有的View都需要它们，我们便这样做了。

### 5. 使用Prerender插件处理Vue.js SEO

![vuejs seo prerendering](/assets/post-images/vue_seo_prerender_spa_plugin.png)

我们应用中的所有内容都是使用JS动态渲染的，这很不利于搜索引擎优化(SEO)：网页中的异步内容不能被“蜘蛛”(search engine bots)有效的识别并抓取，这样的话，我们的电子商务网站错过了所有有用的“网络爬虫”，这不是一个明智的选择！

让我们使用**prerendering**技术来为我们的Vue.js应用程序带来更多的SEO机会。

相对于Vue的SSR(服务器端渲染)，prerendering则更容易使用。坦率地说，前者[有些矫枉过正了](https://github.com/chrisvfritz/prerender-spa-plugin#prerendering-vs-server-side-rendering-ssr)，除非你有**大量的**路由要处理。另外，这两种技术在实现SEO层面所达到的效果是相似的。

预渲染将使我们能够保持我们的前端作为一种快速，轻量级的静态网站，以便于“蜘蛛”进行爬取。

让我们来看看如何使用它：转到`WebPack`配置文件，在`plugin`配置项中添加以下配置：

```
plugins: [
  new CopyWebpackPlugin([{
    from: 'src/static'
  }]),
  new PrerenderSpaPlugin(
    path.join(__dirname, 'dist'),
    [ '/', '/products/1', '/products/2', '/products/3']
  )
]
```

好吧，它是如何工作的呢？

`CopyWebpackPlugin`将会复制`static`文件夹中的文件到`dist`文件夹中(只包含引用`Vue App`的应用程序的视图)。然后，`PrerenderSpaPlugin`使用`PhantomJS`加载网页的内容，并将结果作为我们的静态资源。

瞧！我们现在已经为我们的Vue应用程序提供了预渲染的，SEO友好的产品页面。

我们使用如下命令来进行测试：

```
npm run build
```

这将生成一个`dist`文件夹，其中包含生产环境所需的一切。

## 其他重要的SEO因素

1. 考虑为您的页面添加适当的`meta`标记和站点地图(sitemap)。您可以在“postProcessHtml”函数(prerender-spa-plugin插件的配置项)中了解有关`meta`标记的[更多信息](https://www.npmjs.com/package/prerender-spa-plugin#webpack-advanced)。
2. [恰当的内容](https://moz.com/blog/the-ten-types-of-content-that-work-best-for-seo-whiteboard-friday)在现代SEO中起了重要作用。建议您确保应用程序中的内容易于创建，编辑和优化。为了授权内容编辑者，请考虑将`headless CMS`放入组合中并用来构建真正的[JAMstack](https://snipcart.com/blog/jamstack-clients-static-site-cms)。
3. 现在，HTTPS连接正式成为Google的排名因素。我们在[Netlify](https://www.netlify.com/)上托管这个演示，Netlify为我们提供了免费的SSL证书。
4. [Mobile-first indexing](http://searchengineland.com/faq-google-mobile-first-index-262751)和 [mobile-friendliness](http://searchengineland.com/library/google/google-mobile-friendly-update)也是排名的重要因素。确保您的移动体验与桌面版一样快速完整！

## GitHub库和在线演示

![vuejs-tutorial-live-demo.png](/assets/post-images/vue_seo_live_store.png)

来吧，这里是在线演示及代码仓库的地址！

> [GitHub仓库](https://github.com/snipcart/vue-prerender-snipcart)

> [在线演示](https://vue-snipcart.netlify.com/)


## 总结

我之前使用过Vue，本教程的制作过程还是相当顺利的。我花了一个小时在Demo上，在使用`CopyWebpackPlugin`时遇到了困难，好在我在他们的文档中找到了答案。

我希望这篇文章能鼓励开发人员在一些项目中开始使用Vue。就像我说的，您可以通过开发一个现有项目的一小部分来逐步地开始，我认为这绝对值得一试。我们的开发主管正在使用Vue编写最新的商业仪表盘功能，他非常喜欢Vue。另外，如果配置正确，Vue完全可以驱动具有良好SEO结果的应用程序。

如果你受到了启发，可以看看[Awesome-vue](https://github.com/vuejs/awesome-vue)，它包含了Vue示例和相关项目。

**如果你真的喜爱Vue，[cop some swag](https://vue.threadless.com/) 或 [support the creator](https://www.patreon.com/evanyou)**

-------

如果你觉得这篇文章有价值，请花一点时间分享到Twitter上。有什么遗漏或错误的？有关于Vue的？或其他框架处理SEO的一些想法？现在评论区是你的了！

End

作者：[Maxime Laboissonniere](https://snipcart.com/blog?author=2765)

原文地址: [Vue.js Tutorial: An Example to Build and Prerender an SEO-Friendly Site](https://snipcart.com/blog/vuejs-tutorial-seo-example)

译者：[jeneser](https://github.com/jeneser)

译者GitHub：[https://github.com/jeneser](https://github.com/jeneser)

版权声明：自由转载-非商用-非衍生-保持署名（[创意共享3.0许可证](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)）

勘误&讨论： [New issue](https://github.com/jeneser/jeneser.github.io/issues/new)