---
layout:     post
title:      "跨域请求的实现"
subtitle:   " \"以小例子来记录如何实现跨域请求\""
date:       2018-04-08
author:     "Kerwin She"
header-img: "img/post-bg-js-version.jpg"
tags:
    - javascript
---

> 有关同源策略详细介绍,请看[这里][1]

# JSONP

JSONP是服务器与客户端跨源通信的常用方法。最大特点就是简单适用，老式浏览器全部支持，服务器改造非常小。
它的基本思想是，网页通过添加一个`<script>`元素，向服务器请求JSON数据，这种做法不受同源政策限制；服务器收到请求后，将数据放在一个指定名字的回调函数里传回来。
首先，网页动态插入`<script>`元素，由它向跨源网址发出请求。
例子：

**客服端**
```javascript
function addScriptTag(src) {
    var script = document.createElement('script');
    script.setAttribute("type","text/javascript");
    script.src = src;
    document.body.appendChild(script);
}
window.onload = function () {
    addScriptTag('http://localhost:3000/test?callback=foo');
}
function foo(data) {
  console.log('Your public IP address is: ' + data.ip);
};
```
上面代码通过动态添加`<script>`元素，向服务器`http://localhost:3000`发出请求。注意，该请求的查询字符串有一个`callback`参数，用来指定回调函数的名字，这对于JSONP是必需的。

服务器收到这个请求以后，会将数据放在回调函数的参数位置返回。

**JavaScript服务端**
```javascript
var express = require('express');
var app = express();

app.get('/test', function (req, res) {
  var cbFunction = req.query.callback
  res.send(`${cbFunction}({"ip":"110.110.110.110"})`);
  //或者不用那么麻烦直接通过express提供的API
  /**
   * res.jsonp({ip:"110.110.110.110"});
   */
});

var server = app.listen(3000, function () {
  var host = server.address().address;
  var port = server.address().port;

  console.log('Example app listening at http://%s:%s', host, port);
});

```
上面代码通过[express][2]做的简单后台,当请求接口时，后端拿到`callback`参数然后将其与要返回的数据组合成字符串的形式返回给客户端。
由于`<script>`元素请求的脚本，直接作为代码运行。这时，只要浏览器定义了`foo`函数，该函数就会立即调用。作为参数的JSON数据被视为JavaScript对象，而不是字符串，因此避免了使用`JSON.parse`的步骤。


# CORS（跨域资源共享）

CORS有两种请求 ：简单请求（simple request）和非简单请求（not-so-simple request）只要同时满足以下两大条件，就属于简单请求。

 1. 请求方法是以下三种方法之一：

    - HEAD
    - GET
    - POST

 2. HTTP的头信息不超出以下几种字段：
    - Accept
    - Accept-Language
    - Content-Language
    - Last-Event-ID
    - Content-Type：只限于三个值`application/x-www-form-urlencoded`、`multipart/form-data`、`text/plain`
 凡是不同时满足上面两个条件，就属于非简单请求。
 浏览器对这两种请求的处理，是不一样的。

## 简单请求

  **客户端**
  ```javascript
    var xhr = new XMLHttpRequest()
    xhr.open('post','http://localhost:3000/test',true)
    xhr.onload = function(e){
      if(e.currentTarget.status==200){
        alert(e.currentTarget.responseText)
      }
    }
    xhr.send()
  ```
  RequestHeaders请求头信息
  ```text
  Accept:/*/
  Accept-Encoding:gzip, deflate, br
  Accept-Language:zh-CN,zh;q=0.8
  Connection:keep-alive
  Content-Length:0
  Host:localhost:3000
  Origin:http://localhost:8020
  Referer:http://localhost:8020/
  User-Agent:Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3112.113 Safari/537.36
  ```
  **JavaScript服务端**
  ```javascript
    app.post('/test', function (req, res) {
      res.set('Access-Control-Allow-Origin','http://localhost:8020')
      res.send({
        name:'tom'
      })
    });
  ```
  ResponseHeaders
  ```text
  Access-Control-Allow-Origin:http://localhost:8020
  Connection:keep-alive
  Content-Length:14
  Content-Type:application/json; charset=utf-8
  Date:Mon, 09 Apr 2018 02:09:05 GMT
  ETag:W/"e-v50e5W1R/vD6VVQyxqDA0eSWedA"
  X-Powered-By:Express
  ```

