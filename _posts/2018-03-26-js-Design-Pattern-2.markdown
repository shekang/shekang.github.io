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

# 观察者模式和发布/订阅模式的不同
观察者模式确实很有用，但是在javascript时间里面，通常我们使用一种叫做发布/订阅模式的变体来实现观察者模式。这两种模式很相似，但是也有一些值得注意的不同。

观察者模式要求想要接受相关通知的观察者必须到发起这个事件的被观察者上注册这个事件。
发布/订阅模式使用一个主题/事件频道，这个频道处于想要获取通知的订阅者和发起事件的发布者之间。这个事件系统允许代码定义应用相关的事件，这个事件可以传递特殊的参数，参数中包含有订阅者所需要的值。这种想法是为了避免订阅者和发布者之间的依赖性。
这种和观察者模式之间的不同，使订阅者可以实现一个合适的事件处理函数，用于注册和接受由发布者广播的相关通知。

这里给出一个关于如何使用发布者/订阅者模式的例子，这个例子中完整地实现了功能强大的publish(), subscribe() 和 unsubscribe()。

```javascript

// 一个非常简单的邮件处理器

// 接受的消息的计数器
var mailCounter = 0;

// 初始化一个订阅者，这个订阅者监听名叫"inbox/newMessage" 的频道

// 渲染新消息的粗略信息
var subscriber1 = subscribe( "inbox/newMessage", function( topic, data ) {

  // 日志记录主题，用于调试
  console.log( "A new message was received: ", topic );

  // 使用来自于被观察者的数据，用于给用户展示一个消息的粗略信息
  $( ".messageSender" ).html( data.sender );
  $( ".messagePreview" ).html( data.body );

});

// 这是另外一个订阅者，使用相同的数据执行不同的任务

// 更细计数器，显示当前来自于发布者的新信息的数量
var subscriber2 = subscribe( "inbox/newMessage", function( topic, data ) {

  $('.newMessageCounter').html( mailCounter++ );

});

publish( "inbox/newMessage", [{
  sender:"hello@google.com",
  body: "Hey there! How are you doing today?"
}]);

// 在之后，我们可以让我们的订阅者通过下面的方式取消订阅来自于新主题的通知
// unsubscribe( subscriber1,  );
// unsubscribe( subscriber2 );
```
这个例子的更广的意义是对松耦合的原则的一种推崇。不是一个对象直接调用另外一个对象的方法，而是通过订阅另外一个对象的一个特定的任务或者活动，从而在这个任务或者活动出现的时候的得到通知。

**优势**

  观察者和发布/订阅模式鼓励人们认真考虑应用不同部分之间的关系，同时帮助我们找出这样的层，该层中包含有直接的关系，这些关系可以通过一些列的观察者和被观察者来替换掉。这中方式可以有效地将一个应用程序切割成小块，这些小块耦合度低，从而改善代码的管理，以及用于潜在的代码复用。

  使用观察者模式更深层次的动机是，当我们需要维护相关对象的一致性的时候，我们可以避免对象之间的紧密耦合。例如，一个对象可以通知另外一个对象，而不需要知道这个对象的信息。

  两种模式下，观察者和被观察者之间都可以存在动态关系。这提供很好的灵活性，而当我们的应用中不同的部分之间紧密耦合的时候，是很难实现这种灵活性的。

  尽管这些模式并不是万能的灵丹妙药，这些模式仍然是作为最好的设计松耦合系统的工具之一，因此在任何的JavaScript 开发者的工具箱里面，都应该有这样一个重要的工具。

