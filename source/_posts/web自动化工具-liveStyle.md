---
title: web自动化工具-liveStyle
date: 2016-11-19 16:00:29
categories: javascript
tags: tool
---

LiveStyle. The first bi-directional real-time edit tool for CSS, LESS and SCSS
主要用来编写 样式文件，可以做到 浏览器和编辑器双向同步。
<!-- more -->
具体安装和使用以及demo，官方有视频，说的很好了，这里简单写写。
推荐先安装一款 [启动服务插件http-server](https://www.npmjs.com/package/http-server)

## demo效果
![liveStyle demo](/images/tool/liveStyle.gif)

## 安装

* 方法1
  打开[liveStyle官网](http://livestyle.io/)
  点击 Download App 的按钮,下载解压
  运行程序，有两个按钮，一个安装Chrome插件，一个安装 Sublime Text插件

* 方法2
  打开Chrome网上应用商店，搜索  liveStyle,添加到扩展程序
  打开sublime编辑器，搜索 liveStyle, 安装(如何安装sublime插件，百度即可)

注意：<b style="color: red;">sublime编辑器，必须是 3以上版本。</b>

## 使用
如下图，打开刚安装的Chrome插件，开启liveStyle
![liveStyle start](/images/tool/liveStyleChrome.png)


demo里启动服务，使用的  node的 插件 http-server.


