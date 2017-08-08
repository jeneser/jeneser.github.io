---
layout: post
title: "部署Angular应用到Github pages"
description: "快速部署angular应用到github pages,利用github提供的pages资源快速创建演示站点"
categories: [angular]
tags: [angular, course, original]
redirect_from:
  - /2017/08/08/
---

> 你将看到：快速部署angular应用到github pages,利用github提供的pages资源快速创建演示站点

* Kramdown table of contents
{:toc .toc}

## 开篇

众所周知，github为开发者提供了免费的静态网站托管服务[github pages](https://pages.github.com/)，我们可以用github pages快速部署我们的项目demo或项目文档。

最近开发angular应用([重构简书](https://github.com/jeneser/jianshu))，总体架构已经做好，在排除了`aot`过程中的种种问题之后，想着先把demo部署到github pages以供大家[查看](https://github.com/jeneser/jianshu)。

![github pages](/assets/post-images/angular_github_pages.png)

## 使用subtree

先来介绍一个通用的方法。在开发[vue豆瓣](https://github.com/jeneser/douban)的过程中，我使用了`git subtree`来快速发布Live demo到`gh-pages`分支。主要使用了以下三条命令：

```
git add -f dist
git commit -am 'Update live demo'
git subtree push --prefix dist origin gh-pages
```

可以看出，该方法使用起来也是相当方便的。用`Git Subtree`来维护我们的子项目代码(/dist)，对于我们整个项目来说是透明的，我们看到的就是一个普通的目录，原来怎么做现在依旧怎么做，只需在需要更新live demo时去做同步代码的操作。如果你对此方法感兴趣可自行搜索`git subtree`来了解更多，在这里就一笔带过了。

当发布angular应用到github pages时，我并不推荐你这样做。因为，我们有更好的方法来部署我们的angular应用到github pages。

## 使用angular-cli-ghpages

[angular-cli-ghpages](https://github.com/angular-buch/angular-cli-ghpages)是angular社区贡献的npm包，提供了一种近乎于傻瓜式的解决方案，即便是你不太了解git。

要注意的是`angular-cli-ghpages`只支持angular2+的项目。Show time ：）

- 首先，我们先来全局安装`angular-cli-ghpages`

```
$ npm install -g angular-cli-ghpages
```

- 接下来，我们使用`angular-cli`的产品模式来打包我们的应用，并且附加`--base-href`参数修改`<base href>`，以便我们的应用能正确访问我们的静态资源。运行如下命令：

```
$ ng build --prod --base-href "https://<user-name>.github.io/<repo>/"
```

- 最后，使用`angular-cli-ghpages`来快速部署我们的应用到github pages。我们使用他的简写命令形式：

```
$ ngh
```

运行命令之后，不出意外的话，控制台将打印`Successfully published!`。此时angular-cli-ghpages已经自动帮我们部署了我们的angular应用。你可以访问`https://username.github.io/app-name/`来查看具体的部署情况。

## 配置参数

`angular-cli-ghpages`提供了很多有用的参数，各个参数的具体用法，请查看angular-cli-ghpages的[官方文档](https://github.com/angular-buch/angular-cli-ghpages)。在这里，我们来说几个常用的：

- 使用`--message`来添加commit信息：

```
$ ngh --message="First deploy"
```

- 部署到指定的特定分支：

```
$ ngh --branch=production
```

- 另外，你可以使用`--dry-run`来查看该命令运行时输出的信息：

```
$ ngh --dry-run
```

## 常见问题

对于单页面应用来说我们应该把所有流量指向我们的`index.html`，来避免异常行为的发生。

在部署过程中你可能会遇到404错误，或者，当我们刷新某个页面时，我们的页面意外的变成了404。

我们需要修复它。对于github pages来说，我们没办法直接配置Github pages，但可以在`commit`时添加一个404页。简单的解决方案如下：

**我们在项目的根目录新建`404.html`，把`index.html`中的内容完全复制到`404.html`中就可以了。**这样做github pages仍然会在恰当的时候给出一个404响应，浏览器将会正确处理该页，并正常加载我们的应用。关于Github pages hack，推荐一篇文章：[S(GH)PA: The Single-Page App Hack for GitHub Pages](http://www.backalleycoder.com/2016/05/13/sghpa-the-single-page-app-hack-for-github-pages/)

（完结）

原创文章转载请注明．

作者：jeneser

作者github: https://github.com/jeneser

版权声明：自由转载-非商用-非衍生-保持署名（[创意共享3.0许可证](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)）

勘误&讨论： [New issue](https://github.com/jeneser/jeneser.github.io/issues/new)