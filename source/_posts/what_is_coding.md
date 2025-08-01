title: 【轻聊前端】编程是什么？
date: 2021-02-22
tags: [分享]
categories: [JavaScript]
toc: true
---

最近在知乎上看到个问题。
![知乎回答](/img/what_is_coding/zhihu_question.png)
然后微博上有网友评论说：这就是“道”。

写的时候没想那么多，但这么形容也没错，这篇文章，就聊聊编程的“道”。

提到编程，多数人会有一个印象：一面黑色屏幕上，密密麻麻的代码。

那一行行代码是什么呢？专业的人也需时间去读懂，对于其他人，无异于天书。

其实大可不必，不懂程序，无非两点：

一、**不知缘由，没有头绪**
二、**不懂概念，不会用法**

## 积少成多

笔者计算机专业出身，但前端开发不在课程范围，算是自学一点皮毛，刚参加工作时，公司电脑还没到位，没法干活，确切地说也不会，就按照主管的吩咐坐在导师旁边看了两天，提的第一个问题就是：“这么多代码都是你写的？你怎么知道哪里是什么呢？”

导师对我说：“这些是我一行一行写出来的，我自己的定义和设计，当然知道是什么了。”

所以，代码不管是一行、一百行、还是一万行，都是由开发者一个个敲出来的，都有其来由和目的，不必被代码量吓到，这是其一。

## 现实天然是程序

知道了代码是积少成多的，那么代码本身是什么？

代码是由计算机能够理解的语言写出来的东西，跟人能够理解自然语言一个道理，而且两种语言之间有着对应的关系，每一个熟练的程序员都会在编程的时候在脑海中自动地转换。

程序设计语言多种多样，每一种都有其擅长做的事情，普通的开发岗位不会要求一个人懂多种语言，会有所分工，比如：前端至少要掌握HTML、JavaScript、CSS，而后端要掌握Java、Go、Python之类的语言和数据库等。

但不论是什么语言，写出的程序都在做三件事：

**数据存储、数据处理、数据传输**

本系列文章就来跟大家分享一些使用JavaScript编写程序的知识。

问题来了，怎么写？探索怎么写，要先了解一件事情，什么是程序。

**现实天然是程序**，拿人一天的生活举例：

到点闹钟响起，起床——穿衣、洗漱——出门（如果有垃圾把垃圾带着）
——交通（有车的开车，没车的由路程及便利性决定是步行、单车、公交还是地铁）——早餐——上班——开会——搬砖/修bug——加班——下班——交通（同上班）——晚餐——娱乐/读书/锻炼…——玩手机——睡觉。

程序员的一天大体如此。

跟写好的一段计算机程序一样，一行行执行，一件件做。

那么这段描述用编程语言如何表达？它们有着怎样的对应关系？

**对象**：人

**属性**：性别、年龄、职业、所在公司等。

**流程**：起床、穿衣洗漱…

流程中的某些环节带着条件判断（有无垃圾、选择哪种交通工具）

**方法**：吃饭、搬砖。

编程就是这么个路子。

具体到写代码，该如何着手和处理呢？简单概括如下：

**定义变量**：当需要存储一个数据，不管是什么，数字、字符串、对象等，都可以定义一个变量。

**定义函数**：当需要执行某个动作，而且需要重复执行，就可以封装一个函数（或者叫方法）。

**条件判断语句**：当需要根据不同条件执行不同动作，就用到了条件判断。

**绑定事件**：当需要点击、滚动、触摸等得到反馈，就用到了事件。

**操作DOM**：当需要获取/操作网页上的元素，就用到了DOM。

…

简单吗？简单，但也不简单，那又复杂在哪里？

## 丰富的工具箱

为什么要写程序，“人的想法”加上“程序的本领”来满足日常需求，需求是多种多样的，“巧妇难为无米之炊”，如果语言本身不具备那个能力，程序员又如何做到呢？

