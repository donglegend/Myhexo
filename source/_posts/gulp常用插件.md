title: gulp常用插件
date: 2016-08-06 13:34:18
categories: javascript
tags: gulp
---

本人就职于一家互联网小公司，根据自己的项目纪录一下常用的gulp插件，项目不是很大！
<!--more-->

## [gulp-load-plugins](https://www.npmjs.com/package/gulp-load-plugins "gulp-load-plugins")
 此插件的功能主要搭配 **package.json** 文件中声明的依赖使用。 自动加载 **package.json** 中声明的gulp-或gulp.开头的插件

不必这样：
```
	var gulp = require('gulp'),
	    jshint = require('gulp-jshint'),
	    uglify = require('gulp-uglify'),
	    concat = require('gulp-concat');
```
只需这样：
```
	var gulp = require('gulp'),
	    $ = require('gulp-load-plugins')();
	gulp.task('js', function () {
	   return gulp.src('js/*.js')
	      .pipe($.jshint())
	      .pipe($.uglify())
	      .pipe($.concat('main.js'))
	      .pipe(gulp.dest('build'));
	});
```
## [yargs](https://www.npmjs.com/package/yargs "yargs")
Node中处理命令行参数，把参数序列化成 json 对象

## [del](https://www.npmjs.com/package/del "del")
自行看文档，不解释


## [gulp-filter](https://www.npmjs.com/package/gulp-filter "gulp-filter")
筛选匹配到的文件，类似于中间件，自行看文档

## [gulp-header](https://www.npmjs.com/package/gulp-header "gulp-header")
这个非常有用，可以在文件中添加头部注释，包含任意想要的信息，通常就是文本更新时间、作者、描述、版本号、license等，比如：
```
function getHeader () {
    var template = ['/**',
    	' * time: <%= time%>',
    	' * site: <%= site%>',
    	' * contact: <%= blog%>',
        ' */',
        ''
    ].join('\n');
    return $.header(template, {
        time: new Date(),
        site: 'http://www.yeezan.com/',
        blog: 'http://donglegend.com/'
    });
}

gulp.task("uglify", function() {
	gulp.src(["./js/build/*.js"])
		.pipe($.uglify({
			compress: {
				drop_console: true,
				unused: true
			}
		}))
		.pipe(getHeader())
		.pipe(gulp.dest("./js/build/min/"));
});
```

## [run-sequence](https://www.npmjs.com/package/run-sequence "run-sequence")
同步按顺序执行gulp的task任务，亲测，类似的插件都是坑，不好用(可能我是用方式不对。。。),望观看[官网api http://www.gulpjs.com.cn/docs/api/](http://www.gulpjs.com.cn/docs/api/)
解决同步执行方法，用Makefile文件，执行命令
demo:
```
build:
	gulp clean
	gulp browserify
	gulp uglify
```

## [gulp-uglify](https://www.npmjs.com/package/gulp-uglify "gulp-uglify")
## [gulp-minify-html](https://www.npmjs.com/package/gulp-minify-html "gulp-minify-html") 
不解释

## [npm-check-updates](https://www.npmjs.com/package/npm-check-updates)
这款插件棒棒哒，主要用来更新package.json的依赖，时间长了，node会升级，项目依赖版本也会升级，用这个是个很不错的主意。

## 待完善......