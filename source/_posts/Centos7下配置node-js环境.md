title: Centos7下配置node.js
date: 2016-01-06 13:05:28
categories: javascript
tags: node
---
注：本文转载于别人csdn上的博客，在此写下，留作笔记！关于Centos7下配置node.js环境搭建,Centos7是Linux发行版之一
<!--more-->

## 1、软件环境：
 - Centos7 、NodeJS(版本随意)

## 2、安装过程

1. 安装过程中需要管理员权限，及root权限，可以敲入如下命令。
```
	[dongsheng@bogon ~]$ su root
```
	 回车后会出现如下提示：

	Password: 
	输入你设置的密码，输入过程中，终端上没有显示，但实际已经输入了。输入完后如下：

	[root@bogon dongsheng]#

2. 首先确认有nodejs编译及依赖相关软件，如果没有可通过运行以下命令安装。
```
	[root@bogon dongsheng]# yum -y install gcc gcc-c++ openssl-devel
```

3. 下载NodeJS源码包并解压。
```
  [root@bogon dongsheng]# wget http://nodejs.org/dist/v4.2.4/node-v4.2.4.tar.gz
  [root@bogon dongsheng]# tar zxvf node-v4.2.4.tar.gz
  [root@bogon dongsheng]# cd node-v4.2.4
```

4. 配置、编译、安装。
```
  [root@bogon node-v4.2.4]# ./configure --prefix=/usr/local/node
  [root@bogon node-v4.2.4]# make && make install
```
 注：此处请耐心等待，安装时间可能有点长!

5. 配置Node环境变量
```
   [root@bogon node-v4.2.4]# vim /etc/profile
```
   vim进入profile后，我们会看到profile文件中的内容，按下键盘i进入编辑模式，然后在文本最后面写下如下内容。

  export NODE_HOME=/usr/local/node
  export PATH=$NODE_HOME/bin:$PATH
  export NODE_PATH=$NODE_HOME/lib/node_modules:$PATH

  输入完后，按Esc退出编辑模式，然后按下:wq，就会退出保存。

  然后输入以下命令可以立即生效。

  [root@bogon node-v4.2.4]# source /etc/profile

6. 测试是否成功
```
  [root@bogon node-v4.2.4]# node -v
```
 v4.2.4

7. 接下来就可以随意使用node了
