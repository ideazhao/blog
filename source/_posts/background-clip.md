title: CSS3高级属性巡礼—一半红一半绿      
date: 2019-08-23
tags: [CSS]
categories: [CSS]
---

CSS3跟大家见面已经有些年头了，都很熟悉，各种高级属性随口就来，但是，如果冷不丁让你实现一个效果，可能并没那么快想出实现方案——一用就想不起来，说的是不是你？~

## 字体样式

我们知道，CSS中设置字体常用的属性，无非是

- font-size
- color
- font-weight等，

比如下面一段代码：

        <div class="test-box">
             <h2>A</h2>
        </div>

我们把它设置为红色，只需要这样：

        .test-box h2{
                color:red;
        }

效果如下：

![](/img/background_clip/A01.png)

你会觉得，这简单得简直侮辱智商，那么问题来了：

怎么让它一半红一半绿呢？

color...似乎无能为力。

## 打破思维的墙

为了实现以上效果，我们总不能去绘制，没必要，也不能把A拆成两半，一左一右，然后分别设置color吧，你拆一个看看？

但总能想到办法，就像以前模拟三角形或者牛角那样。

我们来分拆：

从一个颜色到另一个颜色，怎么实现？——渐变。
一半红，一半绿呢？起点到中点是一个颜色，中点到终点是另外一个颜色即可。
颜色解决了，文字呢？我们的主角要登场了。

## background-clip

废话不多说，直接亮代码：

        .test-box h2{
            display: inline-block;
            background: linear-gradient(to right, #f44e30 0%,#f44e30 50%,#29db35 51%,#29db35 100%); 
            color: transparent; //关键
            font-size: 20px;
            -webkit-background-clip: text;  //关键
        }

效果如下：

![](/img/background_clip/A02.png)

可以看到，上面的代码里，我标了两处”关键“，是因为除了这两个地方，其他均可变，只是视觉效果不同，而这两者是必须的。

看到这里，你突然觉得，也不难啊，的确不难，这个问题只是将我们思考问题的维度缩小了，以至于忘了本来可能熟悉的东西，例如，我们通常会实现这样的效果：

        <div class="test-box">
             <h2>Hello world</h2>
        </div>

        .test-box h2{
            display:inline-block;
            font-size:40px;
            background: linear-gradient(to bottom, #f44e30 0%,#29db35 100%); 
            -webkit-background-clip:text;
            -webkit-text-fill-color:transparent;
        }

![](/img/background_clip/A03.png)

变一种形式可能那个弯儿就转不过来了。

>小tips：上面代码我都加了display:inline-block;为什么，不加不行？不是，这关乎一个细节，很多时候，如果你用一个块元素直接包裹，它会撑满整行，那么渐变的背景也会撑满整行，而文字可能只占了它的一个片段，多数情况下，都会跟想要的视觉效果有差别，故而，我们需要渐变的区域和文字区域完全重合。

## 常规应用

”text“这个值虽然神奇，但只能算是它带给我们的奖励，它还有三个常规的值：

        background-clip: border-box|padding-box|content-box;

拿一张图片举例，它们分别对应的效果如下：

        .img-clip{
                width: 80px;
                height: 80px;
                background: url(98.jpg);           
                background-repeat: no-repeat;
                background-size: cover;
                border:10px solid rgba(207, 56, 56, 0.3);
                padding: 20px;
        }


![](/img/background_clip/A04.png)

![](/img/background_clip/A05.png)

![](/img/background_clip/A06.png)

可看出，属性值代表的意思——即背景图可以延展到的范围。

## 小结

关于**background-clip**就先聊到这儿，它可能远不止我们聊的这些看起来普通的应用，更重要的，是需要它们派上用场的时候，我们能够想到，祝我们好运！~
