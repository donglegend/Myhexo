---
title: iphone/ios修改title无效
date: 2017-02-07 15:36:44
categories: javascript
tags: title
---

动态修改iOS微信浏览器的标题无效解决黑科技

<!-- more -->

## 需求
从后台请求数据，然后动态设置网站标题。

## 问题
运行在微信浏览器中，修改之后标题无效。

## 解决方案(直接上代码)
```
document.title =  "new title";
//判断运行设备
if (/ip(hone|od|ad)/i.test(navigator.userAgent)) {
    var i = document.createElement('iframe');
    i.src = '/favicon.ico'; //可以请求任何资源，为了性能，这里请求icon
    i.style.display = 'none';
    i.onload = function() {
        setTimeout(function(){
            i.remove();
        }, 0)
    }
    document.body.appendChild(i);
}
```

## 思路
因为刷新页面时能及时显示title，所以用iframe做一次伪请求，在body中添加一个iframe标签，请求成功之后再移除dom