---
title: git commit 规范
date: 2018-07-19 09:55:12
tags: [git, 代码规范]
mathjax: true
toc: true
categories: 编码规范
---

&emsp;&emsp;前段时间组内规范了工作流，设定了commit message 规范，开始时甚是痛苦，但是习惯了发现好处颇多。现在社区中有很多 Commit message的写法规范，个人认为 **Angular 规范** 是目前使用最广的写法。

### commit message 的作用
* 提供更多的历史信息，方便快速浏览。
* 可以过滤某些commit（比如文档改动），便于快速查找信息。
* 可以直接从commit生成`Change log`。
* 可读性好，清晰，不必深入看代码即可了解当前commit的作用。
* 为 Code Reviewing做准备
* 方便跟踪工程历史
* 让其他的开发者在运行 git blame 的时候想跪谢
* 提高项目的整体质量，提高个人工程素质

---------

### commit message 的格式
&emsp;&emsp;Commit message 包括三个部分：header(必需)、body(可省)和footer(可省).
任何一行都不得超过72个字符（或100个字符），这是为了避免自动换行影响美观。

#### Header
Header 部分只有一行，包括三个字段 `type`（必需）、`scope`（可选）和`subject`（必需）。
> ** type ：用于说明 commit 的类别，只允许使用下面7个标识。**
* feat：新功能（feature）
* fix：修补bug
* docs：文档（documentation）
* style： 格式（不影响代码运行的变动）
* refactor：重构（即不是新增功能，也不是修改bug的代码变动）
* test：增加测试
* chore：构建过程或辅助工具的变动

一般情况下，type为feat和fix，则该 commit 将肯定出现在 Change log 之中，其他的自己可以设置。

> ** scope: 用于说明 commit 影响的范围，比如数据层、控制层、视图层等等。** 
例如在Angular，可以是`$location`, `$browser`, `$compile`, `$rootScope`, `ngHref`, `ngClick`, `ngView`等。

> ** subject: 是 commit 目的的简短描述，开头需要使用第一人称现在时的动词开头。**

#### Body
Body 部分是对本次 commit 的详细描述，可以分成多行
1、使用第一人称现在时，比如使用change而不是changed或changes。
2、永远别忘了第2行是空行。
3、应该说明代码变动的动机，以及与以前行为的对比。

#### Footer
Footer 部分只用于以下两种情况：
1、不兼容变动
&emsp;&emsp;&emsp;&emsp;若当前代码与上一个版本不兼容，则 Footer 部分以BREAKING CHANGE开头，后面是对变动的描述、以及变动理由和迁移方法。
``` js
BREAKING CHANGE: isolate scope bindings definition has changed.
  To migrate the code follow the example below:

  Before:
  scope: {
    myAttr: 'attribute',
  }

  After:
  scope: {
    myAttr: '@',
  }
The removed `inject` wasn't generaly useful for directives so there should be no code using it.
```
2、关闭issue
&emsp;&emsp;&emsp;&emsp;若当前 commit 针对某个issue，那么可以在 Footer 部分关闭这个 issue 。
`Closes #234`

---------
### 补充 Revrt
若当前 commit 用于撤销以前的 commit，则必须以revert:开头，后面跟着被撤销 Commit 的 Header。
```
revert: feat(pencil): add 'graphiteWidth' option
This reverts commit 667ecc1654a317a13331b17617d973392f415f02.
```
Body部分的格式是固定的，必须写成`This reverts commit &lt;hash>`，其中的hash是被撤销 commit 的 SHA 标识符。
若当前 commit 与被撤销的 commit，在同一个发布（release）里面，那么它们都不会出现在 Change log 里面。如果两者在不同的发布，那么当前 commit，会出现在 Change log 的Reverts小标题下面。

---------
### Commitizen
可以使用典型的git工作流程或通过使用CLI向导Commitizen来添加提交消息格式。

** 安装 ** `npm i -g commitizen`
然后，在项目目录里，运行下面的命令，使其支持 Angular 的 Commit message 格式。
`commitizen init cz-conventional-changelog --save --save-exact`
以后，凡是用到git commit命令，一律改为使用git cz。这时，就会出现选项，用来生成符合格式的 Commit message。

![git commit](https://raw.githubusercontent.com/commitizen/cz-cli/master/meta/screenshots/add-commit.png)

---------
### validate-commit-msg
validate-commit-msg 用于检查项目的 Commit message 是否符合Angular规范。
该包提供了使用githooks来校验commit message的一些二进制文件。推荐使用husky，只需要添加 "commitmsg": "validate-commit-msg" 到package.json中的nam scripts即可.

当然，还可以通过定义配置文件.vcmrc来自定义校验格式。详细使用请见文档 [validate-commit-msg](https://github.com/conventional-changelog-archived-repos/validate-commit-msg)

---------
### 生成Change log
如果commit都符合规则，则发布新版本时，Change log 就可以用脚本自动生成。
生成的文档主要包括：New feature、Bug fixed、Breaking changes。
每个部分都会罗列相关的 commit ，并且有指向这些 commit 的链接。

![change log](https://sfault-image.b0.upaiyun.com/268/595/2685952301-58eee4f6f1747)

[conventional-changelog](https://github.com/conventional-changelog/conventional-changelog) 就是生成 Change log 的工具，运行下面的命令即可。
``` linux
$ npm install -g conventional-changelog
$ cd my-project
$ conventional-changelog -p angular -i CHANGELOG.md -w
```
