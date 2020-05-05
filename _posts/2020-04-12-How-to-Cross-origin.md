---
layout: post
title:  How to Cross-origin
date:   2020-04-16 00:30:00 +0800
categories: [web]
---

# Same-origin Policy

![Cache meme logo](/img/posts/cross-origin/cross-origin-sky.png)

## 1. 什么是同源

同源策略是一个重要的安全策略，它用于限制一个origin的文档或者它加载的脚本如何能与另一个源的资源进行交互。它能帮助阻隔恶意文档，减少可能被攻击的媒介。

> 浏览器安全的基石是"同源政策"（same-origin policy）

### 1.1 为什么有同源策略?

图 TODO 读取Cookie发送请求到iwencai.com

### 1.2 同源定义

如果两个 URL 的 protocol、port 和 host 都相同的话，则这两个 URL 是同源。

> https://www.iwencai.com/a/index.html

| URL  | 结果 |
| ------------- | ------------- |
| https://www.iwencai.com/b/index.html | 同源 |
| https://www.iwencai.com/a/index1.html  | 同源 |
| http://www.iwencai.com/a/index1.html  | 不同源 |
| http://www.iwencai.com:81/a/index1.html | 不同源 |
|https://ai.iwencai.com/a/index.html | 不同源 


## 2. 跨源网络访问

同源策略控制不同源之间的交互，例如在使用XMLHttpRequest 或 <img> 标签时则会受到同源策略的约束。这些交互通常分为三类：

* 跨域写操作（Cross-origin writes）一般是被允许的。例如链接（links），重定向以及表单提交。特定少数的HTTP请求需要添加 preflight。
* 跨域资源嵌入（Cross-origin embedding）一般是被允许。
* 跨域读操作（Cross-origin reads）一般是不被允许的，但常可以通过内嵌资源来巧妙的进行读取访问。例如，你可以读取嵌入图片的高度和宽度，调用内嵌脚本的方法，或availability of an embedded resource.

比如:

| 类型 | 结果 |
| ------------- | ------------- |
| iframes | iframe的跨源embedding通常是允许的，但是跨域的读操作是不行的 |
| CSS | 可以通过 `<link rel="stylesheet" href="...">`embedding，CSS跨域需要设置正确的Http头部 Content-Type |
| forms | 跨域的URL可以作为 form的action属性，可以提交表单到跨域的地址 |
| images | 图片的跨域embedding是允许的，但是读取跨域的图片（在canvas中使用Javascript加载跨域的图片） |
| multimedia | `<video>` 和 `<audio>` 播放的多媒体资源可以跨域embedding |
| script | 跨域的`<script src="..."></script>`是允许的，但使用一些的API会被禁止 |

### 2.1 如何允许跨源访问



* 非简单请求


### 2.2 如何阻止跨源访问

CSRF(Cross-site request forgery) 跨站请求伪造

![https://www.imperva.com/learn/application-security/csrf-cross-site-request-forgery/](/img/posts/cross-origin/csrf-cross-site-request-forgery.png)


### 2. 脚本API访问

JavaScript 的 API 中，如 iframe.contentWindow、 window.parent、window.open 和 window.opener 允许文档间直接相互引用。当两个文档的源不同时，这些引用方式将对 Window 和 Location对象的访问添加限制，如下两节所述。

为了能让不同源中文档进行交流，可以使用 window.postMessage。

### 3. 跨源数据存储访问

#### LocalStorage 和 IndexedDB

#### Cookie

##### 什么是Cookie?

Cookie 就是访问者在访问网站后留下的一个信息片段。

> Cookie 用于个性化用户的体验。可能包含用户在访问网站时的参数或输入。用户可以自定义浏览器来接受，拒绝或删除 Cookie。
>
> Cookie 可以在服务器级别设置和修改，使用 Set-Cookie HTTP header, 或者在 JavaScript 中用 document.cookie.

##### Cookie 同源

只有同源的网页才能共享。但是，两个网页一级域名相同，只是二级域名不同，浏览器允许通过设置document.domain共享 Cookie。

举例来说，A网页是http://ai.iwencai.com/a.html，B网页是http://www.iwencai.com/b.html，那么只要设置相同的document.domain，两个网页就可以共享Cookie。

> document.domain = 'example.com';

现在，A网页通过脚本设置一个 Cookie。

> document.cookie = "myCookie=fromAI;path=/;domain=ai.iwencai.com";

B网页就可以读到这个 Cookie。

> var allCookie = document.cookie;

服务端也可以在设置Cookie的时候, 指定Cookie的所属域名为一级域名, 比如.example.com。

> Set-Cookie: key=value; domain=.example.com; path=/


## 3. 跨域资源共享CORS

跨域资源共享(CORS) 是一种机制，它使用额外的HTTP头来告诉浏览器让运行在一个 origin (domain) 上的Web应用被准许访问来自不同源服务器上的指定的资源。当一个资源从与该资源本身所在的服务器不同的域、协议或端口请求一个资源时，资源会发起一个跨域 HTTP 请求。

比如https://www.iwencai.com/ 里面发起跨源Http请求(XMLHttpRequest Fetch)，会被浏览器拦截，除非响应报文包含了正确CORS响应头。

> 并不一定是浏览器限制了发起跨站请求，也可能是跨站请求可以正常发起，但是返回结果被浏览器拦截了

浏览器将CORS请求分成两类：简单请求（simple request）和非简单请求（not-so-simple request）。

## 4. SameSite Cookie

## 

###






