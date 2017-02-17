---
layout: post
title: 学习CSP(Content-Security-Policy)
description: 学习CSP(Content-Security-Policy)
image: assets/images/pic02.jpg
---

### Content-Security-Policy

> CSP的安全策略，很多人把它喻为XSS攻击的终结者，因为这种策略不再像传统只靠各种正则和特征匹配来识别跨站攻击Payload，而是直接从协议层把一些存在安全隐患的用法默认给干掉了，把同源同域更发挥到了极致。

CSP策略在默认的情况下是不允许使用data URIs资源的,如果要使用，那么需要显示的指定,比如:img-src 'self' data:script-src：在处理脚本资源的时候设置"unsafe-inline"可以阻止内联Js代码的执行。使用unsafe-eval开关可以禁止eval,setTimeout,setInterval函数的执行。
object-src：控制embed,code,archive applet等对象。

 **style-src** ：会控制样式表@import和rel时所引入的URI资源，设置unsafe-inline规则可以是浏览器拒绝解析内部样式和内联样式定义。并不会阻止链入外部样式表。

img-src：可以控制图片资源的连接，包括img标签的src属性，以及CSS3中的url()和image()方法，以及link标签中的href属性(当rel设置成与图像相关的值，比如HTML支持的icon)
media-src:控制媒体类型的外部链入资源，如video, audio, source, 和track标签的src属性。

frame-src:控制内嵌框架包含的外部页面连接：iframe or a frame。

font-src：控制CSS中的@font-face

connect-src：控制XMLHttpRequest中的open()，WebSocket，EventSource
inline script和eval类型函数(包括eval、setInterval、setTimeout和new Function())是不被执行的。另外data URIs也是默认不允许使用的，XBL,只允许通过chrome:和resource:形式uri请求的XBL,其它的比如在CSS中通过-moz-binding来指定的XBL则不允许被执行。

关于Bypass：
通过CRLF相应头分裂注入来BypassCSP需要将新的相应头插入到原来的CSP下面，在处理相同名字的Http头时候，少数浏览器是根据第一次出现的来设置，大部分则是根据最后一次出现的同名Http头来设置。这种属于伪绕过。


CSP2开始支持nonce-来解决Inline JavaScript的问题，在HTTP Header中声明一个随机字符串，在HTML中的JavaScript标签上带了nonce属性，nonce的值和Header指定的一致才会执行对应的JavaScript代码。


[W3C 文档](https://www.w3.org/html/ig/zh/wiki/CSP)
