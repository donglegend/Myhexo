---
title: vue2/vuex2的那点坑
date: 2016-11-24 23:46:16
categories: javascript
tags: vue
---
说是坑，其实大部分是我们自己的过错！

[vuex官方demo在1.0可以运行，在2.0报错？](https://www.zhihu.com/question/51628897)
此类问题，应该很常见吧？

还有顺溜的利用1.0搭建的webpack编译环境到了vue2.0突然失效了，报错了？简直要疯啊

<!-- more -->

## 1.问题一
<b style="color: red;">
\*Failed to load resource: net::ERR_FILE_NOT_FOUND<br>
\*app.js:16381 Uncaught SyntaxError: Unexpected token import
</b>
可能你会疑惑，webpack中明明写了 babel 加载器，query中也写了参数presets，然而 es6 的 import依然不识别，这是为什么呢？
答案是如果你的 include 路径没写错，那么 你就应该添加 <b style="color: green; font-size: 20px;">.babelrc</b> 的配置文件,比如：
```
{
  "presets": ["es2015", "stage-2"],
  "comments": false
}
```

## 2.问题二
<b  style="color: red;">Failed to mount component: template or render function not defined</b>
这是什么问题呢？因为是2.0的时代了，但我们没认真看api，可能顺手用了1.0的习惯，很明显template or render是 模版编译渲染问题。
[官方文档解释  独立构建-vs-运行时构建](http://cn.vuejs.org/v2/guide/installation.html#独立构建-vs-运行时构建)  解释的多么清楚，一定要看文档
默认 NPM 包导出的是 运行时 构建。为了使用独立构建，在 webpack 配置中添加下面的别名：
```
resolve: {
  alias: {
    'vue$': 'vue/dist/vue.js'
  }
}
```

## 3.问题3
<b style="color: red;">
vuex官方demo在1.0可以运行，在2.0报错？
[Vue warn]: Property or method "counterValue" is not defined 
</b>

这又是什么原因呢，因为用着  vue2.0和  vuex2.0 却 使用着 vuex1.0 的语法文档。

什么也不说了，看文档吧  [Vuex2 中文文档](http://vuex.vuejs.org/zh-cn/)

这里有写的一个[vuex2 demo预览](http://donglegend.com/effects/vuexscript/demo.html) ，[demo源代码](https://github.com/donglegend/vuexscript/tree/master) 直接使用，无需安装webpack打包，无模块.
