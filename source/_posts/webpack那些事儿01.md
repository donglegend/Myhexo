---
title: webpack那些事儿01
date: 2016-08-24 00:17:40
categories: javascript
tags: webpack
---

##  为什么引入webpack?
*webpack到底是什么，怎么用，可能有人不是很清楚，但是绝对听说过。
套用俗语：“没吃过猪肉，也见过猪跑”。
webpack有多火，到Github逛逛便知，Github 上各大主流的项目都是基于webpack搭建。*

奔着学海无涯的精神，尽管项目没用webpack，偶尔还是看了看。之前项目一直沿用gulp构建，人都有惰性，本人有时更甚之，懒于折腾，直到引入vue，.vue文件用webpack的vue-loader加载打包比较简单，加上想引入webpack的一些其他功能，至此不得不更改配置构建。

<b style="color: red">我们拥有两只脚，却不想走的更远，人性惫懒！</b>

##  webpack到底是什么?
迫于天朝环境，打开度娘，输入webpack,然后轻敲回车。第一个就是[webpack官网](http://webpack.github.io/)了，打开官网首页那张图很 简单粗暴直接的 向你说明了webpack到底是个什么样的存在。
如果之前你有模块化开发的经历，相信很容易看明白，<b style="color: red">webpack就是一模块加载器兼打包工具</b>，就像一个皮包公司，任何资源都可以接受，然后经过一系列处理转化输出一个结果。这里是[webpack api docs](http://webpack.github.io/docs/),英文不好的同学，可召唤度娘来帮忙。

##  webpack的工作流程理念
webpack真正牛逼之处在于玩配置，按照它的规则，你配置告诉webpack需要做什么，用什么做就万事大吉了，说起来简单，真要写好一个配置也不是一件容易的事，后面会详细说明如何写webpack配置文件。
> 下面列个对比表：

| diff | Gulp          | Webpack       |
|:----------: | :-----------: |:-------------:| 
| pipe(流) | 基本一个task一个pipe   | 共享一个pipe |
| 模块 |对模块的定义比较局限   | 各种资源（包括图片、样式、字体等）      |
| 构建 | 自己构建维护任务流 | 写好配置，等待结果（ 比如打包、压缩混淆等）      |
| 插件 | 插件多 | 插件更完善，扩展好 |
| watch refresh | livereload  监听打包 | 热插拔（内存缓存机制） |
| more | ... | ... |
当然，说这么多，并不是说gulp不好，相反**gulp已经足够优秀**，很多task必须依赖gulp来完成。
<b style="color: red">一种技术就在哪里，运用之妙，存乎一心</b>

## webpack的使用
目前，最迫切的就是装好webpack，先用来打包我的.vue 爽一把，直接上干货。

- ### install webpack

```
npm install webpack -g  #首先全局安装(注意权限问题，用sudo)
mkdir projectname && cd projectname #新建项目目录
npm init # 初始化自动创建package.json文件，一路默认回车即可
npm install webpack --save-dev #局部安装，写入依赖
/**	
* packago.json 文件
*/
{
  "name": "projectname",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^1.13.2"
  }
}

```
至此webpack已正确安装，下篇具体讲解使用方法，基本命令行参数不做详细解释，直接从webpack.config.js配置文件开始。

## 分享源码demo
[预览demo](http://donglegend.com/vue-webpack-router-/dist/index.html)
[vue + webpack + vue-router test demo源码](https://github.com/donglegend/vue-webpack-router-)
借用vue写的一个test程序，用vue作者的话说，用vue-router写一个spa应用简直易如反掌。

