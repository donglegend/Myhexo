---
title: webpack那些事儿03
date: 2016-08-24 21:16:23
categories: javascript
tags: webpack
---
<b style="color: red;">本节主要讲述 webpack的两大经典开发调试插件，热插拔内存缓存机制</b>
<!--more-->
## [html-webpack-plugin插件的使用](https://www.npmjs.com/package/html-webpack-plugin)

如果没记错，上篇的时候构建完成的js文件是我们在页面用 **script** 标签手动引入的, 聪明的您应该马上看出问题来了，难道每次更改输出path，都要手动更新引入链接吗？如果加上 hash防止缓存，那么一串，岂不头疼欲死。有需求就有解决方案，此款插件就是用来 <b style="color: red;">将依赖自动写入html文件</b>的。
```
sudo cnpm i html-webpack-plugin --save-dev
```
修改配置如下：
```
# 终于动用了配置文件里的 plugins 功能选项，已经快遗忘在角落了
plugins: [
	new HtmlWebpackPlugin({
        filename: '../index.html', 
        //渲染输出html文件名,路径相对于 output.path 的值

        template: path.resolve(__dirname, './app/views/index.html'),
        //渲染源模版文件

        inject: true 
        //这个东西非常重要，true: 自动写入依赖文件; false: 不写入依赖，构建多页面非常有用
    }) 
] 

# 自动写入依赖，所以删除app/views/index.html 的script标签链接
<body>
	<mountain></mountain>
</body>
# 执行命令： webpack
webpack 
```
查看output目录，发现多了一个 index.html 
```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
	<style>
		body{
			background: #f90;
		}
		h2{
			text-align: center;
			font-size: 30px;
			padding: 10px 0;
		}
	</style>
</head>
<body>
	<mountain></mountain>
<script type="text/javascript" src="static/app.js"></script></body>
</html>
# 上面的script就是自动写入的依赖，src路径就是  配置文件中 output.publicPath 
# 终于用到了这个调试功能
```
用chrome 打开链接http://localhost:3000/output/index.html 可以看到一样的屎黄色结果

可能聪明的您又发现了一个问题，我们每次改动一句代码，哪怕更改一个字体大小，几像素留白，就要编译打包一次才能看到结果，有没有好烦呢？

## [webpack-dev-middleware 插件登场](https://www.npmjs.com/package/webpack-dev-middleware)
```
sudo cnpm i webpack-dev-middleware --save-dev
```
此插件主要结合 express的 中间件使用，修改dev-server.js，方法如下:
```
var express = require("express");
var app = express();
var port = process.env.PORT || 3000;

/**
 * 引入webpack 及其 配置config
 */
var webpack = require("webpack");
var webpackConfig = require("./webpack.config.js");
//调用配置,生成 compiler instance
var compiler = webpack(webpackConfig); 

//这里是重点，使用 webpack-dev-middleware 插件
var devMiddleware = require('webpack-dev-middleware')(compiler, {
    publicPath: webpackConfig.output.publicPath,
    stats: {
        colors: true,
        chunks: false
    }
})
// 注册中间件
app.use(devMiddleware);

// 使用静态资源
app.use(express.static(__dirname+'/'));

app.listen(port, function (err){
	if (err) {
		throw err;    
	}
	console.log('Listening at http://localhost:' + port + '\n')
})
```
为了方便调试 和 理解什么叫 <b style="color: red">内存缓存，不写入硬盘</b>，我们修改一点webpack配置
- 修改 output.publicPath: "/",修改为根目录
- plugins中的filename: "index.html" ,置换到根目录
```
output: {
	path: path.resolve(__dirname, "./output/static"), //输出路径
	publicPath: '/', //调试或者 CDN 之类的域名,稍候会用到
	filename: "[name].js" //配置生成的文件名
}

plugins: [
	new HtmlWebpackPlugin({
        filename: 'index.html',
        template: path.resolve(__dirname, './app/views/index.html'),
        inject: true
    })
] 
```
然后，<b style="color: green">重启服务 node dev-server.js</b>

打来浏览器输入： http://localhost:3000/index.html
哇塞，又看到了记忆中 屎黄色 的界面，有木有很开心,打开控制台,会看到这句代码
```
<script type="text/javascript" src="/app.js"></script>
```
惊奇的事情发生了，我们的根目录根本没有index.html 和 app.js，这些东西哪里来的呢？
这就是我们的 伟大的中间件 webpack-dev-middleware 的功劳。

然后去mountains.vue文件中修改背景色或者其他，然后刷新浏览器就能看到效果了
有木有很赞，速度还超快哦,如图改动了背景色，<b style="color: red;">只需 55ms,就是快</b> 
![time](/images/webpack/4.png)

接下来就是解放我们的双手，自动实时刷新页面.

