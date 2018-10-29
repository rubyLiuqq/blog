---
title: 客户端存储技术.md
date: 2018-10-29 16:45:25
tags: [JS]
categories: 基础知识
toc: true
---

## 常用的客户端存储方法有哪些？
`cookie`：是客户端用来存储数据的一种选项，它既可以在客户端设置也可以在服务器端设置。cookie会跟随任意HTTP请求一起发送。是「浏览器」提供的一种机制。
`sessionStorage`：严格用于一个浏览器会话中存储数据，因为数据在浏览器关闭后会立即删除
`localStorage`：用于跨会话持久化地存储数据
`indexedDB`：在浏览器中保存结构化数据的一种「数据库」
cookie：兼容性好，可以通过`document.cookie`访问它
缺点：1、增加了网络流量；2、它的数据容量有限，最多只能存储4KB的数据，浏览器之间各有不同；3、不安全。
SessionStorage、LocalStorage缺点：IE不支持 SessionStorage，低版本IE ( IE6, IE7 ) 不支持 LocalStorage，并且不支持查询语言
indexedDB：兼容性不好，未得到大部分浏览器的支持

## cookie、sessionStorage和localStorage的区别？
1. 存储时效：
cookie可以手动设置失效期，默认为会话级
sessionStorage的存储时长是会话级
localStorage的存储时长是永久，除非用户手动利用浏览器的工具删除

2. 访问的局限性：
cookie可以设置路径path，所以它要比另外两个多了一层访问限制
localStorage和sessionStorage的访问限制是文档源级别，即协议、主机名和端口
cookie可以通过设置domain属性值，可以在不同二级域名下共享cookie，而Storage不可以，比如http://image.baidu.com的cookie http://map.baidu.com是可以访问的，前提是cookie的domain设置为http://baidu.com，而Storage是不可以的

3. 存储大小限制：
cookie适合存储少量数据，他的大小限制是个数进行限制，每个浏览器的限制数量不同
Storage可以存储数据的量较大，此外它是通过占用空间大小来做限制的，每个浏览器的实现也是不同的

4. 操作方法：
cookie是作为document的属性存在，并没有提供标准的方法来直接操作cookie
Storage提供了setItem()和getItem()还有removeItem()方法，操作方便不易出错

5. 其他：
cookie在发送http请求时，会将本地的cookie作为http头部信息传递给服务器
cookie可以由服务器通过http来设定

## cookie由哪些部分组成？
一般有以下几部分组成:
`set cookie: name=value; domain=.mozilla.org; expires=Feb, 13-Mar-2018 11:47:50; path=/; secure`
1、`name名称`：一个唯一确定cookie的名称，部分大小写，cookie的名字必须是经过URL编码的，一般可以采用某个前缀在加上当前时间的做法，这样的话名称能够确保是唯一的，也比较方便。
2、`value值`：存储在cookie中的字符串值，必须经过被URL编码
3、`domain域`：对于哪个域是有效的，如果没有设置的话，默认来自设置cookie的那个域，在上诉例子中就是.Mozilla.org
4、`expires失效时间`：表示cookie何时应该被删除的时间戳，这个日期是GMT格式的日期，如果设置是以前的时间，cookie会被立刻删除。上诉cookie的失效时间是Feb,13-Mar-2018 11:47:50。
5、`path路径`：指定域中的那个路径，应该想服务器发送cookie，/ 表示没有限制
6、`secure安全标志`：指定以后，cookie只有在使用SSL连接的时候才可以发送到服务器。