---
title: 正则表达式
date: 2018-09-29 16:20:42
tags: [JS]
categories: 基础知识
toc: true
---

正则表达式是匹配模式，要么匹配字符，要么匹配位置。

## 正则表达式字符匹配攻略
1. 两种模糊匹配：横向模糊和纵向模糊
横向模糊指的是，一个正则可匹配的字符串的长度不是固定的。
纵向模糊指的是，一个正则匹配的字符串，具体到某一位字符时，它可以不是某个确定的字符。

2. 字符组
`[123456abcdefGHIJKLM] 可以写成 [1-6a-fG-M]`
可以写成如下的方式：`[-az]`或`[az-]`或`[a\-z]`, 即要么放在开头，要么放在结尾，要么转义

排除字符组：例如`[^abc]`，表示是一个除"a"、"b"、"c"之外的任意一个字符。字符组的第一位放`^（脱字符）`，表示求反。

> - `\d`就是[0-9]。表示是一位数字。记忆方式：其英文是digit（数字）
> - `\D`就是[^0-9]。表示除数字外的任意字符。\w就是[0-9a-zA-Z_]。表示数字、大小写字母和下划线。
> - `\W`是[^0-9a-zA-Z_]。非单词字符。记忆方式：w是word的简写，也称单词字符。
> - `\s`是[ \t\v\n\r\f]。表示空白符，包括空格、水平制表符、垂直制表符、换行符、回车符、换页符。记忆方式：s是space character的首字母。
> - `\S`是[^ \t\v\n\r\f]。 非空白符。
> - `.`就是[^\n\r\u2028\u2029]。通配符，表示几乎任意字符。换行符、回车符、行分隔符和段分隔符除外。记忆方式：想想省略号...中的每个点，都可以理解成占位符，表示任何类似的东西。

3. 量词：

> - `{m,}` 表示至少出现m次。
> - `{m}` 等价于{m,m}，表示出现m次。
> - `?` 等价于{0,1}，表示出现或者不出现。记忆方式：问号的意思表示，有吗？
> - `+` 等价于{1,}，表示出现至少一次。记忆方式：加号是追加的意思，得先有一个，然后才考虑追加。
> - `*` 等价于{0,}，表示出现任意次，有可能不出现。记忆方式：看看天上的星星，可能一颗没有，可能零散有几颗，可能数也数不过来。

**贪婪匹配**: 尽可能多的匹配
**惰性匹配**: 尽可能少的匹配

4. 多选分支
`(p1|p2|p3)`，其中p1、p2和p3是子模式，用|（管道符）分隔，表示其中任何之一。

5. 案例
- [x] 匹配时间（24小时制）：`/^(0?[0-9]|1[0-9]|[2][0-3]):(0?[0-9]|[1-5][0-9])$/`
- [x] 匹配日期（yyyy-mm-dd格式）： `/^[0-9]{4}-(0[1-9]|1[0-2])-(0[1-9]|[12][0-9]|3[01])$/`
- [x] window操作系统文件路径：`/^[a-zA-Z]:\\([^\\:*<>|"?\r\n/]+\\)*([^\\:*<>|"?\r\n/]+)?$/`
eg：盘符:\文件夹\文件夹\文件夹\
F:\study\javascript\regex\regular expression.pdf
F:\study\javascript\regex\
F:\study\javascript
F:\

文件名或者文件夹名，不能包含一些特殊字符，排除字符组`[^\\:*<>|"?\r\n/]`来表示合法字符
路径的最后一部分可以是“文件夹”，没有\，因此需要添加`([^\\:*<>|"?\r\n/]+)?`

