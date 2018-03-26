---
layout:     post
title:      "Javascript设计模式 (模块化)"
subtitle:   " \"开发中不可忽略的设计思想...\""
date:       2018-03-16
author:     "Kerwin She"
header-img: "img/post-bg-js-version.jpg"
tags:
    - javascript
---



# javascript 模块化

## 模块化模式

模块化模式最初被定义为一种对传统软件工程中的类提供私有和公共封装的方法。
在JavaScript中，模块化模式用来进一步模拟类的概念，通过这样一种方式：我们可以在一个单一的对象中包含公共/私有的方法和变量，从而从全局范围中屏蔽特定的部分。这个结果是可以减少我们的函数名称与在页面中其他脚本区域定义的函数名称冲突的可能性。

**例子** ：下面这个例子通过创建一个自包含的模块实现了模块模式
```javascript
var testModule = (function () {

var counter = 0;

return {

  incrementCounter: function () {
    return counter++;
  },

  resetCounter: function () {
    console.log( "counter value prior to reset: " + counter );
    counter = 0;
  }
};

})();

// Usage:

// Increment our counter
testModule.incrementCounter();

// Check the counter value and reset
// Outputs: 1
testModule.resetCounter();
```
在这里我们看到，其它部分的代码不能直接访问我们的incrementCounter() 或者 resetCounter()的值。counter变量被完全从全局域中隔离起来了，因此其表现的就像一个私有变量一样，它的存在只局限于模块的闭包内部，因此只有两个函数可以访问counter。我们的方法是有名字空间限制的，因此在我们代码的测试部分，我们需要给所有函数调用前面加上模块的名字(例如"testModule")。

当使用模块模式时，我们会发现通过使用简单的模板，对于开始使用模块模式非常有用。下面是一个模板包含了命名空间，公共变量和私有变量。
```javascript
var myNamespace = (function () {

var myPrivateVar, myPrivateMethod;

// 私有变量myPrivateVar 外部无法改变其值，因为木有讲其return
myPrivateVar = 0;

// 私有函数myPrivateMethod 外部无法改变，因为木有讲其return
myPrivateMethod = function( foo ) {
    console.log( foo );
};

return {

  // 公共变量
  myPublicVar: "foo",

  // A public function utilizing privates
  myPublicFunction: function( bar ) {

    // Increment our private counter
    myPrivateVar++;

    // Call our private method using bar
    myPrivateMethod( bar );

  }
};

})();
```
## 模块模式的变体
### Import mixins(导入混合)
这个变体展示了如何将全局（例如 jQuery, Underscore）作为一个参数传入模块的匿名函数。这种方式允许我们导入全局，并且按照我们的想法在本地为这些全局起一个别名。
```javascript
// Global module
var myModule = (function ( jQ, _ ) {

  function privateMethod1(){
      jQ(".container").html("test");
  }

  function privateMethod2(){
    console.log( _.min([10, 5, 100, 2, 1000]) );
  }

  return{
      publicMethod: function(){
          privateMethod1();               
      }           
  };

// Pull in jQuery and Underscore
}( jQuery, _ ));

myModule.publicMethod();
```
### Exports（导出）
这个变体允许我们声明全局对象而不用使用它们，同样也支持在下一个例子中我们将会看到的全局导入的概念。

