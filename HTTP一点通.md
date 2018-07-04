# HTTP一点通

[TOC]

## 从请求头格式看get与post

我们都知道一个HTTP请求头格式是这样的：

```http
<method> <request-URL> <HTTP/version>
<request-headers>

<request-body>
```

当然了，如果`method`为`GET`，那么`request-body`为`null`。

一个`GET`请求是这样的：

```javascript
xhr.open(method, requestURL?params);
xhr.send(request-body: null);
```

一个`POST`请求是这样的：

```javascript
xhr.open(method, requestURL);
xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');//POST必须设置请求头。
xhr.send(request-body: data);
```

认真观察两种方式，其实就是在构建请求头。

------

## GET方式有数据长度限制？

有是有的，但是不是HTTP协议限定的。

HTTP协议从来没有限制过`GET`方式可传数据的长度，真正限制的是浏览器和后台程序，而且限制的是整个`URL`的长度。

那个啥，构造 GET 参数的时候，总是会通过 `encodeURIComponent` 编码的，总是会。

> 致敬一下：
>
> 1. HTTP 协议 未规定 GET 和POST的长度限制
> 2. GET的最大长度显示是因为 浏览器和 web服务器限制了 URI的长度
> 3. 不同的浏览器和WEB服务器，限制的最大长度不一样
> 4. 要支持IE，则最大长度为2083byte，若只支持Chrome，则最大长度 8182byte

原文链接：[https://github.com/zhongxia245/blog/issues/35](https://link.jianshu.com?t=https://github.com/zhongxia245/blog/issues/35)

------

## 四种常见的POST提交数据方式

原文链接：https://imququ.com/post/four-ways-to-post-data-in-http.html

对于`POST`请求来说，提交的数据必须放在`request-body`中，而在那之前，要为传输的数据设置一种**编码方式**。服务器端通常是通过请求头中的Content-Type字段来获知请求中的数据主体是用何种方式编码。

如上，例子中设置的POST数据提交方式是`'application/x-www-form-urlencoded'`，其实常见的POST数据提交方式一共有四种：

`application/x-www-form-urlencoded`（**表单**默认）

`multipart/form-data`（**表单**的另一种方式）

`application/json`

`text/xml`

下面逐一介绍：

### application/x-www-form-urlencoded

`application/x-www-form-urlencoded`是最常见的POST提交数据的方式。浏览器原生`form`表单，如果不设置`enctype`属性，那么最终就会以`application/x-www-form-urlencoded`这种方式提交数据。请求类似这样：

```http
POST http://www.example.com HTTP/1.1
Content-Type: application/x-www-form-urlencoded;charset=utf-8

title=test&sub%5B%5D=1&sub%5B%5D=2&sub%5B%5D=3
```

首先，Content-Type 被指定为 application/x-www-form-urlencoded；

其次，提交的数据按照 key1=val1&key2=val2 的方式进行编码，key 和 val 都进行了 URL 转码。

### multipart/form-data

`multipart/form-data`也是一种常见的POST数据提交方式。使用表单上传文件，就必须使`enctype`等于`multipart/form-data`。下面是一个示例：

```http
POST http://www.example.com HTTP/1.1
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryrGKCBY7qhFd3TrwA

------WebKitFormBoundaryrGKCBY7qhFd3TrwA
Content-Disposition: form-data; name="text"

title
------WebKitFormBoundaryrGKCBY7qhFd3TrwA
Content-Disposition: form-data; name="file"; filename="chrome.png"
Content-Type: image/png

PNG ... content of chrome.png ...
------WebKitFormBoundaryrGKCBY7qhFd3TrwA--
```

首先生成一个`boundary`用于分割不同的字段；

然后`Content-Type`指明数据以`multipart/form-data`方式来编码；

消息主体里按照字段个数，以`--boundary`分割为多个结构类似的部分，每部分都是以`--boundary`开始，紧接着是内容描述信息，然后是空行，最后是内容（文本，或二进制数据），消息主体最后以`--boundary--`结束。

### application/json

application/json作为响应头肯定不陌生。实际上，现在越来越多人把它作为请求头，用来告诉服务端消息主体是序列化后的JSON字符串。下面是示例：

```http
POST http://www.example.com HTTP/1.1 
Content-Type: application/json;charset=utf-8

{"title":"test","sub":[1,2,3]}
```

这种方案，可以方便的提交复杂的结构化数据，特别适合 RESTful 的接口。

### text/xml

有些臃肿，不做记录。