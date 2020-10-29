---
layout: post
title:  "XMLHttpRequest请求不带Cookie"
categories: Http
tags:  Http
author: DuGuYu
---
今天同事请求后端接口，需要登陆验证cokkie发现总是没传过来，而直接在浏览器上敲地址就带着Cookie,于是我看了下他写的代码发现是用-XMLHttpRequest进行请求提交的











> **XMLHttpRequest.withCredentials**  属性是一个[`Boolean`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Boolean)类型，它指示了是否该使用类似cookies,authorization headers(头部授权)或者TLS客户端证书这一类资格证书来创建一个跨站点访问控制（cross-site `Access-Control`）请求。在同一个站点下使用`withCredentials属性是无效的。`
>
> `此外，这个指示`也会被用做`响应中`cookies 被忽视的标示。默认值是false。
>
> 如果在发送来自其他域的XMLHttpRequest请求之前，未设置`withCredentials` 为true，那么就不能为它自己的域设置cookie值。而通过设置`withCredentials` 为true获得的第三方cookies，将会依旧享受同源策略，因此不能被通过[document.cookie](https://developer.mozilla.org/en-US/docs/Web/API/Document/cookie)或者从头部相应请求的脚本等访问。
>
> **注:** 永远不会影响到同源请求
>
> **Note:** 不同域下的`XmlHttpRequest` 响应，不论其`Access-Control-` header 设置什么值，都无法为它自身站点设置cookie值，除非它在请求之前将`withCredentials` 设为true。

