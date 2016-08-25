---
title: "《JavaScript DOM 编程艺术》读书笔记（二）"
categories: "JavaScript"
tags: ["JavaScript", "DOM", "读书笔记"]
---


一般而言，在网页设计的时候，我们应该选择最适用的工具去解决问题，具体来讲就是使用**HTML**去搭建文档的结构，使用**CSS**去设计文档的呈现效果，使用**JavaScript**去实现文档的交互行为，但是在这三者之间并不是完全的相斥而是有一部分的重叠区域，比如使用DOM里面的 *createElement* 和 *appendChild* 可以改变网页的结构，比如由于每个元素节点都有属性*style*，改变元素节点的这个属性即可获得和CSS一致的效果，并且在CSS中使用`：hover` 这些伪类可以触发和使用JavaScript中 *onmouseover* 等相似的响应事件，所以我们不仅需要一一掌握这三者的具体使用还需要了解他们之间的联系，以便在具体实践中选择最适合的方法解决问题

上周在[理解DOM脚本编程背后的艺术（一）](http://ppmenghome.com/javascript/Javascript-DOM1/)里面以创建图片库的形式讲解了JavaScript中一些主要的DOM操作，那这次就主要使用上周已经掌握的DOM操作，设计一些具体实例，来分析 **JavaScript** 和 **HTML**，**CSS** 的联系以及使用

## JavaScript之充实文档的内容
为保证在最坏的情况下（比如浏览器版本太旧或者JS脚本被禁止）用户依然可以理解网页主要内容，我们要在渐进增强和平稳退化的思想上设计网页，将网页的主要结构以及内容用**HTML**直接显示，但是在下面两种情况下我们不得不使用**JacaScript**来改变网页内容

### 将“不可见”变为“可见”
HTML中绝大多数属性在网页中是不显示的，少数可显示的属性（eg：title）在不同的浏览器里面显示的状态也不一样，假如没有JavaScript，属性的显示问题上面我们就只能屈服于浏览器，但是现在我们可以使用一些简单的DOM操作将属性显示问题掌握在自己手里，下面以具体实例(将HTML中所有缩写词显示在文档最后的列表中)来讲解如何将不可见的属性变为可见
点击查看demo------[demo](http://codepen.io/ppmeng/pen/dMdMzQ)

**1. 获取将要显示的属性节点**

```javascript
var abbreviations = document.getElementsByTagName("abbr");
if (abbreviations.length == 0) return false;
var defs = new Array();
//遍历所有缩略词
for (var i = 0; i < abbreviations.length; i++) {
    var current_abbr = abbreviations[i]
    //兼容低版本IE（IE6）
    if (current_abbr.childNodes.length < 1) continue;
    var definition = current_abbr.getAttribute("title");
    var key = current_abbr.lastChild.nodeValue;
    defs[key] = definition;
}
```

**2. 创建标记**

其中创建定义标题和定义描述里面的文本节点时采用了两种不同的方法，一种是直接创建文本节点**createTextNode**再添加到元素节点，另一种直接使用**innerHTML**赋值,两种方法都可以，相对而言采用innerHTML代码量比较少

```javascript
for (var key in defs) {
    //创建定义标题
	var dtitle = document.createElement("dt");
	var dtitle_text = document.createTextNode(key);
    dtitle.appendChild(dtitle_text);
    //创建定义描述
	var ddesc = document.createElement("dd");
	ddesc.innerHTML = defs[key];
    //添加到定义列表
	dlist.appendChild(dtitle);
	dlist.appendChild(ddesc);
}
```

**3. 添加到指定位置**

```javascript
//若dl没有子节点立即退出
if (dlist.childNodes.length < 1) return false;
//创建定义列表标题
var abbrheader = document.createElement("h2");
var header_text = document.createTextNode("Abbreviations");
abbrheader.appendChild(header_text);
//添加到body之后
document.body.appendChild(abbrheader);
document.body.appendChild(dlist);
```

### 响应用户的操作增加内容
点击查看这个部分的demo------[demo](http://ppmeng.github.io/baidu.IFE2016/task2/task2-4/task2-4.html),
示例采用[百度前端技术学院任务十六](http://ife.baidu.com/task/detail?taskId=16)，简单讲就是把用户输入的文本采用表格的形式添加到文档body之后，并且要完成一些验证以及添加删除按钮，验证部分采用正则仔细分析发现，这个部分和前面例子大同小异，都是要先获取节点，然后创建标记，最后添加至指定位置，所以不再赘述，接下来主要讲一下如何在用户点击表格列中的“删除”按钮之后，删掉那一行的数据

```javascript
function delBtnHandle(button) {
    var clicktr = button.parentNode.parentNode;
    var city = clicktr.childNodes[0].innerHTML;
    delete aqiData[city];
    renderAqiList();
}
aqi_table.addEventListener("click", function(event) {
    if (event.target && event.target.nodeName.toLowerCase() === "button") {
        delBtnHandle(event.target);
    }
})
```

要添加删除操作，首先要找到删除按钮所在的行（`clicktr = button.parentNode.parentNode;`），接着找到第一个td对应的innerHTML即aqiData对应的数组下标并删除，最后调用 *renderAqiList()* 函数重绘表格
删除操作完成后，需要绑定按钮和函数，这里采用*addEventListener*，参考[addEventListener](http://www.runoob.com/jsref/met-element-addeventlistener.html),按要求使用并实现兼容

## JavaScript之改变样式
许多DOM属性是只读的，比如previousSibling，nextSibling，parentNode，firstChild，lastChild等等，我们只能使用他们获取信息但是不能用来设置或者更新信息，但是style对象的各个属性都是可读写的，因此我们可以很方便的使用DOM来获取改变样式。 **注意：1.style属性的属性值永远是字符串。2.style只能返回内嵌样式，即只有把样式插入到标记里面才可以使用 DOM style属性来查询信息**

### 根据元素在节点树里面的位置设置样式
CSS2里面有：first-child等，CSS3引入有：nth-of-type()之类的位置选择器，相较之前来说根据位置设置样式已经简单很多了，但是对于CSS3，还是有很多属性浏览器并不支持，鉴于CSS还无法根据元素之间的相对位置找出某个特定的元素（例如设置指定标记的下一个元素节点样式），但是DOM在这方面非常简单，因此我们还是需要掌握使用DOM来设置样式
点击查看------[demo](http://codepen.io/ppmeng/pen/BKYLVv)

```javascript
function styleHeaderSiblings(node) {
    if (!document.getElementsByTagName) return false;
    var elemlist = document.getElementsByTagName(node);
    var elem;
    for (var i = 0; i < elemlist.length; i++) {
    	elem = getNextElement(elemlist[i].nextSibling);
    	elem.style.·······
    }
}
function getNextElement(node) {
	if (node.nodeType == 1) return node;
	if (node.nextSibling) return getNextElement(node.nextSibling);
    else return null; 
}
//addLoadEvent(styleHeaderSiblings("h1"));调用addLoadEvent函数改变每个h1标签后面的第一个兄弟节点样式
```

### 根据某种条件反复设置某种样式
以表格不同行切换颜色实现斑马线效果为例，点击查看demo------[demo](http://codepen.io/ppmeng/pen/xVYEJq)

[demo](http://codepen.io/ppmeng/pen/xVYEJq)里面我采用的是获取所有的table里面的tr，设置一个布尔变量odd，从第一个tr开始，若该tr对应着的odd为true，则使用类odd里面所示的样式，然后将odd值设为false，以此类推，当odd为false时，不改变样式仅仅将odd设为true，随着odd值交替改变即可达到交替改变tr样式的目的

除了可以分别设置奇数行和偶数行样式的办法可以实现斑马线效果之外（工作量大且不易修改），我们还可以使用CSS3的nth-child选择器来达到一致的效果并且所需代码量极少（如果不考虑IE8以及更早版本的话这个就是最优的方法了~），如下所示：

```css
tr:nth-child(odd) {
	background: #ffc;
}
tr:nth-child(even) {
	background: #fff;
}
```


### 响应事件
依旧以表格为例，鼠标移过每行元素时改变字体样式，点击查看------[demo](http://codepen.io/ppmeng/pen/RaQGBy)

[demo](http://codepen.io/ppmeng/pen/RaQGBy)里面采用 `onmouseover` 来代替`：hover`，其实使用 `：hover` 时仅仅需要在tr的样式里面添加 `tr:hover` 并在这个类里面添加字体样式即可，比DOM要简单得多。 之所以还要使用DOM是因为伪类`：hover`除了在改变链接的样式时得到了绝大多数浏览器的支持，在其他元素上依然还是有一部分浏览器不支持(但是[W3school ：hover](http://www.w3school.com.cn/cssref/selector_hover.asp)里面说所有主流浏览器都支持 `:hover` 选择器。并且`:hover` 选择器用于选择鼠标指针浮动在上面的元素。`:hover` 选择器可用于所有元素，不只是链接。照这样看在这儿使用DOM有点儿鸡肋)，按照书上来说，本着选择确保更多浏览器支持的原则，DOM的 `onmouseover` 可以很好的代替`：hover`，具体就是先获取所有的tr再使用for循环一一改变在 `onmouseover` 和 `onmouseout` 下的字体样式达到响应的目的