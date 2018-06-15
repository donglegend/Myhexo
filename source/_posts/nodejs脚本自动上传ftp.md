---
title: nodejs脚本自动上传ftp
date: 2018-06-14 09:15:31
categories: javascript
tags: ftp
---

我怀念的是一起做梦
<!-- more -->

## 起因
刚加入一个小组的项目开发，开发环境是基于node环境，通过webpack打包构建代码，然后上传sftp，在浏览器测试。这种开发模式无可厚非，但是每次修改源代码，然后build，然后upload，不胜其烦。之前项目中有过 gulp-sftp任务脚本，然而并不是生效。于是自力更生，另谋他法，搞一个自动上传sftp的服务脚本。

## 设想
因为基于webpack，所以直接启动webpack编译的watch监听即可，在watch回调里执行stfp的上传，上传去npm社区找一个sftp的客户端插件

## 实现
使用了插件[ssh2-sftp-client](https://www.npmjs.com/package/ssh2-sftp-client)，文档有使用说明和api

写书写了一个 sftp 模块，连接完，直接导出
```
const Client = require('ssh2-sftp-client');
const fs = require('fs');

const sftp = new Client();
sftp
  .connect({
    host: '0.0.0.0', // ftp服务器ip地址
    port: '22', // ftp服务器port
    username: 'yourname', // 你的登录用户名
    password: 'yourpass', // 你的密码
    privateKey: fs.readFileSync('/Users/yourname/.ssh/id_rsa'), // 私钥
    passphrase: 'yourpass', // 私钥密码
  })
  .then(() => {
    console.log('ftp文件服务器连接成功');
  })
  .catch(err => {
    console.log(err, 'catch error');
  });

module.exports = sftp;
```


然后在webpack的watch里进行 上传文件即可,关于上传文件，图片的等类型需要使用Buffer类型上传，做一个特殊处理
```
const path = require('path');
const fs = require('fs');
const yargs = require('yargs');
const webpack = require('webpack');
const webpackConfig = require('./webpack.prod.config');
const sftp = require('./sftp');

const user = yargs.argv.user || '';

console.log(user);

const staticFilesPath = {
  js: {
    local: path.resolve(__dirname, '../dist/js'),
    remote: `/upload_code/${user}/static/mobile/js/dist`,
  },
  css: {
    local: path.resolve(__dirname, '../dist/css'),
    remote: `/upload_code/${user}/static/mobile/css/`,
  },
  img: {
    local: path.resolve(__dirname, '../dist/images'),
    remote: `/upload_code/${user}/static/mobile/images/`,
  },
};

let isFirstBuild = true;

const compiler = webpack(webpackConfig);
const watching = compiler.watch(
  {
    ignored: /node_modules/,
    aggregateTimeout: 100,
    poll: 1000,
  },
  (err, stats) => {
    if (err || stats.hasErrors()) {
      console.log(err);
    }
    console.log('编译成功!');
    if (isFirstBuild) {
      isFirstBuild = false;
      return;
    }
    console.log('正在上传...');
    uploadFile()
      .then(() => {
        console.log('------所有文件上传完成!-------\n');
      })
      .catch(() => {
        console.log('------上传失败,请检查!-------\n');
      });
  }
);
/**
* 处理文件路径，循环所有文件，如果是图片需要读取成Buffer类型
**/
function handleFilePath(obj, type) {
  const { local, remote } = obj;
  const files = fs.readdirSync(local);
  return files.map(file => {
    const _lp = `${local}/${file}`;
    return {
      type: type,
      file: file,
      localPath: type !== 'img' ? _lp : fs.readFileSync(_lp),
      remotePath: `${remote}/${file}`,
    };
  });
}
/**
* 上传文件
**/
function uploadFile() {
  let files = [];

  Object.keys(staticFilesPath).forEach(key => {
    files = files.concat(handleFilePath(staticFilesPath[key], key));
  });

  const tasks = files.map(item => {
    return new Promise((resolve, reject) => {
      sftp
        .put(item.localPath, item.remotePath)
        .then(() => {
          console.log(`${item.file}上传完成`);
          resolve();
        })
        .catch(err => {
          console.log(`${item.file}上传失败`);
          reject();
        });
    });
  });

  return Promise.all(tasks);
}

```

## 注意点
- 连接sftp服务器，推荐使用 私钥文件连接，使用password出错可能性比较大
- 上传文件部分，目前不支持上传一个目录，所以需要循环处理文件
- 上传文件部分，容易出错，一定要保证远端服务器存在对应目录，目前插件没有自动创建目录的机制












