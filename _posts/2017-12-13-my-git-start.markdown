---
 layout: post
 title: "git start 分类"
 date: 2017-12-13
 author: "Kerwin She"
 fn: ""
 tags:
     - git
---
<!-- standalone (20.3kb) -->
<style>

@-webkit-keyframes spinner {
  from {
    -webkit-transform: rotate(0deg);
            transform: rotate(0deg);
  }
  to {
    -webkit-transform: rotate(360deg);
            transform: rotate(360deg);
  }
}

@keyframes spinner {
  from {
    -webkit-transform: rotate(0deg);
            transform: rotate(0deg);
  }
  to {
    -webkit-transform: rotate(360deg);
            transform: rotate(360deg);
  }
}

.postSpinner {
  position: absolute;
  width: 34px;
  height: 34px;
  top: 0%;
  left: 50%;
  margin-left: -17px;
  margin-top: -17px;
}

.postSpinner:after, .postSpinner:before {
  content: '';
  position: fixed;
  border: 2px solid #337ab7;
  width: 30px;
  height: 30px;
}

.postSpinner:after {
  -webkit-animation: spinner 2.5s linear infinite;
          animation: spinner 2.5s linear infinite;
}

.postSpinner:before {
  width: 44px;
  height: 44px;
  margin-left: -7px;
  margin-top: -7px;
  -webkit-animation: spinner 2.5s linear infinite;
          animation: spinner 2.5s linear infinite;
  animation-direction: reverse;
}
</style>
<script src="https://unpkg.com/github-api/dist/GitHub.bundle.min.js"></script>
<script src="https://cdn.bootcss.com/lodash.js/4.17.4/lodash.min.js"></script>

git start 分类
==============
<div class="postSpinner"></div>
## 目录
### 按语言分类
<ul class="displayNone" id="table_content">
</ul>
### 按功能分类
<ul class="displayNone" id="function_content">
</ul>





<script>
  (function(){
    var target = document.getElementById('table_content')
    function init (){
      var table_content = ['C','C#','C++','Javascript','Go','HTML','CSS','Java','OCaml','Jupyter Notebook','Kotlin','Objective-C','PHP','Python','Ruby','Shell','Typescript','Vim script','Vue']
      var finalArray = table_content.map(function(value){
        return '<li><a href="#'+value+'">'+value+'</a></li>'
      })
      target.innerHTML = finalArray.join(' ')
    }
    init()
    var gh = new GitHub({
     username: 'shekang',
     password: '123mutouren'
    });
    var me = gh.getUser();
    me.listStarredRepos().then(function(data) {

    });
  })()
</script>
