title: 《前端圈技术论坛-腾讯互娱专场》观后感          
date: 2014-11-23
tags: [分享交流]
categories: [分享交流]
toc: true
---

自从知道了[前端圈](http://www.fequan.com/)会有走进名企的论坛，就期待什么时候能够去参加，10月份有一次在广州vip，不算远，本来要去的，但某种原因未能去。接下来偶然知道了走进互娱，而且是讲重构和H5游戏方面，再加上刚来到企鹅做重构，似乎没理由不去了~

整个过程是这样的：

1、TGideas副总监PANTHER致辞
2、TGideas重构四组（重构工程师）黄勇尤：**Can I use webp？**

首先不得不坦诚的说第一次到互娱去，而且交通问题，所以迟到了，很遗憾的错过了前两个部分，等我去了之后已经到了第三个话题的提问环节。立即坐下专心听。第二个部分的如果后面有资料放出，会做个弥补。

3、TGideas重构一组（重构工程师）谭杰俊：**逐帧动画-让魔法小精灵在你的页面上起舞**

这个部分只听到了提问的环节，也稍有遗憾，提问的时候有问到图片压缩和代码压缩的问题，做动画的时候，如何选择css3和js，要把文件最好控制到多大。这方面之前也有了解，讲师主张优先选择css3，低版本的浏览器做一些降级的处理。还有提到了Gpu加速的问题，是有些css3的属性可以自动触发gpu加速的。

4、TGideas多媒体开发组（多媒体开发工程师） 罗飞虎

这段是我不熟悉的部分，不熟悉才要去了解和学习。 当然，平时也爱玩他们做的酷跑~游戏没有做过，所以，还是有一些收获，比如游戏大概要做的几个部分，加载状态、欢迎状态、新手引导、游戏中、结算分数、分享战绩等等，突然一下子使得对于游戏的几个部分变得明朗起来，然后提到了一些游戏开发需要注意的东西。还见识到了一款游戏当中要用到多少种类和状态的图片，提到了两种引擎，cocos2d-html5和createjs。但我个人对这方面的了解不多，以后要加强了。 提问环节有人问到了如何做到手机上和ipad上的适配问题，当时他未做过多的解释，后来另一位嘉宾补充说，可以做两套方案，根据不同设备使用不同的css或图片。

5、TGideas重构二组（重构工程师）丁亮亮：**重构代码修炼之道**

这一段是中间休息之后的，说实话，从这一个话题加上最后一个，是跟我平时工作最贴切的。smallni的博客平时也常看，whycss网址导航应该也是他做的，都很不错，这次有幸听到他讲解一些代码相关的东西也很荣幸。语速适中，不是那种让人想睡着的感觉，挺好~

他提了一个问题，什么是优雅的代码，他采用了代码截图作对比的方法，展示了不同格式，比如缩进，不同类命名方法之下的html和css，也就是不仅仅做到标签语义化，同样类的命名也要语义化，利用好基类，扩展类，实例类这些技巧。怎样写代码更一目了然，更清晰，可读性更强，代码组织的更好，便能让代码更优雅。还要注意对代码进行注释，提出好的代码其实可以不要注释，但为了更好的维护或者团队协作，要加上合理的注释。确认设计稿中交互是否完整，比如是否具有了各种状态下应有的UI，确认固定标题字数、固定图片位大小规范等等。也提到了代码的模块化，比如两个部分看起来是相似的，标题和内容，那如果另一个部分只有跟它相似的标题，而另外还有一部分只有跟它相似的内容，这个时候，去把整个一大块模块化明显就行不通了，也做不到很好的复用。由此引出了到底怎样去划分，以怎样的标准去划分，提到了颗粒化，就是把你要模块化的东西范围缩小，另外的部分划分成一个颗粒，这样子就像是颗粒和模块进行组合协作一样去完成不同部分。比较好的一个思路。

6、TGideas重构二组（重构工程师）肖鑫裕：**前端爬坑指南**

此人风格幽默诙谐，自我介绍是css森林群里面的“怪兽”，PPT背景是粉红色，自我调侃比较“骚”。没有代码展示，不会显得很闷。介绍了一下自己的从业经历，提出了几个问题，比如说，是不是一定要兼容IE？这个问题相关你的用户群到底是哪部分，他们使用的是什么浏览器。还有讲到说如果重构比较早的进入到了项目的规划，在没有拿到设计稿之前，如何去确定公用或者可复用和扩展的部分，这个是之前一个童鞋提的问题，他做了补充解答，当然了，他给的建议，不能确定的情况下就把所有的都做成公用部分~呵呵，在实际中是否可行还是要自己去考量了。然后还举了几个不同的产品例子比如Qzone，我友网和其他的一款产品记不大清楚了，来举例子去分析在不同复杂度和不用量级用户群，不同产品迭代速度，不同规模的情况下，怎样取舍。在跟同事之间的合作也是不能“言听即从”，为什么这么说呢，别人让你改你就改，别人让你加班重做，你就加班重做，这样对于个人和团队都不一定是好事情，有些时候要会适当的为自己去争取而拒绝一些要求。还举了一个例子去说明，很多时候想象中的东西跟做出来的东西不同，做出来的东西跟客户真正想要的可能又有很大不同，这就需要更好的沟通，或者更多的去关注用户，关注数据分析等。另外一点就是要做到有理有据，比如产品认为该怎么做，或者设计认为该怎么做，你不同意，他们问你为什么，你回答：我就是觉得那样比这样好。这显然是不能够有说服力的，讨论也不会很快有结果，最好是拿出实例或者实际的市场数据，这样就很明显的看出孰优孰劣。
这个话题所讲的是一些爬坑，内容不止这么多的，但由于信息量稍大，当时又去晚了坐在后排，所以也没拍照，后期前端群应该会有PPT发布出来到时再回味一下。

好吧，这次的分享就先到这里，虽然没有完整的把论坛的内容都听完，但收获也是蛮大的。见到了一些比较有经验的牛人们。当然了，也参加了最后的游戏环节并拿到了一份礼物哦！下面奉上照片。

集体合照留念（女生后面那排中间左手拿礼物的格格短袖男生就是我哦~）

![大合影](fequan-2014/1.jpg)
得到的小礼物

![充电宝](fequan-2014/2.jpg) 
 

