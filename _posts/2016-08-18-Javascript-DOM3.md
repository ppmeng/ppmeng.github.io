---
title: "《JavaScript DOM 编程艺术》读书笔记（三）"
excerpt_seoarator:
categories: "JavaScript"
tags: ["JavaScript", "DOM", "读书笔记"]
---


前面提到了使用JavaScript来修改文档的样式信息，但是随着浏览器的的不断升级更新，CSS的兼容性以及功能也越来越强大，所以总的来说使用CSS来改变样式依旧是最佳的选择，但是要想达到随着时间的变化而不断改变元素样式的话，CSS就没有办法了，而JavaScript可以很完美的实现，其中动画就是随着时间不断改变样式（元素位置）的例子之一
##简单动画之定时改变元素位置
点击查看一个甜蜜的demo------[demo](http://ppmeng.github.io/somedemo/JavaScript/animation/kiss/kiss.html)
这个小动画就是按照随着时间不断改变元素位置的思想完成的，脚本很简单，具体代码查看demo，下面是核心代码：
```
//采用绝对定位以及setTimeout来定时改变元素位置
function moveElement(element, final_x, final_y, interval) {
	var elem = document.getElementById(element);
	var xpos = parseInt(elem.style.left);
	var ypos = parseInt(elem.style.top);
	if (xpos == final_x && ypos == final_y) return true;
	if (xpos < final_x) xpos++;
	if (ypos < final_y) ypos++;
	if (xpos > final_x) xpos--;
	if (ypos > final_y) ypos--;
	elem.style.left = xpos + "px";
	elem.style.top = ypos + "px";
	var repeat = function() {
		moveElement(element, final_x, final_y, interval);
	}
	setTimeout(repeat, interval);
}
//由于style只能返回内嵌样式，所以只能采用DOM初始化元素位置并调用moveElement()函数
function positionmessage() {}
```
##实用的动画
现如今，不光是JavaScript还有很多CSS3的属性可以做出来动画的效果，但是动画元素过多不仅容易引起用户的不满，而且会导致出现一些可访问性的问题，作者引用了W3C里面:
>- 除非浏览器允许用户“冻结”移动的内容，否则就应该避免让内容在页面中移动。如果页面上有移动的内容，就应该使用脚本或者插件的机制允许用户冻结这种移动或者动态更新行为

也就是说我们应该只在必须使用动画的时候或者用户可以自己控制动画的时候使用动画。
下面采用的实例是一个比较常见比较有用的动画，经常会在一些电商网站，类似taobao，JD之类的看到，效果类似首页中间的大广告一直在不停的切换，或者鼠标放上去就可以切换图片，这时就应该选择JavaScript脚本来实现动画。这里我们采用的实例是在一个包含链接的网页上面，实现用户鼠标扫过链接时预览链接对应的图片
首先我们需要完成大致的html结构，包含一个标题一个简短的段落以及一个有3个列表项的有序列表，非常简单的结构
```
<h1></h1>
<p></p>
<ol>
    <li><a></a></li>
    ····
</ol>
```

###初始版本
回想一下之前做过的DOM练习，发现这个和构建图片库很相似，好像只需要把点击操作换**onmouseover**,仿照之前的例子来实现的话首先需要建立一个占位符，可以采取之间在 *ol* 之后添加一个图片的标签或者在脚本里面创造标签，基于分离的思想当然后者更好，但是首先我们要让程序先跑起来嘛~~~所以，偷懒使用第一种~来看我的demo------[demo](http://codepen.io/ppmeng/pen/mPXWeB)
核心代码如下：
```
function prepareSlideshow() {
	//为图片应用样式以便后期使用
	var preview = document.getElementById("preview");
	preview.style.position = "absolute";
	//位置初始值
	preview.style.top = "0px";
	preview.style.left = "0px";
	//获取所有链接
	var linklist = document.getElementById("linklist");
	var links = linklist.getElementsByTagName("a");
	links[0].onmouseover = function() {
		moveElement("preview", -100, 0, 10);
	}
	links[1].onmouseover = function() {
		moveElement("preview", -200, 0, 10);
	}
	links[2].onmouseover = function() {
		moveElement("preview", -300, 0 , 10);
	}
}
```
乍一看好像还不错，至少当我鼠标扫过每个链接的时候图片在切换，这里需要注意的是：
1. 如果每个链接对应不同的图片的时候，图片在加载的时候即使是高速网络也不免得有延迟。为避免在切换不同的图片的时候出现延迟而使得动画不够流畅，首先使用photoshop将图片拼接到一起在使用，其次为了可以分别显示每个图片则可以再img外围加一个div并设置其宽高，然后设置其溢出表现 **overflow：hidden；**，每个图片的位置则采用绝对定位的方法将图片移动并显示
2. 获取每一个 **li**，然后在图片上面确定扫过每个 **li** 之后图片的位置；为达到动画效果采用 **moveElement** 函数
嗯，还不错，可是当我扫了几下链接之后，发现图片在颤抖。。这是为什么呢？

###改进版本
因为鼠标移动过快造成颤抖所以可以确定一定是由于 **moveElement** 函数造成的，所以我们先来分析一下 **moveElement** 函数里面的构成。

-  **movuElement** 函数里面我们使用了 **setTimeout** 。然后里面我们递归调用 **moveElement** 函数来实现动画效果，这里我们有必要了解一下 **setTimeout** 函数的使用，参考[WindowTimers.setTimeout()](https://developer.mozilla.org/en-US/docs/Web/API/WindowTimers/setTimeout)和[setTimeout何时被调用执行
](http://www.cnblogs.com/dolphinX/archive/2013/04/05/2784933.html)来涨涨姿势，简单讲就是他是window的一个方法，可以直接使用，含两个参数，一个是重复调用的函数名，第二个参数是调用间隔时间
- 首先我们要明确JavaScript是运行在单线程环境中的，意味着鼠标悬停的操作过快的时候，**setTimeout**会把调用的事件累计在队列里面等待执行，当我们一直移动鼠标的时候，等待的事件就会越来越多，然后在进程空闲的时候一件一件执行造成图片好像是在颤抖的现象，但是我们想要达到的效果是，当我们移动鼠标的时候，最后一件事件的优先级是要高于前面的事件的，具体在这个实例里面就是每次调用**moveElement**函数的时候可以先把之前的**moveElement**事件清除，由于调用的时候我们采用的是将**moveElement**包含在**setElement**函数里面的，所以可以想到使用**clearTimeout**来清除累积事件
- 到这里我们应该就明白该将**setTimeout**用一个名字来标识，在下一次调用**moveElement**的时候先使用**clearTimeout**来清除之前移动事件。首先很容易理解这个名字不能使局部变量，因为若是局部变量的话意味着在**clearTimeout** 函数上下文中不存在，也就没有办法使清除操作进行；其次如果在函数外部定义一个全局变量来使用的话，就会出现一个问题，明明我们全部的操作都是在一个函数内部而不是很多函数都会使用，那么使用全局变量就有点大材小用。我们需要一个可以在**moveElement**函数内部当全局存在的局部变量，听起来好像是有点怪。。但这种变量我们一直都有使用，就是“属性”。
- 使用自定义的属性来命名函数是作者想到的一个很独特的方法，给移动的元素传建一个独特的属性来标识函数，下面就是改进后的核心代码啦~对了，附赠一个小小的demo------[点击查看demo](http://ppmeng.github.io/somedemo/JavaScript/animation/changeimg/second/2.html )

```
function moveElement(element, final_x, final_y, interval) {
	if (!document.getElementById) return false;
	if (!document.getElementById(element)) return false;
	var elem = document.getElementById(element);
	//给elem增加一个属性来使每个元素在移动前都获得一个名为movement的属性来使img复位
	if (elem.movement) {
		clearTimeout(elem.movement);
	}
	//验证elem是否存在left和top属性，若没有该属性就直接赋值为初始值，此时可以将之前的赋值删去
	if (!elem.style.left) elem.style.left = "0px";
	if (!elem.style.top) elem.style.top = "0px";
 	var xpos = parseInt(elem.style.left);
	var ypos = parseInt(elem.style.top);
	var dist = 0;
	if (xpos == final_x && ypos == final_y) {
		return true;
	}
	if (xpos < final_x) {
		dist = Math.ceil((final_x - xpos)/10);
		xpos += dist;
	}
	if (ypos < final_y) {
		dist = Math.ceil((final_y - ypos)/10);
		ypos += dist;
	}
	if (xpos > final_x) {
		dist = Math.ceil((xpos - final_x)/10);
		xpos-= dist;
	}
	if (ypos > final_y) {
		dist = Math.ceil((ypos - final_y)/10)
		ypos-=dist;
	}
	elem.style.left = xpos + "px";
	elem.style.top = ypos + "px";
	var repeat = function() {
	moveElement(element, final_x, final_y, interval);
	}
	//第一次移动之后elem即可获得movement属性
	elem.movement = setTimeout(repeat, interval);
}
```
在这里我做了三处改变，一个就是修复之前的问题，另一个关于改变移动速度，最后给移动元素检验了是否含有left和top属性。

- 我们先继续谈一下第一个改变。示例代码里面可以看到给移动节点元素自定义了一个属性为**movement**，在首次调用**moveElement**函数时这个属性被创建，然后只要页面被载入，节点元素就一直存在于DOM文档树中，所以其属性可以当做全局变量来使用。然后在每次移动元素前都要确定元素是否含有**movement**属性，如果有就清除之前的移动操作再移动，没有的话就继续执行。这就保证了即使用户快速移动鼠标，实际执行的也只有一个**setTimeout**调用语句，不会出现累积事件也就不会再有之前的颤抖现象，更加符合直觉
- 第二个改变是加了一个变量 *dist* 作为移动距离，大小为剩余距离的十分之一，这样每次移动的时候开始速度就会非常快然后渐渐平缓，给人的感觉是图片切换更加迅速，引用了**Math.ceil()**方法，除此之外还有**Math.floor()**，**Math.round()**等等，选用**Math.ceil()**向上取整可以保证元素可以尽快到达目的地，是数学领域的问题了，这里没有什么难理解的地方，想想就明白了，参考[Math.ceil](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/ceil)， [Math.floor](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/floor)， [Math.round](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/round)
- 第三个添加检验可以保证所有脚本在没有假设的前提下进行，保证即使一部分脚本丢失也不会出粗，直觉上可以直接使用if，若没有这些属性就结束运行，但是我采用的是若没有就赋值为初始值，这样就可以直接把之前元素属性赋值的地方把关于这两个属性的语句删去

###最终版本
开始时我偷懒在HTML结构里面创建了 *div* 和 *img* ，鉴于有一部分用户可能会吧JavaScript脚本屏蔽，那HTML里面加的元素就没有任何用处了，不如在脚本里面创建，点击查看这个最终版本的demo-----[最终版本](http://ppmeng.github.io/somedemo/JavaScript/animation/changeimg/final/index.html)
核心代码如下：
```
var imgelem = document.createElement("img");
imgelem.setAttribute("id", "preview");
imgelem.setAttribute("src", "../img/donghua.png");
imgelem.setAttribute("alt", "beauty girl");
	
var divelem = document.createElement("div");
divelem.setAttribute("id", "slideshow");
divelem.appendChild(imgelem); 

var linklist = document.getElementById("linklist");
insertAfter(divelem, linklist);
```
insertAfter函数之前已经[第一篇](http://www.cnblogs.com/ppmeng/p/5369135.html)最后一部分讲到过，其余的就是DOM的核心方法了，可以把移动元素的其他CSS属性放置在外部CSS文件里面，这样每一部分的用处更加明显
##总结
编者语：实现动画效果并不难，难在在实践中该不该使用动画。同样，写代码并不难，难在写出大家都看得懂的代码。
书中使用了这样一条语句：
`var repeat = "moveElement('"+elementID+", "+final_x+", "+final_y+", "+interval+"')"`,怎么说呢，括号里面是在拼接字符串，又是单引号又是双引号的很容易出错，而且还有很多人不理解，参考[提问](http://bbs.csdn.net/topics/391905753?page=1)，可以改写成
```
elem.movement = setTimeout(function(){
    moveElement(elementID,final_x,final_y,interval);
},interval);
```
或者更加清楚明了的
```
var repeat = function() {
moveElement(element, final_x, final_y, interval);
}
elem.movement = setTimeout(repeat, interval);
```
嗯，愿我们都能遵守规范，写出清楚易懂的代码，减少不必要的脚本和不那么需要的动画，让世界更美好~