```javascript
// Global module
var myModule = (function () {

    // Module object
  var module = {},
    privateVariable = "Hello World";

  function privateMethod() {
    // ...
  }

  module.publicProperty = "Foobar";
  module.publicMethod = function () {
    console.log( privateVariable );
  };

  return module;

}());
```
### Dojo
Dojo提供了一个方便的方法 dojo.setObject() 来设置对象。这需要将以"."符号为第一个参数的分隔符，如：myObj.parent.child 是指定义在"myOjb"内部的一个对象“parent”，它的一个属性为"child"。使用setObject()方法允许我们设置children 的值，可以创建路径传递过程中的任何对象即使这些它们根本不存在。
例如，如果我们声明商店命名空间的对象basket.coreas，可以实现使用传统的方式如下：
```javascript
var store = window.store || {};

if ( !store["basket"] ) {
  store.basket = {};
}

if ( !store.basket["core"] ) {
  store.basket.core = {};
}

store.basket.core = {
  // ...rest of our logic
};
```
或使用Dojo1.7（AMD兼容的版本）及以上如下：
```javascript
require(["dojo/_base/customStore"], function( store ){

// using dojo.setObject()
store.setObject( "basket.core", (function() {

    var basket = [];

    function privateMethod() {
        console.log(basket);
    }

    return {
        publicMethod: function(){
                privateMethod();
        }
    };

}()));

});
```
### ExtJS
对于这些使用Sencha的ExtJS的人们，你们很幸运，因为官方文档包含一些例子，用于展示如何正确地在框架里面使用模块模式。
下面我们可以看到一个例子关于如何定义一个名字空间，然后填入一个包含有私有和公有API的模块。除了一些语义上的不同之外，这个例子和使用vanilla javascript 实现的模块模式非常相似
```javascript
// create namespace
Ext.namespace("myNameSpace");

// create application
myNameSpace.app = function () {

  // do NOT access DOM from here; elements don't exist yet
  // private variables

  var btn1,
      privVar1 = 11;

  // private functions
  var btn1Handler = function ( button, event ) {
      console.log( "privVar1=" + privVar1 );
      console.log( "this.btn1Text=" + this.btn1Text );
    };

  // public space
  return {
    // public properties, e.g. strings to translate
    btn1Text: "Button 1",

    // public methods
    init: function () {

      if ( Ext.Ext2 ) {

        btn1 = new Ext.Button({
          renderTo: "btn1-ct",
          text: this.btn1Text,
          handler: btn1Handler
        });

      } else {

        btn1 = new Ext.Button( "btn1-ct", {
          text: this.btn1Text,
          handler: btn1Handler
        });

      }
    }
  };
}();
```

### YUI
类似地，我们也可以使用YUI3来实现模块模式。下面的例子很大程度上是基于原始由Eric Miraglia实现的YUI本身的模块模式，但是和vanillla Javascript 实现的版本比较起来差异不是很大。
```javascript
Y.namespace( "store.basket" ) = (function () {

    var myPrivateVar, myPrivateMethod;

    // private variables:
    myPrivateVar = "I can be accessed only within Y.store.basket.";

    // private method:
    myPrivateMethod = function () {
        Y.log( "I can be accessed only from within YAHOO.store.basket" );
    }

    return {
        myPublicProperty: "I'm a public property.",

        myPublicMethod: function () {
            Y.log( "I'm a public method." );

            // Within basket, I can access "private" vars and methods:
            Y.log( myPrivateVar );
            Y.log( myPrivateMethod() );

            // The native scope of myPublicMethod is store so we can
            // access public members using "this":
            Y.log( this.myPublicProperty );
        }
    };

})();
```
### jQuery
因为jQuery编码规范没有规定插件如何实现模块模式，因此有很多种方式可以实现模块模式。Ben Cherry 之间提供一种方案，因为模块之间可能存在大量的共性，因此通过使用函数包装器封装模块的定义。
在下面的例子中，定义了一个library 函数，这个函数声明了一个新的库，并且在新的库（例如 模块）创建的时候，自动将初始化函数绑定到document的ready上。
```javascript
function library( module ) {

  $( function() {
    if ( module.init ) {
      module.init();
    }
  });

  return module;
}

var myLibrary = library(function () {

  return {
    init: function () {
      // module implementation
    }
  };
}());
```

优势

既然我们已经看到单例模式很有用，为什么还是使用模块模式呢？首先，对于有面向对象背景的开发者来讲，至少从javascript语言上来讲，模块模式相对于真正的封装概念更清晰。
其次，模块模式支持私有数据-因此，在模块模式中，公共部分代码可以访问私有数据，但是在模块外部，不能访问类的私有部分（没开玩笑！感谢David Engfer 的玩笑）。

缺点

模块模式的缺点是因为我们采用不同的方式访问公有和私有成员，因此当我们想要改变这些成员的可见性的时候，我们不得不在所有使用这些成员的地方修改代码。
我们也不能在对象之后添加的方法里面访问这些私有变量。也就是说，很多情况下，模块模式很有用，并且当使用正确的时候，潜在地可以改善我们代码的结构。
其它缺点包括不能为私有成员创建自动化的单元测试，以及在紧急修复bug时所带来的额外的复杂性。根本没有可能可以对私有成员打补丁。相反地，我们必须覆盖所有的使用存在bug私有成员的公共方法。开发者不能简单的扩展私有成员，因此我们需要记得，私有成员并非它们表面上看上去那么具有扩展性。
想要了解更深入的信息，可以阅读 [Ben Cherry][2] 这篇精彩的文章。

[1]:http://shekang.me/2017/12/08/js-proto/
[2]:http://www.adequatelygood.com/JavaScript-Module-Pattern-In-Depth.html
