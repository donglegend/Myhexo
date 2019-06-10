---
title: nginx系列2
date: 2019-06-10 16:16:30
categories: http
tags: nginx
---

Nginx 配置系统

<!-- more -->

## 配置介绍

Nginx 的配置系统有一个主配置文件和其他一些辅助配置文件构成。这些配置文件全部都是纯文本文件，位于nginx配置conf目录下。

配置文件中以 # 开头的是注释 语句，对于程序执行没有意义

每个配置项由  配置指令  和 指令参数 两部分组成

## 指令概述
配置指令是一个字符串，可以使用单引号或者双引号括起来，也可以不括，但是如果配置指令包含空格，一定要引起来。

## 指令参数
指令的参数使用一个或者多个空格或者 TAB 字符与指令分开。指令的参数有一个或者多个 TOKEN 串组成。TOKEN 串之间由空格或者 TAB 键分隔。

TOKEN 串分为简单字符串或者是复合配置块。复合配置块即是由大括号括起来的一堆内容。一个复合配置块中可能包含若干其他的配置指令。

如果一个配置指令的参数全部由简单字符串构成，也就是不包含复合配置块，那么我们就说这个配置指令是一个简单配置项，否则称之为复杂配置项。例如下面这个是一个简单配置项：
```
error_page   500 502 503 504  /50x.html;
```
对于简单配置，配置项的结尾使用分号结束。对于复杂配置项，包含多个 TOKEN 串的，一般都是简单 TOKEN 串放在前面，复合配置块一般位于最后，而且其结尾，并不需要再添加分号。例如下面这个复杂配置项：
```nginx
location / {
    root   html;
    index  index.html index.htm;
}
```
## 指令上下文
nginx.conf 中的配置信息，根据其逻辑上的意义，对它们进行了分类，也就是分成了多个作用域，或者称之为配置指令上下文。不同的作用域含有一个或者多个配置项。

当前 Nginx 支持的几个指令上下文：
- main: Nginx 在运行时与具体业务功能（比如http服务或者email服务代理）无关的一些参数，比如工作进程数，运行的身份等。
- http: 与提供 http 服务相关的一些配置参数。例如：是否使用 keepalive 啊，是否使用gzip进行压缩等。
- server: http 服务上支持若干虚拟主机。每个虚拟主机一个对应的 server 配置项，配置项里面包含该虚拟主机相关的配置。在提供 mail 服务的代理时，也可以建立若干 server，每个 server 通过监听的地址来区分。
- location: http 服务中，某些特定的URL对应的一系列配置项。
- mail: 实现 email 相关的 SMTP/IMAP/POP3 代理时，共享的一些配置项（因为可能实现多个代理，工作在多个监听地址上）。
指令上下文，可能有包含的情况出现。例如：通常 http 上下文和 mail 上下文一定是出现在 main 上下文里的。在一个上下文里，可能包含另外一种类型的上下文多次。例如：如果 http 服务，支持了多个虚拟主机，那么在 http 上下文里，就会出现多个 server 上下文。

例如：
```nginx
    user  nobody;
    worker_processes  1;
    error_log  logs/error.log  info;

    events {
        worker_connections  1024;
    }

    http {  
        server {  
            listen          80;  
            server_name     www.linuxidc.com;  
            access_log      logs/linuxidc.access.log main;  
            location / {  
                index index.html;  
                root  /var/www/linuxidc.com/htdocs;  
            }  
        }  

        server {  
            listen          80;  
            server_name     www.Androidj.com;  
            access_log      logs/androidj.access.log main;  
            location / {  
                index index.html;  
                root  /var/www/androidj.com/htdocs;  
            }  
        }  
    }

    mail {
        auth_http  127.0.0.1:80/auth.php;
        pop3_capabilities  "TOP"  "USER";
        imap_capabilities  "IMAP4rev1"  "UIDPLUS";

        server {
            listen     110;
            protocol   pop3;
            proxy      on;
        }
        server {
            listen      25;
            protocol    smtp;
            proxy       on;
            smtp_auth   login plain;
            xclient     off;
        }
    }
```