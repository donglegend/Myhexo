---
title: webpack那些事儿06
date: 2016-08-25 16:50:22
categories: javascript
tags: webpack
---
<b style="color: red;">本篇主要讲述用gulp+webpack构建多页应用</b>
<!--more-->
折腾到现在，项目还必须要进行，.vue文件必须要加载，也就是webpack必须引入。
时间不多了，抛弃上个方案之后，只能牺牲热加载性能，用gulp+webpack构建。

**理论支持：**沿用之前的gulp配置，唯一需要改变的是 之前用browserify打包js替换成webpack打包，想来也好像没什么问题的样子。

## 替换js打包方法

```
browserify -----> webpack
#增加一个 gulp task，没什么可说的
```
当然，程序没那么容易跑起来，换了一个打包方式，肯定各种文件资源路径不对等等各种报错，请自行根据具体Error具体解决。

## 项目优化（基于webpack）
程序可以正常启动以后，就要考虑优化的方式了，毕竟我们的初衷并不是仅仅换一个js的打包方式。

### 1. [alias 还记得小李子的故事吗](http://donglegend.com/2016/08/25/webpack%E9%82%A3%E4%BA%9B%E4%BA%8B%E5%84%BF04/#webpack-base-conf-js)


### 2. 暴露全局变量
 暴露常用插件到全局共用，比如vue,jquery等等，[具体方式点参考之前讲述](http://donglegend.com/2016/08/25/webpack%E9%82%A3%E4%BA%9B%E4%BA%8B%E5%84%BF04/#webpack-base-conf-js)
### 3. 提取公共文件
[调用webpack内置插件功能 点击查看](http://webpack.github.io/docs/list-of-plugins.html#commonschunkplugin)
```
new webpack.optimize.CommonsChunkPlugin({
    name: "common",
    filename: "js/common.js",
    chunks: ['vue', 'jquery]
})
```
### 4. webpack编译加cache 缓存
- webpack 开启 cache: true
- babel转码开启cacheDirectory: true (此点极其重要，速度提升几倍)
### 更多优化点，问度娘。
