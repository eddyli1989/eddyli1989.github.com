---
layout: post
title: "ActionScript 与 JavaScript 通信"
description: ""
category: ramblings
tags: [ramblings, startups]
---
{% include JB/setup %}

网上有很多帖子了，这里有一个完整的例子
http://help.adobe.com/zh_CN/as3/dev/WS5b3ccc516d4fbf351e63e3d118a9b90204-7cb1.html

按照这里例子搞完，死活还是调用不成功，最后发现了这么一句话

调用 getSWF() JavaScript 函数，返回对表示 SWF 文件的 JavaScript 对象的引用。当 HTML 页中存在多个 SWF 文件时，传递给 getSWF() 的参数决定了返回哪一个浏览器对象。传递给该参数的值必须与 object 标签的 id 属性以及用于包括 SWF 文件的 embed 标签的 name 属性相匹配。


原来在html中声明的object控件必须与embed标签的name属性值一样
坑啊

