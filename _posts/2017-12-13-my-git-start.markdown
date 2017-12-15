---
 layout: post
 title: "git start 分类"
 date: 2017-12-13
 author: "Kerwin She"
 fn: ""
 spinner: true
 tags:
     - git
---
<!-- standalone (20.3kb) -->
<link rel="stylesheet" href="../../../../css/loading.css">
<script src="https://unpkg.com/github-api/dist/GitHub.bundle.min.js"></script>



git start 分类
==============

## 目录
### 按语言分类
<ul  id="table_content">
</ul>
### 按功能分类
<ul id="function_content">
</ul>
<div id="sortByLagContent">
</div>



<script>
  (function(){
    var target = document.getElementById('table_content')
    var target1 = document.getElementById('function_content')
    var loading = document.getElementById('loading')
    var postContent = document.getElementById('postContent')
    postContent.classList.add('displayNone')
    var sortByLagContent = document.getElementById('sortByLagContent')

    function insertAfter( newElement, targetElement ){
         var parent = targetElement.parentNode;
         if( parent.lastChild == targetElement ){
        parent.appendChild( newElement, targetElement );
     }else{
        parent.insertBefore( newElement, targetElement.nextSibling );
     }
    }
    function init (){
      var table_content = ['C','C#','C++','JavaScript','Go','HTML','CSS','Java','OCaml','Jupyter Notebook','VimL','Kotlin','Objective-C','PHP','Python','Ruby','Shell','TypeScript','Vue','Others']
      var elementTitleStr = table_content.map(function(value){
        return '<h4 id="'+value+'">'+value+'</h4>'
      })
      var finalArray = table_content.map(function(value){
        return '<li><a href="#'+value+'">'+value+'</a></li>'
      })
      target.innerHTML = finalArray.join(' ')
      sortByLagContent.innerHTML = elementTitleStr.join(' ')
      table_content.forEach(function(targetId){
        var tableId = document.getElementById(targetId)
        var tableElement = document.createElement('div')
        tableElement.className = 'table-responsive'
        tableElement.innerHTML = '<table class="table"><tbody id="'+targetId+'tbody"><tr><td>项目名称</td><td>描述</td><td>语言</td><td>地址</td><td>Stars</td><td>Fork</td><td>更新日期</td></tr></tbody></table>'
        insertAfter(tableElement,tableId)
      })
    }
    init()
    var gh = new GitHub({
     username: 'shekang',
     password: '123mutouren'
    });
    var me = gh.getUser();
    me.listStarredRepos().then(function(data) {
      var allData = data.data
      allData.forEach(function(element){
        postContent.classList.remove('displayNone')
        loading.classList.add('displayNone')
        var elementId = element.language  
        var targetElement = document.getElementById(elementId+'tbody')
        if(targetElement){
          var innerHTMLED = targetElement.innerHTML
          innerHTMLED+='<tr><td>'+element.name+'</td><td>'+element.description+'</td><td>'+element.language+'</td><td><a target="_blank" href="'+element.svn_url+'">'+element.svn_url+'</a></td><td>'+element.stargazers_count+'</td><td>'+element.forks_count+'</td><td>'+element.updated_at+'</td></tr>'
          targetElement.innerHTML = innerHTMLED
        }else{
          var othersElement = document.getElementById('Otherstbody')
          var otherInnerHTML = othersElement.innerHTML
          otherInnerHTML+='<tr><td>'+element.name+'</td><td>'+element.description+'</td><td>'+element.language+'</td><td><a target="_blank"  href="'+element.svn_url+'">'+element.svn_url+'</a></td><td>'+element.stargazers_count+'</td><td>'+element.forks_count+'</td><td>'+element.updated_at+'</td></tr>'
          othersElement.innerHTML = otherInnerHTML
        }
      })
    });


  })()
</script>
