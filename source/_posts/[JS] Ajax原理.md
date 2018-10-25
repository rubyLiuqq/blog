---
title: Ajax 原理
date: 2018-09-30 17:20:31
tags: [JS]
categories: 基础知识
toc: true
---

http://jartto.wang/2015/09/01/chattered-about-ajax/

http://jartto.wang/2015/10/01/advantage-and-disadvantages-of-Ajax/

Ajax函数的封装
```js
function ajax(url, fnSucc, fnFailed) {
  const oAjax;
  // 1、建立Ajax对象
  if (window.XMLHTTPRequest) {
    oAjax = new XMLHTTPRequest();
  } else {
    oAjax = new ActiveXObjext('Micosoft.XMLHTTP');
  }
  // 2、连接服务器（打开和连接服务器）
  oAjax.open('GET', url, true);
  // 3、发送
  oAjax.send();
  // 4、接收
  oAjax.onreadyStateChange = function() {
    if (oAjax.readyState === 4) {
      if (oAjax.status === 200) {
        // fnSucc
        fnSucc(oAjax.requesText);
      } else {
        fnSucc(oAjax.status);
      }
    }
  }
}
```