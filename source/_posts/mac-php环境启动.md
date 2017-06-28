---
title: mac php环境启动
date: 2016-12-09 10:16:19
categories: 后端
tags: php
---

mac 环境下，用brew安装php相关环境启动命令

<!-- more -->
## 说明
这里php，mysql，nginx都是用brew安装，安装目录默认，在Cellar下面

## php-fpm 带配置重启
```
/*注意权限，加 sudo*/
/usr/local/Cellar/php56/5.6.27_4/sbin/php-fpm -c /usr/local/etc/php/5.6/php-fpm.conf
```

## nginx 带配置重启
```
/*注意权限，加 sudo*/
/usr/local/Cellar/nginx/1.10.2_1/bin/nginx -c /usr/local/etc/nginx/nginx.conf
```

## mysql
```
/usr/local/Cellar/mysql/5.7.16/support-files/mysql.server 
```