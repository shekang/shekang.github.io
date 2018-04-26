---
layout:     post
title:      "call,apply,bind"
date:       2018-04-19
author:     "Kerwin She"
header-img: "img/post-bg-js-version.jpg"
tags:
    - javascript
---
> 本文参考了这位[大神][1]


## 函数的三种角色

首先要先了解在函数本身会有一些自己的属性，比如：

- `length`：形参的个数；
- `name`：函数名；
- `prototype`：类的原型，在原型上定义的方法都是当前这个类的实例的公有方法；
- `__proto__`：把函数当做一个普通对象，指向Function这个类的原型

函数在整个JavaScript中是最复杂也是最重要的知识，对于一个函数来说，会存在多种角色

```javascript
function Fn() {
  var num = 500;
  this.x = 100;
}
Fn.prototype.getX = function () {
  console.log(this.x);
}
Fn.aaa = 1000;

var f = new Fn;

f.num // undefined
f.aaa // undefined
var res = Fn(); // res是undefined  Fn中的this是window

```

- 角色一：**普通函数**，对于`Fn`而言，它本身是一个普通的函数，执行的时候会形成私有的作用域，然后进行形参赋值、预解析、代码执行、执行完成后内存销毁；

- 角色二：**类**，它有自己的实例，`f`就是`Fn`作为类而产生的一个实例，也有一个叫做`prototype`的属性是自己的原型，它的实例都可以指向自己的原型；

- 角色三：**普通对象**，`Fn`和 `var obj = {}` 中的`obj`一样，就是一个普通的对象（所有的函数都是`Function`的实例），它作为对象可以有一些自己的私有属性，也可以通过`__proto__`找到`Function.prototype`；

函数的以上三种角色，可能大多数同学对于角色一和角色二都是没有任何疑问的，不过对于角色三可能会稍有疑惑，那么画张图来理解下吧：
![call]({{site.url}}/img/call-bg.png)

## call&apply的使用

关于call() 和 apply() 我的理解就是，它们的作用是： 让函数在某个指定的对象下执行。
```javascript
  var obj = {x: 1}
  function foo() {console.log(this.x)}
  foo.call(obj)       //打印结果： 1
```
除了传入对象外，还可以传入一个函数，因为函数作为**角色三**时就是个普通的对象
```javascript
  function a (a,b,c,d){
   console.log(this.length)
  };
  function b(a,b){
  console.log(this.length)
  };
  b.call(a)
  //4
```

call()和apply()的区别就在于，两者之间的参数，call()在第一个参数之后的  后续所有参数就是传入该函数的值。apply() 只有两个参数，第一个是对象，第二个是数组，这个数组就是该函数的参数。

```javascript
  var obj = {};
  function foo(a, b, c) {
   console.log(b);
  }
  foo.apply(obj, [1, 2, 3])   打印结果： 2;
```

bind() 方法和前两者不同在于： bind() 方法会返回执行上下文被改变的函数而不会立即执行，而前两者是直接执行该函数。他的参数和call()相同。



[1]:https://www.jianshu.com/p/00dc4ad9b83f
