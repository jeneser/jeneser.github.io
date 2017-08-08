---
layout: post
title: "Ionic实现透明状态栏"
description: "文章发出来一段时间后，得到了很多小伙伴的支持，也积累了一些问题。于是就抽时间写了个DEMO，解决了Ionic实现透明状态栏，呐，你想要的都在这里了"
categories: [ionic, demo]
tags: [ionic, angular, android, demo]
redirect_from:
  - /2017/03/11/
---

> 透明状态栏网上能google出一大堆的解决方案，但大多是针对原生代码的解决方案，都不错，但有关于ionic2的解决方案少之又少，并且说法不一，方法各异，不知道谁对谁错...于是，抽时间写了个DEMO，解决了小伙伴们疑问比较多的地方，呐，你想要的都在这里了

* Kramdown table of contents
{:toc .toc}

![透明状态栏效果](https://raw.githubusercontent.com/jeneser/ionic-super-bar/master/screenshot.gif)

## 如何实现？

**第一步**: 在`platforms/android/src/../../MainActivity.java`路径下`MainActivity.java()` 中的`super.onCreate()` 函数后添加如下代码:

```
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
   getWindow().getDecorView().setSystemUiVisibility(
       View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN |
       View.SYSTEM_UI_FLAG_LAYOUT_STABLE);
}
```
不要忘了在MainActivity.java 中导入依赖包:
```
import android.os.Build;
import android.view.View;
```

**第二步**:在项目根目录的config.xml文件中添加下面配置，这里需要注意
颜色的设置至关重要，此处是八位`hex color`，前两位是透明度，后六位是RGB颜色值．可以根据自己的需要配置．
```
<preference name="StatusBarBackgroundColor" value="#551b1d23" />
```

**第三步**: 在app.scss中添加样式
```
.platform-android {
    ion-header {
      padding-top: $cordova-md-statusbar-padding;
      background-color: color($colors, primary);
    }
}
```

OK! 你现在有一个透明的状态栏了。详细内容或具体的实现细节请查看[项目仓库](https://github.com/jeneser/ionic-super-bar) ，项目将会持续更新，同时你能获取到更多有用的bug修复。我们建议你这样做。

## 集成DEMO

**项目地址:**
[[ionic2-super-bar]](https://github.com/jeneser/ionic-super-bar) https://github.com/jeneser/ionic-super-bar

（2017/4/1更新）
还是有点麻烦，我按照你的方法怎么都实现不了，能不能来点刺激的.....嗯，你的要求我尽量满足。
文章发出来一段时间后，得到了很多小伙伴的支持，也积累了一些问题。于是就抽时间写了个DEMO，解决了小伙伴本疑问比较多的地方，呐，你想要的都在这里了。你可以狠狠的点击下面的链接去往仓库地址，一探究竟。可以直接clone下来，根据README里`Transparent statusBar for android`进行简单的配置，直接运行，便能看到效果了，之后你便可以自由发挥了......

(2017/4/17更新)
*关于兼容性的说明:*
文章发布之后，很多同学说按照你的说法，不能实现啊，很多同学的问题都在于没有关注android版本号。首先透明状态栏(国内大多称沉浸式状态栏)是在android5之后添加的新特性，android4经过hack只能实现半透明的状态,不是特别美观，实现起来有些复杂。所以这个文章以及下面的demo专注于android5+，**在android5+下表现正常**.在android4中状态栏默认黑色，不会因此影响你的页面布局，和整体的效果。
兼容性列表：
![兼容性说明](http://upload-images.jianshu.io/upload_images/4566732-fb9da5e3a07e238b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

(2017/7/7更新)
*关于同一个项目中使用本项目的方法和原生状态栏插件冲突的问题？*
你只需要调整一下以上方法的第二步：在`src/app/app.component.ts`中修改statusbarbackgroundcolor。这样，同一个项目中使用本项目的方法和原生状态栏插件。android和IOS之间将不会有任何冲突。我们建议你这样做。这些代码是无害的！
```
// #AARRGGBB where AA is an alpha value
if (this.platform.is('android')) {
	this.statusBar.backgroundColorByHexString("#33000000");
}
```
你可以查看这个issue来了解更多：[Can we use your option for Android and native status bar plugin for iOS devices on the same project?](https://github.com/jeneser/ionic-super-bar/issues/13)

## 简短的分析

第一步，将视口设置全屏将状态栏固定在视图之上，第二步，配置状态栏颜色，这一步有一个概念需要清楚，hex color是能定义透明度的，我们平常所见的类似`#ffffff`是六位的RGB颜色，这里在前面再加两位便是能定义透明度的ARGB，也就是RGB色彩模式附加上Alpha（透明度）通道．所以可以根据自己的需要进行不同透明度的设置．第三步，由于我们将视口扩大至了整个屏幕，自然我们的页面会被覆盖在状态栏下面，我的做法是给`ion-header`加一个内边距，并且指定一个背景颜色，当作默认的状态栏底色。当然这里你可以自由发挥，你只要记着现在你写的页面已经在透明的状态栏下面了，你要做的就是让页面下移状态栏的高度，腾出位置避免覆盖．
此时，你可能已经意识到，这个方案是比较灵活的，你可以自由的控制你的页面和状态栏的位置．很多同学想要这样一种效果：图片深入到透明的状态栏下面．想一想是不是很容易实现呢？你只需要简单修改或添加第三步中的css样式即可完成你的设计，是不是很酷．．．

*补充:*很多小伙伴记不住`hex color`各透明度的值，或者不是太了解`hex color`，请各位小伙伴自行google吧。我这里给出一个`hex color`各透明度的值.。
参考链接：[Hex Opacity Values](http://stackoverflow.com/questions/15852122/hex-transparency-in-colors)

## 一个额外的小栗子

还是先贴效果图

![透明状态栏效果](http://upload-images.jianshu.io/upload_images/4566732-1d954aef578f16f0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

左侧有一个隐藏的滑动菜单，在Material Design中，上面的那张图片一般是延伸到透明的状态栏下面的，如果状态栏不透明，会很难看，对设计细节比较在意的同学肯定不能忍......
其实实现起来也很简单，还是上面的步骤，只是第三步有一些变化，在`ion-header`外面又套了一层`.ion-page`.如果你还有点迷糊，可以查看项目源码，一探究竟。
```
.platform-android {
  .ion-page {
    ion-header {
      padding-top: $cordova-md-statusbar-padding;
      background-color: color($colors, primary);
    }
  }
}
```

## 我想让状态栏变色怎么办？

在上面第三步，我设置header的padding并指定了background-color,很容易理解这里的背景颜色深入到状态栏下面，提供一种近似于沉浸式的效果，这样做简单，对不了解android的童鞋理解起来很轻松。
第一种方法是覆盖上文第三步中的css`background-color: color($colors, primary);`,正确使用选择器可以实现不同页面拥有不同颜色的状态栏。
第二种通过编程的方式：可以使用`cordova-plugin-statusbar`插件，
在不同视图加载时调用`StatusBar.backgroundColorByHexString("#55C0C0C0");`（同样这里的颜色值也是能添加透明度的）来实现不同页面不同颜色的状态栏．

## 为什么这么做？

*下面是我解决这个问题所走的弯路可掉的坑，如果你有兴趣也可以接着读下去。*

其实呢这一段套路够深的话，应该是放在前面调胃口用的....haha...嗯，闲话不扯，就说说我为什么要这么做呢？其实我和很多小伙伴一样不是专门做android开发的，我是一名小前端，灌水党，能力还是比较有限的。所以很多地方也是有问题的，也请小伙伴们多多指教。定当感激不尽！

**下面是我解决问题的思路和做法 供参考**

透明状态栏网上能google出一大堆的解决方案，但大多是针对原生代码的解决方案，都不错，但有关于ionic2的解决方案少之又少，并且说法不一，方法各异，不知道谁对谁错，满满的都是泪。并且在google `ionic2 statusbar transparent`时结果的第一条答案还是有偏差的，标题是[How can I make a transparent toolbar? - ionic 2 - Ionic](https://forum.ionicframework.com/t/how-can-i-make-a-transparent-toolbar/46401)注意是toolbar而不是statusbar，很多同学把这两个东西混在了一起，导致很多童鞋云里雾里的傻傻分不清.....
在那个帖子中，大家讨论的是toolbar的透明，其实在[Ionic 2 Beta 10中](http://blog.ionic.io/ionic-2-beta-10-is-live/)已经解决这个问题了．可以参考里面的做法．我这里就不说了．
好吧，接着google，接着从各种信息中筛选我想要的结果，未果.....大概是一天之后，偶然发现了[这个帖子](https://forum.ionicframework.com/t/transparent-status-bar-on-ionic-2/77183/3)首先要感谢＠peterbetos．
我按照peterbetos提供的方法，但是遇到很多编译错误，尤其是下面这句，ContextCompat依赖android.support.v4.content.ContextCompat，各种尝试各种错误，是有点麻烦嘛！又试了试，搜了搜，还是有问题。
```
getWindow().setStatusBarColor(
  ContextCompat.getColor(this, R.color.status_bar_transparet));
```
既然上面一小段代码问题这么多，干脆不要，于是就有了上面那三步．我能通过第二步的hex color实现透明，就不用这么麻烦了，理解起来也更简单......于是简单起见，决定根据现有代码，按照自己的思路实现一下，说干就干......

## 最后

ionic正在快速迭代，前两天刚发布2.2.0．很多东西在开发和完善中，就透明状态栏来讲，ionic官方给出的方案是使用`cordova-plugin-statusbar`插件，但是目前这个插件透明状态栏在android下不支持，这就尴尬了不是，好消息是已经有人[PR](https://github.com/apache/cordova-plugin-statusbar/pull/51),坏消息是,一年了还没有合并.....从兼容性和技术上讲是有一定的难度的，需要考虑的东西也很多。
目前ionic团队正在做重要的事，而`cordova-plugin-statusbar`插件也不知何时解决这个问题，也许是明天，也许是很久，谁知道呢？所以你若是觉得这个方案还可以接受，用以用也是极好的！！！

（完结）

原创文章转载请注明．

作者：jeneser

项目地址:·[ionic实现透明状态栏](https://github.com/jeneser/ionic-super-bar)

作者github: https://github.com/jeneser

版权声明：自由转载-非商用-非衍生-保持署名（[创意共享3.0许可证](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)）

勘误&讨论： [New issue](https://github.com/jeneser/jeneser.github.io/issues/new)