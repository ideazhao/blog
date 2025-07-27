title: 图片以及其他替换元素的空隙问题         
date: 2015-04-18
tags: [CSS]
categories: [CSS]
toc: true
---

今天来聊聊图片，当然，对于web来说，图片是个大命题，多大呢？要多大有多大~ 这里只谈它是怎么“不听话的”，然后让它乖一点。

Web的世界里有很多问题，与之对应的基本也有很多情景和对策。那么问题就来了，我们遇到某种情景和找到的对策也都具有偶然性，下面来挑很多人都遇到过的一种情况说说。

上篇文章我们谈了inline-block的空隙，这篇文依然是聊空隙。通过上篇文，我们可以看出，其实空隙和元素本身并无直接关系，因为它真的就是“空隙”。但是小小的空隙却常给我们带来小小的困惑和麻烦。

场景：“这是怎么回事啊，我什么都没有给图片设置，为什么这里有个空隙？！然后，margin、padding、border、outline…（七大姑八大姨）全都去掉了，为什么还有？！肿么办呐？！”

对于以上场景你是否熟悉捏？~颇有病急乱投医，reset掉它全家的意味哈！

再稍微分两种情况来说（其实没啥区别），一种是img“裸奔”，另一种是被其他元素包裹，比如div或者li等。

“裸奔”代码如下：

    <div class="img_list">
        <img src="images/xiang01.jpg" alt="">
        <img src="images/xiang02.jpg" alt="">            
        <img src="images/xiang03.jpg" alt="">
        <img src="images/xiang04.jpg" alt="">
        <img src="images/xiang05.jpg" alt="">            
    </div>

实现如下：

![](img-ele-gap/1.png)

聪明如你应该看出端倪了对吧？是的，每张图片的右边如预期般出现了空隙，但事实仅仅如此么？~ 我们把容器缩小看一下 duang~

![](img-ele-gap/2.png)

原来不只是右边有，下面也有？似曾相识吗？

**方法一：font-size：0；**

是的。“音烂不唠嗑”那家伙好像也是有空隙，那我们用其中一种方法把它消除掉试试，把font-size设置为0；

![](img-ele-gap/3.png)

呜呼，果然，那这是第一种方法。

**方法二：float**

由于float能让元素脱离正常文档流，可谓有摧毁一切的架势，这里也不意外，使用float就可以使他们消失于无形，如果赶上你制作图文混排文字环绕的效果，那就是一举两得了~

**方法三：vertical-align：XX；**

或许你会有点奇怪，为什么是XX？没错，有些文章中有说设为“top”，有些文章中说用“bottom”，可是在css如此的精彩世界里，你会对这么一个简单的答案满足么？总会多问几个why？然后，you try try 便会发现，，原来设置top|bottom|middle|text-bottom|text-top等都是可以消除掉这个空隙的，但需要注意，往往页面中不是一个图片孤立的存在着，设定不同的值可能会对其他的内容产生不同的影响，这里不细说，具体请各位去了解下vertical-align方面的东东（下面有个张老师的链接）。至于为什么vertical-align有除掉这个空隙的作用呢？

据不完全考察，图片文字等inline元素默认是和父级元素的baseline对齐的，而baseline又和父级底边有一定距离（这个距离和 font-size，font-family 相关），所以设置 vertical-align的某些值可以避免这种情况出现。你可以试一下设置一个具体的负百分比值也行哦~

**方法四：display：block；**

为什么这种方法是可行的呢？其实这个方法跟方法三和方法二有着某种微妙的联系，what？莫急，先来说跟方法三有何联系，方法三里，扯到了baseline，也扯到了vertical-align，但没有扯一扯它在哪些元素上起作用，是的，对于block级的元素，它们是玩儿不了的，也可见张老师牛奶/果冻/坚果，故而，设置为block，它就干掉了空隙。跟方法二的关系是，不知大家是否注意到行级元素在设置float之后，具备了块级元素所具备的功能，比如设置宽高，内外边距等，所以，浮动，效果跟此法类似。 当然，鉴于block之后会改变它本身inline的表现，有些朋友可能会考虑inline-block，but这样是不凑效的，况且，它本身就存在这样的问题。

**方法五：为父容器设置高度**

现在才说起这个看起来平常的方法可不是我不够坦白，因为很多人可能从来没有遇到过图片空隙这样的问题，会觉得这是个挺难遇到的bug，其实不然，没有遇到右边的空隙是因为，只要它不是裸奔，譬如这样：

    <ul class="img_list">
        <li><img src="images/xiang01.jpg" alt="">
            </li>
        <li><img src="images/xiang02.jpg" alt="">
            </li>
        <li><img src="images/xiang03.jpg" alt="">
            </li>
        <li><img src="images/xiang04.jpg" alt="">
            </li>
        <li><img src="images/xiang05.jpg" alt="">
            </li>
    </ul>

img被一个容器包裹起来，这里是li，还可以是别的，那么右边的空隙就没了，因为它是存在于img之间，这里有一层容器将其隔开。当然了，可能还会因为设置了margin值，从视觉上直接忽略了空隙的存在，不在讨论之列哈~ 没有遇到过下面的空隙，可能是无意间使用了能够消除掉它的方法，最平常和自然的就是设置父容器高度了。

**方法六：无缝排列**

这个方法和消除inline-block空隙同理，让它们紧密排列即可，不过同样，从代码的格式上来说不宜采取。

讨论了这么多，貌似这种现象是img所独有的，是么？聪明如你可能又猜对，no！~ 可能大家都知道html的元素中有一类元素叫做替换元素，什么是替换元素呢，咱拿规范说说事儿吧；以img为例：

The IMG element embeds an image in the current document at the location of the element’s definition. The IMG element has no content; it is usually replaced inline by the image designated by the src attribute。

这是规范里对img的描述，简单说来img元素没有内容，它是把来自src属性值的那个图片嵌入页面中。

说到这里，是不是想起还有不少元素是这样的？比如input、textarea、select等等？

是的，它们同样存在着缝隙问题，为什么呢，看图

![](img-ele-gap/4.png)

是否有秒懂的感觉？

好吧，扯了不少了，里面提到了好几个东东，没有细谈，因为每一个挑出来都够喝一壶的，所以，不细聊了，重点还是帮助有遇到类似问题的朋友找到较合适的解决方法。文中可能有不当之处，还望指教。

欢迎来这里交流：152128548 
欢迎关注微信公众号：前端周末 