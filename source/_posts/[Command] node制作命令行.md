---
title: node制作命令行
date: 2018-08-25 10:55:51
tags: [node, commander]
categories: Command
toc: true
cover: http://odwjyz4z6.bkt.clouddn.com/1-131123121938-50.jpg
---

目标：利用自制的命令行实现输出自己的简历；增加常用的命令：ls、 ls -a、打开当前目录

#### 怎么制作命令行
1、配置基础框架
2、新建bin/qqResume.js目录，并如下:
``` js
  #!/usr/bin/env node
  // 被成为 shebang ，表示用后面的路径所示的程序来执行当前文件夹

  const fs = require('fs');
  const path = process.cwd();

  const run = (obj) => {
    if (obj[0] === '-v') {
      console.log('verson is 1.0.0.0');
    } else if (obj[0] === '-h'){
      console.log('Useage:');
      console.log('  -v --version [show version]');
    } else {
      fs.readdir(path, (err, files) => {
        if (err) {
          return console.log(err);
        }
        for(var i = 0; i < files.length; i += 1){
          console.log(files[i]);
        }
      })
    }
  }

  //获取除第一个命令以后的参数，使用空格拆分
  run(process.argv.slice(2));
```

运行可使用 node，在 package.json文件中配置 `"bin": { "qqResume": "bin/qqResume.js" }`;
3、全局运营命令
  ` sudo npm install . -g `
  ` npm link `
4、发布安装
发布到npm中，首先需要 npm、github 账号：npm publish
发布完成后可和平时的命令一样，全局安装: sudo npm i -g qqResume

#### commander
Commander 是一款重量轻，表现力和强大的命令行框架。提供了用户命令行输入和参数解析强大功能。
Commander的优势：自记录代码、自动生成帮助、合并短参数（“ABC”==“-A-B-C”）、默认选项、强制选项​​、命令解析、提示符

##### API
`Option()`: 初始化自定义参数对象，设置“关键字”和“描述” 
`Command()`: 初始化命令行参数对象，直接获得命令行输入 
`Command#command()`: 定义一个命令名字 
`Command#action()`: 注册一个callback函数 
`Command#option()`: 定义参数，需要设置“关键字”和“描述”，关键字包括“简写”和“全写”两部分，以”,”,”|”,”空格”做分隔。 
`Command#parse()`: 解析命令行参数argv 
`Command#description()`: 设置description值 
`Command#usage()`: 设置usage值
