---
layout: post
title:  How to Cross-origin
date:   2020-04-16 00:30:00 +0800
categories: [web]
---

# Same-origin Policy

## 什么是同源

同源策略是一个重要的安全策略，它用于限制一个origin的文档或者它加载的脚本如何能与另一个源的资源进行交互。它能帮助阻隔恶意文档，减少可能被攻击的媒介。

> 浏览器安全的基石是"同源政策"（same-origin policy）

### 同源定义

如果两个 URL 的 protocol、port (如果有指定的话)和 host 都相同的话，则这两个 URL 是同源。这个方案也被称为“协议/主机/端口元组”，或者直接是 “元组”。（“元组” 是指一组项目构成的整体，双重/三重/四重/五重/等的通用形式）。

## 访问类型

### 1. 网络访问

同源策略控制不同源之间的交互，例如在使用XMLHttpRequest 或 <img> 标签时则会受到同源策略的约束。这些交互通常分为三类：

* 跨域写操作（Cross-origin writes）一般是被允许的。例如链接（links），重定向以及表单提交。特定少数的HTTP请求需要添加 preflight。
* 跨域资源嵌入（Cross-origin embedding）一般是被允许（后面会举例说明）。
* 跨域读操作（Cross-origin reads）一般是不被允许的，但常可以通过内嵌资源来巧妙的进行读取访问。例如，你可以读取嵌入图片的高度和宽度，调用内嵌脚本的方法，或availability of an embedded resource.

```
1. <script src="..."></script> 标签嵌入跨域脚本。语法错误信息只能被同源脚本中捕捉到。
2. <link rel="stylesheet" href="..."> 标签嵌入CSS。由于CSS的松散的语法规则，CSS的跨域需要一个设置正确的 HTTP 头部 Content-Type 。不同浏览器有不同的限制： IE, Firefox, Chrome, Safari (跳至CVE-2010-0051)部分 和 Opera。
3. 通过 <img> 展示的图片。支持的图片格式包括PNG,JPEG,GIF,BMP,SVG,...
4. 通过 <video> 和 <audio> 播放的多媒体资源。
5. 通过 <object>、 <embed> 和 <applet> 嵌入的插件。
6. 通过 @font-face 引入的字体。一些浏览器允许跨域字体（ cross-origin fonts），一些需要同源字体（same-origin fonts）。
7. 通过 <iframe> 载入的任何资源。站点可以使用 X-Frame-Options 消息头来阻止这种形式的跨域交互。
```
#### 如何允许跨源访问

CORS

#### 如何阻止跨源访问
csrf

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

##### SameSite Cookie

## 

###