## 正则表达式位置匹配攻略
> - `^`（脱字符）匹配开头，在多行匹配中匹配行开头。
> - `$`（美元符号）匹配结尾，在多行匹配中匹配行结尾。
> - `\b` 是单词边界，具体就是\w和\W之间的位置，也包括\w和^之间的位置，也包括\w和$之间的位置。\w是字符组[0-9a-zA-Z_]的简写形式，
> - `\B` 是\b的反面的意思，非单词边界
> - `(?=p)` 其中p是一个子模式，即p前面的位置。eg：(?=l)，表示'l'字符前面的位置。（positive lookahead）
> - `(?!p)` 是(?=p)的反面意思。（negative lookahead）

1. 案例
- [x] 不匹配任何东西的正则: `/.^/`
- [x] 数字的千位分隔符表示法: "12,345,678" 正则为：`/(?!^)(?=(\d{3})+$)/g`
"12345678  123456789" 替换成"12,345,678  123,456,789"  正则为: `/\B(?=(\d{3})+\b)/g`，其中 `(?!\b) 为 \B`
- [x] 必须包含数字: `(?=.*[0-9])`
- [x] 同时包含数字和小写字母:  `(?=.*[0-9])(?=.*[a-z])`
- [x] 密码长度6-12位，由数字、小写字符和大写字母组成，但必须至少包括2种字符。
` /((?=.*[0-9])(?=.*[a-z])|(?=.*[0-9])(?=.*[A-Z])|(?=.*[a-z])(?=.*[A-Z]))^[0-9A-Za-z]{6,12}$/`

## 正则表达式括号的作用

- [x] "2017-06-12".replace(/(\d{4})-(\d{2})-(\d{2})/, "$2/$3/$1"); // "06/12/2017"
- [x] 匹配"2016-06-12"、"2016/06/12"、"2016.06.12" 正则为: `/\d{4}(-|\/|\.)\d{2}\1\d{2}/`
**注意里面的\1，表示的引用之前的那个分组`(-|\/|\.)`。不管它匹配到什么（比如-），\1都匹配那个同样的具体某个字符。**


1. 括号嵌套
2. `\10`表示第10个分组
```js
var regex = /(1)(2)(3)(4)(5)(6)(7)(8)(9)(#) \10+/;
var string = "123456789# ######"
console.log( regex.test(string) );  // => true 
```

3. 引用不存在的分组
因为反向引用，是引用前面的分组，但我们在正则里引用了不存在的分组时，此时正则不会报错，只是匹配反向引用的字符本身。
- [x] 字符串trim方法模拟
```js
function trim(str) {
	return str.replace(/^\s+|\s+$/g, '');
}
console.log( trim("  foobar   ") );   // => "foobar"
```
- [x] 将每个单词的首字母转换为大写
```js
function titleize(str) {
	return str.toLowerCase().replace(/(?:^|\s)\w/g, function(c) {
		return c.toUpperCase();
	});
}
console.log( titleize('my name is epeli') );  // => "My Name Is Epeli"
```

- [x] 驼峰化
```js
function camelize(str) {
	return str.replace(/[-_\s]+(.)?/g, function(match, c) {
		return c ? c.toUpperCase() : '';
	});
}
console.log( camelize('-moz-transform') );  // => "MozTransform"
```

- [x] 匹配成对标签  `/<([^>]+)>[\d\D]*<\/\1>/`
开标签: `<[^>]+>`, 闭标签: `<\/[^>]+>`
`[\d\D]`的意思是，这个字符是数字或者不是数字，因此，也就是匹配任意字符的意思。

## 正则表达式回溯法原理
## 正则表达式的拆分
## 正则表达式的构建
## 正则表达式编程

# 正则表达式的一些规则
RegExp对象的方法：
- * test：在字符串中测试模式匹配,返回 true 或 false
- * exec：在字符串中执行匹配搜索,返回结果数组
- * match(pattern)：返回 pattern 中的子串或 null
- * replace(pattern, replacement)：用 replacement 替换 pattern
- * search(pattern)：返回字符串中 pattern 开始位置
- * split(pattern)：返回字符串按指定 pattern 拆分的数组