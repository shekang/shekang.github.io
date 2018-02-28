---
layout:     post
title:      "在Atom中添加对TypeScript的支持"
author:     "Kerwin She"
header-img: "img/post/atom-typescript-bg.jpg"
tags:
    - typescript
    - javascript
---

------------------------
# 在Atom中添加对TypeScript的支持

1. 首先确保Atom中的`language-typescript`是开启的，可以在File - setting - Packages 中找到

2. 安装 `atom-typescript` 包,最简便的方法直接在 File - setting - Packages - Install 中搜索然后安装,如下图:

![atom-typescript]({{site.url}}/img/post/atom-typescript-1.png)

安装完成之后,当你编辑`.ts`文件时在报错的地方就会有这样的提示,你就能及时改正拉
![error]({{site.url}}/img/post/atom-typescript-2.png)

最后附上`atom-typescript`相关快捷键说明,这些有在包的`setting`中找到或者直接到[这里看](https://atom.io/packages/atom-typescript)

Keystroke| Command |  Instruction
--------   | -----:   | :----:
`alt+cmd+l `| typescript:format-code | 格式化代码
`ctrl+alt+l`|	typescript:format-code | 格式化代码
`f2`|typescript:rename-refactor | 暂时不知道有什么用
`shift+f12`|typescript:find-references | 查找不同
`f6` | 	typescript:build | 快速编译当前的`.ts`文件
`f10`|	typescript:return-from-declaration |回到上一处声明的地方
`f12`|	typescript:go-to-declaration |回到第一个声明的i放
