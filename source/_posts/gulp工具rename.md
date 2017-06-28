---
title: gulp工具rename
date: 2016-12-08 21:40:21
categories: javascript
tags: gulp
---

gulp 对文件批量重命名
<!-- more -->
## [gulp-rename重命名](https://www.npmjs.com/package/gulp-rename)
```
var gulp = require('gulp');
var rename = require("gulp-rename");

// rename via function 
gulp.task('name', function() {
  return gulp.src('./dist/**/*')
    .pipe(rename(function(path) {
      console.log(path)
      var name = new Date().getTime();
      path.basename = name;
    }))
    .pipe(gulp.dest('rename/'))
})

// rename via hash 
gulp.task('name2', function() {
  return gulp.src('./dist/**/*')
    .pipe(rename({
	    dirname: "main", 
	    basename: "dong",
	    prefix: "", 
	    suffix: "", 
	    extname: ""
	}))
    .pipe(gulp.dest('rename/'))
})
```