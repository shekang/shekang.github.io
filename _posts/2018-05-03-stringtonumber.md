---
layout:     post
title:      "JS 将 string 转换成为 number"
date:       2018-04-17
author:     "Kerwin She"
header-img: "img/post-bg-js-version.jpg"
tags:
    - javascript
---

JS 中将 `number` 转换为 `string` 我们比较熟悉，直接用`toString()` 的方法就可以了；那么将 `string` 转换成为 `number` 都有些什么方法呢？如下便简单的列举了一些，以便记录，说不定什么时候就用到了~~

我们可以使用**parseInt** ，或者**unary plus** 或者**parseFloat with floor** 或者**Math.round**这些方式，请看如下具体的代码：


## parseInt
```javascript

   var x = parseInt("10");  // 10
```
但是这种只带一个参数的方式会存在诟病。比如我们想将字符串 "010" 转化为数字 10，直接用这个方法将会返回8.
```javascript
  var x = parseInt("010"); // 8
```
因此这里提供另外一个方法，带有两个参数：第一个是目标转换字符串，第二个用来指定将字符串转化为几进制的数字，并且radix的范围是(2~36)
```javascript
 parseInt(string, radix)
```
## unary plus (一元操作符"+")，这种方式非常的巧妙
```javascript
var x = +"1000"; // 1000

var x = +"1000.12"; // 1000.12
```
## parseFloat 方法可以将对应的字符串转化为浮点类型的数字

## Math.floor 的方式可以对字符和数字进行向下取整

## Math.round 的方式可以对字符和数字进行四舍五入取整
