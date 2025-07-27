title: CSS“非常规”垂直居中
date: 2019-03-20
tags: [CSS]
categories: [总结]
toc: true
---

鄙人从业五年有余，切图无数，见过一篇又一篇写“居中”的文章，已经熟视无睹。

当已经习惯了CSS3的舒爽，有一天突然接到PC网站的需求，施展不开手脚的时候，发现自己脑海中并不能马上想出一个很好用的垂直居中方案。

这里说的方案，当然不包括定位和margin负值之类，也不包括flex、grid、transform等，前者不够灵活，破坏正常文档流，后者略高级。


那么该如何居中？margin或者padding？给多少合适？加了最外面的，里面还是贴着边的，还要继续撑空间。

虽说这种地方不会经常动，即使代码写起来略繁琐，不是那么优雅，写完再也不会改，但如果我们有更好更强大的实现方案，为何不用呢？

于是不得不找寻其他可能，结果是令人惊喜的。

结构代码统一如下：

        <div class="box">
            <span>垂直居中</span> 
        </div>

## 方案一：table-cell

日常需求中，我们乐于用line-height去做元素内文案的垂直居中，比较常见的是按钮，但时间长了就会有所顾忌，因为很多地方你不知道它什么时候就从单行文字变成了两行，其后果可能是灾难性的，所以即使不愿意用table来书写结构，也是可以使用如下代码来实现：

        .box {
        display: table;
        }
        .box span {
        display: table-cell;
        vertical-align: middle;
        }

这样不论是几行文字，都能垂直居中，而不需要去管间距问题。
## 方案二：vertical-align

        .box:before {
        content: "";
        display: inline-block;
        vertical-align: middle;
        height: 100%;
        }

只要这么简单的一行代码，是不是很厉害？

原理就是行内元素的垂直居中，只是这里那个“元素”并不真实存在，而是用伪元素模拟出来，这是第一点，第二点就是 heigh:100%，这样就能让内容相对整个容器垂直居中，不发生上下偏移。

但这种方法有个需要注意的点，单行的时候正常，变多行的时候就无法居中了，不过也不用慌，给span同时加上vertical-align: middle; 即可重新居中。

## 方案三：writing-mode

        .box {
        writing-mode: tb-lr;
        writing-mode: vertical-lr;
        text-align: center;
        }
        .box span {
        display: inline-block;
        writing-mode: lr-tb;
        writing-mode: horizontal-tb; 
        }

这种方案一般情况下也不会使用到，不是它不够好，而是它同样改变正常文档流，将网页的行内元素从左右排列方式改成了上下，然后再把行内元素居中的方法应用其上，即可实现垂直居中，问题是需要在居中的元素里面再把方向改回来，否则文字也会从上到下排列。

需要注意的一点，这里多写了两行 writing-mode:tb-lr;和 writing-mode:lr-tb;是因为下面的两个更高级的写法不支持IE8，我们找这种方法就是要兼容低版本的，所以要加上。

## 方案四：absolute; margin: auto;

        .box {
            position: relative;
        }
        .box span {
            position: absolute;
            height:100px;
            top: 0;
            bottom: 0;
            left: 0;
            right: 0;
            margin: auto;
        }

这种方法有两个小缺陷:

一、会使用绝对定位破坏文档流
二、需要给居中元素定高（通过计算分配空间）

但这种方式是具备启发性的，它会让子元素跟父元素保持所设置的距离，就是这里的left、top等值（可以不为0，同一纬度的值相等即可），如果不够，就用空隙去填充，使用 margin:auto; 让填充空间在元素的上下和左右进行均分，以达到居中的效果。

这个作为最后一点列出来，在某些适用场景下可以选用，但不作为优先考虑。

以上方案主要讨论的是垂直方向的居中，水平方向代码并未贴出，大家可以根据需要自行设置宽度。

## 总结
这篇文提到的知识点都稍显老旧，但需要派上用场时候还是很实用的，大家在积极学习和探索新知识的同时，不宜把旧知识丢掉呀~

如果正在读这篇文章的、机智的你还有其他好方法，欢迎不吝赐教！~