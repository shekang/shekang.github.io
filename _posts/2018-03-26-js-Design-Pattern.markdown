---
layout:     post
title:      "Javascript设计模式（单例模式）"
subtitle:   " \"开发中不可忽略的设计思想...\""
date:       2018-03-26
author:     "Kerwin She"
header-img: "img/post-bg-js-version.jpg"
tags:
    - javascript
---
# 单例模式

单例模式之所以这么叫，是因为它限制一个类只能有一个实例化对象。经典的实现方式是，创建一个类，这个类包含一个方法，这个方法在没有对象存在的情况下，将会创建一个新的实例对象。如果对象存在，这个方法只是返回这个对象的引用。

单例和静态类不同，因为我们可以退出单例的初始化时间。通常这样做是因为，在初始化的时候需要一些额外的信息，而这些信息在声明的时候无法得知。对于并不知晓对单例模式引用的代码来讲，单例模式没有为它们提供一种方式可以简单的获取单例模式。这是因为，单例模式既不返回对象也不返回类，它只返回一种结构。可以类比闭包中的变量不是闭包-提供闭包的函数域是闭包（绕进去了）。

在JavaScript语言中, 单例服务作为一个从全局空间的代码实现中隔离出来共享的资源空间是为了提供一个单独的函数访问指针。

我们能像这样实现一个单例:
```javascript
var mySingleton = (function () {

// Instance stores a reference to the Singleton
var instance;

function init() {

  // 单例

  // 私有方法和变量
  function privateMethod(){
      console.log( "I am private" );
  }

  var privateVariable = "Im also private";

  var privateRandomNumber = Math.random();

  return {

    // 共有方法和变量
    publicMethod: function () {
      console.log( "The public can see me!" );
    },

    publicProperty: "I am also public",

    getRandomNumber: function() {
      return privateRandomNumber;
    }

  };

};

return {

  // 如果存在获取此单例实例，如果不存在创建一个单例实例
  getInstance: function () {

    if ( !instance ) {
      instance = init();
    }

    return instance;
  }

};

})();

var myBadSingleton = (function () {

// 存储单例实例的引用
var instance;

function init() {

  // 单例

  var privateRandomNumber = Math.random();

  return {

    getRandomNumber: function() {
      return privateRandomNumber;
    }

  };

};

return {

  // 总是创建一个新的实例
  getInstance: function () {

    instance = init();

    return instance;
  }

};

})();

// 使用:

var singleA = mySingleton.getInstance();
var singleB = mySingleton.getInstance();
console.log( singleA.getRandomNumber() === singleB.getRandomNumber() ); // true

var badSingleA = myBadSingleton.getInstance();
var badSingleB = myBadSingleton.getInstance();
console.log( badSingleA.getRandomNumber() !== badSingleB.getRandomNumber() ); // true
```
