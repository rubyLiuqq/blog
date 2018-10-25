---
title: 小细节小技巧
date: 2018-08-08 17:59:12
tags: [CSS]
toc: true
categories: 基础知识
cover: http://pe7k320h3.bkt.clouddn.com/psb%20%281%29.jpeg
---

[1、overflow的奇葩事件](#1、overflow的奇葩事件)
[2、BFC块级格式化上下文](#2、BFC块级格式化上下文)
[3、font-size](#3、font-size)
[4、position:sticky](#4、position:sticky)
[5、清除浮动](#5、清除浮动)
&emsp;&emsp;这篇文章专门用于记录在开发过程中遇到的奇葩现象，平时经常更新，用于自己的一个记录。

### 1、overflow的奇葩事件
&emsp;&emsp;背景：这次遇到一个很奇怪的样子，其实应用的场景很简单，就是一个轮播，采用hidden处理周边的待滚动的li，但又接到hover上有动画的需求，此时设置overflow-x: hidden; overflow-y: visible;不能实现。没办法，看到网上好多人说增加父级，并对父级分别设置overflow来处理，不靠谱，实现不了。
&emsp;&emsp;原因：这块和overflow非visible值会使一个块级元素形成一个BFC（块级格式化上下文）有关。overflow-x和overflow-y的计算值跟给定的值相同，除了某些跟’visible’值的不合理组合：如果一个其中一个属性的值被赋为’visible’，而另一个被赋值为’scroll’或’auto’，那么’visible’会被重置为’auto’。overflow的计算值与overflow-x相等（如果overflow-y相同的话）；否则就是一对overflow-x和overflow-y的计算值。 其实另一个值设置为hidden的时候，visible也会被重置为auto。
&emsp;&emsp;处理方法：增加外围父级的高度，用于hover时的阴影用，然后margin点位置，用于hover时的上移。其他依旧正常设置，只是不让动画局限在撑起的高度内。

### 2、BFC块级格式化上下文

### 3、font-size
&emsp;&emsp;很简单的div模型，设置宽高后，同样可以利用font-size来扩大div的宽度，其原因可由 line-height 解释

### 4、position:sticky
&emsp;&emsp;sticky原意为黏糊的，但在css中更多的表现为吸顶、吸底(移动端网站的头部返回栏,底部切换栏之类)的效果用这个属性非常适合。
导航的效果像是在页面打开时是 relative 的,向下滑动的时候 fixed 并且 top:0 为零。

position:sticky可以认为是 position:relative 和 position:fixed 两种定位功能于一体的特殊定位。
``` css 
.sticky { 
  position: sticky; 
  position: -webkit-sticky; 
  top: 0; 
} 
```
**注意：可以放心在ios上使用，可以使用polyfill进行处理**
**注意坑**
<!-- 注意 -->
* 1、sticky 不会触发 BFC。
* 2、z-index 无效。
* 3、sticky 是容器相关的,也就说 sticky 的特性只会在他所处的容器里生效。碰到 body 设置 height:100% 的时候 sticky 元素停在某一个位置不动了。

### 5、清除浮动
方法一：
```css
/* 在容器内添加一个CSS伪元素， */
.clear::before {
  content: '';
  clear: both;
}
```

方法二：清除浮动的另一个方法是在容器内创建BFC，一个BFC元素完全包裹住了它内部的所有元素，包括内部的浮动元素，保证浮动元素不会超出其底部。
清除浮动的方式是为元素设置除visible（默认）之外的overflow属性值。
```css
.container {
  overflow: auto;
}
```
然而，在某些情况下，这可能会带来一些阴影的截断或是非预期的滚动条。同时它也使你的CSS变得不那么直观.

方法三：`display: flow-root`
```css
.container {
  display: flow-root;
}
```
`display: flow-root`做的唯一的一件事就是去创建一个BFC，因此可以避免其他创建BFC方法带来的问题。

### 6、css容易不注意的
#### 块元素和行内元素
块元素：会独自占据一整行或多整行，可以对其设置宽高
<font color="d00">`<h1>~<h6>、<p>、<div>、<ul>、<li>、<ol>`</font>
行内元素：靠自身的字体大小和版本尺寸来支撑结构
<font color="d00">`<strong>、<b>、<em>、<i>、<del>、<s>、<ins>、<u>、<a>、<span>`</font>

#### 选择优先级
行内 > id > 类 > 标签 > 通配符选择器 > 继承样式 > 浏览器默认的样式