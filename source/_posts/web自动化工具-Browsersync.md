---
title: web自动化工具-Browsersync
date: 2016-11-19 17:12:37
categories: javascript
tags: tool
---
browser-sync才是神器中的神器，和livereload一样支持监听所有文件。可是和livereload简单粗暴的F5刷新相比，browsersync的局部刷新造成了本质的区别，推荐使用browsersync。
browser-sync更是可以做到多页面终端同步，文件同步，交互同步，神器也。
<!-- more -->
[browser-sync官方文档](https://browsersync.io/)
## 安装
全局安装即可： 
```
sudo npm install -g browser-sync
```

## 使用
静态站，直接到项目目录启动即可:
```
browser-sync start --server --files "**"
// 参数 files 后面是路径，这里要监听所有文件，写了 **
[BS] Access URLs:
 ------------------------------------
       Local: http://localhost:3000
    External: http://192.168.1.5:3000
 ------------------------------------
          UI: http://localhost:3001
 UI External: http://192.168.1.5:3001
 ------------------------------------
[BS] Serving files from: ./
[BS] Watching files...

```

启动后，就可以愉快的玩耍了,自己试试体验更好。

上面启动之后，会自动启动浏览器，同时我们可以看到启动了两个端口的服务，打开下面3001的端口，可以看到配置页面，请自行修改配置体验效果。
