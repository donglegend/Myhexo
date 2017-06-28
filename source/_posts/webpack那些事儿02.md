---
title: webpack那些事儿02
date: 2016-08-24 14:23:20
categories: javascript
tags: webpack
---
接着上篇我们有了最简单的安装了webpack的项目目录
<b style="color: red;">这节我们从零开始搭建一个简单的基于webpack的spa应用demo</b>
本节只说基础常用配置项，复杂后续讲解.
<!--more-->
## 新建项目结构目录，如下
![目录结构](/images/webpack/1.png)

	views: 模版目录
	modules: js模块目录

code:
```html
# index.html
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
	<script src="/output/static/app.js"></script>
</body>
</html>
```
```
# main.js
var Hello = require("./modules/hello");
document.write(Hello)
```

```
# hello.js
module.exports = "<h2>Hello donglegend.</h2>";
```



## 配置webpack.config.js文件
```
# 根目录新建webpack.config.js(webpack 会自动寻找一个叫webpack.config.js的文件去执行)
var path = require("path");

module.exports = {
	entry: {
		app: path.resolve(__dirname, "./app/main.js")
		// entry:顾名思义入口文件,app/main.js,输入名字为 app.js
	},
	output: {
		path: path.resolve(__dirname, "./output/static"),//输出路径
		publicPath: 'static/', //调试或者 CDN 之类的域名,稍候会用到
		filename: "[name].js" //配置生成的文件名
	},
	resolve: {
		root: __dirname,  //模块从里开始查找
		extensions: ['', '.js', '.vue'] //模块后缀名，先这么些，稍候会用到
	},
	module: {
		loaders: [] //模块加载器，默认null
	},
	plugins: [] //插件，默认null
}
# 在终端执行命令：webpack 
#（不出意外，会生成output目录,/output/static/app.js）
```

## 启动一个http服务
```
# 根目录新建dev-server.js
var express = require("express");
var app = express();
var port = process.env.PORT || 3000;


app.use(express.static(__dirname+'/'));

app.listen(port, function (err){
	if (err) {
		throw err;    
	}
	console.log('Listening at http://localhost:' + port + '\n')
})
# 执行命令 sudo cnpm i express --save-dev
# 安装完成 执行命令: node dev-server.js
# Listening at http://localhost:3000
# 服务正常启动,chrome浏览器输入  http://localhost:3000/app/views/index.html
```
ok,看到这个就表示成功了
![](/images/webpack/2.png)

回想一下，我们做了什么，<b style="color: red">用webpack命令打包了main.js-->app.js</b>
没错这是最简单的webpack，项目当然不能这么做，我们还没用任何loader和plugins

## 体验loader加载器功能
这里用 vue-loader做一个案例(好兴奋，终于可以可以加载我的.vue文件了

要想试用，先行安装，麻利利的：

	sudo cnpm i vue vue-loader --save-dev

看package.json(vue默认依赖babel等模块，既然如此奔着不浪费原则，就用es6书写吧)
```
"devDependencies": {
    "babel-core": "^6.13.2",
    "babel-loader": "^6.2.5",
    "babel-plugin-transform-runtime": "^6.12.0",
    "babel-preset-es2015": "^6.13.2",
    "babel-runtime": "^6.11.6",
    "css-loader": "^0.24.0",
    "express": "^4.14.0",
    "vue": "^1.0.26",
    "vue-hot-reload-api": "^1.3.3",
    "vue-html-loader": "^1.2.3",
    "vue-loader": "^8.5.2",
    "vue-style-loader": "^1.0.0",
    "webpack": "^1.13.2"
}
```
- app目录下新建components目录
- components下新建mountains.vue

```
# mountains.vue
<template>
	<ul>
		<li v-for="v in list" v-text="v"></li>
	</ul>
</template>
<style>
	ul{
		width: 800px;
		margin: 0 auto;
		text-align: center;
	}
	li{
		padding: 10px 0;
		margin: 6px 0;
		background: #efefef;
		-webkit-transition: all .3s;
	}
	li:hover{
		background: #ccc;
	}
</style>
<script>
	export default {
		data () {
			return {
				list: ["恒山", "衡山", "华山", "泰山", "嵩山"]
			}
		}
	}
</script>
```

```
# main.js
import Vue from "vue";
import Mountain from "./components/mountains.vue";

new Vue({
	el: "body",
	components: {
		Mountain
	}
})
```

```
# index.html 增加下列标签
<mountain></mountain>
```

```
# webpack.config.js 文件的 module增加vue和babel加载器
loaders: [{
	test: /\.vue$/,
	loader: 'vue'
}, {
	test: /\.js$/,
	loader: 'babel',
	query: {
        presets: ['es2015']
    },
	include: "/",
	exclude: /node_modules/
}]
```

执行命令 :  <b style="color: green;">webpack</b>
刷新页面  http://localhost:3000/app/views/index.html

![](/images/webpack/3.png)

至此，vue文件和es6代码 正确被加载执行，有木有很开心。
小提示：<b style="color: #f90">如果执行webpack命令有出错，可以加上参数 webpack --display-error-details</b>
注意：diplay-error-details 前面是 两个短横杠噢


## 两个重量级中间件
不知不觉又写了这么多，然而还没真正开始，接着观看下篇，
<b style="color: red">webpack真正的牛逼之处，两个中间件: [webpack-dev-middleware](https://www.npmjs.com/package/webpack-dev-middleware) 和 [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware)</b>



