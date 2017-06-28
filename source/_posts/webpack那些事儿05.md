---
title: webpack那些事儿05
date: 2016-08-25 15:47:32
categories: javascript
tags: webpack
---
<b style="color: red;">本篇主要关于如何用webpack构建多页应用</b>
<!--more-->

为什么要构建多页应用呢？因为我的项目本来就是多页应用啊
至于为什么要用webpack？因为我要用vue啊，嫌gulp 每次打包慢 啊

## 利用vue-cli的配置，扩展成多页应用
> 想法总是简单，实践总是多坑。

理论支持：spa为单页，那么我是不是输出多个页面就成了多页呢？好像很对的样子。
问题来了，如何才能输出多个页面，主意很容易打到 html处理插件 [html-webpack-plugin](https://www.npmjs.com/package/html-webpack-plugin)上面。
<b style="color: red;">单页面输出:</b>
```
new HtmlWebpackPlugin({
    filename: 'index.html',
    template: path.resolve(__dirname, './app/views/index.html'),
    inject: true
})
```
<b style="color: red;">多页面只需要多new 几个HtmlWebpackPlugin就好了嘛</b>
马上实践，果然可以，✌️。那就动动吧！
兴奋的修改配置如下:
```
# webpack.base.conf.js

entry: getEntry(),
//获取js下面的所有输出js,多页面嘛
function getEntry() {
	var jsPath = fs.readdirSync('./js/');
	var matchs = [],files = {};
	jsPath.forEach(function(item) {
		matchs = item.match(/(.+)\.js$/);
		if (matchs) {
			files[matchs[1]] = path.resolve('./js/', item);
		}
	});
	return files;
}
```

```
# webpack.dev.conf.js
var baseWebpackConfig = require('./webpack.base.conf');
var pages = function() {
  var dir = fs.readdirSync(path.resolve(__dirname, '../views/'));
  return dir.filter(function(name) {
    return name.match(/\.html$/);
  });
}();

for(var i = 0; i<pages.length; i++){
  var chunkname = pages[i];
  var conf = {
    filename: chunkname,
    template: path.resolve(__dirname, '../views/'+chunkname),
    inject: false
  }
  baseWebpackConfig.plugins.push(new HtmlWebpackPlugin(conf));
}
```
<b style="color: red;">想必聪明的你还记得我之前说过的 inject 这个参数吧</b>
如果inject: true,那么每个页面会注入entry中所有的js，所以配置为false，js自己写入

相对的,webpack.prod.conf.js也需要做相应配置。
至于其他资源文件路径什么报错问题，请自行解决，毕竟项目架构不同，错误也不尽相同。

我也是吃了很多苦头，排尽万难，终于把之前用gulp搭建的项目转换成webpack之后，并没有兴奋。
因为结果并不令我满意，为什么呢？
## gulp项目转化成webpack之后的痛

- 项目大概二十多个页面（就是.html文件），启动项目需要<b style="color: red;">一分钟</b>
 > 啦啦啦
   我在等一分钟
   或许下一分钟
   能够感觉你也心痛
   ...
   我想哭
- 还没有引入babel，引入babel转码之后，<b style="color: red;">es6语法不生效</b>
  明明引入了babel转码，为毛始终不生效，头痛许久，查找到问题根源，
  修改： include: './js' ----> include: "/"
  原因未明
- es6语法解决之后，打包更慢了，何止一分钟。。。这次不仅仅想哭

- 虽说整个项目用webpack打包，但是还有一些task必须依赖gulp完成，定制需求。

## 抛弃webpack多页应用配置
<b style="color: red;">因为坑太多，太慢，太痛，太捉急，等等。至此搁笔，果断抛弃此方案！</b>

## [下篇基于webpack的多页应用新方案](http://donglegend.com/2016/08/25/webpack%E9%82%A3%E4%BA%9B%E4%BA%8B%E5%84%BF06/)




