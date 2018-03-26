---
layout:     post
title:      "Javascript设计模式（构造器）"
subtitle:   " \"开发中不可忽略的设计思想...\""
date:       2018-03-16
author:     "Kerwin She"
header-img: "img/post-bg-js-version.jpg"
tags:
    - javascript
---

# 构造器模式

## 基础构造器
Javascript不支持类的概念，但它有一种与对象一起工作的构造器函数。使用`new`关键字来调用该函数，我们可以告诉Javascript把这个函数当做一个构造器来用,它可以用自己所定义的成员来初始化一个对象

```javascript
function Car( model, year, miles ) {

this.model = model;
this.year = year;
this.miles = miles;

this.toString = function () {
 return this.model + " has done " + this.miles + " miles";
};
}

// 使用:

// 我们可以示例化一个Car
var civic = new Car( "Honda Civic", 2009, 20000 );
var mondeo = new Car( "Ford Mondeo", 2010, 5000 );

// 打开浏览器控制台查看这些对象toString()方法的输出值
// output of the toString() method being called on
// these objects
console.log( civic.toString() );
console.log( mondeo.toString() );
```

上面这是个简单版本的构造器模式，但它还是有些**问题** :
1. 难以继承
2. 每个`Car`构造函数创建的对象中，`toString()`之类的函数都被重新定义
> 这不是非常好，理想的情况是所有`Car`类型的对象都应该引用同一个函数。

## 原型构造器
在Javascript中函数有一个prototype的属性。当我们调用Javascript的构造器创建一个对象时，构造函数prototype上的属性对于所创建的对象来说都看见。照这样，就可以创建多个访问相同prototype的Car对象了。这样也就解决了基础构造器的问题。
> 对JS中的__proto__、prototype、constructor不是很明白的可以查看这篇[文章][1]

```javascript
function Car( model, year, miles ) {

this.model = model;
this.year = year;
this.miles = miles;

}

// 注意这里我们使用Note here that we are using Object.prototype.newMethod 而不是
// Object.prototype ，以避免我们重新定义原型对象
Car.prototype.toString = function () {
return this.model + " has done " + this.miles + " miles";
};

// 使用:

var civic = new Car( "Honda Civic", 2009, 20000 );
var mondeo = new Car( "Ford Mondeo", 2010, 5000 );

console.log( civic.toString() );
console.log( mondeo.toString() );
```
通过上面代码，单个toString()实例被所有的Car对象所共享了。



[1]:http://shekang.me/2017/12/08/js-proto/
