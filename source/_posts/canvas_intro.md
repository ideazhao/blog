title: 【Canvas系列一】Canvas的绚烂起点       
date: 2019-05-01
tags: [Canvas]
categories: [总结]
---

很多人知道canvas，但不一定都熟悉或者经常使用，作为一种专项工具，如果业务不是跟图像或者图表强相关，的确不常用，但对它的学习和了解还是必要的，这篇文章我们就一起来入个门。

## Canvas是什么

可以理解为，它提供了一块画布，在它所掌控的区域，可以运用各种方法绘制图形、文本，或者制作动画、处理图像，等等。

通常我们能够看到一些炫酷的动画，很多都是canvas做出来的，但是需要各种元素和技巧综合使用，不是一蹴而就的，我们先从基础看起。

## 搭好舞台

        var canvas = document.getElementById("canvas");
        var ctx = canvas.getContext("2d");

        <canvas id="canvas" width="150" height="150"></canvas>

三行代码，完成舞台搭建。

## 绘制的几要素

**位置**：从哪里开始

**路径**：图形的轨迹

**轮廓**：绘制空心图形

**填充**：绘制实心区域

用代码来表达就是：

**画笔位置**——moveTo(x, y)

将画笔移动到指定的坐标x、y。

**直线**——lineTo(x, y)

绘制一条从当前位置到指定位置的直线。

**圆弧**——arc(x, y, radius, startAngle, endAngle, anticlockwise)

画一个以（x,y）为圆心、以radius为半径的圆弧（圆），从startAngle开始到endAngle结束，按照anticlockwise给定的方向（默认为顺时针）来生成。

**矩形**——rect(x, y, width, height)

绘制一个左上角坐标为（x,y），宽高为width以及height的矩形。

几个基本的图形就是这样了，还有一种比较复杂的是贝塞尔曲线。

**二次贝塞尔**——quadraticCurveTo(cp1x, cp1y, x, y)

**三次贝塞尔**——bezierCurveTo(cp1x, cp1y, cp2x, cp2y, x, y)

什么是较复杂的图形呢？比如：气泡、心形，或者其他不是很常规的形状，如使用上面的方法就较难实现，贝塞尔曲线更能胜任。

那其实除了这些，canvas还有两个动作要做，就是**开始**和**结束**绘制的指令，分别是：

**beginPath()** 和 **closePath()**

放在路径绘制的开端和末尾，就像这样：

        ctx.beginPath();
        ctx.moveTo(125,125);
        ctx.lineTo(125,45);
        ctx.lineTo(45,125);
        ctx.closePath();

（如果路径本身就是闭合的，可以省去closePath，但一般建议保留）

完成这些之后，就可以进行下一步，绘制轮廓，或者填充区域，当然，可以两者都做：

        ctx.stroke();  //轮廓
        ctx.fill();   //填充

除了这两个操作，还有其他的操作，比如设置色值、线宽、是否旋转等，后面做丰富示例时我们再一一介绍。

完整示例如下：

        var canvas = document.getElementById("canvas");
        var ctx = canvas.getContext("2d");
        ctx.beginPath();
        ctx.moveTo(125,125);
        ctx.lineTo(125,45);
        ctx.lineTo(45,125);
        ctx.closePath();
        ctx.stroke();//轮廓

        ctx.beginPath();
        ctx.arc(200, 200, 40, 0, 36, false);
        ctx.closePath();
        ctx.fillStyle="rgb(2,100,30)";//填充颜色
        ctx.fill();//填充

![](/img/canvas_intro/canvas_01.png)
![](/img/canvas_intro/canvas_02.png)

这只是一个很简单的效果，我们也看到了路径之后其他设置项的出现，就是填充颜色，这只是其中之一，但这里暂时不详述，我们先看看，除了图形，canvas还能做什么？

## 文本和图片

文本很好实现，直接上代码：

        ctx.font = "48px serif"; //字号、字体类型
        ctx.fillText("Hello world", 10, 300); // 文字内容和位置

但其实文字也是有两种效果的，填充和轮廓，上面这种是填充：

![](/img/canvas_intro/canvas_03.png)

如果把第二行代码换成：

    ctx.strokeText("Hello world", 10, 50);

就是轮廓了

![](/img/canvas_intro/canvas_04.png)

除此之外，还有一些对齐方式和文本方向的设置，先略去。

来看看图片

图片分为两块，处理图片和绘制图片。

获取同页面内的图片不再赘述，跟通常的dom操作一样，看看创建图片的方法：

        var img = new Image();   // 创建一个<img>元素
        img.src = 'myImage.png'; // 设置图片源地址

获取或者创建了图像，怎么把它绘制到canvas里去呢？

**drawImage(image, x, y)**

其中 image 是 image 或者 canvas 对象，x 和 y 是其在目标 canvas 里的起始坐标。

比如我们可以把上面的代码写成这样：

        var ctx = document.getElementById('canvas').getContext('2d');
            var img = new Image();
            img.onload = function(){
            ctx.drawImage(img,0,0);
            }
            img.src = '/img/canvas_intro/canvas_01.png';

上面这段代码，我把当前页面的三角形拿了过来，就是跟前面那个效果一样的了

![](/img/canvas_intro/canvas_01.png)

这里只是把图像放入我们的工作区，如果想在这个区域画别的图形亦可，代码接着往下写就好了，有时候这可以为我们节省成本，比如，稍复杂一点的图像作为背景，简单的图形作为前景画出。

那其实这个方法也不止这么简单，它有两个变种

**缩放**

**drawImage(image, x, y, width, height)**

即指定图像大小，也就是可以根据需要进行缩放。

**裁切**

**drawImage(image, sx, sy, sWidth, sHeight, dx, dy, dWidth, dHeight)**

一长串参数看着有点头皮发麻，但就因为这样，才能让我们去操作细节，它是用来裁切图像的。

前面四个用来处理图片源的剪切起始位置和大小，就跟我们平时截图一个道理，后面的参数，是将裁切后的图像，放置在画布的什么位置，以及那个区域的大小。如下所示：

![](/img/canvas_intro/canvas_slice.jpg)

## 小结

这篇文从零开始介绍了canvas的工具箱里有哪些工具，虽然尚不完整，作为开端，已经够多，先到这，更多功能我们下篇再聊~


