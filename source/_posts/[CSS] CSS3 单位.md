---
title: CSS3 单位汇总
date: 2018-09-12 16:31:38
tags: [CSS3]
categories: 基础知识
toc: true
cover: http://pe7k320h3.bkt.clouddn.com/psb%20%281%29.jpeg
---

1. `px`：绝对单位，页面按精确像素展示。
2. `em`：相对单位，基准点为父节点字体的大小。
  如果自身定义了font-size按自身来计算（浏览器默认字体是16px），整个页面内1em不是一个固定的值。
3. `rem`：相对单位，可理解为”root em”, 相对根节点html的字体大小来计算，CSS3新加属性，chrome/firefox/IE9+支持。
  (另外需注意chrome强制最小字体为12号，即使设置成 10px 最终都会显示成 12px，当把html的font-size设置成10px,子节点rem的计算还是以12px为基准)。
4. `vw`：viewpoint width，视窗宽度，1vw等于视窗宽度的1%。
5. `vh`：viewpoint height，视窗高度，1vh等于视窗高度的1%。
6. `vmin`：vw和vh中较小的那个。
7. `vmax`：vw和vh中较大的那个。
  vw, vh, vmin, vmax：IE9+局部支持，chrome/firefox/safari/opera支持，ios safari 8+支持，android browser4.4+支持，chrome for android39支持
8. `ex`：取当前作用效果的字体的x的高度，在无法确定x高度的情况下以0.5em计算(IE11及以下均不支持 firefox/chrome/safari/opera/ios safari/android browser4.4+等均需属性加么有前缀)
9. `ch`:以节点所使用字体中的“0”字符为基准，找不到时为0.5em(ie10+,chrome31+,safair7.1+,opera26+,ios safari 7.1+,android browser4.4+支持)
