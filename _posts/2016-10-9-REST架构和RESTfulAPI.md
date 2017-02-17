---
layout: post
title: REST架构和RESTfulAPI
description: IE条件注释
image: assets/images/pic08.jpg
---

## 精辟的概括

> 用URL定位资源，用HTTP描述操作。
@Ivony 老师

## REST名称

REST是Roy Fielding的毕业论文，晦涩难懂也就不足为奇了。
REST -- REpresentational State Transfer
全称是 Resource Representational State Transfer：通俗来讲就是：资源在网络中以某种表现形式进行状态转移。分解开来：
Resource：资源，即数据（前面说过网络的核心）。比如 newsfeed，friends等；
Representational：某种表现形式，比如用JSON，XML，JPEG等；
State Transfer：状态变化。通过HTTP动词实现。

## RESTful API

实用的是如何正确地理解 RESTful架构和设计好RESTful API。

## 为什么要用RESTful结构呢？

大家都知道"古代"网页是前端后端融在一起的，比如之前的PHP，JSP等。在之前的桌面时代问题不大，但是近年来移动互联网的发展，各种类型的Client层出不穷，RESTful可以通过一套统一的接口为 Web，iOS和Android提供服务。另外对于广大平台来说，比如Facebook platform，微博开放平台，微信公共平台等，它们不需要有显式的前端，只需要一套提供服务的接口，于是RESTful更是它们最好的选择。

## Server的API如何设计才满足RESTful要求?

首先是简洁版里面的那几点。外加一些附带的 best practices：
1. URL root:
https://example.org/api/v1/*
https://api.example.com/v1/*2. API versioning:
可以放在URL里面，也可以用HTTP的header：
/api/v1/
3. URI使用名词而不是动词，且推荐用复数。
BAD
/getProducts/listOrders/retrieveClientByOrder?orderId=1GOOD
GET /products : will return the list of all productsPOST /products : will add a product to the collectionGET /products/4 : will retrieve product #4PATCH/PUT /products/4 : will update product #44. 保证 HEAD 和 GET 方法是安全的，不会对资源状态有所改变（污染）。比如严格杜绝如下情况：
GET /deleteProduct?id=1
5. 资源的地址推荐用嵌套结构。比如：
GET /friends/10375923/profile
UPDATE /profile/primaryAddress/city6. 警惕返回结果的大小。如果过大，及时进行分页（pagination）或者加入限制（limit）。HTTP协议支持分页（Pagination）操作，在Header中使用 Link 即可。
7. 使用正确的HTTP Status Code表示访问状态：HTTP/1.1: Status Code Definitions
8. 在返回结果用明确易懂的文本（String。注意返回的错误是要给人看的，避免用 1001 这种错误信息），而且适当地加入注释。
9. 关于安全：自己的接口就用https，加上一个key做一次hash放在最后即可。考虑到国情，HTTPS在无线网络里不稳定，可以使用Application Level的加密手段把整个HTTP的payload加密。有兴趣的朋友可以用手机连上电脑的共享Wi-Fi，然后用Charles监听微信的网络请求（发照片或者刷朋友圈）。
如果是平台的API，可以用成熟但是复杂的OAuth2

> 最后，要说一下解放思想，Web端不再用之前典型的PHP或JSP架构，而是改为前段渲染和附带处理简单的商务逻辑（比如AngularJS或者BackBone的一些样例）。Web端和Server只使用上述定义的API来传递数据和改变数据状态。格式一般是JSON。iOS和Android同理可得。由此可见，Web，iOS，Android和第三方开发者变为平等的角色通过一套API来共同消费Server提供的服务。

参考
http://www.zhihu.com/question/28557115/answer/41265890

http://www.ruanyifeng.com/blog/2014/05/restful_api.html
