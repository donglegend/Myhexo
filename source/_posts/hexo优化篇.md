---
title: hexo优化篇
date: 2015-12-08 21:03:27
categories: javascript
tags: hexo
---
hexo 书写博客文章，主要使用markdown语法，一种类似html的标记型语言，所见即所得，简单好用。关于美观方面，github上有很多的主题可供我们选择，下载后，我们可根据喜好自行修改配置。(some problem)

<!-- more -->
## markdown新手指南
- 一种很简单的标记型语言，不多做解释，推荐几篇文章以供学习参考
 1. [Markdown 基本语法](https://github.com/younghz/Markdown "_blank")
 2. [markdown 简明语法](http://lutaf.com/markdown-simple-usage.htm  "_blank")

## hexo主题
- 下载 到github 或者百度 找你喜欢的主题，用命令`git clone`克隆, eg:
```
git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
```
- 配置
 打开根目录下的配置文件"_config.yml" 修改主题 `theme: yilia`, 即可生效
 如果不生效，请 执行命令 `hexo clean` 清除缓存，再次运行

## hexo优化
- hexo项目目录![目录结构](/images/hexo_dir.png)
 我们主要修改  _config.yml 和 所用主题里面的 配置和 样式文件
 这里主要介绍 配置文件的含义，请根据喜好自行修改
- /_config.yml

```markdown
# Hexo Configuration
## Docs: http://zespia.tw/hexo/docs/configure.html
## Source: https://github.com/tommy351/hexo/

# Site 修改查看效果，即可知 对应位置关系
title: donglegend #站点名，站点左上角
subtitle: 阳光总在风雨后 #副标题，站点左上角
description:  #给搜索引擎看的，对站点的描述，可以自定义
author: 东胜 #在站点左下角可以看到
email: #你的联系邮箱
language: zh-CN #可选择其他

# URL #这项暂不配置，绑定域名后，欲创建sitemap.xml需要配置该项
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: 
root: /
permalink: :year/:month/:day/:title/
tag_dir: tags
archive_dir: archives
category_dir: categories

# Writing 文章布局、写作格式
new_post_name: :title.md # File name of new posts
default_layout: post
auto_spacing: false # Add spaces between asian characters and western characters
titlecase: false # Transform title into titlecase
max_open_file: 100
filename_case: 0

# 代码高亮设置，语义就说明了一切
highlight:
  enable: true
  backtick_code_block: true
  line_number: true
  tab_replace:

# Category & Tag
default_category: uncategorized
category_map:
tag_map:

# Archives 默认值为2，这里都修改为1，相应页面就只会列出标题，而非全文 ，貌似木有生效
## 2: Enable pagination
## 1: Disable pagination
## 0: Fully Disable
archive: 1
category: 1
tag: 1


# Date / Time format 日期格式，不修改
## Hexo uses Moment.js to parse and display date
## You can customize the date format as defined in
## http://momentjs.com/docs/#/displaying/format/
date_format: MMM D YYYY
time_format: H:mm:ss

# Pagination 每页显示文章数，可以自定义
## Set per_page to 0 to disable pagination
per_page: 5
pagination_dir: page


# Extensions 这里配置站点所用主题和插件
## Plugins: https://github.com/tommy351/hexo/wiki/Plugins
## Themes: https://github.com/tommy351/hexo/wiki/Themes
theme: yilia
exclude_generator:
plugins:
- hexo-generator-feed
- hexo-generator-sitemap

# Deployment 站点部署到github要配置
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: https://github.com/donglegend/donglegend.github.io/
```

- themes/yilia/_config.yml 
 这里不讲解了，顾名思义吧

- themes/yilia/source/css
 此目录下对应各个模块的css样式定义，可对照自行修改

# － fork me on github 彩带配置
  [点击这里](https://github.com/blog/273-github-ribbons "_blank")获取喜欢的样式代码copy到任意全局模版文件即可，如`/themes/yilia/layout/layout.ejs`


## rss订阅
 关于rss订阅的生成，网上有很多教程，也很简单 ，安装一个插件即可,不过这里有一个坑，必须局部安装，
 ```
 sudo npm install hexo-generator-feed --save
 ```
 然后修改根目录下的_config.yml，增加以下内容:
```
# Extensions
plugins:
- hexo-generator-feed

#Feed Atom
feed:
  type: atom
  path: atom.xml
  limit: 20
```
配置文件中也有 网址教程,另外根据主题的不提，在主题的配置文件中 添加rss的地址即可。


 ## 结语
  由于比较忙，没多少时间详细书写，另外，我也才研究了一天多，算当作笔记吧。有问题可联系我，一起探讨和学习
  邮箱： 309093631@qq.com
