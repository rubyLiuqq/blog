---
title: flex遇到的那些坑
date: 2018-07-17 22:26:08
tags: [css3]
categories: 基础知识
toc: true
cover: http://pe7k320h3.bkt.clouddn.com/psb%20%288%29.jpeg
---

&emsp;在一次H5的项目开发中，由于是典型的左中右的布局，就使用了flex，但是遇到了一些坑，在记录之前，先简单的说下flex吧，想兼容PC IE10之前的，就不要用了，还是老实的使用float吧。

### flex基础
&emsp; flex布局主要设计12个属性（父容器和子容器各6个），但实际常用的只有4个（各2个）

##### 作用于父容器的属性：
`flex-direction`：决定主轴的方向
`flex-wrap`：换行的方式
`flex-flow`：为flex-direction和flex-wrap的简写形式，默认为：row nowrap
`just-content`：在主轴上的对齐方式（eg 水平）
`align-items`：在交叉轴上的对齐方式（eg 垂直）
`align-content`：多根轴线的对齐方式，若只有一根轴线，不起作用

##### 作用于子容器的属性：
`order`：排列顺序，数值越小，排列越靠前
`flex-grow`：放大比例，默认为0，不放大
&emsp;&emsp;&emsp;&emsp;&emsp;若所有项目该值都为1，表示等分
&emsp;&emsp;&emsp;&emsp;&emsp;若有一个是2，其他的都为1 ，前者占据的剩余空间将比其他项多一倍
`flex-shrink`：缩小比例，默认为1 ，即空间不足，项目将缩小
&emsp;&emsp;&emsp;&emsp;&emsp;若所有项目该值为1 ，当空间不足时，将等比例缩小
&emsp;&emsp;&emsp;&emsp;&emsp;若有一个是0，其他的都为1，则空间不足时，前者不缩小
`flex-basis`：在分配多余空间之前，项目占据的主轴空间
`flex`：flex-grow, flex-shrink 和 flex-basis的缩写，有auto(1 1 auto)和none(0 0 auto)快捷键
`align-self`：允许单个项目有与其他小哀公母不一样的对齐方式，可覆盖align-items属性

### 遇坑计
#### 设置了flex-grow元素的子级宽度问题



基础总结：
![flex](https://juejin.im/post/58e3a5a0a0bb9f0069fc16bb)
