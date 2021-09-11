---
layout: post
title:  How to Cross-origin
date:   2020-04-16 00:30:00 +0800
tags: [web]
---

![how to cross origin sky](/img/posts/cross-origin/cross-origin-sky.png)

## 1. 什么是同源策略（Same-origin Policy）

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
| iframes | iframe的跨源embedding通常是允许的（有`X-Frame-Options`的请求头限制），但是跨域的读操作是不行的 |
| CSS | 可以通过 `<link rel="stylesheet" href="...">`embedding，CSS跨域需要设置正确的Http头部 Content-Type |
| forms | 跨域的URL可以作为 form的action属性，可以提交表单到跨域的地址 |
| images | 图片的跨域embedding是允许的，但是读取跨域的图片（在canvas中使用Javascript加载跨域的图片） |
| multimedia | `<video>` 和 `<audio>` 播放的多媒体资源可以跨域embedding |
| script | 跨域的`<script src="..."></script>`是允许的，但使用一些的API会被禁止（XMLHttpRequest，Fetch） |

### 2.1 如何允许跨源访问

CORS

### 2.2 如何阻止跨源访问

CSRF(Cross-site request forgery) 跨站请求伪造

![https://www.imperva.com/learn/application-security/csrf-cross-site-request-forgery/](/img/posts/cross-origin/csrf-cross-site-request-forgery.png)


## 3. 跨源脚本API访问

JavaScript 的 API 中，如 iframe.contentWindow、 window.parent、window.open 和 window.opener 允许文档间直接相互引用。当两个文档的源不同时，这些引用方式将对 Window 和 Location对象的访问添加限制。

为了能让不同源中文档进行交流，可以使用 window.postMessage。

## 4. 跨源数据存储访问

### 4.1 LocalStorage 和 IndexedDB

访问存储在浏览器中的数据，如 localStorage 和 IndexedDB，是以源进行分割。每个源都拥有自己单独的存储空间，一个源中的 JavaScript 脚本不能对属于其它源的数据进行读写操作。

### 4.2 Cookie

Cookie稍微有一点不太一样，一个页面可以为本域和其父域设置 cookie。

#### 什么是Cookie?

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

## 5. 跨域资源共享CORS

![cross-origin error](/img/posts/cross-origin/cross-origin-fetch-error.png)

跨域资源共享(CORS) 是一种机制，它使用额外的HTTP头来告诉浏览器让运行在一个 origin (domain) 上的Web应用被准许访问来自不同源服务器上的指定的资源。当一个资源从与该资源本身所在的服务器不同的域、协议或端口请求一个资源时，资源会发起一个跨域 HTTP 请求。

比如https://www.iwencai.com/ 里面发起跨源Http请求(XMLHttpRequest Fetch)，会被浏览器拦截，除非响应报文包含了正确CORS响应头。

> 并不一定是浏览器限制了发起跨站请求，也可能是跨站请求可以正常发起，但是返回结果被浏览器拦截了

浏览器将CORS请求分成两类：简单请求（simple request）和非简单请求（not-so-simple request）

简单请求条件: 

```
（1) 请求方法是以下三种方法之一：
	HEAD
	GET
	POST
（2）HTTP的头信息不超出以下几种字段：
	Accept
	Accept-Language
	Content-Language
	Last-Event-ID
	Content-Type：只限于三个值application/x-www-form-urlencoded、multipart/form-data、text/plain
```

### 5.1 简单请求

对于一个简单请求，浏览器会对在跨域请求发起时增加一个`Origin`字段的`Http`头，向服务端标识本次请求的来源。

服务端可以判断请求的`Origin`的内容后，返回给浏览器。如果返回内容的`Http`头中没有`Access-Control-Allow-Origin`等字段，浏览器就会阻止本次请求，并且抛出异常。

相关字段有:

* Access-Control-Allow-Origin

该字段是必须的。它的值要么是请求时Origin字段的值，要么是一个*，表示接受任意域名的请求。

* Access-Control-Allow-Credentials

该字段可选。它的值是一个布尔值，表示是否允许发送Cookie。默认情况下，Cookie不包括在CORS请求之中。设为true，即表示服务器明确许可，Cookie可以包含在请求中，一起发给服务器。这个值也只能设为true，如果服务器不要浏览器发送Cookie，删除该字段即可。

* Access-Control-Expose-Headers

该字段可选。CORS请求时，XMLHttpRequest对象的getResponseHeader()方法只能拿到6个基本字段：Cache-Control、Content-Language、Content-Type、Expires、Last-Modified、Pragma。如果想拿到其他字段，就必须在Access-Control-Expose-Headers里面指定。上面的例子指定，getResponseHeader('FooBar')可以返回FooBar字段的值。

>上面说到了`Access-Control-Allow-Credentials` 请求携带`Cookie`的问题，在JavaScript发送XHR请求时需要设置`xhr.withCredentials = true` 如:

**jQuery**

```
$.ajax({
    url: 'http://bar.other',
    data: { whatever:'cool' },
    type: 'GET',
    beforeSend: function(xhr){
       xhr.withCredentials = true;
    }
});
```
**Fetch**

```
fetch('https://example.com', {
  credentials: 'include'
});
```

> 需要注意的是，如果要发送Cookie，Access-Control-Allow-Origin就不能设为星号，必须指定明确的、与请求网页一致的域名。同时，Cookie依然遵循同源政策，只有用服务器域名设置的Cookie才会上传，其他域名的Cookie并不会上传，且（跨源）原网页代码中的document.cookie也无法读取服务器域名下的Cookie。

### 5.2 非简单请求

非简单请求是那种对服务器有特殊要求的请求，比如请求方法是`PUT`或`DELETE`，或者`Content-Type`字段的类型是`application/json`

非简单请求的CORS请求，会在正式通信之前，增加一次HTTP查询请求，称为`预检`请求（`preflight`）

#### 预检请求

先发送一个请求询问服务器，是不是支持当前请求的域名，支持哪些HTTP方法，哪些HTTP头部字段，浏览器根据返回的内容判断是否继续发出正式的`XMLHttpRequest`请求。

* Access-Control-Request-Method

该字段是必须的，用来列出浏览器的CORS请求会用到哪些HTTP方法。

* Access-Control-Request-Headers

该字段是一个逗号分隔的字符串，指定浏览器CORS请求会额外发送的头信息字段。

* Access-Control-Allow-Credentials

该字段与简单请求时的含义相同。

* Access-Control-Max-Age

该字段可选，用来指定本次预检请求的有效期，单位为秒。上面结果中，有效期是20天（1728000秒），即允许缓存该条回应1728000秒（即20天），在此期间，不用发出另一条预检请求。

```
Access-Control-Allow-Credentials: true
Access-Control-Allow-Origin: https://ai.iwencai.com
Connection: keep-alive
Content-Encoding: gzip
Content-Type: application/json;charset=utf-8
```
> `Access-Control-Request-Method`默认值为简单请求的支持类型，如`GET`，`POST`。

## 6. JSONP

JSON with Padding

```
    <script>
        var myFunc = function(data) {
            console.log(data);
        }
    </script>
    <script src="xyz.com?wrap=myFunc"></script>
```

## 7. SameSite Cookie

### 7.1 SameSite


|Origin A	| Origin B |	Explanation of whether Origin A and B are "same-site" or "cross-site"
| ------------- | ------------- | ------------- |
| https://www.example.com:443	| https://www.evil.com:443 | cross-site: different domains |
|									| https://login.example.com:443 | same-site: different subdomains don't matter |
|									| http://www.example.com:443 | same-site: different schemes don't matter |
| | https://www.example.com:80 | same-site: different ports don't matter |
| | https://www.example.com:443 | same-site: exact match |
| | https://www.example.com	| same-site: ports don't matter |

### 7.2 Chrome限制

![cross-origin error](/img/posts/cross-origin/same-site-cookie.png)

![cross-origin flag](/img/posts/cross-origin/same-site-flag.png)

2016年引进了`SameSite`属性，来对Cookie的能够使用的环境进行限制。`SameSite`有3种取值: `Strict`, `Lax`, `None`。

* `Strict`

完全禁止第三方Cookie，跨站点的请求都不会发送Cookie，

* `Lax`

稍微放松一点，基本上也禁止第三方Cookie，有些情况例外：

| 请求类型 | 示例 | 正常情况 | Lax |
| ------------- | ------------- | ------------- | ------------- |
| 链接	|  `<a href="..."></a>` | 发送 Cookie	| 发送 Cookie | 
| 预加载 | `<link rel="prerender" href="..."/>` | 发送 Cookie | 发送 Cookie | 
| GET 表单 | `<form method="GET" action="...">` | 发送 Cookie | 发送 Cookie |
| POST 表单 | `<form method="POST" action="...">` | 发送 Cookie | 不发送 | 
|  iframe	| `<iframe src="..."></iframe>`	| 发送 Cookie | 不发送 | 
|  AJAX | `$.get("...")` | 发送 Cookie | 不发送 |
| Image | `<img src="...">` | 发送 Cookie | 不发送 | 

Chrome默认设置Lax

* `None`

设置为`None`时，需要同时设置为`Secure`（Cookie只能在HTTPS协议下才能发送），否则不发送。

## References

[1] https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy

[2] http://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html

[3] http://www.ruanyifeng.com/blog/2016/04/cors.html

[4] https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS

[5] https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options

[6] https://web.dev/samesite-cookies-explained/



