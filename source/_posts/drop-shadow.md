title: 巧用drop-shadow，拯救不开心    
date: 2019-10-11
tags: [CSS]
categories: [CSS]
toc: true
---

说到页面效果，阴影是很小的元素，很少被提及。

当然，面试的时候，还是有人会问你：**扩展半径**和**模糊半径**。

在设计师的眼里，也还是会纠结：范围太大、颜色太重之类的问题。

我们简单列一下。

## 最常用——box-shadow

box-shadow说来很简单。

代码如下：

    .box-shadow{
      width: 50px;
      height: 50px;
      background: rgb(123, 199, 123);
      border-radius: 4px;
      box-shadow: 0 3px 10px 1px rgba(0, 0, 0, .5);
    }

效果如下：

![](/img/drop-shadow/box-shadow.png)

这是最常规的实现，我们可以任意修改阴影的偏移值和半径、颜色，达到我们想要的效果。

但有时我们会遇到这样的效果，

![](/img/drop-shadow/box-shadow-single.png)

乍一看，也很普通，但你会发现，当我们按照常规写法去写阴影的时候，它会出现三种可能情况：

- 偏移量很小，四边都有阴影
- 单侧偏移较大，三边有阴影
- 两侧偏移都达到一定值，相邻两边有阴影，对边没有

好像都不是这张图的效果？它仅有一边有阴影，何解？

如果你从未遇到过这种需求，可能就会打个问号，不卖关子，上关键代码：

    .box-shadow-single{
      box-shadow: 0 8px 5px -5px rgba(0, 0, 0, .7);
    }

可以看到，我们将阴影的扩展半径写成了负值。仅此而已么，它是怎么做到的呢？

不妨来看看浏览器是怎么制造阴影的。我们把代码改成这样：

    box-shadow: 0px 55px 0px 0px rgba(0, 0, 0, .7);

盒子背后的秘密被暴露了出来，

![](/img/drop-shadow/shadow-back.png)

哇偶，原来它只是在我们盒子的背后绘制了一个一模一样的形状：

- 改变模糊半径，它会从边缘到中心逐渐变模糊
- 改变扩展半径，会改变它的大小

看到这里，你应该明白了什么，从上图可看出，阴影的初始大小跟元素完全吻合，那么，给予它一个单边的偏移值，再给它负的扩展半径，它就能成为一个——三边都躲在元素背后，只有一边露出来的单边阴影~

![](/img/drop-shadow/single-test.png)

(发现长得有点像U盘，哈哈)

你一定想尝试，模糊半径负值会怎样？嗯，它没了...

## 次常用——text-shadow

说text-shadow次常用，是因为它涉及到一定特效，常规字体一般是不需要的，什么样的效果呢？比如：

![](/img/drop-shadow/text-shadow.png)

这样的效果在一些活动页是少不了的，因为纯色会显得太素，视觉效果适当地丰富，能使得用户更兴奋。实现代码如下：

    .textshadow{
      color: #FFF;
      text-shadow: 0 0 2px #000,0 0 10px gold;
    }

当然，这只是小试牛刀，不过，我们可以看出，text-shadow只有四个值供设置，分别是X、Y方向的偏移和模糊半径、色值。还反应另一点是，可以写多重阴影的效果，这就为我们创造出更多效果提供了可能。譬如：

**3D**

    text-shadow: 0 1px #808d93,
       	       -1px 0 #cdd2d5,
       	       -1px 2px #808d93,
       	       -2px 1px #cdd2d5,
       	       -2px 3px #808d93,
       	       -3px 2px #cdd2d5,
       	       -3px 4px #808d93,
       	       -4px 3px #cdd2d5,
       	       -4px 5px #808d93;

![](/img/drop-shadow/3d-shadow.png)

**火焰**

    text-shadow: 0 0 20px #fefcc9,
                 10px -10px 30px #feec85,
                 -20px -20px 40px #ffae34,
                 20px -30px 35px #ec760c,
                 -20px -40px 40px #cd4606,
                 0 -50px 65px #973716,
                 10px -70px 70px #451b0e;

