---
layout: post
title: "[豆瓣]精致完整的Vue2+项目"
description: "分享一个比较精致完整的Vue2+项目供大家交流学习"
categories: [vue, demo]
tags: [vue, demo]
redirect_from:
  - /2017/04/16/
---

> 分享一个比较精致完整的Vue2+项目供大家交流学习，这个项目的英文简介:Awesome douban DEMO created with Vue2.x + Vuex + Vue-router + <strike>vue-resource</strike> + Superagent 

[项目地址](https://github.com/jeneser/douban
):https://github.com/jeneser/douban

[在线演示](https://jeneser.github.io/douban/):https://jeneser.github.io/douban

翻译过来呢就是一个涉及面较广的使用豆瓣API作为数据源的Vue2+DEMO。使用了Vue团队所推荐的一些流行的周边插件或库，从路由，到http请求，再到集中式状态管理，总之你要的，都在这。如果你还不清楚怎样将这些库和插件整合在一起，同时使用SCSS来编写具有漂亮UI界面的完整项目，又或者不能简单的勾勒出整体的架构，那么这个项目很可能和你胃口，又或者，你想了解一下Vue的各个API到底怎么用，用在哪里?呐，给你，都在这了。

我相信很多童鞋在找这么一个项目，能由浅入深，而不总是泛泛而谈的简单的使用几个指令或一些模板语法来糊弄大家。我读书少，你别忽悠我。当然啦，很多同学对Vue跃跃欲试，希望能快速上手Vue，享受Vue带来的乐趣，却总是无从下手，不知道用它能做什么，要做点什么，又或是一些同学在网上零零碎碎看了一些文章，就到处说Vue是多么多么简单，我不看文档，不看你什么有多么NB的项目就能怎么怎么样，那你倒是怎么怎么样啊。

我只能呵呵...记得有句话是这么说的，你的能力和你的谦虚程度是有很大关系的。平时在群里遇到的最有趣的莫过于看大家在群里讨论了:是日，有同学问了一句，为啥我的代码在IE8里不能运行呢，有人回到:我的也是，我换了360浏览器，极速模式，普通模式切换了还是不行，真奇怪啊!于是乎大家开始讨论浏览器内核，讨论你都用什么浏览器，讨论蹲在角落满脸无辜的IE系浏览器，哎呦我去，学习呢，别介，斗图吧...你一图我一图，问题也就不了了之了。

讲个冷笑话，别当真。不过这个问题还是要说一下的。挖了坑终究要填。Vue响应式原理中的变化追踪是基于Object.defineProperty的，而Object.defineProperty 是仅 ES5 支持，且无法 shim 的特性，这也就是为什么 Vue 不支持 IE8 以及更低版本浏览器的原因。[了解更多](https://cn.vuejs.org/v2/guide/reactivity.html)

如果你还不了解这些东西，也没事，慢慢来，静下心来，好好读读官方文档，如此通俗易懂，行云流水般的中文文档实在少见。少一些浮夸与套路，多一些务实与真诚!

昨天晚上更新了LiveDemo，整理了英文README,便给awsome-vue提交了PR,今天早上便被Merge了，是加在了Examples分类下。awsome-vue大概是8到24个小时集中处理一次pr和issue，主要由一位来自日本的开发者kazupon负责这个事情。GitHub上成千上万的开源爱好者年复一年，日复一日，无偿的为社区服务，热心的帮助来自世界各地的志同道合的小伙伴，嗯，这大概便是开源精神吧！又如卡尔维诺所说：“在梦中，他是一个青年，来到这里他已是老年，欲望已成记忆，行动起来吧少年，趁时间还早......

好了，扯了这么多，我都不知道你到底要说什么了。回到正题，这个项目可能不尽完美，是的，我也在学习中，项目也一直在更新中。对于很多小伙伴要求的完整的登录逻辑，也正在完善，后端node已经写了大半了，相信很快就能完成更新。我将会抽时间写个系列文章，把开发中遇到的问题，坑，或者是小技巧沉淀下来，这一系列文章并不会按部就班的从头到尾复制粘贴代码一步步告诉你怎样做个项目，应为这样的文章太多了，xx安装教程，xx入门hello Word教程，不能说没用，比较鸡肋吧。这一系列文章能写多少就写多少，能解决问题，尽量不扯淡。这大概是第一篇。

最后如果你在查看这个项目时有什么意见或建议可以直接联系我或到项目地址提交英文issue，只要我看到了会立即回复的，大家共同学习共同进步。

就这么多吧。
Hope you like it!


![douban_home.png](/assets/post-images/vue_douban_home_001.png)
![douban_movie.png](/assets/post-images/vue_douban_movie_002.png)

![douban_detial.png](/assets/post-images/vue_douban_detial_003.png)
![douban_search.png](/assets/post-images/vue_douban_search_004.png)


-------

(5月26日更新)
过来更新一下:
这次加了许多东西，主要是用户登录注册逻辑，express+jwt，后端代码部署在heroku上(可能有些慢)供大家在线体验，具体后端代码可以看[这里](https://github.com/jeneser/douban)
项目的整体架构已经OK。辣么，接下来要做的便是把握细节了...

Next step:
1. 本地直接mock的数据全部转移到后端，由后端统一提供所有数据。
2. 添加评论，发话题功能?
3. 规范部分可复用的组件,合理使用Slot分发内容。(已完成70%)
4. 抽离公用函数，混合，合理使用自定义指令
5. 表单字段校验(准备造一个字符校验的轮子...)
6. 加载动画以及转场动画(loading组件已完成)
7. 注释，完善注释和文档，然后再写一点文章
......

(end)

*本文首发于[简书](http://www.jianshu.com/u/00500711278f)*

*原创文章转载请注明*

作者：jeneser

Github：https://github.com/jeneser

勘误&讨论： [New issue](https://github.com/jeneser/jeneser.github.io/issues/new)