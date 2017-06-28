---
title: gulp图片压缩
date: 2016-12-08 21:29:57
categories: javascript
tags: gulp
---
网页性能优化，通常要处理图片，尤其图片量大的时候，更需要工具来批量处理，这里使用gulp。
<!-- more -->
## [image-resize压缩尺寸](https://www.npmjs.com/package/gulp-image-resize)
```
var gulp = require('gulp');
var imageResize = require('gulp-image-resize');

gulp.task('resize', function() {
  return gulp.src('./images/**/*')
    .pipe(imageResize({
      width: 400
    }))
    .pipe(gulp.dest('dist/resize'));
});
```

## [gulp-imagemin品质压缩](https://www.npmjs.com/package/imagemin-pngquant)
```
var gulp = require('gulp');
var imagemin = require('gulp-imagemin');
var pngquant = require('imagemin-pngquant');
gulp.task('min', function() {
  return gulp.src('./images/**/*')
    .pipe(imagemin({
      progressive: true,
      use: [pngquant({
        quality: '65-80'
      })]
    }))
    .pipe(gulp.dest('dist/min'));
})
```