## [webpack-hot-middleware 为了左手](https://www.npmjs.com/package/webpack-hot-middleware)

```
# 老套路 install
sudo cnpm i webpack-hot-middleware --save-dev
```
使用步骤,参照api，很简单
- 增加插件plugins
```
# 注意先行引入 webpack  
# var webpack = require("webpack");
plugins: [
	// Webpack 1.0 
    new webpack.optimize.OccurenceOrderPlugin(),
    // Webpack 2.0 fixed this mispelling 
    // new webpack.optimize.OccurrenceOrderPlugin(), 
    new webpack.HotModuleReplacementPlugin(),
    new webpack.NoErrorsPlugin(),
	new HtmlWebpackPlugin({
        filename: 'index.html',
        template: path.resolve(__dirname, './app/views/index.html'),
        inject: true
    })
] 
```
- Add 'webpack-hot-middleware/client' into the entry array(添加xx到入口数组)
```
entry: {
	app: ['webpack-hot-middleware/client',path.resolve(__dirname, "./app/main.js")]
}
更好的方法是不动基本配置，稍候会在dev-server.js中 书写
```
- Add webpack-hot-middleware attached to the same compiler instance
```
# dev-server.js中 app.use(devMiddleware); 之后增加
app.use(require("webpack-hot-middleware")(compiler));
```
重新启动服务  <b style="color: green;">node der-server.js</b>
然后修改一个 <b style="color: red;">body 背景，切回浏览器，哇塞，自动刷新了</b>哦
怀着激动的心情改了一下main.js，开心的切回浏览器哇靠靠，<b style="color: red;">一切都没变化，错觉？</b>果然手动刷新， 看到了你想看到的。
你没错，是我错了，更改上面第二部的 entry参数配置如下:
```
# 增加 参数reload=true
entry: {
	app: ['webpack-hot-middleware/client?noInfo=true&reload=true',path.resolve(__dirname, "./app/main.js")]
}
```
果然重启服务，一切ok.
闲的蛋疼又去，更改了.vue文件里面的 data 数据，然后。。。。问题又来了，无热加载，控制台却能看到vue文件变化消息
查了查资料：发现这可能是vue的热加载机制和策略问题，目前我不知道怎么解决。


接下来还有一件事，那就是html模版改动的自动刷新，现在是没有这个功能的，不信你试试！

## 实现html模版更改自动刷新
- 更改entry注入参数方式，从dev-server.js写入，example:

```
# webpack.config.js 恢复初始设置
entry: {
	app: path.resolve(__dirname, "./app/main.js")
}
```

```
# dev-server.js
# 增加文件，刷新client单独文件配置，配合html reload
var express = require("express");
var app = express();
var port = process.env.PORT || 3000;

/**
 * 引入webpack 及其 配置config
 */
var webpack = require("webpack");
var webpackConfig = require("./webpack.config.js");

// var devClient = 'webpack-hot-middleware/client?noInfo=true&reload=true';
var devClient = './dev-client';
Object.keys(webpackConfig.entry).forEach(function (name, i) {
    var extras = [devClient]
    webpackConfig.entry[name] = extras.concat(webpackConfig.entry[name])
})
//调用配置
var compiler = webpack(webpackConfig); 

//这里是重点，使用 webpack-dev-middleware 插件
var devMiddleware = require('webpack-dev-middleware')(compiler, {
    publicPath: '/',
    stats: {
        colors: true,
        chunks: false
    }
})

var hotMiddleware = require('webpack-hot-middleware')(compiler)
// 监听html文件改变事件
compiler.plugin('compilation', function (compilation) {
    compilation.plugin('html-webpack-plugin-after-emit', function (data, cb) {
        // 发布事件 reload,这个事件会在dev-client.js中接受到，然后刷新
        hotMiddleware.publish({ action: 'reload' })
        cb()
    })
})


// 注册中间件
app.use(devMiddleware);
app.use(hotMiddleware);

// 使用静态资源
app.use(express.static(__dirname+'/'));

app.listen(port, function (err){
	if (err) {
		throw err;    
	}
	console.log('Listening at http://localhost:' + port + '\n')
})

```

<b style="color: red;">dev-client.js 接受reload事件</b>
```
var hotClient = require('webpack-hot-middleware/client?noInfo=true&reload=true')
// 订阅事件，当 event.action === 'reload' 时执行页面刷新
// 还记得 dev-server.js中 派发的reload事件吧
hotClient.subscribe(function (event) {
    if (event.action === 'reload') {
        window.location.reload()
    }
})
```
重启服务，修改试试？不出意外，应该万事大吉了。
ok，至此，测试程序已经结束了。下篇讲解项目中的webpack配置.

## [本案例测试源码下载](https://github.com/donglegend/webpackprojectstudy)
