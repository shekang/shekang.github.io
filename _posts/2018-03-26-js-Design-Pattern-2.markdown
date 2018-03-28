---
layout:     post
title:      "Javascript设计模式 (观察者) "
subtitle:   " \"开发中不可忽略的设计思想...\""
date:       2018-03-26
author:     "Kerwin She"
header-img: "img/post-bg-js-version.jpg"

tags:
    - javascript
---

# 观察者模式

观察者模式是这样一种设计模式。一个被称作被观察者的对象，维护一组被称为观察者的对象，这些对象依赖于被观察者，被观察者自动将自身的状态的任何变化通知给它们。

当一个被观察者需要将一些变化通知给观察者的时候，它将采用广播的方式，这条广播可能包含特定于这条通知的一些数据。

当特定的观察者不再需要接受来自于它所注册的被观察者的通知的时候，被观察者可以将其从所维护的组中删除。 在这里提及一下设计模式现有的定义很有必要。这个定义是与所使用的语言无关的。通过这个定义，最终我们可以更深层次地了解到设计模式如何使用以及其优势。在四人帮的《设计模式:可重用的面向对象软件的元素》这本书中，是这样定义观察者模式的:

一个或者更多的观察者对一个被观察者的状态感兴趣，将自身的这种兴趣通过附着自身的方式注册在被观察者身上。当被观察者发生变化，而这种便可也是观察者所关心的，就会产生一个通知，这个通知将会被送出去，最后将会调用每个观察者的更新方法。当观察者不在对被观察者的状态感兴趣的时候，它们只需要简单的将自身剥离即可。

我们现在可以通过实现一个观察者模式来进一步扩展我们刚才所学到的东西. 这个实现包含一下组件：

- **被观察者：维护一组观察者， 提供用于增加和移除观察者的方法**。
- **观察者：提供一个更新接口，用于当被观察者状态变化时，得到通知**。
- **具体的被观察者：状态变化时广播通知给观察者，保持具体的观察者的信息并扩展被观察者的方法**。
- **具体的观察者：保持一个指向具体被观察者的引用，实现一个更新接口，用于观察，以便保证自身状态总是和被观察者状态一致的，并扩展观察者的方法**。

首先我们来实现第一部分**被观察者**：维护一组观察者， 提供用于增加和移除观察者的方法
```javascript
function ObserverList(){
this.observerList = []; //储存观察者的数组
}

ObserverList.prototype.Add = function( obj ){
return this.observerList.push( obj );
};

ObserverList.prototype.Empty = function(){
this.observerList = [];
};

ObserverList.prototype.Count = function(){
return this.observerList.length;
};
//根据index值获取观察者
ObserverList.prototype.Get = function( index ){
if( index > -1 && index < this.observerList.length ){
 return this.observerList[ index ];  
}
};
//往前或后插入观察者
ObserverList.prototype.Insert = function( obj, index ){
var pointer = -1;

if( index === 0 ){
 this.observerList.unshift( obj );
 pointer = index;
}else if( index === this.observerList.length ){
 this.observerList.push( obj );
 pointer = index;
}

return pointer;
};
//判断观察者是否存在并返回pointer
ObserverList.prototype.IndexOf = function( obj, startIndex ){
var i = startIndex, pointer = -1;

while( i < this.observerList.length ){
 if( this.observerList[i] === obj ){
   pointer = i;
 }
 i++;
}

return pointer;
};
//移除观察者
ObserverList.prototype.RemoveAt = function( index ){
if( index === 0 ){
 this.observerList.shift();
}else if( index === this.observerList.length -1 ){
 this.observerList.pop();
}
};

// 扩展属性函数
function extend( extension, obj ){
for ( var key in extension ){
 obj[key] = extension[key];
}
}
```
定义好被观察者对象相应的信息后再对其具体化，添加广播函数
```javascript
function Subject(){
this.observers = new ObserverList();
}

Subject.prototype.AddObserver = function( observer ){
this.observers.Add( observer );
};

Subject.prototype.RemoveObserver = function( observer ){
this.observers.RemoveAt( this.observers.IndexOf( observer, 0 ) );
};

Subject.prototype.Notify = function( context ){
var observerCount = this.observers.Count();
for(var i=0; i < observerCount; i++){
  this.observers.Get(i).Update( context );  //这里获取到了观察者后循环调用了其Update函数实现了通知
}
};
```
第二部分**观察者**：提供一个更新接口，用于当被观察者状态变化时，得到通知
```javascript
// The Observer
function Observer(){
 this.Update = function(){
   // ...
 };
}
```

第三部分和第四部分就需要通过具体的实例来说明,
在我们的样例应用里面，我们使用上面的观察者组件，现在我们定义：
- 一个按钮，这个按钮用于增加新的充当观察者的选择框到页面上
- 一个控制用的选择框 , 充当一个被观察者，通知其它选择框是否应该被选中
- 一个容器，用于放置新的选择框

HTML

```html
<button id="addNewObserver">Add New Observer checkbox</button>
<input id="mainCheckbox" type="checkbox"/>
<div id="observersContainer"></div>
```
JAVASCRIPT

```javascript
// 我们DOM 元素的引用

var controlCheckbox = document.getElementById( "mainCheckbox" ),
addBtn = document.getElementById( "addNewObserver" ),
container = document.getElementById( "observersContainer" );

// 具体的被观察者

//Subject 类扩展controlCheckbox 类
extend( new Subject(), controlCheckbox );

//点击checkbox 将会触发对观察者的通知
controlCheckbox["onclick"] = new Function( "controlCheckbox.Notify(controlCheckbox.checked)" );

addBtn["onclick"] = AddNewObserver;

// 具体的观察者

function AddNewObserver(){

//建立一个新的用于增加的checkbox
var check  = document.createElement( "input" );
check.type = "checkbox";

// 使用Observer 类扩展checkbox
extend( new Observer(), check );

// 使用定制的Update函数重载
check.Update = function( value ){
  this.checked = value;
};

// 增加新的观察者到我们主要的被观察者的观察者列表中
controlCheckbox.AddObserver( check );

// 将元素添加到容器的最后
container.appendChild( check );
}
```
> 上面代码需要注意几个地方：
- 具体被观察者需要通用 `extend`方法扩展被观察者对象`new Subject()`的方法
- 具体被观察者还需根据自己需求在合适的时候触发`Notify`广播方法
- 具体观察者需要通用 `extend`方法扩展观察者对象`new Observer()`的方法
- 在具体观察者中需要重载`Update`方法来实现你要处理的效果
- 在具体观察者处理完成后需要执行`AddObserver`方法将其添加到观察者对象中
