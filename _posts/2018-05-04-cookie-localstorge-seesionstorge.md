---
layout:     post
title:      "cookie、sessionStorage、localStorage 详解及应用场景"
date:       2018-05-04
author:     "Kerwin She"
header-img: "img/post-bg-js-version.jpg"
tags:
    - 前端性能
---

## cookie、sessionStorage、localStorage 详解及应用场景

> Cookie的作用是与服务器进行交互，作为HTTP规范的一部分而存在，而Web Storage仅仅是为了在本地“存储”数据而生


## cookie
>`Cookie`的作用是与服务器进行交互，作为`HTTP`规范的一部分而存在

### 了解cookie
1. 要表示唯一的一个`cookie`值需要：`name`、`domain`、`path`
2. 一个`cookie`就是一个小型的文本文件
3. 虽然`cookie`保存在浏览器端，但是一般是在服务器端设置的。
4. 可以在`HTTP`返回体里，通过设置`Set-Cookie`来告诉浏览器端所要存储的`cookie`。
5. 用来保存客户浏览器请求服务器页面的请求信息

### cookie相关字段的说明
1. 名称：一个唯一确定`cookie`的名称。`cookie`名称是不区分大小写的。`cookie`的名称必须是经过URL编码的。
2. 值：储存在`cookie`中的字符串值。值必须被`URL`编码。
3. 域：`cookie`对于哪个域是有效的。所有向该域发送的请求中都会包含这个cookie信息。如果没有明确设定，那么这个域会被认作来自设置`cookie`的那个域。
4. 路径：对于指定域中的那个路径，应该向服务器发送`cookie`。例如，你可以指定`cookie`只有从`http://www.wrox.com/books/`中才能访问，那么`http://www.wrox.com`的页面就不会发送cookie信息，即使请求都是来自同一个域的。
5. 失效时间：表示`cookie`何时应该被删除的时间戳（也就是，何时应该停止向服务器发送这个`cookie`）。默认情况下，浏览器会话结束时即将所有`cookie`删除；不过也可以自己设置删除时间。这个值是个`GMT`格式的日期（`Wdy,DD-Mon-YYYY HH:MM:SSGMT`），用于指定应该删除`cookie`的准确时间。因此，cookie可在浏览器关闭后依然保存在用户的机器上。如果你设置的失效日期是个以前的时间，则cookie会被立刻删除。
6. 安全标志：指定后，`cookie`只有在使用`SSL`连接的时候才发送到服务器。例如，cookie信息只能发送给 `https://www.wrox.com`，而`http://www.wrox.com`的请求则不能发送 `cookie`。

### cookie的应用场景
* 简单来说，`Cookie`就是服务器暂存放在你的电脑里的资料（`.txt`格式的文本文件），好让服务器用来辨认你的计算机。当你在浏览网站的时候，`Web`服务器会先送一小小资料放在你的计算机上，`Cookie` 会把你在网站上所打的文字或是一些选择都记录下来。当下次你再访问同一个网站，`Web`服务器会先看看有没有它上次留下的`Cookie`资料，有的话，就会依据`Cookie`里的内容来判断使用者，送出特定的网页内容给你。
* 网站可以利用`cookie`跟踪统计用户访问该网站的习惯，比如什么时间访问，访问了哪些页面，在每个网页的停留时间等。利用这些信息，一方面是可以为用户提供个性化的服务，另一方面，也可以作为了解所有用户行为的工具，对于网站经营策略的改进有一定参考价值。
* 目前`Cookie`最广泛的是记录用户登录信息，这样下次访问时可以不需要输入自己的用户名、密码了——当然这种方便也存在用户信息泄密的问题，尤其在多个用户共用一台电脑时很容易出现这样的问题。

### 设置／修改 `cookie`
`cookie`的原生的`API`，需要我们自己进行封装

```javascript

//直接复制 【直接复制不是覆盖，而是追加】
document.cookie = 'name=value;'

//封装setCookie方法
//setCookie 首先对name和value进行编码
function setCookie(name,value,expires,path,domain,secure){

   var cookie = encodeURIComponent(name)+ '=' +encodeURIComponent(value);

   //注意分号后面要有空格
   //后面的4个参数是可选的，所以用if判断并追加

   if(expires){
       cookie +='; expires='+expires.toGMTString();
   }
   if(path){
       cookie += '; path='+path;
   }
   if(domain){
       cookie += '; domain='+domain;
   }
   if(secure){
       cookie += '; secure='+secure;
   }
   document.cookie = cookie;
}
```

### 删除cookie
输入参数为`name`、`path`、`domain` 这3个是唯一标识`cookie`的,将`max-age`设置为0，就可以立即删除了.
```javascript
function remove(name,domain,path){
   document.cookie = 'name='+name
                   +'; domain='+domain
                   +'; path='+path
                   +'; max-age=0';
}
```
### cookie缺点
- `Cookie` **数量** 和 **长度** 的限制。`IE6`或更低版本每个`domian`下最多20个`cookie`，`IE7`和之后的版本最多可以有 `50`个`cookie`，`Firefox`最多`50`个`cookie`，`chrome`和`Safari`没有做硬性限制，每个`cookie`长度不能超过`4KB`，否则会被截掉。
- `IE`和`Opera` 会清理近期最少使用的`cookie`，`Firefox`会随机清理`cookie`。这就导致不能永久储存信息。
- **安全性问题**。如果`cookie`被人拦截了，那人就可以取得所有的`session`信息。即使加密也与事无补，因为拦截者并不需要知道`cookie`的意义，他只要原样转发`cookie`就可以达到目的了。
- 并且每次你请求一个新的页面的时候，`cookie`只要满足作用域和作用路径，`Cookie`都会被发送过去，这样无形中**浪费了带宽**。

