---
layout: post
title: "杂篇 - Vue豆瓣系列文章"
description: "大概一个月前，开源了Vue重构豆瓣移动端的项目，效果还可以，收到了很多小伙伴的反馈，话说是要写一些文章的，但迟迟没有动笔，估计小伙伴们等的花都谢了"
categories: [vue, original]
tags: [vue, original, course, demo]
redirect_from:
  - /2017/05/24/
---

* Kramdown table of contents
{:toc .toc}

> [**项目地址**](https://github.com/jeneser/douban)

> [**在线演示**](https://jeneser.github.io/douban/)

>不识庐山真面目，只缘身在此山中。

大概一个月前，开源了Vue重构豆瓣移动端的项目，效果还可以，收到了很多小伙伴的反馈，话说是要写一些文章的，但迟迟没有动笔，估计小伙伴们等的花都谢了，拖延症是病，需要治疗...

接下来开始填坑，这一系列的文章会把在开发中遇到的问题总结下来。这篇文章是第一篇，是一些准备工作，包括从idea到具体项目实现...

## 目录结构

万事开头难，当有idea从你脑海中闪过的时候，你第一个想到的应该是:脚手架工具，快速生成基本的项目结构，即刻上手。Vue官方就提供了方便易用的Vue-cli命令行工具，快速，高效，爽! 

这个项目本身是基于Vue-cli的webpack模板，简单的输入一条命令`vue init webpack my-project`，便能生成一个使用webpack作为打包工具，具备热重载，代码检查，css预处理等一系列功能的初始项目。

这一系列文章不会讲webpack如何使用?有可能会涉及一些简单的配置项。当然，学习webpack能很好的理解和解决开发中遇到的问题，有需要可自行谷歌。
不过，也不必担心，正如上面所说，即使你不了解webpack，你仍然可以用Vue-cli快速开发出高逼格Vue应用。

来看看我们的目录结构:
```
.
├── build                      // Webpack打包相关配置
├── config                     // 基本环境配置
├── index.html                 // 通用HTML模板
├── package.json               // 相关依赖
├── README.md                  // README
├── src                        // 源码目录
│   ├── App.vue                // 入口页面
│   ├── assets                 // 静态资源目录
│   ├── components             // 全局公用组件目录
│   │   ├── Banner.vue         // 广告横幅组件
│   │   ├── Card.vue           // 卡片组件
│   │   ├── DownloadApp.vue    // 底部app下载组件
│   │   ├── Group.vue          // 小组组件
│   │   ├── HeaderBar.vue      // 顶部导航组件
│   │   ├── List.vue           // 列表组件
│   │   ├── Rating.vue         // 星级评分组件
│   │   ├── Scroller.vue       // 横向滚动组件
│   │   ├── Marking.vue        // 标记组件
│   │   ├── SubNav.vue         // 二级导航组件
│   │   ├── Tags.vue           // 标签组件
│   │   ├── Types.vue          // 项目类型组件
│   │   └── UserBar.vue        // 用户栏组件
│   ├── main.js                // 应用初始化入口文件
│   ├── router                 // 路由目录
│   │   └── index.js           // 路由配置
│   ├── store                  // Vuex全局状态目录
│   │   ├── index.js           // Store根文件
│   │   └── modules            // 模块目录
│   │       ├── activities.js  // 活动相关状态
│   │       ├── book.js        // 书籍相关状态
│   │       ├── group.js       // 小组相关状态
│   │       ├── movie.js       // 电影相关状态
│   │       ├── search.js      // 搜索相关状态
│   │       ├── subject.js     // 主题相关状态
│   │       └── user.js        // 用户相关状态
│   └── views                  // 视图目录
│       ├── BookView.vue       // 书籍视图
│       ├── DetailView.vue     // 首页活动详情视图
│       ├── GroupView.vue      // 小组视图
│       ├── HomeView.vue       // 主页视图
│       ├── LoginView.vue      // 登录视图
│       ├── MovieView.vue      // 电影视图
│       ├── PagesView.vue      // 综合视图
│       ├── RegisterView.vue   // 注册视图
│       ├── SearchView.vue     // 搜索视图
│       ├── StatusView.vue     // 广播视图
│       ├── SubjectView.vue    // 主题视图
│       └── TalionView.vue     // 覆盖层视图
└── static                     // 静态文件目录
```
能开始了吗？。客官，莫急!

首先，我们要对写的项目有一个整体的印象，哪些东西是公用的？可以抽离为组件的？哪些是一次性组件？这些组件之间是否拥有某种关系？通过怎样的逻辑把他们关联起来？哪些页面是可重用的？又有哪些页面需要同时显示在视图里？路由要怎么划分？项目是否足够的大？要不要统一状态管理......

从上面结构中可以看出，我们把一些可重用的或者是一次性组件写在了`src/components`中。
把完全不同或者是关联性不大的页面独立为不同的视图放在`src/views`里，这些包含了一个个子组件的视图同样可视为组件。

上面结构中对各个目录和文件做了简单的描述，可能有些不太精确，建议结合代码来看。
关于视图方面，主要想说一下`PagesView`视图和`TalionView`视图。`TalionView`在这里是作为弹出层，覆盖在其他页面之上，拥有较高的在z-index，其内容包括了搜索框和一些快速导航。`PagesView`视图，又对各个页面做了一次组合，包括了`<header-bar>`头部导航组件，路由出口`<router-view>`，还有一个`TalionView`视图。

![douban_TalionView.gif](/assets/post-images/vue_douban_talionview.gif)

为什么要这么做呢？
综合项目所有视图来看，除了登录注册页面外，其他页面的布局大致相同：顶部导航+中间内容视图+覆盖层。这在无形中通过视图将此项目分成了两大模块：登录注册类无通用样式模块，和具备通用公共样式的普通模块。

当然这样划分的另一个基础是对路由的配置。接下来，我们来看看路由，进行进一步解释。

## 路由

先瞄一眼我们的路由配置：

```
routes: [
    {                           // [1]
      path: '/',
      redirect: '/pages/'
    },
    {                           // [2]
      path: '/pages',
      component: PagesView,
      children: [
        {
          path: '',
          redirect: '/pages/home'
        },
        {
          path: 'home',
          name: 'HomeView',
          component: HomeView
        },
        {
          path: 'movie',
          name: 'MovieView',
          component: MovieView
        },
        ......
        {
          path: 'detail/:id',
          name: 'DetailView',
          component: DetailView
        }
      ]
    },
    {                           // [3]
      path: '/pages/:classify/subject/:id',
      name: 'SubjectView',
      components: {
        default: PagesView,
        subject: SubjectView
      }
    },
    {                           // [4]
      path: '/search',
      name: 'SearchView',
      components: {
        default: PagesView,
        search: SearchView
      }
    },
    {                           // [5]
      path: '/login',
      name: 'LoginView',
      component: LoginView
    },
    {                           // [6]
      path: '/register',
      name: 'RegisterView',
      component: RegisterView
    },
    {                           // [7]
      path: '*',
      redirect: '/pages/'
    }
  ]
```
路由的匹配规则是自上而下的，第一条[1]规则对路由进行了重定向。接着，第二条[2]，对应了组件`PagesView`，其中又包含了若干条子路由。这相当于上面描述的第一类模块，拥有统一的视图，子路由的路由出口为本组件`PagesView`中定义的`<router-view>`。而这一个聚合的拥有子路由和子视图的模块对应的路由出口为入口文件`App.vue`中定义的无名路由出口`<router-view class="view"></router-view>`。

可以看到在`App.vue`中还有两个具名的路由出口：`name="subject"`和`name="search"`。`subject`出口对应的路由为[3]，是电影，图书等的详情页面，是不是觉得可以把它划分到[2]中的子路由中？完全可以，但这里没有这么做，为了将视图和路由对应起来，拥有一个好看直观的路径，所以就抽了出来，单独配置了，是不是有点蛇精病...回正题，为了同时（同级）展示多个视图，而不是嵌套展示，我们配置了`components`，将`PagesView`和`SubjectView`同时展示在视图中。`search`路由也做了同样的处理。

[5][6]为登录注册路由，只在视图中展示了各自对应的单个组件，不再受约束。自由发挥...

最后一条路由[7]，相当于404页面，在以上所有路由都不匹配的情况下，重定向到404页面，这里又把它重定向到了主页，是不是很机智...

## 状态管理

数据驱动应用，当我们的应用越来越大时，对数据的操控将会越来越复杂。那么，我们到底需要不需要集中式的状态管理(Vuex)？

>Flux 架构就像眼镜：您自会知道什么时候需要它。 --Dan Abramov

对于组件来说，如果，数据本身高度隶属于本组件，那么，直接封装在组件里将是一个好的选择。当父子组件之间共享数据，我们也能通过`props`和自定义事件解决。即使组件越来越多，视图越来越多，如果彼此之间并无太大联系，我们也没有必要使用`Vuex`。

稍微吐槽一下本项目：除了用户模块和搜索模块的状态之外，其他的状态是没有必要一定使用Vuex的。电影数据，图书数据，小组数据等是一次性数据，什么时候访问就什么时候呈现，顶多会通过路由传递一些必要的参数。即是说，其他视图不依赖这些数据，它本身也不需要来自其他视图的状态，并且这些状态并不影响全局状态。这个时候你不必使用Vuex。但你这里使用了，作何解释呢？的确，Vuex很酷，我想试试。没别的理由。我能清楚明了的查看每一个组件的状态，掌控全局，这的确很酷。

![douban_vuex_store.gif](/assets/post-images/vue_douban_vuex_state.gif)

吐槽完了！回正题。现在假设我们的项目足够大了(接着更新说不定就大了哦-_-!)。组件，视图多的数不过来，并且他们之间的关系错综复杂。整个应用也需要记录和维护更多的复杂的全局状态。现在我们有了Vuex，我们把共享状态抽取出来，以一个全局单例模式管理。我们的应用构成一张巨大的蜘蛛网，而不论网的哪一个节点都能访问状态。描述为星型拓扑结构更容易理解一些吧。

为了防止单一的状态集中到一个文件中而变得冗余，难以维护，我们进一步的将状态分割成了模块，这些模块和视图一一对应，这样代码就变得更结构化，且非常容易维护了。

```
│   ├── store                  // Vuex全局状态目录
│   │   ├── index.js           // Store根文件
│   │   └── modules            // 模块目录
│   │       ├── activities.js  // 活动相关状态
│   │       ├── book.js        // 书籍相关状态
│   │       ├── group.js       // 小组相关状态
│   │       ├── movie.js       // 电影相关状态
│   │       ├── search.js      // 搜索相关状态
│   │       ├── subject.js     // 主题相关状态
│   │       └── user.js        // 用户相关状态
```

>不要为了用vuex而用vuex

 如果应用确实不大，只是有一些多层嵌套组件，兄弟组件之间传递状态的需求，使用事件总线[global event bus](http://vuejs.org/guide/components.html#Non-Parent-Child-Communication)就ok啦！如果需要构建一个中大型应用，为了更好地管理状态，上Vuex。至于，不要为了用vuex而用vuex，只要你觉得用着爽，拿来用又何妨！

## HTTP请求

Ajax库的选择是比较自由的。起初，项目使用的是`vue-resource`，其提供的jsonp方法用着比较爽，由于项目的很多数据来自豆瓣的api，直接上jsonp简单方便，跨域什么的不考虑。你很机智嘛！vue-resource不支持服务端渲染，官方也不再推荐vue-resource。但这个不影响它的使用，如果你喜欢，接着用也没有问题。这里我将vue-resource的版本released出来：[The last version of vue-resource](https://github.com/jeneser/douban/releases/tag/v1.0)

花了一点时间，迁移到了`Superagent`，准备做一些有趣的事情。和`axios`基本相同，很明显axios更火，axios的文章也更多一点，有的选未必不好。Superagent是在写express时接触的，前后端通用，作者杀马特TJ，用了一段时间，还不错。现在拿来和vue一块用，更亲切。
Superagent的api简单易用，能满足大多数应用需求，轻量级，支持ssr，可扩展性强。Superagent和axios的核心都不支持jsonp，这里采用了Superagent的插件`superagent-jsonp`。

我们可以这么使用：
```javaScript
import request from 'superagent'
import jsonp from 'superagent-jsonp'

request
    .get('https://api.douban.com/v2/event/list?loc=108288&start=' +
       state.skip + '&count=3')
    .use(jsonp)
    .end((err, res) => {
        if (!err) {
          commit({
            type: 'loadMore',
            res: res.body.events
          })
        }
    })
```
有关于Superagent的更多问题可以阅读[官方文档](https://visionmedia.github.io/superagent/)或参考本项目源码。

## 表单验证

登录注册部分只简单的在前后端做了基本检验，更严格的表单验证，没有做。社区里表单验证的插件也很多，[vee-validate](https://github.com/logaretm/vee-validate)比较好用，就不多说了，如果有需要可以仔细阅读文档。这里先挖个坑，我想再造个轮子，试一试...
我暂时总结了一些关于表单验证的关键词，算是占个坑吧！
```
冗余，远程，时机，分离，集中式管理，重用，可配置，顺序，自定义，字段消毒，随机补充规则，优雅
```
我去！要求很多嘛...

## API

数据来源，分两个部分，一个是豆瓣的api：[Douban Api V2](https://developers.douban.com/wiki/?title=api_v2)，另一个是mock的一些数据：[Douban Backend](https://github.com/jeneser/douban-backend)。采用express手写完成。跨域问题，上面已经介绍，在不能操控的豆瓣数据上，使用的是jsonp。部署在heroku上的mock数据服务，完全可控，采用cors，借助cors模块完成。当然，方法很多，你的地盘，你做主。

## 发布到服务器

在构建成功之后，我们将`dist/`中的文件发布到我们已经准备好的服务器上。
在发布中会遇到两个常见的问题：

第一：`npm run build`后，项目部署到服务器，访问页面空白？
由于项目是用vue-cli构建，我们并没有对项目做任何配置，尽管默认配置下它工作的很好。看控制台，报错，一连串的404，文件找不到。js没加载上，vue没有初始化，页面自然空白。
解决方法：把`congif/`目录下`index.js`中`build`的`assetsPublicPath`改为 `./ `，然后重新npm run build。

第二：`assetsPublicPath`配置正确，页面依旧空白？
查看路由配置信息：`mode: 'history'`启用了History模式。使用 history 模式时，URL 就像正常的 url，不过这种模式要玩好，还需要后台配置支持，vue-router文档给出了不同服务器的简单配置：[HTML5 History 模式](https://router.vuejs.org/zh-cn/essentials/history-mode.html)

**发布到github pages上：**
[github pages](https://pages.github.com/)用来展示项目再好不过了。不过，我们没办法做服务器配置，HTML5 History 模式要舍弃了，页面的滚动行为的体验差点也没关系，关于这两点有后续文章。
`npm run build`后通过下面git命令发布：
```bash
git add -f dist
git commit -am 'Update live demo'
git subtree push --prefix dist origin gh-pages
```

## 最后

这是第一篇，比较杂乱一些，写了这么长，希望对你有所帮助。如果你在查看这个项目，或阅读本文章时有什么问题可以直接联系我或到项目地址提交英文issue，欢迎各位小伙伴前来斧正，不胜感激......项目，系列文章将会持续更新，拖延症也在治疗中...

*本文首发于[简书](http://www.jianshu.com/u/00500711278f)*

作者：jeneser

Github：[https://github.com/jeneser](https://github.com/jeneser)

版权声明：自由转载-非商用-非衍生-保持署名（[创意共享3.0许可证](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)）

勘误&讨论： [New issue](https://github.com/jeneser/jeneser.github.io/issues/new)