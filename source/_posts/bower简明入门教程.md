title: bower简明入门教程
date: 2016-02-22 11:59:51
categories: javascript
tags: bower
---
Bower是一个客户端技术的软件包管理器，它可用于搜索、安装和卸载如JavaScript、HTML、CSS之类的网络资源
<!-- more -->

## 环境
	确保已经安装 node  和 git

## 安装bower
使用npm，打开终端，输入：
```
	npm install -g bower
```

## 查看参数
	bower --help

## 自定义包的安装目录

首先进入项目目录下，新建文件 .bowerrc
这个.bowerrc文件是自定义bower下载的代码包的目录
比如我的.bowerrc文件内容如下：
```
{
  "directory" : "components"
}
```

## bower初始化
	bower init
会提示你输入一些基本信息，根据提示按回车或者空格即可，然后会生成一个bower.json文件，用来保存该项目的配置

## 包的安装
比如我要安装一个jquery，输入如下命令：
```
bower install jquery --save
```
然后bower就会从远程下载jquery最新版本到你的components目录下
其中--save参数是保存配置到你的bower.json

## 包的信息
比如我们想要查找jquery都有哪些个版本，输入如下命令：
```
bower info jquery
```
会看到jquery的bower.json的信息，和可用的版本信息

## 包的更新
上面安装的是最新版的高版本jquery，假如想要指定某个版本呢？
比如jquery版本为1.11.3，下面直接修改bower.json文件中的jquery版本号
然后执行如下命令：
```
	bower update
```

## 包的查找
还有一个很重要的功能，就是包的查找，比如我想要安装bootstrap的某个插件，就可以直接在命令行输入：
```
bower search bootstrap
```
bower就会列出包含字符串bootstrap的可用包了


## 包的卸载
	bower uninstall jquery


## 把自己的插件发布到bower(补充)
自己开发一个插件推送到github之后，一定要打一个tag才能成功提交到bower
### 打标签
```
git tag -a 1.0.0 -m “version info″
// -a是添加 标签名，一般写版本号, -m为标签添加注释信息
git push origin --tags
// –tags参数表示提交所有tag至服务器端，普通的git push origin master操作不会推送标签到服务器端
 git tag -d 1.0.0
 // 删除标签
```
### 注册
```
bower register projectName  项目的github地址   
// demo: bower register ui-loading https://github.com/donglegend/ui-loading
```
## 删除bower平台上的插件
```
bower unregister projectName
```

