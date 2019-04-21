---
title: web加载优化
date: 2019-04-20 12:47:29
categories:
tags:
---
如何提高页面加载性能?
<!-- more -->


# 网页性能分析工具
[Lighthouse](https://developers.google.com/web/tools/lighthouse/)
[PageSpeed](https://developers.google.com/speed/pagespeed/insights/)

# 如何提高网页加载性能

## 网页主要在处理文本字符串
-  Minify Your Code 压缩清理空格等减小文本体积
-  Compress Text Resources (Gzip)
- 减少库的使用

## 图片资源处理
- 移除不必要的图片 (是否真正的需要图片?)
- 选择合适的图片类型
- 清除图片 元数据（拍着日期，地点，设备等）
- 如有必要，调整图片尺寸
- 压缩

## 合并文本资源(从http请求角度考虑,暂不考虑http2)
- 合并css文件（css是什么？层叠样式表，所以合并注意层叠覆盖现象）
- 合并js文件(js作用域问题，存在和css同样的问题)
- 内联js和css文件
- 调整资源加载优先级
```
<!-- 预加载 (提高资源请求优先级)-->
<link rel="preload" as="script" href="xxx.js">
<link rel="preload" as="style" href="xxx.css">

<!-- 预连接 (提前简历http请求连接,做好资源提取准备工作)-->
<link rel="preconnect" href="https://example.com">

<!-- 预提取 （非关键操作更早发生）-->
<link rel="prefetch" href="other.html">
```

http2为什么特殊，资源为啥不用捆绑打包？
- 复用请求连接
- 服务器主动推送资源到浏览器

在 HTTP/1 中：
浏览器请求 HTML 文件。
服务器返回 HTML 文件，然后浏览器开始解析。
浏览器遇到 <link rel="stylesheet"> 标记，启动对样式表的新请求。
浏览器接收样式表。

## http缓存
Cache-control & Expires

## 网页构建绘制
协调好 html css javascript 三者依赖关系

# webpack优化加载速度

## 1.减小生产环境资源文件大小
从两个方面处理：
- 减小打包文件bundle大小
启用mode为production即可，也可通过optimization增加自定义配置
webpack3 需要 UglifyJsPlugin
- 从loader加载器着手处理
比如：

```css
/* comments.css */
.comment {
  color: black;
}
```
构建压缩后：
```js
// 这里为什么没有压缩呢？
exports=module.exports=__webpack_require__(1)(),
exports.push([module.i,".comment {\r\n  color: black;\r\n}",""]);
```
```js
// webpack.config.js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          'style-loader',
          { loader: 'css-loader', options: { minimize: true } },
        ],
      },
    ],
  },
};
```

## 2.声明构建环境Evn变量
第三方库文件会依赖此条件判断
```js
process.env.NODE_ENV=production 
```

## 3. 启用ES modules
压缩的时候会清除 没有使用的 代码
```js
const render = () => { return 'Rendered!'; };
/* harmony export (immutable) */ __webpack_exports__["a"] = render;
const commentRestEndpoint = '/rest/comments';
/* unused harmony export commentRestEndpoint */
```
## 4. images优化
url-loader 内联base64

## 5. 优化依赖库
[webpack相关优化插件文档](https://github.com/GoogleChromeLabs/webpack-libs-optimizations)
比如：lodash.js Moment.js
```js
babel-plugin-lodash
moment-locales-webpack-plugin
```

## 6. 选择启用 externals 选项
启用externals，从cdn加载

## 7. webpack mode 
```js
// webpack.production.config.js
module.exports = {
+  mode: 'production',
- performance: {
-   hints: 'warning'
- },
- output: {
-   pathinfo: false
- },
- optimization: {
-   namedModules: false,
-   namedChunks: false,
-   nodeEnv: 'production',
-   flagIncludedChunks: true,
-   occurrenceOrder: true,
-   sideEffects: true,
-   usedExports: true,
-   concatenateModules: true,
-   splitChunks: {
-     hidePathInfo: true,
-     minSize: 30000,
-     maxAsyncRequests: 5,
-     maxInitialRequests: 3,
-   },
-   noEmitOnErrors: true,
-   checkWasmTypes: true,
-   minimize: true,
- },
- plugins: [
-   new TerserPlugin(/* ... */),
-   new webpack.DefinePlugin({ "process.env.NODE_ENV": JSON.stringify("production") }),
-   new webpack.optimize.ModuleConcatenationPlugin(),
-   new webpack.NoEmitOnErrorsPlugin()
- ]
}

// webpack.development.config.js
module.exports = {
+ mode: 'development'
- devtool: 'eval',
- cache: true,
- performance: {
-   hints: false
- },
- output: {
-   pathinfo: true
- },
- optimization: {
-   namedModules: true,
-   namedChunks: true,
-   nodeEnv: 'development',
-   flagIncludedChunks: false,
-   occurrenceOrder: false,
-   sideEffects: false,
-   usedExports: false,
-   concatenateModules: false,
-   splitChunks: {
-     hidePathInfo: false,
-     minSize: 10000,
-     maxAsyncRequests: Infinity,
-     maxInitialRequests: Infinity,
-   },
-   noEmitOnErrors: false,
-   checkWasmTypes: false,
-   minimize: false,
- },
- plugins: [
-   new webpack.NamedModulesPlugin(),
-   new webpack.NamedChunksPlugin(),
-   new webpack.DefinePlugin({ "process.env.NODE_ENV": JSON.stringify("development") }),
- ]
}
```

## 8. 利用资源缓存
```js
# Server header
Cache-Control: max-age=31536000
filename: 'bundle.[chunkhash].js',
```

```js
// 解决 runtime 运行时 和依赖块，防止源文件更改导致 第三方依赖的hash也会发生改变
optimization: {
    runtimeChunk: true,
}
```
部分 js 文件  可  内嵌到 html文件里

懒加载

## 9. 监控 和 分析应用程序
- webpack-dashboard 
- bundlesize
- webpack-bundle-analyzer 

## 10. webpack 部分总结
- 清理不必要的代码（压缩，tree shaking, 按需加载依赖）
- 路由懒加载
- 跟踪分子app

