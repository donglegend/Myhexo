---
title: web自动化工具-livereload
date: 2016-11-19 16:29:27
categories: javascript
tags: tool
---
livereload是一个很神奇的工具，主要解放了F5键，监听文件变动，整个页面自动刷新。
可搭载gulp等构建工具使用。和liveStyle 针对样式文件相比，可监听任何文件。
<!-- more -->

 [livereload文档](https://www.npmjs.com/package/livereload)

## 安装插件
- 打开Chrome网上应用商店，安装插件livereload.
- 打开终端控制台
```
sudo npm install -g livereload
```

## 使用
- 路径切到项目目录，启动服务（这里依然使用http-server启动）。
```
http-server
Starting up http-server, serving ./
Available on:
  http://127.0.0.1:8080
  http://192.168.1.5:8080
```
- 路径切到项目目录，启动livereload。（切到项目目录，是为了只监听本项目文件，防止监听文件过多，造成机器卡顿）
```
livereload
Starting LiveReload v0.6.0 for /Users/xudongsheng/Documents/project/web-auto-tool/livereload on port 35729.
```
- 启动Chrome浏览器，打开服务文件，启动 livereload插件，插件中心的空心圈圈会变成实心圆圈。
打开控制台，开启插件的时候，细心的同学可能会发现dom中多了一行：
```
<script src="http://127.0.0.1:35729/livereload.js?ext=Chrome&amp;extver=2.1.0"></script>
//注意端口号：35729，想具体了解原理的，自行查找资料即可。
```
至此，相信你就可以愉快的体验 自动刷新功能了，赶快试试吧。

## 高级使用
当然，具体到项目都是搭载脚本使用的，这些教程应该有很多，推荐参考官方教程使用。
