---
title: webpack那些事儿04
date: 2016-08-25 12:16:58
categories: javascript
tags: webpack
---
<b style="color: red;">这节主要讲解真正项目用用到的 webpack配置问题，项目实战篇</b>
<!--more-->
就像我们不会完全做一个项目，不用别人的轮子一样。
这个配置我们借用 vue-cli 搭建的配置来研究，因为它已经足够优秀。

有了前面的基础，这节快速切入主题。

## 用 [vue-cli脚手架](http://cn.vuejs.org/guide/installation.html) 初始化项目
> Vue.js 提供一个官方命令行工具，可用于快速搭建大型单页应用。该工具提供开箱即用的构建工具配置，带来现代化的前端开发流程。只需一分钟即可启动带热重载、保存时静态检查以及可用于生产环境的构建配置的项目：

<b style="color: green;">install</b> 

> #全局安装 vue-cli
$npm install -g vue-cli
#创建一个基于 "webpack" 模板的新项目
$ vue init webpack my-project
#安装依赖，走你
$ cd my-project
$ npm install
$ npm run dev

注意：vue init webpack my-project 这一步初始化过程中，可根据需求选择是否需要某些插件
![](/images/webpack/5.png)
ok，就是这么简单！

## 学习分析vue-cli的配置

- ### 目录结构
![](/images/webpack/6.png)
- ### config目录
 主要是index.js配置，build 和 dev各自的输入输出，以及开发环境服务的默认端口和代理配置项等
 其他三个文件顾名思义，都是方便配置切换环境用的变量。
 <b style="color: red;">这种思路非常值得学习，抽离配置，合并（merge）使用。</b>

- ### build目录,webpack配置精华
  * #### webpack.base.conf.js
  此项配置之前已经讲解过，这里主要说明 **resolve** 配置项
  ```
  resolve: {
    extensions: ['', '.js', '.vue'], //后缀名，用于模块查找
    fallback: [path.join(__dirname, '../node_modules')], 
    alias: {
      jquery: path.resolve(__dirname, "./components/jquery/dist/jquery.js")
    }
  }
  // fallback: 这歌主要用于解决路径找不到问题，配置成绝对路径

  /**
   * [alias 这是个重点,别名，别笑这不是废话]
   * 简单说有一个人叫 莱昂纳多·迪卡普里奥（Leonardo DiCaprio）
   * 这名字真特么长，不符合我们习惯，所以给你另起了一个代号叫  小李子
   * 以后大家叫小李子，我们就会明白是叫 莱昂纳多·迪卡普里奥
   * 所以配置好上面那个之后，我们想召唤 jQuery的时候，
   * 不用写那么多，只需 require("jquery") 即可
   * 有没有瞬间觉得世界很美好呢
   */
  
  ```
  聪明的您可能还会发现一个问题，jquery这种东西，几乎每个页面都需要，<b style="color: red;">每次都召唤require("jquery")</b>有没有很烦恼。
  这里传授你 什么叫做 **大召唤术** ,一次召唤，随身使用。说人话就是做成 **全局变量**。。。
  我们只需要在中加入一个插件即可:
  ```
  plugins: [
			new webpack.ProvidePlugin({
				$: "jquery",
				_: "underscore"
			})
  ]
  ```
  看见没，上面想召唤什么就 加进去就好了，阳光真好啊.

  * #### webpack.dev.conf.js
   [这里就很简单了，简直和我们上篇的测试教程一样嘛（我们那个本来就是只用来测试哈）参照webpack那些事儿03](http://donglegend.com/2016/08/24/webpack%E9%82%A3%E4%BA%9B%E4%BA%8B%E5%84%BF03/)

  * #### webpack.prod.conf.js
  打开看看源码，上线嘛，就是一些代码打包压缩放缓存等等处理，webpack本身就拥有这些功能，正常使用即可.

  * #### utils.js
  这个是作者用来提取打包vue里面的静态资源写的工具函数，我对vue还没有太过深入使用，请自行理解。

  * #### dev-client.js
  [参照webpack那些事儿03](http://donglegend.com/2016/08/24/webpack%E9%82%A3%E4%BA%9B%E4%BA%8B%E5%84%BF03/)

  * #### build.js
  这里也没什么可说的，主要调用用webpack.prod.conf.js配置运行打包程序.
  这里着重推荐  [shelljs 插件](https://github.com/shelljs/shelljs)

 ## 一点闲话 es6 babel
 用babel转码，切记一点，必须有转码规则配置，可写入.babelrc文件
 ```
 # .babelrc
 {
  "presets": ["es2015", "stage-2"],
  "plugins": ["transform-runtime"],
  "comments": false
}
 ```
 或者写入babel-loader加载器
 ```
 loaders: [
	{
	    test: /\.js$/,
	    loader: 'babel',
	    query: {
	        presets: ['es2015']
	    },
	    exclude: '/(node_modules|components)/',
	    include: "/"
	}
]
```
<b style="color: red;">如果发现 便已完毕仍不识别es6语法，不识别import,请更改上面 include 值为 根目录 试试。</b>

## 一点想法关于spa单页应用
构建单页应用，用vue作者大神的这个配置就好了，根据需求自行调整，太优秀。