## 非简单请求

### 预检请求

 **客户端**
 ```javascript
   var xhr = new XMLHttpRequest()
   xhr.open('put','http://localhost:3000/test',true)
   xhr.setRequestHeader('X-Custom-Header', 'value');
   xhr.onload = function(e){
     if(e.currentTarget.status==200){
       alert(e.currentTarget.responseText)
     }
   }
   xhr.send()
 ```
 当发送上面的http请求时浏览器会先发送一个options类型的http的预检请求

 RequestHeaders请求头信息
 ```text
  Accept:*/*
  Accept-Encoding:gzip, deflate, br
  Accept-Language:zh-CN,zh;q=0.8
  Access-Control-Request-Headers:x-custom-header
  Access-Control-Request-Method:PUT
  Connection:keep-alive
  Host:localhost:3000
  Origin:http://localhost:8020
  Referer:http://localhost:8020/
  User-Agent:Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3112.113 Safari/537.36
 ```
 **JavaScript服务端**
 ```javascript
   //options类型用于CORS非简单请求的预检请求
   app.options('/test',function(req,res){
     res.set('Access-Control-Allow-Origin','http://localhost:8020')  //必须设置
     res.set('Access-Control-Allow-Methods','PUT') //必须设置
     res.set('Access-Control-Allow-Headers','X-Custom-Header') //如果浏览器请求包括Access-Control-Request-Headers字段，则Access-Control-Allow-Headers字段是必需的。
     res.send({status:200,msg:'预检成功！'})
   })
 ```
 ResponseHeaders
 ```text
  Access-Control-Allow-Headers:X-Custom-Header
  Access-Control-Allow-Methods:PUT
  Access-Control-Allow-Origin:http://localhost:8020
  Connection:keep-alive
  Content-Length:38
  Content-Type:application/json; charset=utf-8
  Date:Mon, 09 Apr 2018 04:08:40 GMT
  ETag:W/"26-+1THtR73k4ca8avMcdsJULlJZUs"
  X-Powered-By:Express
 ```
 > 关于客户端和服务端字段相应说明可以在[这里查看][3]

### 正常请求和回应

一旦服务器通过了"预检"请求，以后每次浏览器正常的CORS请求，就都跟简单请求一样，会有一个`Origin`头信息字段。服务器的回应，也都会有一个`Access-Control-Allow-Origin`头信息字段。

**客户端**
RequestHeaders请求头信息
```text
  Accept:*/*
  Accept-Encoding:gzip, deflate, br
  Accept-Language:zh-CN,zh;q=0.8
  Connection:keep-alive
  Content-Length:0
  Host:localhost:3000
  Origin:http://localhost:8020
  Referer:http://localhost:8020/
  User-Agent:Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3112.113 Safari/537.36
  X-Custom-Header:value
```
上面头信息的`Origin`字段是浏览器自动添加的。
**JavaScript服务端**

 ```javascript
   app.put('/test', function (req, res) {
     res.set('Access-Control-Allow-Origin','http://localhost:8020')
     res.send({
       name:'tom'
     })
  });
 ```
 ResponseHeaders
 ```text
  Access-Control-Allow-Origin:http://localhost:8020
  Connection:keep-alive
  Content-Length:14
  Content-Type:application/json; charset=utf-8
  Date:Mon, 09 Apr 2018 04:08:40 GMT
  ETag:W/"e-v50e5W1R/vD6VVQyxqDA0eSWedA"
  X-Powered-By:Express
 ```
 上面头信息中，`Access-Control-Allow-Origin`字段是每次回应都必定包含的。

[1]:http://shekang.me/2018/03/28/homologousStrategy/
[2]:http://www.expressjs.com.cn/
[3]:http://shekang.me/2018/03/28/homologousStrategy/#跨域资源共享-cors
