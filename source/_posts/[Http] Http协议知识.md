---
title: Http协议知识
date: 2018-10-12 15:44:41
tags: [HTTP]
categories: 基础知识
toc: true
---

## http协议的请求报文组成格式
HTTP请求报文组成：请求行（＜request-line＞）、请求头部（＜headers＞）、空行（＜blank line＞）和请求数据（＜request-body＞）。

#### 1. 请求行
请求行组成：请求方法、URL和HTTP协议版本字段组成，它们用空格分隔。
例如，GET /index.html HTTP/1.1。
<font color="#dd0000">HTTP协议的请求方法有GET、POST、HEAD、PUT、DELETE、OPTIONS、TRACE、CONNECT。</font>

#### 2.请求头部 
请求头部通知服务器有关于客户端请求的信息：
`User-Agent`：产生请求的浏览器类型。
`Accept`：客户端可识别的内容类型列表。
`Host`：请求的主机名，允许多个域名同处一个IP地址，即虚拟主机。

#### 3.请求数据
<font color="#dd0000">请求数据不在GET方法中使用，而是在POST方法中使用。</font>
与请求数据相关的最常使用的请求头是Content-Type和Content-Length。

## http协议的响应报文组成格式
HTTP响应也组成：状态行（＜status-line＞）、消息报头（＜headers＞）、响应正文（＜response-body＞）。

#### 1.状态行
状态行（status line）通过提供一个状态码来说明所请求的资源情况。

状态行格式：服务器HTTP协议的版本（HTTP-Version） 服务器发回的响应状态代码（Status-Code） 状态代码的文本描述（Reason-Phrase） CRLF
状态代码：
- 1xx：指示信息--表示请求已接收，继续处理。
- 2xx：成功--表示请求已被成功接收、理解、接受。
- 3xx：重定向--要完成请求必须进行更进一步的操作。
- 4xx：客户端错误--请求有语法错误或请求无法实现。
- 5xx：服务器端错误--服务器未能实现合法的请求。

- 200 OK：客户端请求成功。
- 400 Bad Request：客户端请求有语法错误，不能被服务器所理解。
- 401 Unauthorized：请求未经授权，这个状态代码必须和WWW-Authenticate报头域一起使用。
- 403 Forbidden：服务器收到请求，但是拒绝提供服务。
- 404 Not Found：请求资源不存在，举个例子：输入了错误的URL。
- 500 Internal Server  Error：服务器发生不可预期的错误。
- 503 Server Unavailable：服务器当前不能处理客户端的请求，一段时间后可能恢复正常，举个例子：HTTP/1.1 200 OK（CRLF）。

## 状态码302与301的区别
302重定向表示临时性转移(Temporarily Moved )，当一个网页URL需要短期变化时使用。
301重定向/跳转一般，表示本网页永久性转移到另一个地址。
301是永久性转移(Permanently Moved), SEO常用的招式，会把旧页面的PR等信息转移到新页面
301重定向是永久的重定向，搜索引擎在抓取新内容的同时也将旧的网址替换为重定向之后的网址。
302重定向是临时的重定向，搜索引擎会抓取新的内容而保留旧的网址。因为服务器返回302代码，搜索引擎认为新的网址只是暂时的。