**缺点**

  事实上，这些模式的一些问题实际上正是来自于它们所带来的一些好处。在发布/订阅模式中，将发布者共订阅者上解耦，将会在一些情况下，导致很难确保我们应用中的特定部分按照我们预期的那样正常工作。

  例如，发布者可以假设有一个或者多个订阅者正在监听它们。比如我们基于这样的假设，在某些应用处理过程中来记录或者输出错误日志。如果订阅者执行日志功能崩溃了（或者因为某些原因不能正常工作），因为系统本身的解耦本质，发布者没有办法感知到这些事情。

  另外一个这种模式的缺点是，订阅者对彼此之间存在没有感知，对切换发布者的代价无从得知。因为订阅者和发布者之间的动态关系，更新依赖也很能去追踪。

  ## 发布/订阅实现

  发布/订阅在JavaScript的生态系统中非常合适，主要是因为作为核心的ECMAScript 实现是事件驱动的。尤其是在浏览器环境下更是如此，因为DOM使用事件作为其主要的用于脚本的交互API。

  也就是说，无论是ECMAScript 还是DOM都没有在实现代码中提供核心对象或者方法用于创建定制的事件系统（DOM3 的CustomEvent是一个例外，这个事件绑定在DOM上，因此通常用处不大）。

  幸运的是，流行的JavaScript库例如dojo, jQuery(定制事件)以及YUI已经有相关的工具，可以帮助我们方便的实现一个发布/订阅者系统。下面我们看一些例子。
  ```javascript
  // 发布

  // jQuery: $(obj).trigger("channel", [arg1, arg2, arg3]);
  $( el ).trigger( "/login", [{username:"test", userData:"test"}] );

  // Dojo: dojo.publish("channel", [arg1, arg2, arg3] );
  dojo.publish( "/login", [{username:"test", userData:"test"}] );

  // YUI: el.publish("channel", [arg1, arg2, arg3]);
  el.publish( "/login", {username:"test", userData:"test"} );

  // 订阅

  // jQuery: $(obj).on( "channel", [data], fn );
  $( el ).on( "/login", function( event ){...} );

  // Dojo: dojo.subscribe( "channel", fn);
  var handle = dojo.subscribe( "/login", function(data){..} );

  // YUI: el.on("channel", handler);
  el.on( "/login", function( data ){...} );

  // 取消订阅

  // jQuery: $(obj).off( "channel" );
  $( el ).off( "/login" );

  // Dojo: dojo.unsubscribe( handle );
  dojo.unsubscribe( handle );

  // YUI: el.detach("channel");
  el.detach( "/login" );
  ```
  **经典的发布/订阅例子**
  ```javascript
  var pubsub = {};

(function(q) {

    var topics = {},
        subUid = -1;

    // Publish or broadcast events of interest
    // with a specific topic name and arguments
    // such as the data to pass along
    q.publish = function( topic, args ) {

        if ( !topics[topic] ) {
            return false;
        }

        var subscribers = topics[topic],
            len = subscribers ? subscribers.length : 0;

        while (len--) {
            subscribers[len].func( topic, args );
        }

        return this;
    };

    // Subscribe to events of interest
    // with a specific topic name and a
    // callback function, to be executed
    // when the topic/event is observed
    q.subscribe = function( topic, func ) {

        if (!topics[topic]) {
            topics[topic] = [];
        }

        var token = ( ++subUid ).toString();
        topics[topic].push({
            token: token,
            func: func
        });
        return token;
    };

    // Unsubscribe from a specific
    // topic, based on a tokenized reference
    // to the subscription
    q.unsubscribe = function( token ) {
        for ( var m in topics ) {
            if ( topics[m] ) {
                for ( var i = 0, j = topics[m].length; i < j; i++ ) {
                    if ( topics[m][i].token === token) {
                        topics[m].splice( i, 1 );
                        return token;
                    }
                }
            }
        }
        return this;
    };
}( pubsub ));

  ```
  **示例:使用我们的实现**
  我们现在可以使用发布实例和订阅感兴趣的事件，例如:
  ```javascript
  // Another simple message handler

  // A simple message logger that logs any topics and data received through our
  // subscriber
  var messageLogger = function ( topics, data ) {
      console.log( "Logging: " + topics + ": " + data );
  };

  // Subscribers listen for topics they have subscribed to and
  // invoke a callback function (e.g messageLogger) once a new
  // notification is broadcast on that topic
  var subscription = pubsub.subscribe( "inbox/newMessage", messageLogger );

  // Publishers are in charge of publishing topics or notifications of
  // interest to the application. e.g:

  pubsub.publish( "inbox/newMessage", "hello world!" );

  // or
  pubsub.publish( "inbox/newMessage", ["test", "a", "b", "c"] );

  // or
  pubsub.publish( "inbox/newMessage", {
    sender: "hello@google.com",
    body: "Hey again!"
  });

  // We cab also unsubscribe if we no longer wish for our subscribers
  // to be notified
  // pubsub.unsubscribe( subscription );

  // Once unsubscribed, this for example won't result in our
  // messageLogger being executed as the subscriber is
  // no longer listening
  pubsub.publish( "inbox/newMessage", "Hello! are you still there?" );
  ```
  **例如：用户界面通知**

  接下来，让我们想象一下，我们有一个Web应用程序，负责显示实时股票信息。

  应用程序可能有一个表格显示股票统计数据和一个计数器显示的最后更新点。当数据模型发生变化时，应用程序将需要更新表格和计数器。在这种情况下，我们的主题（这将发布主题/通知）是数据模型以及我们的订阅者是表格和计数器。

  当我们的订阅者收到通知：该模型本身已经改变，他们自己可以进行相应的更新。

  在我们的实现中，如果发现新的股票信息是可用的，我们的订阅者将收听到的主题“新数据可用”。如果一个新的通知发布到该主题，那将触发表格去添加一个包含此信息的新行。它也将更新最后更新计数器，记录最后一次添加的数据
  ```javascript
  // Return the current local time to be used in our UI later
 getCurrentTime = function (){

    var date = new Date(),
          m = date.getMonth() + 1,
          d = date.getDate(),
          y = date.getFullYear(),
          t = date.toLocaleTimeString().toLowerCase();

         return (m + "/" + d + "/" + y + " " + t);
 };

 // Add a new row of data to our fictional grid component
 function addGridRow( data ) {

    // ui.grid.addRow( data );
    console.log( "updated grid component with:" + data );

 }

 // Update our fictional grid to show the time it was last
 // updated
 function updateCounter( data ) {

    // ui.grid.updateLastChanged( getCurrentTime() );  
    console.log( "data last updated at: " + getCurrentTime() + " with " + data);

 }

 // Update the grid using the data passed to our subscribers
 gridUpdate = function( topic, data ){

   if ( data !== "undefined" ) {
      addGridRow( data );
      updateCounter( data );
    }

 };

 // Create a subscription to the newDataAvailable topic
 var subscriber = pubsub.subscribe( "newDataAvailable", gridUpdate );

 // The following represents updates to our data layer. This could be
 // powered by ajax requests which broadcast that new data is available
 // to the rest of the application.

 // Publish changes to the gridUpdated topic representing new entries
 pubsub.publish( "newDataAvailable", {
   summary: "Apple made $5 billion",
   identifier: "APPL",
   stockPrice: 570.91
 });

 pubsub.publish( "newDataAvailable", {
   summary: "Microsoft made $20 million",
   identifier: "MSFT",
   stockPrice: 30.85
 });
  ```
  样例：在下面这个电影评分的例子里面，我们使用Ben Alman的发布/订阅实现来解耦应用程序。我们使用Ben Alman的jQuery实现，来展示如何解耦用户界面。请注意，我们如何做到提交一个评分，来产生一个发布信息，这个信息表明了当前新的用户和评分数据可用。
  剩余的工作留给订阅者，由订阅者来代理这些主题中的数据发生的变化。在我们的例子中，我们将新的数据压入到现存的数组中，接着使用Underscore库的template()方法来渲染模板。

  **HTML/模板**

 ```javascript
 <script id="userTemplate" type="text/html">
 <li><%= name %></li>
</script>

<script id="ratingsTemplate" type="text/html">
 <li><strong><%= title %></strong> was rated <%= rating %>/5</li>
</script>

<div id="container">

 <div class="sampleForm">
     <p>
         <label for="twitter_handle">Twitter handle:</label>
         <input type="text" id="twitter_handle" />
     </p>
     <p>
         <label for="movie_seen">Name a movie you’ve seen this year:</label>
         <input type="text" id="movie_seen" />
     </p>
     <p>

         <label for="movie_rating">Rate the movie you saw:</label>
         <select id="movie_rating">
               <option value="1">1</option>
                <option value="2">2</option>
                <option value="3">3</option>
                <option value="4">4</option>
                <option value="5" selected>5</option>

        </select>
      </p>
      <p>

          <button id="add">Submit rating</button>
      </p>
  </div>

  <div class="summaryTable">
      <div id="users"><h3>Recent users</h3></div>
      <div id="ratings"><h3>Recent movies rated</h3></div>
  </div>

</div>
 ```

 **JavaScript**

 ```javascript  
 ;(function( $ ) {

   // Pre-compile templates and "cache" them using closure
   var
     userTemplate = _.template($( "#userTemplate" ).html()),
     ratingsTemplate = _.template($( "#ratingsTemplate" ).html());

   // Subscribe to the new user topic, which adds a user
   // to a list of users who have submitted reviews
   $.subscribe( "/new/user", function( e, data ){

     if( data ){

       $('#users').append( userTemplate( data ));

     }

   });

   // Subscribe to the new rating topic. This is composed of a title and
   // rating. New ratings are appended to a running list of added user
   // ratings.
   $.subscribe( "/new/rating", function( e, data ){

     var compiledTemplate;

     if( data ){

       $( "#ratings" ).append( ratingsTemplate( data );

     }

   });

   // Handler for adding a new user
   $("#add").on("click", function( e ) {

     e.preventDefault();

     var strUser = $("#twitter_handle").val(),
        strMovie = $("#movie_seen").val(),
        strRating = $("#movie_rating").val();

     // Inform the application a new user is available
     $.publish( "/new/user",  { name: strUser } );

     // Inform the app a new rating is available
     $.publish( "/new/rating",  { title: strMovie, rating: strRating} );

     });

 })( jQuery );

 ```
 样例：解耦一个基于Ajax的jQuery应用。
 在我们最后的例子中，我们将从实用的角度来看一下如何在开发早起使用发布/订阅模式来解耦代码，这样可以帮助我们避免之后痛苦的重构过程。
 在Ajax重度依赖的应用里面，我们常会见到这种情况，当我们收到一个请求的响应之后，我们希望能够完成不仅仅一个特定的操作。我们可以简单的将所有请求后的逻辑加入到成功的回调函数里面，但是这样做有一些问题。

 高度耦合的应用优势会增加重用功能的代价，因为高度耦合增加了内部函数/代码的依赖性。这意味着如果我们只是希望获取一次性获取结果集，可以将请求后 的逻辑代码 硬编码在回调函数里面，这种方式可以正常工作，但是当我们想要对相同的数据源(不同的最终行为)做更多的Ajax调用的时候，这种方式就不适合了，我们必须要多次重写部分代码。与其回溯调用相同数据源的每一层，然后在将它们泛化，不如一开始就使用发布/订阅模式来节约时间。

 使用观察者，我们可以简单的将整个应用范围的通知进行隔离，针对不同的事件，我们可以把这种隔离做到我们想要的粒度上，如果使用其它模式，则可能不会有这么优雅的实现。

 注意我们下面的例子中，当用户表明他们想要做一次搜索查询的时候，一个话题通知就会生成，而当请求返回，并且实际的数据可用的时候，又会生成另外一个通知。而如何使用这些事件（或者返回的数据），都是由订阅者自己决定的。这样做的好处是，如果我们想要，我们可以有10个不同的订阅者，以不同的方式使用返回的数据，而对于Ajax层来讲，它不会关心你如何处理数据。它唯一的责任就是请求和返回数据，接着将数据发送给所有想要使用数据的地方。这种相关性上的隔离可以是我们整个代码设计更为清晰。

 **HTML/Templates**
 ```javascript
 <form id="flickrSearch">

    <input type="text" name="tag" id="query"/>

    <input type="submit" name="submit" value="submit"/>

 </form>

 <div id="lastQuery"></div>

 <div id="searchResults"></div>

 <script id="resultTemplate" type="text/html">
     <% _.each(items, function( item ){  %>
             <li><p><img src="<%= item.media.m %>"/></p></li>
     <% });%>
 </script>
 ```
 **JavaScript**
 ```javascript
 ;(function( $ ) {

  // Pre-compile template and "cache" it using closure
  var resultTemplate = _.template($( "#resultTemplate" ).html());

  // Subscribe to the new search tags topic
  $.subscribe( "/search/tags" , function( tags ) {
      $( "#searchResults" )
               .html("
<p>
   Searched for:<strong>" + tags + "</strong>
</p>
");
  });

  // Subscribe to the new results topic
  $.subscribe( "/search/resultSet" , function( results ){

      $( "#searchResults" ).append(resultTemplate( results ));

  });

  // Submit a search query and publish tags on the /search/tags topic
  $( "#flickrSearch" ).submit( function( e ) {

      e.preventDefault();
      var tags = $(this).find( "#query").val();

      if ( !tags ){
       return;
      }

      $.publish( "/search/tags" , [ $.trim(tags) ]);

  });

  // Subscribe to new tags being published and perform
  // a search query using them. Once data has returned
  // publish this data for the rest of the application
  // to consume

  $.subscribe("/search/tags", function( tags ) {

      $.getJSON( "http://api.flickr.com/services/feeds/photos_public.gne?jsoncallback=?" ,{
             tags: tags,
             tagmode: "any",
             format: "json"
           },

         function( data ){

             if( !data.items.length ) {
               return;
             }

             $.publish( "/search/resultSet" , data.items  );
      });

  });

})();
 ```
 观察者模式在应用设计中，解耦一系列不同的场景上非常有用，如果你没有用过它，我推荐你尝试一下今天提到的之前写到的某个实现。这个模式是一个易于学习的模式，同时也是一个威力巨大的模式。
