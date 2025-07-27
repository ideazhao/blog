title: 认识“新”朋友—conic-gradient
date: 2020-04-21
tags: [CSS、学习]
categories: [总结]
toc: true
---

曾经，我们需要用图片来完成很多视觉效果，代码难以实现的形状、色彩等等，还好后来有了CSS3，很多新的属性可以实现以前无法想象的元素，帮我们节省了大量的开发成本和网络传输成本。

本篇我们聊一种新的渐变—conic-gradient，说新其实也不算新，早在两三年以前，就有书籍和开发者做过分享，但那个时候浏览器的支持情况很不乐观，即便是现在，也只有较新版本的浏览器支持，不过这不影响我们去学习。

## 现役成员

我们熟悉的渐变有线性渐变（linear-gradient）和径向渐变（radial-gradient），并且在日常需求中用处很广，而它们也往往不负所托，能发挥奇效。

比如：

**三角**

```CSS
background-image:linear-gradient(to bottom right,orange 0,orange 49%,transparent 50%,transparent 100%)
```

![](/img/conic_gradient/sanjiao.png)

**条纹**

```CSS
background:repeating-linear-gradient(45deg,#fb3,#fb3,5px ,#58a 0,#5ba  10px);
```
![](/img/conic_gradient/tiaowen.png)

**镂空**

```CSS
background-image:radial-gradient(transparent 0,transparent 49%,orange 50%,orange 100%)
```
![](/img/conic_gradient/loukong.png)

当这些创意第一次出现时，无不令人惊叹，但现在大家已经习以为常了。

今天介绍的这位就更具特色，你可能真没听过。

## 锥形渐变

我们知道，线性渐变是颜色沿着一条笔直的轴线变化，还可以调整角度；径向渐变则是以一个点为圆心向四周扩散，锥形是怎样的呢？

不妨直接看个效果：

>以下代码在新版Chrome中均可正常运行

```CSS
background: conic-gradient(green, orange);
```

这是个简单的例子，看起来要比之前的那些简单许多。效果如下：

![](/img/conic_gradient/zhuxing01.png)

这么看来，它的工作原理似乎明了,和径向渐变类似，它也是以一个点为中心起始点，不同的是，不是由内向外扩散，而是**沿着圆周**变化。

正式看一下语法规则：

>conic-gradient( from 起始角度  at 中心点位置, 渐变断点 )

从上面的例子可以看出，前两个参数是可以省略的，不影响代码执行，不过还有一个细节这里没有体现，即**颜色断点是可以添加角度（或者百分比）的**，就像线性渐变可以添加断点一样。

我们索性把上面的例子用新数据改一下看看效果：

```CSS
background: conic-gradient(from 60deg at 20% 20%, green 40%, orange 40%);
```
![](/img/conic_gradient/percent_conic.png)

哇偶，乍一看有点奇怪，但不觉得有点神奇么？换个角度思考，如果让你实现这么个效果，是不是会摸不着头脑？

我们往往对规则图形敏感，一遇到不规则，就脑瓜短路，所以，多思考，多尝试~

好了，是时候看看我们可以用它实现哪些实用的效果了！

### 色盘

```CSS
background: conic-gradient(red, magenta, blue, aqua, lime, yellow, red);
```
![](/img/conic_gradient/sepan.png)

美轮美奂。

### 饼图

```CSS
background: conic-gradient(#3ebd3e 0, #3ebd3e 30%, #ff9800 30%, #ff9800 70%, #03a9f4 70%, #03a9f4 100%);
```

![](/img/conic_gradient/bingtu.png)

无缝衔接

既然这些可以，那么彩色边框也就可以。

![](/img/conic_gradient/caiseborder.png)

环状图也不在话下。

![](/img/conic_gradient/huanborder.png)

类似loading应该也可以。

![](/img/conic_gradient/loading.png)

咋不上天呢？~

好了，效果是说不完的，先展示到这儿了，部分代码没有列出，想看的话可以来[这里](https://codepen.io/ideazhao/pen/LYpbYLO)。

它能做的当然不止这些，但笔者一向以实用为准则，花样是其次，就不玩儿那么多了，有兴趣你可以随意玩儿~

## 总结

虽然我向来认为JavaScript要比CSS难一些，但总有一些人会说CSS才是最难的，也不无道理，但它的难，也正是它出彩的地方，每一个属性出来感觉都很简单，但如果能够充分发挥想象力，再结合数学、图形以及动画方面的能力，就能做出很多意想不到的效果。

不知你看完感觉如何呢，是否跃跃欲试？那就撸起来吧！下篇见！~
