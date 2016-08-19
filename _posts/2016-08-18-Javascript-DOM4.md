---
title: "《JavaScript DOM 编程艺术》读书笔记（四）"
excerpt_seoarator:
categories: "JavaScript"
tags: ["JavaScript", "DOM", "读书笔记"]
---


## [Modernizr](http://www.modernizr.com/)
[Modernizr](https://modernizr.com/docs)是一个开源JavaScript库，用法和功能如下：

- 在文档中嵌入Modernizr之后，他会随着页面加载改变一些标签的class属性，使用的时候要在<html>标签里面添加no-js的类，这样在浏览器不支持JS的时候就会应用css样式，
- 还会自动检测浏览器可能支持的web技术，并添加相应类名（feature or no-feature）
- 帮老旧浏览器处理HTML5的新元素而不用用户自己定义：关于使用：在[Modernizr](http://www.modernizr.com/)下载并在<head>中添加`<script src="文件本地地址></script>`以便在文档呈现之前传建好html5元素
##HTML5新元素
点击查看[HTML5标签](http://www.w3school.com.cn/tags/index.asp)
```
更加语义化的标签(Internet Explorer 9+, Firefox, Opera, Chrome 以及 Safari 支持以下标签)：
<artical>, <aside>, <footer>, <header>, <section>, <nav>, <source>, <time>, <figcaption>, <figure>, <mark>, <embed>, <progress>
```
除了上面的，还有很多HTML5标签，下面从实例出发，了解下其中的canvas：
**根据路径绘图~画一个小盒子**
首先，明确canvas本质就是一个画布，在python里面有类似的用法，所以我们先来熟悉一下用canvas标签来画一个简单的小方块------点击查看[demo](http://ppmeng.github.io/somedemo/HTML/11.html)
核心代码如下：
```
function draw() {
	var canvas = document.getElementById("draw-in-me");
	if (canvas.getContext) {
		var ctx = canvas.getContext("2d");
		ctx.beginPath();
		ctx.moveTo(120.0, 32.0);
		//三次贝塞尔曲线
		ctx.bezierCurveTo(120.0, 36.4, 116.4, 40.0, 112.0, 40.0);
		ctx.lineTo(8.0, 40.0);
		ctx.bezierCurveTo(3.6, 40.0, 0.0, 36.4, 0.0, 32.0);
		ctx.lineTo(0.0, 8.0);
		ctx.bezierCurveTo(0.0, 3.6, 3.6, 0.0, 8.0, 0.0);
		ctx.lineTo(112.0, 0.0);
		ctx.bezierCurveTo(116.4, 0.0, 120.0, 3.6, 120.0, 8.0);
		ctx.lineTo(120.0, 32.0);
		ctx.closePath(); //关闭此次绘制
		ctx.fill(); //默认填充黑色
		ctx.lineWidth = 2.0;
		ctx.strokeStyle = "rgb(255, 255, 255)"; //路径颜色
		ctx.stroke(); //实际绘制路径
	}
}
```
原理很简单，canvas是一块有着横纵坐标的画布，左上角为原点，越往右x越大，越往下y越大，变量ctx引用的是画布的绘图空间（二维），简单的根据矢量作图就是在一个二维平面上面确定几个点，连线作为绘图经过的路径，这里中间对绘图结果做了一个边角的处理，可以参考[三次贝塞尔曲线是什么](http://www.w3school.com.cn/tags/canvas_beziercurveto.asp)，  [用js计算三次贝塞尔曲线](http://bbs.9ria.com/thread-71296-1-1.html)

**根据位图绘图~处理灰度图片**
这个例子是将一个彩色图片用canvas处理成灰度版，并用 *onmouseover* 以及 *onmouseout* 来进行彩色版和灰度版本的切换，点击查看demo------[demo](http://ppmeng.github.io/somedemo/HTML/11-2.html);
核心代码如下：
```
function convertToGS(img) {
    //存储原始彩色版
    img.color = img.src;
    //创建灰度版
    img.grayscale = createGSCanvas(img);
	//在onmouseover时候发生切换
    img.onmouseover = function() {
        this.src = this.color;
    }
    img.onmouseout = function() {
        this.src = this.grayscale;
    }
    img.onmouseout();
}
function createGSCanvas(img) {
    var canvas = document.createElement("canvas");
    canvas.width = img.width;
    canvas.height = img.height;
	var ctx = canvas.getContext("2d");
	//将图片绘制入画布
    ctx.drawImage(img, 0, 0);
    //getImageData只能操作和脚本处于同一个域中的图片
    var c = ctx.getImageData(0, 0, img.width, img.height);
    //按照像素修改图片的颜色
    for (var i = 0; i < c.height; i++) {
    	for (var j = 0; j < c.width; j++) {
    	    var x = (i * c.width + j) * 4;
    	    var r = c.data[x];
    	    var g = c.data[x + 1];
    	    var b = c.data[x + 2];
    	    c.data[x] = c.data[x + 1] = c.data[x + 2] = (r+g+b)/3;
        }
    }
    ctx.putImageData(c, 0, 0, 0, 0, c.width, c.height);
    return canvas.toDataURL();
}
//添加load事件，如果有其他脚本，换用addEventLoad函数比较好
window.onload = function() {
    convertToGS(document.getElementById("cute"));
}
```
原理：首先创建切换函数，鼠标悬停（**onmouseover**）时图片为彩色版，离开（**onmouseout**）时图片为灰度版；接着完成构建灰度图片的函数，简单讲先把图片写入画布（**ctx.drawImage(img, 0, 0)**），然后读出图片的每一个像素，然后将该像素的rgb颜色变为(r+g+b)/3后修改图片参数（**ctx.putImageData**），这样在鼠标悬停时为原本的彩色，离开时调用这个函数将图片处理成灰度版。原理应该是很好理解的，但是具体操作的时候会遇到几个问题：

- 首先，怎么读出每个像素？
- 我在本地编辑后在chrome浏览器下打开，开发者工具下在第24行**getImageData**出现了一个图片跨域的错误：11-2.html:37 Uncaught SecurityError: Failed to execute 'getImageData' on 'CanvasRenderingContext2D': The canvas has been tainted by cross-origin data.

 **关于像素遍历的理解**：
- 对于第一个问题，我们要先了解**getImageData()**方法得到的数组形态，参考[w3school](http://www.w3school.com.cn/tags/canvas_getimagedata.asp)， [MDN](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/getImageData)， [知乎讨论](https://www.zhihu.com/question/39819798/answer/83252754)，附赠一个略深入的[关于getImageData()的性能问题](http://sphinx.oupeng.com/articles/the-stories-about-getimagedata-and-putimagedata)的讨论,　看过之后应该就理解了，*getImageData()* 方法返回 *imageData*　对象，这个对象拷贝了所有像素信息，包含四个方面：R - 红色 (0-255)，　G - 绿色 (0-255)，　B - 蓝色 (0-255)，　A - alpha 通道 (0-255; 0 是透明的，255 是完全可见的)，　很容易被迷惑的是，因为每个像素包含四个方面，直觉总会想着应该讲数据保存到四维数组里面，这样每个数组元素就包含一个像素的值，但是实际上，这个像素信息是包含在一维数组里面，所有的四个方面的值都一个一个分散在数组里面，也就是说数组里面的元素是这样的：像素一Ｒ，像素一Ｇ，像素一Ｂ，像素一Ａ，像素二Ｒ，像素二Ｇ，像素二Ｂ，像素二Ａ······，所以要按照像素遍历数组元素应该是每遍历数组四个元素找到一个像素位置，然后将每个像素的RGB相加后求平均数（将A忽略）再重新赋值；上面代码里面用了一个循环嵌套，嗯，原理如上，不再讲了
 **关于getImageData的思考及尝试**
- 关于使用getImageData()的时候因为画布被跨域的数据'污染'所以出错，这里是因为JavaScript处于安全的考虑不允许跨域调用其他页面的对象（这是个很大的坑，开发的时候会遇到很多跨域问题，以后得整理一下），所以 *getImageData* 只能操作和脚本处于同一个域中的图片（不允许操作非此域名外的图片资源，即使是子域也不行），但是我在本地编辑的时候，图片和脚本文件是保存在本地的同一个文件夹里面，参考[HTML Canvas, getImageData and Browser Security](http://blog.project-sierra.de/archives/1577)，可以知道本地文件是没有所谓的域，所以浏览器默认跨域，故出错，但是里面给出的解决办法让我不知所云，get不到点，测试也没有什么区别，只有Firefox浏览器才会出现切换效果，但是不加文章中给出的代码仅仅采用我给出的代码在Firefox下也是没有问题的；
- 另一个找到的解决办法是[ 解决getImageData跨域问题](http://blog.csdn.net/molaifeng/article/details/42293509)，原理是[用php将图片编码嵌入html](http://blog.csdn.net/molaifeng/article/details/9617327)来解决跨域问题，不会php，测试无果（也有可能是我修改的有问题）
```
<?php  
$pic = 'http://avatar.csdn.net/7/5/0/1_molaifeng.jpg';  
$arr = getimagesize($pic);  
$pic = "data:{$arr['mime']};base64," . base64_encode(file_get_contents($pic));  
?>  
<img src="<?php echo $pic ?>" /> 
```

- 第三种找到的：[stackOverflow上提问一](http://stackoverflow.com/questions/26688168/uncaught-securityerror-failed-to-execute-getimagedata-on-canvasrenderingcont), [stackOverflow上提问二](http://stackoverflow.com/questions/22097747/getimagedata-error-the-canvas-has-been-tainted-by-cross-origin-data) 里面的回答和上面的有些类似，要么就是用php编码图片，要么就是用类似下面的代码修改图片的crossOrigin属性，但是根据[stackOverflow上提问二](http://stackoverflow.com/questions/22097747/getimagedata-error-the-canvas-has-been-tainted-by-cross-origin-data)回答一必须在服务器端也做一个修改，这对我来说并不适用
```
var uimageObj = new Image();
UimageObj.crossOrigin = 'anonymous';   // crossOrigin attribute has to be set before setting src.If reversed, it wont work.  
UimageObj.src = obj_data.srcUser;
```
- 到最后我采取的办法是什么呢？？？其实只从原理上入手就可以了，就是把代码和图片放在一个文件夹里面上传到github的gh-pages分支，这样他们就在一个相同的域下就不会有跨域的问题了。《JavaScript DOM编程艺术》这本书中也提到“从图片之类的文件中读取数据时，不同的浏览器有不同的安全考虑，为了保证例子正常运行，必须在同一个站点提供图片和文档，而且，就算是在本地硬盘中使用file协议加载这个页面，例子也无法运行，虽然可以修改浏览器的安全设置，但是还是处于安全考虑还是建议把相关文件都上传到web服务器上”，也可以理解为什么Firefox浏览器并没有出现跨域bug了
- 为了解决这个问题还看了一些别的资料，[CORS（CrossOrigin Resource Sharing）跨域资源共享](http://www.it165.net/pro/html/201412/29106.html), [Ajax跨域、Json跨域、Socket跨域和Canvas跨域等同源策略限制的解决方法](http://blog.csdn.net/freshlover/article/details/44223467#comments)，[如何捕获和分析 JavaScript Error](http://www.css88.com/archives/5014), 看看涨涨姿势就好了，不过不得不承认遇到问题去Stackflow比百度强多了
- CSS解决办法：[使用CSS将图片转换成黑白(灰色、置灰](http://www.zhangxinxu.com/wordpress/2012/08/css-svg-filter-image-grayscale/), 然后在鼠标悬停和离开的时候切换设置不同的属性，，更快而且不用考虑跨域问题