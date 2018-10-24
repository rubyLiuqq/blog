---
title: 学习cli
date: 2018-07-18 18:08:12
tags: [cli, webpack]
categories: Command
toc: true
cover: http://pe7k320h3.bkt.clouddn.com/psb%20%287%29.jpeg
---

&emsp;在这之前一直想做一个cli的脚手架，近期公司团队中有一位大牛开发完成，想自己学习，做了这篇学习记录，他的cli工具支持ValillaJS、VueJS、React、Angular等四大主流框架，我就学习Vue的即可，等之后有了自己的需求，自己再加。
&emsp;Cli有三个模块组成：初始化、开发和构建

**co、co-prompt的方法类似于 inquirer**

1、项目的初始化目录
> .gitignore
> .editorconfig
> index.js
> LINCESE
> package.json
> README.md

2、安装eslint以及配置
`lint`、`eslintConfig`
  在package.json中对 lint和eslint进行配置，对eslint中的rules进行约束
  comma-dangle、no-console等
3、利用husky设置git hooks
`precommit`
  结合2 中的lint 规范代码
4、利用commitlint lint commit 信息
`commitmsg`、`commitlint`
  提交信息的lint 规范，可参考 git commit 规范
5、增加基础的cli信息，commander的使用
`init|server|build command` ->  bin/cli.js
  commander 命令行自制 init | server | build
6、i18n（国际化）的支持
  增加i18n 英中文 对init|server|build 的说明，并修改bin/cli.js 的描述
7、在lib中增加 logger.js
  增加 logger util，用于 commander 命令的处理并作出不同的运行结果显示，比如：安装完毕，服务开启完毕，报错提示等。


补充：
1、chalk 是一个颜色的插件
``` js
const chalk = require('chalk');
// Use RGB colors in terminal emulators that support it.
console.log(chalk.blue('Hello') + 'World' + chalk.red('!'));
console.log(chalk.keyword('orange')('Yay for orange colored text!'));
console.log(chalk.rgb(123, 45, 67).underline('Underlined reddish color'));
console.log(chalk.hex('#DEADED').bold('Bold gray!'));
```
2、readline 处理日志文本很方便，逐行读取
  可参考[node中文网](http://nodejs.cn/api/readline.html)查看API
  Node.js里实现标准输入输出的封装好的模块，通过这个模块我们可以以逐行的方式读取数据流。

3、nodejs的知识点：
process.cwd() 是当前执行node命令时候的文件夹地址 ——工作目录（当前Node.js进程执行时的工作目录）
__dirname 是被执行的js 文件的地址 ——文件所在目录（当前模块的目录名）
  node.js进程当前工作的目录有可能不是该文件所在目录的完整目录
__filename 获取当前模块文件的带有完整绝对路径的文件名

eg:
`process.cwd()   /Users/meizu/myfile/programFile/cli-learn/lib`
`__dirname       /Users/meizu/myfile/programFile/cli-learn/lib`
`__filename      /Users/meizu/myfile/programFile/cli-learn/lib/config.js`

4、node child_process 子进程

4、rimraf：以包的形式包装rm -rf命令，用来删除文件和文件夹的，不管文件夹是否为空，都可删除。
5、extend： 


