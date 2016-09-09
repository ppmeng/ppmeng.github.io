---
title: "《JavaScript DOM 编程艺术》读书笔记（一）"
categories: "JavaScript"
tags: ["JavaScript", "DOM", "读书笔记"]
---

初学JavaScript，最近看了一本备受推荐的书《JavaScript DOM 编程艺术》，书内容很简单但是编程思路很棒，主要讲的是基于DOM编程的思路和原则，强调使用 *JavaScript* 应从开始就规范自己的编程习惯，遵从平稳退化、渐进增强、以用户为中心的设计模式，下面以书中的例子（创建图片库）并基于这个理念来一步步完善代码。

## 初始图片库

用无序列表`ul`来列出图片链接------点击查看[demo](http://codepen.io/ppmeng/pen/oxGmqN)

```html
<!--所用到的初始html结构-->
<body>
    <h1>萌妹子合集</h1>
    <ul>
        <li><a></a></li>
        ...
        <li><a></a></li>
    </ul>
</body>
```

### 已达到成果

点击链接可转到图片

### 存在问题

打开图片后只能看到该图片，看不到清单，且需要借助浏览器 *back* 才可返回

## 增加占位符图片
鉴于初始图片库的缺点做以下改进------点击查看[demo](http://codepen.io/ppmeng/pen/GZMzbm)

```html
<!--html部分 在ul后面增加img作为显示图片的位置并在将onclick事件嵌入每个<a>里面-->
<img src="" id="placeholder" alt="">
```

```javascript
//javascript部分 增加showPic函数修改占位符处图片的src属性
function showPic(whichpic) {
    var placeholder = document.getElementById("placeholder");
    var source = whichpic.getAttribute("href");
    placeholder.setAttribute("src", source);
}
```

### 改进部分
1. 增加占位符 `img` 来作为封面以及接下来显示图片的位置
2. 点击链接时阻止网页默认行为（跳转到图片查看页面）

### 思路以及原理
1. 增加img，仅仅是需要使用 `img`的 `src` 属性来替换列表里面的 `href`，与真正有没有图片并没有关系，所以 `src` 属性的值为空也可以
2. 若仅仅增加 `onclick` 的话点击链接的时候网页默认行为还是会进行（转到图片查看窗口，看不到原始链接列表），所以需要在每个 `onclick` 事件处理函数里面增加 `return:false;`来阻止默认行为被调用，这是因为事件处理函数的处理机制是，一旦事件发生（这里是一旦点击链接），相应的JS代码就会执行，被调用的代码会返回一个值传递给时间处理函数---若为true：事件处理函数认为链接已经被点击,当前操作继续进行（即打开href所指的图片）；若为false：事件处理函数认为链接没有被点击，中断操作，相应的，默认的网页行为就不会再执行
3. 在这个[demo](http://codepen.io/ppmeng/pen/GZMzbm)里面，我采取的是直接在 `a` 标签里面增加`onclick="showPic(this);return：false;"`， `this` 这个关键字在这里是指这个a对象的意思，后面加的 `return`语句也可以内置到 *showPic* 函数里面，但是需要将 `onclick` 事件处理函数改为`onclick="return showPic(this)"`（相对而言没有分开写返回值直观），原理同上，`return` 值决定了执行完 *showPic* 函数之后操作是否继续进行，参考[JS时间处理函数中`return`的作用](http://blog.csdn.net/gchonghavefun/article/details/8112830)

### 存在问题
1. 需要在每个链接里面增加事件处理函数的，比较麻烦且不利于修改
2. `onclick` 内嵌在HTML里面，没有真正实现分离JavaScript
3. 之前写过一个[纯css实现tab切换](http://blog.qiji.tech/archives/7967)，相应的，这个图片库也可以用文中提到的方法实现，并且还可以将显示的图片封装在一个 *DIV* 里面并添加描述，那如何用JS给每个图片添加描述，在切换图片的时候实现文本切换呢，让我们继续往下看

## 添加文本切换以及CSS样式
点击查看这个版本的[demo](http://codepen.io/ppmeng/pen/reGbVM)

```javascript
//JavaScript增加部分---实现文本切换
var description = document.getElementById("description");
var text = whichpic.getAttribute("title");
description.firstChild.nodeValue = text;
```

### 改进部分
1. 增加对图片的描述并实现点击链接后图片进行切换
2. 增加一些简单的样式使页面更协调
3. 平稳退化，确保网页在没有JS情况下也能正常工作

### 思路以及原理
1. 首先想到的是根据图片切换的原理来增加文本切换，即需要一个文本占位符，故在 `img` 后面增加一个 `p` 并把 *id* 设为 description；需要给每个图片增加一个属性来保存文本，故给每个 `a` 增加一个属性 `title` 并将值写为对于图片的描述
2. 样式部分不做介绍，主要的改动是在JS部分，唯一需要解释的就是最后一句`description.firstChild.nodeValue = text;`，其实很简单，nodeValue是指节点值，而元素节点是没有值的，只有属性节点和文本节点有值，`description` 是元素节点，里面的文字描述是既是他的子节点也是文本节点，故需使用 `firstChild`属性来获取文字描述，也可以更改最后一句为 `description .innerHTML = text;`（innerHTML返回该节点所有的子节点及其值，参考[nodeValue和innerHTML区别](https://www.zhihu.com/question/30728623/answer/49204607)）

### 存在问题
1. 仍旧未完全将网页结构和JavaScript脚本的动作行为分开
2. 向后兼容，确保老版本的浏览器不会因为JS脚本而挂掉

## 分离JavaScript并实现向后兼容

点击查看这个版本的[demo](http://codepen.io/ppmeng/pen/yOzWOP)

```javascript
//JavaScript添加部分
function prepareGallery() {
    if (!document.getElementsByTagName) return false;
    if (!document.getElementById) return false;
	if (!document.getElementById("imagegallery")) return false;
	var gallery = document.getElementById("imagegallery");
	var links = gallery.getElementsByTagName("a");
	for (var i = 0; i < links.length; i++) {
		links[i].onclick = function () {
			showPic(this);
			return false;
		}
    }
}
window.onload = prepareGallery;
```

### 改进部分
1. 将网页结构和JavaScript脚本分离开
2. 新函数实现向后兼容，确保老浏览器不会出错

### 思路以及原理
1. 在前几个版本里面，大部分的JavaScript脚本都在外置的JS文件里面，只有一个事件处理函数 *onclick*　内嵌在HTML文件里面，要实现脚本分离则需删除内嵌的 *onclick* 并在外置文件中实现相应操作
2. 解决思路：首先需要找出所有的 `a` ，然后对每一个 `a` 添加一个事件处理函数；
3. 方法原理： 找出所有的 `a` 采用的是`document.getElementsByTagName("a")` ,为方便书写将其赋给 *links*，然后在找到的 *links* 数组里面采用 *for* 循环，对于每一个 `a` 即 *links[i]* 加载事件处理函数（代码8-12行），跟之前提到的一致，因为需要在执行完 *showPic* 函数之后阻止网页默认行为故返回 *false*
4. 实现上面的解决思路之后发现，点击链接后仍然没有在指定位置切换图片，仔细查看代码，原来是忘记执行 *prepareGallery* 函数了，这真是一个愚蠢的错误。因为在 *prepareGallery* 函数的3-5行代码里面我们做了几个检测来确保老浏览器不会出错，所以 *prepareGallery* 必须在整个网页加载完毕之后立刻执行，而网页加载完毕时会触发一个 *onload* 事件（这个时间与window对象相关联）， 参考[window.onload用法详解](http://www.softwhy.com/forum.php?mod=viewthread&tid=6191), 选了一个最简单的语句`window.onload = prepareGallery;`解决了这个问题

### 存在问题
1. *showPic* 函数没有进行任何的测试和检查
2. 若在网页完全加载之后执行多个函数的话，按照上面的方法对 *onload* 重复赋值则前面的会被替换只留最后一个事件在网页加载完毕后执行，[window.onload用法详解](http://www.softwhy.com/forum.php?mod=viewthread&tid=6191) 里面有提到将多个事件绑定到`window.onload`事件之后的方法故不再赘述

## 不做假设，全部检测，结构和行为分开
点击查看这次的[demo](http://codepen.io/ppmeng/pen/zqEVKY)

```javascript
//JavaScript增加部分
function preparePlaceholder() {
	if (!document.createElement) return false;
	if (!document.createTextNode) return false;
	if (!document.getElementById) return false;
	if (!document.getElementById("imagegallery")) return false;
	var placeholder = document.createElement("img");
	placeholder.setAttribute("id", "placeholder");
	placeholder.setAttribute("src", "http://v1.qzone.cc/pic/201410/02/17/58/542d21c62def9426.jpg%21600x600.jpg");
    placeholder.setAttribute("alt", "图片集封面");
    var description = document.createElement("p");
    description.setAttribute("id", "description");
    var des_text = document.createTextNode("任意点击图片欣赏萌妹子");
    description.appendChild(des_text);
    var gallery = document.getElementById("imagegallery");
    insertAfter(placeholder, gallery);
    insertAfter(description, placeholder);
}

function insertAfter(newElement, targetElement) {
	var parent = targetElement.parentNode;
	if (parent.lastChild === targetElement) {
		parent.appendChild(newElement);
	}
	else {
		parent.insertBefore(newElement, targetElement.nextSibling);
	}
}
addLoadEvent(preparePlaceholder);
```

### 改进部分
1. 原始的HTML文件中占位符的存在仅仅是为了给 *showPic* 函数提供属性，故将其直接在JavaScript实现，和HTML结构分开
2. 对showPic所使用的DOM方法进行检测
3. 替换将事件绑定到`window.onload` 方法，便于后期修改以及维护

### 思路以及原理
1. 改进的后两点前面的修改版本里面已经提及，故此次修改主要是增加了两个函数，*preparePlaceholder()* 是用来创建占位符 *placeholder*，`insertAfter(newElement, targetElement)` 是用来将*placeholder*正确的插入图片链接之后，第一个主要是一些DOM方法和属性的使用，不在讲解；主要的部分在于第二个函数
2. DOM只有 *insertBefore* 没有 *insertAfter*  方法，故在这里另外写了一个函数`insertAfter(newElement, targetElement)`来实现。原理很简单，就是先检查目标元素的位置是不是在父元素的最末尾，如果是，直接对父元素使用 `parent.appendChild(newElement);` 插入新元素，若不是，则在目标元素的兄弟节点之前使用 *insertBefore* 插入新元素



## 参考资料
>- [JS时间处理函数中return的作用](http://blog.csdn.net/gchonghavefun/article/details/8112830)
>- [nodeValue和innerHTML区别](https://www.zhihu.com/question/30728623/answer/49204607)
>- [window.onload用法详解](http://www.softwhy.com/forum.php?mod=viewthread&tid=6191)
>- [JavaScript DOM编程艺术 （第2版）](https://book.douban.com/subject/60)