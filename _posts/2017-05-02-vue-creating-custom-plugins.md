---
layout: post
title: "Vue.js插件开发[译]"
description: "一般来说，Vue插件的开发是非常简单的。Vue插件仅仅是包含一个公开方法`install`的对象、这个方法有两个参数：Vue 构造器和一个可选的选项对象。尽管，插件系统看起来十分简单，但其仍然可以产生相当大的作用。"
categories: [vue, translation]
tags: [vue, vue-plugin, translation]
redirect_from:
  - /2017/05/02/
---

作者：[Joshua Bemenderfer](https://alligator.io/info/#joshua_bemenderfer)

原文地址: [creating-custom-plugins](https://alligator.io/vuejs/creating-custom-plugins/)

译者：[jeneser](https://github.com/jeneser)

* Kramdown table of contents
{:toc .toc}

>Vue.js插件是为应用添加全局功能的一种强大而且简单的方式。插件的用途很广泛，从全局组件，到为应用添加一些额外的功能、如路由(Vue Router)，存储在应用程序里的不可变数据(Vuex)。

一般来说，Vue插件的开发是非常简单的。Vue插件仅仅是包含一个公开方法`install`的对象、这个方法有两个参数：Vue 构造器和一个可选的选项对象。尽管，插件系统看起来十分简单，但其仍然可以产生相当大的作用。

## 你的第一个插件

为了打开vue插件开发的大门，下面我们将先实现一个最简单的插件。这个插件的功能是：当组件被挂载到DOM后，控制台输出`Mounted!`。

```
// 这是你的插件对象。 它可以导出到任何地方使用。
const MyPlugin = {
  // install方法是必需的
  // 包含两个参数：Vue 构造器，一个可选的选项对象
  install(Vue, options) {
      // 使用minxin将功能注入所有组件
  	Vue.mixin({
      // 添加到mixin中的任何内容将被注入到所有组件中。
      //在这个例子中， mounted() 方法将在组件被挂载到DOM后调用
      mounted() {
        console.log('Mounted!');
      }
    });
  }
};

export default MyPlugin;
```

现在，你的插件便可以使用了。你可以通过导入并用`Vue.use`来显式调用你的插件：`Vue.use(MyPlugin)`

```
import Vue from 'vue'
import MyPlugin from './my-vue-plugin.js'
import App from './App.vue'

// 在这里显式调用插件
Vue.use(MyPlugin)

new Vue({
  el: '#app',
  render: h => h(App)
});
```

你可能会奇怪，为什么我不能直接在`main.js`中调用`Vue.mixin()`来实现这一点呢？其原因就是：我们向Vue添加全局功能，**而没有直接修改应用逻辑**。拆分模块这种做法总是极好的，你可以随时添加或移除一个单独的模块。同时，这也使得插件非常容易分发。

## 添加一些其他的功能

**安装应用范围的组件和指令**

如果你想把组件或指令打包为一个插件来进行分发，可以这样写：

```
import MyComponent from './components/MyComponent.vue';
import MyDirective from './directives/MyDirective.js';

const MyPlugin {
  install(Vue, options) {
    Vue.component(MyComponent.name, MyComponent)
    Vue.directive(MyDirective.name, MyDirective)
  }
};

export default MyPlugin;
```

**修改Vue构造器**

你可以在插件中以你期待的方式来修改Vue构造器(全局Vue对象)。下面的代码在Vue构造器中添加了一个属性`myAddedProperty`和一个方法`myAddedMethod`。

```
const MyPlugin {
  install(Vue, options) {
    Vue.myAddedProperty = 'Example Property'
    Vue.myAddedMethod = function() {
   	  return Vue.myAddedProperty
    }
  }
};

export default MyPlugin;
```

**修改Vue实例**

不需要任何注入机制便可以将属性或方法直接添加到组件实例，你可以这样来修改Vue构造器的`prototype`：

```
const MyPlugin {
  install(Vue, options) {
    Vue.prototype.$myAddedProperty = 'Example Property'
    Vue.prototype.$myAddedMethod = function() {
   	  return Vue.myAddedProperty
    }
  }
};

export default MyPlugin;
```

这些属性将会被加到所有的组件和Vue实例中。

>*社区里公认的做法是：添加在Vue prototype里的任何属性都要以美元符`$`作为其前缀*

**添加组件的生命周期钩子或属性**

如上文中“你的第一个插件”示例所示，你可以通过[Mixin](https://vuejs.org/v2/guide/mixins.html)添加生命周期钩子对Vue组件进行修改。

```
const MyPlugin = {
  install(Vue, options) {

    Vue.mixin({
      mounted() {
        console.log('Mounted!');
      }
    });

  }
};

export default MyPlugin;
```
*Mixin是一个相当重要的话题，但不在本文的讨论范围之内。目前，可以肯定的是，Mixin是一种灵活的分布式复用 Vue 组件的方式，Mixin可以包含任意组件选项并可以混合进其他组件之中。*

## 自动安装插件

对于那些没有在应用中使用模块化系统的用户，他们往往将通过`<script>`标签引用你的插件，并期待插件无需调用`Vue.use()`便会自动安装 。你可以在插件最后添加如下几行代码来实现自动安装：
```
// 如果Vue是全局对象自动安装插件
if (typeof window !== 'undefined' && window.Vue) {
  window.Vue.use(MyPlugin)
}
```

## 发布你的插件

如果你已经写了一个插件，并准备将其分享到社区，下面是一些常用的帮助别人发现你的插件的方法，如果你还不熟悉这些流程的话。

- 选择一个合适的开源协议，然后将源码发布到[NPM](https://npmjs.com/)和[GitHub](https://github.com/)。

- 向[Awesome-Vue](https://github.com/vuejs/awesome-vue)提交PR。很多人会来这里寻找插件。

- (其他) [Vue论坛](https://forum.vuejs.org/c/show-and-tell)，[Vue Gitter Channel](https://gitter.im/vuejs/vue)，或者在Twitter上@ *#vuejs*。

赶快去开发一些插件吧！

End

作者：[Joshua Bemenderfer](https://alligator.io/info/#joshua_bemenderfer)

原文地址:[creating-custom-plugins](https://alligator.io/vuejs/creating-custom-plugins/)

译者：[jeneser](https://github.com/jeneser)

译者GitHub：https://github.com/jeneser

版权声明：自由转载-非商用-非衍生-保持署名（[创意共享3.0许可证](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)）

勘误&讨论： [New issue](https://github.com/jeneser/jeneser.github.io/issues/new)