什么能力？譬如：

画图(CSS/SVG/Canvas)，音视频(Audio/Video)，地理位置(Geolocation)，通知(Notification)…

先抛开特定场景的需求不谈，最常规的人机交互，也需要DOM/BOM这些浏览器提供的接口来做到。

故而，语言所具备的，多种类型的数据，每种类型所具备的属性和方法，繁多的特点、特性、API，都是为我们编写程序提供便利的，是丰富的工具箱。

面对这个工具箱，你可能惧怕太多东西一时无法记住，但当你需要的时候，又会庆幸它们的存在。

## 优雅的代码

我们做每件事情，都是从不会到会，到熟练，再到强大（规模上）、细致（细节质量）的。明白了怎么写程序，有什么“工具”可用，就是个能做事的程序员了，但离优秀的程序员还有距离。

如果你编码经历尚少，或许觉得有点故弄玄虚，但迟早会有所体会。

优雅的代码是什么？笔者认为，需要综合考虑多方面因素，而不只是“代码量少”、“实现巧妙”等。

**组织性**

说白了，就是文件的命名及存放，业界有不少做法，占比最大的是两种：
一、按文件类型，html、js、css，图片，每个大类里又可按照用途划分小类
二、按业务模块，首页、登录、个人中心等

当然，也可以将二者进行适当的结合。

合理的代码组织，能够使得一个大型项目的代码显得多而不乱，利于维护、查找问题，也利于团队协作。

**可读性**

有句话说得好——“代码是写给人看的，顺便给机器运行”。

给机器运行的代码只要没有语法错误，怎样都可以，但人不行，还有另一句自我调侃的话叫“我现在写的代码是干什么的只有上帝和我知道，再过两个月就只有上帝知道。”

这就很不好，没有任何一段代码是能够保证不再回头去看、去调整，或者某一天交到别人手里维护和加工，如果不易读，是一件左右为难的事，继续用，该怎么写保持风格不变且不出问题，不用，代码冗余会增多，且可能越来越乱，不可收拾。

**健壮性**

也可理解为“适应性”，越健壮的代码，越能适应更多场景甚至状况，越少出现问题，两个简单的例子，你的网页是否能够在iphone5和iphone12上都能良好展现，或者当页面图片加载失败/文件找不到，是否有备用措施来弥补，以使用户体验不会大打折扣。做好了这些，代码靠谱，人也靠谱~

**扩展性**

也可理解为代码的通用性、复用性，比如，网页换肤、根据用户的不同身份展示不同内容，在现有的模块当中持续地添加新的模块等。代码扩展性越好，迭代过程的成本就越低。

**性能**

在编程领域，性能问题百说不厌，具体到代码层面，就是代码的执行时间，体验是否流畅。

同一件事情用代码可以多种写法，除了“写对”之外，还要考虑是否够快，能否给用户的操作提供符合预期的反馈，多是细节，这里不赘述。

以上几点，如果做不到，程序也能正常运行，但做得到，对自己持续性的编码，对用户体验，对团队协作，都是很有利的，也会给他人留下好的印象。

## 代码之外的工具

如果只写页面，三驾马车足够，但随着技术迭代，需求变更，项目规模等变化的发生，有追求的程序员们动用自己的聪慧和技术做出很多更利于现代开发的工具，比如：npm（包管理器）、gulp（任务处理工具）、webpack（模块打包工具）等，当然，还有丰富的组件库和流行框架，起初这并不是一名开发者的必备技能，但它们的确因为提供了便利而被广泛使用，久而久之成为标配，你可以不全学，但不能都不会，这就成了编程语言之外我们需要学习的东西。

上述的内容，构成了一名前端开发者在当下需要掌握的技能的主要部分。

## 总结

到这里，你应该大体看到了编程是什么样子，它不难，但对于初学者足够复杂，需要，也仅仅需要投入足够多的时间就能征服。

后面我们一起征服~





