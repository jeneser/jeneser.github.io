---
layout: post
title: absolute无依赖的绝对定位
description: absolute无依赖的绝对定位
image: assets/images/pic06.jpg
---

定位是css最重要的姿势点，了解这些定位之间的关系以及各自的优缺点很重要．
这里是看了张鑫旭老师的视频后整理出来的，瞬间感觉absolute定位竟然如此强大．get it!

### absolute和float相似点

> 1. 包裹性

父元素设置后，都会包裹相应子元素

> 2. 破坏性

子元素设置absolute也会使*父元素塌陷*

> 绝对定位与浮动是不能同时存在的。其中一个生效另一个则无效。

 **对于此限制，absolute采用relative进行限制，float采用clear清除浮动来限制**

### absolute和float相互替换

在有些布局中，两者可相互替换。页面效果相同，例如头像浮动在左侧，右侧自适应文字，此时，头像图片设置absolute或者float可有相同布局效果。

### 误区:子元素定位于父元素左上方

通常写法(问题代码)

`.father{ position:relative;}`

`.son{ position:absolute;top:0;left:0;}`

其实:父元素设置的相对定位和子元素`top:0 ` `left:0` 可以去掉，没有任何问题，代码冗余，另外父元素设置了relative也可能会带来其他问题。

### 无依赖的absolute更强大的定位

当absolute不与relative联合使用时，absolute会表现的更为强大。 独立的absolute可以摆脱overflow的限制，不论是滚动还是隐藏。

> 无依赖的absolute是指:不与relative结合使用，不使用top/left/right/bottom/auto等值。

**绝对定位与位置跟随(inline-block)**

当图片(inline-block)设置左浮动时，给其中一张设置absolute，首先脱离文档流，悬在原来位置，会遮盖后来图片，后来图片占据其位置。如果图片设置block，则会在下一行显示(此时该图片的float失效)

>在测试时会发现 谷歌浏览器下，js给元素添加`display:block`不会有任何变化。这是由于谷歌浏览器本身的机制的原因。

> IE7浏览器bug:设置absolute后任何元素都会inline-block化，所以在任何时候都会呈现跟随状态。
解决:给该元素外包一层没有任何样式的div标签`<div>...</div>`

*怎样精确定位?*

**使用margin**

>此方法兼容IE6

## 实例应用

**1. 图片图标覆盖在图片或元素上面**
eg:导航上的hot图标，活动图片上覆盖的推荐图标等。

>利用以上无依赖absolute，跟随等特性进行实现,应注意的是，其所在位置非常重要，因为其原来位置在哪，无依赖absolute后位置就在哪。

传统上实现方法是，给父元素设置`relative`，子元素`absolute，top，left`进行定位。
这种固定的定位方法，能实现效果，但对于自适应布局并不友好。

利用无依赖absolute结合margin进行精确定位，代码更加健壮，适应性更好。

> 注意:跟随的元素是inline-block,并且要注意其中间的空格，一般在其中间加空注释，来避免空格影响。`<img src="x"><--    --><i class="icon"></i>`

2.下拉框，日期选择器等定位。

3.右侧固定导航，分享菜单

实现原理:`text-align:right;`把*&nbsp;*居右，紧跟随的div设为inline跟随其后，定位，margin微调位置。
`.course-fixed-x { height: 0; text-align: right; overflow: hidden; }`

`.course-fixed { display: inline; position: fixed; margin-left: 20px; bottom: 100px; }`

`<div class="course-fixed-x">
<-- 此处&nbsp-->
	&nbsp;<div class="course-fixed">
<a href="http://www.imooc.com/activity/diaocha" class="goto_top_diaocha"></a>
<a href="http://www.imooc.com/mobile/app" class="goto_top_app"></a>
<a href="http://www.imooc.com/user/feedback" class="goto_top_feed"></a>
</div>
</div>`

3.文本图标对齐
图标绝对定位，margin调整位置，

####  动画尽量作用在绝对定位元素上

当多个元素绝对定位，遵循后来居上，

### 绝对定位拉伸

  `position: absolute;
	left: 0; top: 0; right: 0; bottom: 0;`

会铺满整个屏幕，当增加宽度或高度时，拉伸效果会小于宽高值，也就是遵循宽高值，如:实现半屏遮罩层，只需在上处代码加height:50%

### 宽高自适应的九宫格

`html, body { height: 100%; margin: 0; }
.page {
  position: absolute;
	left: 0; top: 0; right: 0; bottom: 0;
}
.list {
	float: left;
	height: 33.3%; width: 33.3%;
	position: relative;
}`

## absolute新方法-整体布局

在移动端，使用flexed布局定位头部和尾部，会出现层级遮盖，键盘遮挡等一系列问题，利用absolute的整体布局，省去不必要的麻烦。

去body化，用div做容器，


`html, body { height: 100%; margin: 0; }
.page {
  position: absolute;
	left: 0; top: 0; right: 0; bottom: 0;
}`

其中html,body高度要设置，其默认为0，absolute撑开布局，移动端固定的头部和尾部，以及aside侧边栏可用absolute+top+left组合定位
把剩余部分看做body，overflow:auto，即可实现头尾固定内容可自由滚动的效果。