## 本地储存
> `Web Storage`是为了在本地“存储”数据而生。`html5`中的 `Web Storage` 包括了两种存储方式：`sessionStorage`和`localStorage`

### localStorage && sessionStorage
> 只要有效期和作用域，浏览器每次访问的时候都会将`Storage`载入到内存里

- `localStorage`用于持久化的本地存储，除非主动删除数据，否则数据是永远不会过期的。
- `sessionStorage`用于本地存储一个会话（`session`）中的数据，这些数据只有在同一个会话中的页面才能访问并且当会话结束后数据也随之销毁。因此`sessionStorage`不是一种持久化的本地存储，仅仅是会话级别的存储。也就是说只要这个浏览器窗口没有关闭，即使刷新页面或进入同源另一页面，数据仍然存在。关闭窗口后，`sessionStorage`即被销毁
- `localStorage`也受同源策略的限制。
- `localStorage`和`sessionStorage`都具有相同的操作方法，如`setItem`,`getItem`,`removeItem`,`clear`等方法，不像`cookie`需要前端开发者自己封装`setCookie`，`getCookie`。

### localStorage应用场景
> `localStorage`可以用于存储该浏览器对该页面的访问次数，当然，如果换个浏览器，这个次数就重新开始计数了。还可以用来存储一些固定不变的页面信息，这样就不需要每次都重新加载了，这个值也可以进行覆盖。

访问这个页面的时候，`script `脚本会自动运行，`localStorage.pagecount`就会 `++` 了，从而达到统计页面访问次数的目的。

```html  
<!DOCTYPE HTML>
<html>
<body>

<script type="text/javascript">

if (localStorage.pagecount){
   localStorage.pagecount=Number(localStorage.pagecount) +1;
}
else{
   localStorage.pagecount=1;
}

document.write("Visits: " + localStorage.pagecount + " time(s).");

</script>

<p>刷新页面会看到计数器在增长。</p>

<p>请关闭浏览器窗口，然后再试一次，计数器会继续计数。</p>

</body>
</html>
```
### sessionStorage应用场景
> 使用 `sessionStorage` 进行页面传值

```javascaript
//有时会有这样的需求，我们从A页面获取的数据，需要在B页面发送给后端，这时就需要我们将数据从A页面传递到B页面。

//A页面
//首先检测Storage
if (typeof(Storage) !== "undefined") {
   sessionStorage.'name'=value;
} else {
   sessionStorage.name = '';
}


//B页面
if (typeof(Storage) !== "undefined") {
   var B_name = sessionStorage.name;
   }
```
```javascript
//注意，如果要储存的数据对象、数组
//那么在储存之前，用JSON.stringify将数据转换为字符串
//读取之后，再用JSON.parse转换为对象、数组

//存储
var obj = {name:"xiaoxiong",age:25};
var arr = [1,2,3,4];
window.sessionStorage.obj = JSON.stringify(obj);
window.sessionStorage.arr = JSON.stringify(arr);

//读取
var OBJ = window.sessionStorage.obj;//"{"name":"xiaoxiong","age":25}"
JSON.parse(OBJ);//Object {name: "xiaoxiong", age: 25}

var ARR = window.sessionStorage.arr;//"[1,2,3,4]"
JSON.parse(ARR);//(4) [1, 2, 3, 4]
```
## 总结(三者的异同)

特性 | 	Cookie | localStorage  | sessionStorage
- | :-: | :-: | -:
数据的生命期 | 可设置失效时间，默认是关闭浏览器后失效| 	除非被清除，否则永久保存 |  仅在当前会话下有效，关闭页面或浏览器后被清除
存放数据大小 | 4K左右 | 一般为5MB | 一般为5MB
与服务器端通信 | 每次都会携带在HTTP头中，如果使用cookie保存过多数据会带来性能问题 | 仅在客户端（即浏览器）中保存，不参与和服务器的通信 | 仅在客户端（即浏览器）中保存，不参与和服务器的通信
易用性 | 需要程序员自己封装，源生的Cookie接口不友好 | 源生接口可以接受，亦可再次封装来对Object和Array有更好的支持 |源生接口可以接受，亦可再次封装来对Object和Array有更好的支持

> 共同点：都是保存在浏览器端、且都受同源策略的影响 

### 安全性的考虑

需要注意的是，不是什么数据都适合放在 Cookie、localStorage 和 sessionStorage 中的。使用它们的时候，需要时刻注意是否有代码存在 XSS 注入的风险。因为只要打开控制台，你就随意修改它们的值，也就是说如果你的网站中有 XSS 的风险，它们就能对你的 localStorage 肆意妄为。所以千万不要用它们存储你系统中的敏感数据