![](/img/drop-shadow/huoyan-shadow.png)

你可能会说，这样的场景很少，不实用。我同意，但**创意并不一定很常用，还可以体现可能性**。

## drop-shadow，拯救不开心

主角终于登场，怎么叫不开心？常规方法突然不适用，一筹莫展的时候。

先来看一个之前分享过的效果——渐变字体。

代码：

      background: linear-gradient(to bottom, #f44e30 0%,#29db35 100%); 
      -webkit-background-clip:text;
      -webkit-text-fill-color:transparent;

效果：

![](/img/drop-shadow/text-clip.png)

这个时候，我们可能还需要给它加个投影，就像这样

![](/img/drop-shadow/clip-shadow.png)

你会想到什么呢？box-shadow？

![](/img/drop-shadow/clip-box.png)

好尴尬...

text-shadow？

![](/img/drop-shadow/clip-text-shadow.png)

这是刚从煤窝里爬出来吗？

为什么？我们再次揭开谜底吧。

将代码改一下

    text-shadow: 0 10px 0px rgba(0,0,0,.9);

我们看到似曾相识的一幕

![](/img/drop-shadow/text-shadow-true.png)

跟box-shadow类似，text-shadow在字体本身的位置上，绘制了一个一样形状的图层，只不过，这里有个层级的问题，正常情况下，阴影在字体后，而当我们把字体填充色设置为transparent，用它来裁剪背景时，阴影就盖到了背景之上，这样就得不到我们想要的效果。

所以，无解？

有个人不答应——drop-shadow

试一下，将代码改成

    filter:drop-shadow(0 6px 2px rgba(0,0,0,.9));

![](/img/drop-shadow/clip-drop-shadow.png)

nice！

故事到这里，当然没有结束。

这里的阴影如果没有实现，只能算小挫折，大不了咱不要了...（如果设计师放过你的话~）

曾经的我呢，也仅仅是知道了这个方法可以解决上面的问题，并未深究，直到有一天，还是先看问题：

![](/img/drop-shadow/tooltips.png)

这个效果你一定做过，也不难，不过这个呢？

![](/img/drop-shadow/tooltips-shadow.png)

对，只是加了个阴影，但是，通过前面的内容我们知道，这个阴影肯定不能用box-shadow，它将会是这样：

![](/img/drop-shadow/tooltips-boxshadow.png)

甚至有看到很多开源的库都是这样写的，不知出于什么考虑。

我们曾经用drop-shadow解决了上面的问题，可以故技重施吗？答案是：可以。

只需要在气泡容器上加这行样式即可：

    filter:drop-shadow(0 4px 2px rgba(0,0,0,.4));

那么，它是怎么做到的？可以来体会一句话：

>box-shadow会应用到元素矩形的边框盒子，而drop-shadow()滤镜会应用到元素透明的轮廓。应用范围包括阿尔法透明度，保持阴影与轮廓吻合，或者给通过clip-path剪切的元素添加阴影。

慢慢体会，对于上面发生的一切，你可能就豁然开朗了。

当然，你不要犯我犯过的错，不能点到为止，你需要明白的是，它不止能解决这个问题，而是能够应对这一类问题，适用于几乎所有不规则形状的投影，相信你会遇到的~

除此之外，它还有另一个优势，大家知道，box-shadow的渲染比较耗浏览器的性能，特别是在有动画的时候，drop-shadow很好地缓解了这个问题，所以，它十分值得被你选用。

那么drop-shadow是不是可以替代box-shadow呢？可惜并不能。相比box-shadow，它少了两项技能：

1、扩展半径，它的效果总会是模糊的，无法绘制出box-shadow那种实线
2、无法应用多重阴影

尽管如此，它已经足够出色了不是吗？

你一定还有更多技巧和创意，欢迎交流！~