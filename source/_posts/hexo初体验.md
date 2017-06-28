title: hexo初体验
categories: javascript
tags: hexo
---

## *简介*
 入行时间比较短，刚开始接触hexo，感觉很不错，顺便安利一下。在这里介绍一下我学习hexo的心得和方法，写的不好，欢迎拍砖。那里出了problem？
<!-- more -->

## *使用hexo前提准备*
- node环境
 node直接到[Node.js](http://nodejs.org/ "_blank")官网下载安装即可，mac上 有一个叫做 n 的node版本管理工具。
 具体请自行百度，或到官网查看api，英文不好的可查看[Node.js API 中文版](http://nodeapi.ucdok.com/#/api/ "_blank")
- git账号
 git的安装及其使用教程，请移步[峰哥的史上最浅显易懂的Git教程！](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/ "_blank")

## *hexo开始*
- 安装hexo (unix 或者linux系统)
```
	sudo npm install hexo -g  //g参数表示 全局安装
	hexo -v //查看版本号
```
- 执行init命令初始化hexo到你指定的目录
```
	hexo init <folder>   // 参数 folder 为你指定的项目目录
```
- 安装hexo 插件依赖
```
	sudo npm install  //具体安装的是  项目根目录下的"package.json"文件里面所依赖的插件
```
- 本地查看 执行命令 hexo server  或者简写版  hexo s 启动服务, 到你喜欢的浏览器 打开`localhost:4000`查看。
```
	hexo s
```
	至此，我们很开心的搭建好了本地 hexo环境

－ hexo常用命令
 1. hexo init //初始化
 2. hexo clean //清除缓存 
 3. hexo new <layout> "projectName" //新建资源
 4. hexo server //启动本地服务
 5. hexo g //生成静态资源
 6. hexo d //推送到远程github仓库,可与 g 连用: hexo d -g


## *向远程github推送内容*
- 打开你喜欢的浏览器，登录github，然后`Create a new repository`，项目名称必须是  `yourname.github.io`
- 打开本地hexo项目目录下面的文件"_config.yml"，找到 `deploy`属性,修改为![](/images/hexo_deploy.png)
  注意："yourname"修改为你的 github的 用户名
- 在终端执行命令 `hexo d -g` 即可编译声称public静态文件并推送到你的github的远程仓库
  在浏览器中输入 `yourname.github.io` 即可查看效果
  至此，我们已经学会了搭建环境和推送资源，但我们还没有自己 写过文章，请移步下一篇，将介绍hexo的其他优化和配置



















