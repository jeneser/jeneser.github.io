---
layout: post
title: line-height实际应用
description: line-height实际应用
image: assets/images/pic04.jpg
---

### 图片水平垂直居中

> *兼容性 IE8+*

`.box{ line-height:200px; text-align:center; }`

`.box > img{ vertical-align: middle;}`

line-height撑开高度。vertical-align针对行内元素。


### 多行文本垂直居中

> *兼容性 IE8+*

`.box{ line-height:150px;text-align:center}`

`.box > img{ display:inline-block; line-height:normal; vertical-align: middle;}`

原理同上图片居中。图片为inline-block元素，行高设为normal消除150px以及继承下来等的值的影响。


### 代替高度height避免IE6/7下haslayout

代替高度height，可以避免在低版本IE浏览器下触发haslayout属性。haslayout会冲破父级限制等表现异常，例如当给文本加高度时，在IE6/7下，原本inline-block的元素会充满整个页面。利用line-height可代替height。

### 多余的height

在设置单行文本垂直居中时:
如下代码:

`{height:30px; line-height:30;}`

**height可去除。**
