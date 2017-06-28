---
title: hexo问题篇(偶尔抽抽疯)
date: 2016-08-23 21:03:27
categories: javascript
tags: hexo
---

hexo安安稳稳的跑了很久，然后 。。。。
让人心碎的hexo问题，华丽丽的摔倒在坑里，只因update了hexo  version
最是哪一句 hexo server让人欲哭无泪

<!--more-->
## -问题场景
设备: Mac OS X
Error with DTrace

	{ [Error: Cannot find module './build/Release/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
	{ [Error: Cannot find module './build/default/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
	{ [Error: Cannot find module './build/Debug/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
	Info Hexo is running at http://0.0.0.0:4000/. Press Ctrl+C to stop.

执行<u style="color: green;"> **hexo s**</u> 之后，出现上述画面，如上所示，服务确实启动了，伴随着让人讨厌的Error提示，作为一名程序员，眼里怎么容忍这等错误,很利索的就执行了 (很久没升级，第一反应版本问题)
```
sudo cnpm update hexo -g
```
满怀希望的敲入<u style="color: green;"> **hexo s**</u>  结果华丽丽的悲剧了，泛着黑色的终端上出现了那么华丽丽的一坨:
```
Usage: hexo <command>

Commands:
  clean     Removed generated files and cache.
  config    Get or set configurations.
  deploy    Deploy your website.
  generate  Generate static files.
  help      Get help on a command.
  init      Create a new Hexo folder.
  list      List the information of the site
  migrate   Migrate your site from other system to Hexo.
  new       Create a new post.
  publish   Moves a draft post from _drafts to _posts folder.
  render    Render files with renderer plugins.
  version   Display version information.

Global Options:
  --config  Specify config file instead of using _config.yml
  --cwd     Specify the CWD
  --debug   Display all verbose messages in the terminal
  --draft   Display draft posts
  --safe    Disable all plugins and scripts
  --silent  Hide output on console

For more help, you can use 'hexo help [command]' for the detailed information
or you can check the docs: http://hexo.io/docs/
```
瞬间我的世界变成了终端一样的酷黑，<u style="color: green;"> **command**</u>就这么没了，被狗吃了？

## -解决问题
上度娘一阵翻搅，有说是node问题，麻利利的用  n  切换node版本，各种升级，包括npm
```
n ls
......
    5.7.1
    5.8.0
    5.9.0
    5.9.1
    5.10.0
    5.10.1
    5.11.0
    5.11.1
    5.12.0
    6.0.0
    6.1.0
    6.2.0 
    6.2.1
    6.2.2
    6.3.0
    6.3.1 
    6.4.0 
......
n 6.4.0 //切换node版本到 6.4.0

sudo cnpm uninstall hexo -g //全局卸载 hexo
sudo cnpm install hexo -g  // 全局重装 hexo

hexo server //满怀希望的敲下回车键
```
<u style="color: red;">*春天没有来到，我听到了心碎的声音,又是更恶心的一坨扑面而来...*</u>
```
{ [Error: Cannot find module './build/Release/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
{ [Error: Cannot find module './build/default/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
{ [Error: Cannot find module './build/Debug/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
Info Hexo is running at http://0.0.0.0:4000/. Press Ctrl+C to stop.
INFO  Start processing
FATAL Something's wrong. Maybe you can find the solution here: http://hexo.io/docs/troubleshooting.html
```
认真的打开了[错误链接http://hexo.io/docs/troubleshooting.html](http://hexo.io/docs/troubleshooting.html)

很容易看到了错误解决方案：
```
$ npm install hexo --no-optional
```
单纯的我开心的敲下这个命令，虽然不知道why,反正别人有这个方案。
事实证明，心碎的多了就不会痛，再一次证明我们年轻和单纯，几度夕阳红，错误依旧在。
问度娘，大家都告诉我 用这个方案妥妥的......

最终一番查找和论证，得出最后结论,  打开 **_config.yml** 配置文件
```
plugins:
# - hexo-generator-feed
# - hexo-generator-sitemap

// 没错 删除plugins依赖的插件 
```
结果，云开见日月，一切恢复了当初，曾经的最好，好想哭....

妈蛋，抽疯一样的hexo

如果hexo d推送不到github或者卡死，尝试以下步骤:
  - 删除根目录下的 .deploy_git 文件夹
  - 删除 db.json 文件
  - hexo clean
  - hexo d -g

至此，确保你的计算机正确安装和配置了 git，依然卡死不动，那么请耐心等待，可能是github的链接问题。

