---
layout: post
title: 跨域
category: root
summary: '九种跨域方式实现原理'
author: hjy
---
# 一、什么是跨域？

* 1、什么是同源策略及其限制内容？

同源策略是一种约定，所谓同源是指"协议+域名+端口"三者相同，缺一不可。如果缺少了同源策略，浏览器很容易受到XSS、CSRF等攻击。

同源策略限制内容有：

  1、Cookie、LocalStorage、IndexedDB 等存储性内容

  2、DOM 节点

  3、AJAX 请求发送后，结果被浏览器拦截了

但是有三个标签是允许跨域加载资源：

````html
  <img src=XXX>
  <link href=XXX>
  <script src=XXX>
````

如果是协议和端口造成的跨域问题“前台”是无能为力的。

# 二、跨域解决方案

1、JSONP



  