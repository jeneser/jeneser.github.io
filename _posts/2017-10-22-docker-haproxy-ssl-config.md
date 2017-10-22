---
layout: post
title: "在docker中实现haproxy SSL安全链接"
description: "使用certbot为容器化的haproxy负载均衡容器配置SSL安全链接"
categories: [haproxy, docker, ssl]
tags: [docker, haproxy, ssl, certbot, letsencrypt]
redirect_from:
  - /2017/10/22/
---

> 使用certbot为容器化的haproxy负载均衡容器配置SSL安全链接。

* Kramdown table of contents
{:toc .toc}

## 引子

![docker](/assets/post-images/http_green_lock.png)

HTTPS是HTTP的加强安全版，用户可以通过安全通道与服务器通信，有效的保护了用户的隐私和服务器安全。

不管你是否留意，已经有越来越多的网站开始使用HTTPS了，为我们的网站加上一把小绿锁是一件有趣而必需的事。

微信小程序要求所有API必须使用HTTPS。

## SSL协议实现过程

## Let’s Encrypt
使用certbot

## 两种方案

## 配置haproxy

## 测试