---
layout: post
title: 函数节流
description: 函数节流
image: assets/images/pic05.jpg
---

### 函数节流

>当某个函数多次重复调用时，会造成资源浪费，尤其是在低版本IE中多次调用`resize()`会造成浏览器崩溃。

函数节流的基本思想是:利用定时器来限制函数执行。*setTimeout()*
