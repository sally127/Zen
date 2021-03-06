---
layout: post
title:  JavaScript 作用域链
description: 
             先说几个概念：<strong>函数</strong>、<strong>执行环境</strong>、<strong>变量对象</strong>、<strong>作用域链</strong>、<strong>活动对象</strong>。这几个东东之间有什么关系呢，往下看~
tags: 
  - javascript
  - 前端
show: true
---

##1.几个概念
先说几个概念：**函数**、**执行环境**、**变量对象**、**作用域链**、**活动对象**。这几个东东之间有什么关系呢，往下看~

###函数
函数大家都知道，我想说的是，js中，在函数内部有两个特殊的对象：`arguments` 和 `this` 。 `arguments` 是一个类数组对象，包含着传入函数中的所有参数。 `this` 引用的是函数据以执行的环境对象。

###执行环境
在《js高级程序设计》中，是这样定义的：

> 执行环境定义了变量或函数有权访问的其他数据，决定了它们各自的行为。

这里要特别提到一个执行环境——全局执行环境。全局执行环境是最外围的执行环境，在web浏览器中，全局执行环境被认为是window对象。全局执行环境会一直存在于环境栈的最底端，直到关闭网页或者浏览器。
执行环境也叫执行上下文。

###变量对象
《js高级程序设计》定义如下：

> 每个执行环境都有一个与之关联的**变量对象**，环境中定义的所有变量和函数都保存在这个对象中。

由上可以看出两点：1.执行环境和变量对象是一一对应的。2.执行环境其实是一个“虚”的概念，而变量对象是实际存在的对象，能够被解析器访问到。不严谨的说，为了访问执行环境，就创造了变量对象这个东东，通过变量对象就可以访问执行环境中的所有变量和函数了，它俩其实是一个东西，只不过一个是虚的，一个是真实存在的。

当一个执行环境中的所有代码执行完毕后，该执行环境被销毁，保存在其中的所有变量和函数定义也随之销毁。其实是这个执行环境对应的变量对象被销毁。发现很多地方都把执行环境和变量对象混谈，大家似乎很少提到变量对象，都用 “执行环境” 这四个字替代了，把执行环境说成了一个对象，没办法，谁让说的是一个东西呢。

####变量对象补充

当JS执行流进入函数时，JavaScript引擎在内部创建一个对象，叫做Variable Object。
对应函数的每一个参数，在Variable Object上添加一个属性，属性的名字、值与参数的名字、值相同。
函数中每声明一个变量，也会在Variable Object上添加一个属性，名字就是变量名，因此为变量赋值就是给Variable Object对应的属性赋值。
在函数中访问参数或者局部变量时，就是在variable Object上搜索相应的属性，返回其值。
一般情况下Variable Object是一个内部对象，JS代码中无法直接访问。

###作用域链

> 作用域链的用途：保证对执行环境有权访问的所有变量和函数的 *有序* 访问。
当代码在一个执行环境中执行时，就会创建变量对象的一个作用域链。

我的理解是，作用域链是由一个一个变量对象链接起来的一个链，整个作用域链构成了当前执行环境中变量和函数可访问的范围，即作用域。由于变量对象是按一定顺序链接在一起的，所以就达到了对所有可访问变量、函数有序访问的效果。那么它们是按怎样的顺序链接成作用域链的呢？这就要说到最后一个概念——活动对象。

###活动对象

> 当函数运行时就会为其创建一个活动对象，其中包含形参和函数特殊的arguments对象。活动对象之后会做为函数执行环境的变量对象来使用。

回到之前的问题，作用域链中的变量对象是如何排序的呢？
作用域链的前端，始终都是当前执行的代码所在环境的变量对象。但如果这个环境是函数，则将其活动对象作为变量对象，放在其作用域链的前端。作用域链中的下一个变量对象来自包含环境，而再下一个变量对象来自下一个包含环境……这样一直延续到全局执行环境。全局执行环境的变量对象始终是作用域链中的最后一个变量对象。

为什么执行环境是函数会有这样特殊的规定呢？
《JS权威指南》中有一句很精辟的描述：

> JavaScript中的函数运行在它们被定义的作用域里，而不是它们被执行的作用域里。

按照之前所说，在函数定义的作用域里，当前执行环境的作用域链上是没有该函数的活动对象的，为了访问函数内部的变量、函数，所以要将其活动对象插在当前作用域链的前端。

##2.它们之间的关系

 - 每个函数都有自己的执行环境，当执行流进入一个函数时，函数的环境就会被推入一个环境栈中。而在执行后，栈将其环境弹出，把控制权返回给之前的执行环境。
 - 综上，每个函数对应一个执行环境，每个执行环境对应一个变量对象，而多个变量对象构成了作用域链，如果当前执行环境是函数，那么其活动对象在作用域链的前端。


----------

##3.其他补充
JS的语法风格和 C/C++ 类似, 但作用域的实现却和 C/C++ 不同，并非用“堆栈”方式，而是使用列表，具体过程如下(ECMA262中所述):

任何执行上下文时刻的作用域, 都是由作用域链(scope chain)来实现.
在执行func的定义语句的时候, 会创建一个这个函数对象的[[scope]]属性(内部属性,只有JS引擎可以访问)，并将这个[[scope]]属性链接到定义它的作用域链上。
在调用func的时候, 会创建一个活动对象，然后将调用参数赋值给形参数，对于缺少的调用参数，赋值为undefined。然后将这个活动对象做为scope chain的最前端, 并将func的[[scope]]属性所指向的，定义func时候的顶级活动对象，加入到scope chain.


----------

##参考资料
- 《js高级程序设计》 P73  4.2 执行环境及作用域

- <a href="http://www.laruence.com/2009/05/28/863.html" target="_blank">鸟哥：JavaScript作用域原理</a>

- <a href="http://www.cnblogs.com/lhb25/archive/2011/09/06/javascript-scope-chain.html" target="_blank">JavaScript 开发进阶：理解 JavaScript 作用域和作用域链</a>
