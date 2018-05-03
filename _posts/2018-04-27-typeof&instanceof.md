---
layout:     post
title:      "深入理解javascript之typeof和instanceof"
date:       2018-04-17
author:     "Kerwin She"
header-img: "img/post-bg-js-version.jpg"
tags:
    - javascript
---
> [原文][1]

## typeof
 先来说说**typeof**吧。首先需要注意的是，typeof方法**返回一个字符串**，来表示数据的类型。

### 语法讲解
 我们先看看各个数据类型对应typeof的值：


 数据类型 | 	Type
- | :-: | -:
Undefined| “undefined”
Null | “object”
布尔值 | “boolean”
数值 | “number”
字符串 | "string"
Symbol (ECMAScript 6 新增) | “symbol”
宿主对象(JS环境提供的，比如浏览器) | Implementation-dependent
函数对象 | “function”
任何其他对象 | “object”

再看看具体的实例：
```javascript
// Numbers
typeof 37 === 'number';
typeof 3.14 === 'number';
typeof Math.LN2 === 'number';
typeof Infinity === 'number';
typeof NaN === 'number'; // 尽管NaN是"Not-A-Number"的缩写,意思是"不是一个数字"
typeof Number(1) === 'number'; // 不要这样使用!

// Strings
typeof "" === 'string';
typeof "bla" === 'string';
typeof (typeof 1) === 'string'; // typeof返回的肯定是一个字符串
typeof String("abc") === 'string'; // 不要这样使用!

// Booleans
typeof true === 'boolean';
typeof false === 'boolean';
typeof Boolean(true) === 'boolean'; // 不要这样使用!

// Symbols
typeof Symbol() === 'symbol';
typeof Symbol('foo') === 'symbol';
typeof Symbol.iterator === 'symbol';

// Undefined
typeof undefined === 'undefined';
typeof blabla === 'undefined'; // 一个未定义的变量,或者一个定义了却未赋初值的变量

// Objects
typeof {a:1} === 'object';

// 使用Array.isArray或者Object.prototype.toString.call方法可以从基本的对象中区分出数组类型
typeof [1, 2, 4] === 'object';

typeof new Date() === 'object';

// 下面的容易令人迷惑，不要这样使用！
typeof new Boolean(true) === 'object';
typeof new Number(1) ==== 'object';
typeof new String("abc") === 'object';

// 函数
typeof function(){} === 'function';
typeof Math.sin === 'function';
```
我们会发现一个问题，就是typeof来判断数据类型其实并不准确。比如数组、正则、日期、对象的typeof返回值都是object，这就会造成一些误差。
所以在typeof判断类型的基础上，我们还需要利用**Object.prototype.toString**方法来进一步判断数据类型。
我们来看看在相同数据类型的情况下，toString方法和typeof方法返回值的区别：

数据 | toString | typeof
- | :-: | -:
“foo” | String |	string
new String(“foo”) |	String	| object
new Number(1.2)	| Number	| object
true	| Boolean	| boolean
new Boolean(true)	| Boolean	| object
new Date()	| Date	| object
new Error()	| Error	| object
new Array(1, 2, 3)	| Array	| object
/abc/g	| RegExp	| object
new RegExp(“meow”) 	| RegExp	| object

可以看到利用toString方法可以正确区分出Array、Error、RegExp、Date等类型。

## instanceof
接下来该说说`instanceof`方法了。`instanceof`运算符可以用来判断某个**构造函数**的`prototype`属性**是否存在于另外一个要检测对象的原型链上**。
下面我们看看instanceof的实例：
```javascript
// 定义构造函数
function C(){}
function D(){}

var o = new C();

// true，因为 Object.getPrototypeOf(o) === C.prototype
o instanceof C;

// false，因为 D.prototype不在o的原型链上
o instanceof D;

o instanceof Object; // true,因为Object.prototype.isPrototypeOf(o)返回true
C.prototype instanceof Object // true,同上

C.prototype = {};
var o2 = new C();

o2 instanceof C; // true

o instanceof C; // false,C.prototype指向了一个空对象,这个空对象不在o的原型链上.

D.prototype = new C(); // 继承
var o3 = new D();
o3 instanceof D; // true
o3 instanceof C; // true
```


[1]:https://blog.csdn.net/mevicky/article/details/50353881
