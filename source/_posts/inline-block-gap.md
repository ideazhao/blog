title: 小谈inline-block的那点空隙         
date: 2015-04-11
tags: [CSS]
categories: [CSS]
toc: true
---

最近有朋友遇到了这个问题，然后他百思不得其解：我没有设置边距呀，这中间怎么有空隙呢？我一看便知它用了inline-block。或许你会问，这个问题应该知道的啊，比较平常的问题。好吧，在两种情况下这是不易察觉的。

**1、设置了父元素背景而子元素没有背景**

**2、子元素本身就设置了外边距，而且远比可能出现的空隙要大**

因为inline-block所引起的空隙本来就很小，没有“像素眼”的话，在这两种情况下就不易察觉了。

## 那么inline-block是什么？

大家应该都知道html元素从表现上来看，可分为“行级”和“块级”。

那么inline-block就是兼具二者咯？yes，回答正确。不过好像又不完全对。

这是w3c的定义：

This value causes an element to generate an inline-level block container. The inside of an inline-block is formatted as a block box, and the element itself is formatted as an atomic inline-level box.

简而言之，元素自身仍然表现为行级，内部被格式化为具有块级盒特性，比如，可设置宽高，上下外边距等。

回归正题，inline-block的元素间隙，间隙从何而来呢？

是的，我们的确无需做任何的设置，它“自带”的就有。

咱们先从解决办法入手：

**1、除掉空白符**

来看两张图

html结构代码

![](inline-block-gap/1.jpg)
![](inline-block-gap/2.jpg)
![](inline-block-gap/3.jpg)

好的，看出来不同了么？两个ul中的li都使用了display:inline-block;But!!为什么第一个没有空隙而第二个有呢？ok，差别很明显，第一个列表中li全都是在同一行，没有换行，也没有任何的空隙，没有换行很明显，为什么强调没有空隙呢？因为的确是不能有空隙的，哪怕一个空格都不行，加上它就又成了下面那个死样子~额，等等，为什么加上一个空格就成了下面那个样子？Because 通常情况下，对于多个连续的空白符（空格，换行符，回车符等），浏览器会将他们合并为一个空白符。所以，一行空白和一个空白，效果上是一样滴。

虽然如此，它可以解决，但是也不是我们推荐的方法，我想没多少人会为了表现去牺牲结构吧。

**2、font-size：0;**

咦？等会儿，吃错药了么，为什么会提到font-size，这里跟字体大小有什么关系？好的，上面提到空白符，那么它归根到底还是字符，既然这样，它就会受到font-size的影响？是的，设置为零它就没了？是的，那太哈皮了。停，有两点疑问，既然设置为0它会消失，那么设置不同大小，它也有不同大小？你真聪明，来看

font-size:14px;

![](inline-block-gap/4.jpg)

font-size:20px;

![](inline-block-gap/5.jpg)

是不是明显看出差别了？

嗯嗯~

不过还是有两点需要考虑，父元素font-size设置为0，那就是还要为子元素重新设置font-size，还有就是，不要忘了有些浏览器目前是不支持font-size设置为0的，比如低版本chrome，还好，我们可以用 -webkit-text-size-adjust 这个私有属性来控制，当设为 none 时就支持字体大小为 0 了。

**3、word-spacing**

不要忘了letter-spacing和word-spacing这两个法宝，它们可是专门负责干这事儿的，从可行性上来说，二者皆可胜任去除空隙的重任，但是推荐使用 word-spacing，因为letter-spacing的绝对值如果大于间隙，内部内容会出现重叠，至于是怎么个重叠法，可以看看我做过的这个东东text-rotate，哈哈.当给word-spacing设置了一定的值之后，就会去除了间隙，这里需要设置的值同样跟字体大小相关。当然，不要忘了，上面字体大小在子元素中需要重置，这里也不例外，否则，就会出问题。

**4、margin负值修复**

相比其他几种方案，这种方案可能会被鄙视了~因为，怎么看都像是很勉强的去达到一种视觉效果，好吧我承认这么干过，不过很痛苦~

**5、去掉中间的闭合标签**

比如这样

![](inline-block-gap/6.jpg)

此处应有砖声~这也太不择手段了吧，但是这样的确是可以达到目的滴。但是为什么呢？我猜测，它是把后面的那些空隙算作了li的内部，而不是外部，这样它们之间就不会产生空隙。不过，同第一种方法一样，个人不推荐，对于有强迫症的人来说简直不可忍受有木有！！~

上面说了这么多，可能还会有人提出不同的意见，比如说，为什么不使用float？

好的，那什么时候适合用inline-block呢？

一、float元素脱离了文档流，会对其周围的元素造成影响，而inline-block不会有这些问题。 二、想要通过设置父容器text-align：center让元素居中，这显然用浮动无法做到，这个在我另一篇谈不定宽居中方案的文章中提过。 三、使元素垂直居中，inline-block元素是沿着默认的基线对齐。浮动元素紧贴顶部。你可以通过vertical属性设置这个默认基线，但对浮动元素这种方法就不行了。

好了，就说到这里了，欢迎大家补充其他的方法。也可以来这里跟我们一起学习交流：152128548

另附两篇文章给大家作为参考

[inline-block前世今生](http://www.iyunlu.com/view/css-xhtml/64.html)

[去除inline-block元素间间距的N种方法](http://www.zhangxinxu.com/wordpress/2012/04/inline-block-space-remove-%E5%8E%BB%E9%99%A4%E9%97%B4%E8%B7%9D/)