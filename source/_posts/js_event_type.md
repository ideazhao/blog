title: 拨开JS事件的迷雾（二）  
date: 2018-09-05
tags: [学习、javascript]
categories: [总结]
toc: true
---

上一篇文章，我们聊了什么是事件，以及多种绑定方法，这篇我们接着聊。

## 怎么实现 

日常工作中，我们要做的东西通常都是来自某个想法，比如，按钮点击之后要怎样，划过要怎样，还有悬停、滚动、拖拽、手机屏幕转换等等，诸多场景。

但这些想法怎么实现呢？似乎有点无从下手，其实编程语言的设计者已经为我们考虑了这个问题，它为我们准备了很多可以和不同场景相匹配的工具，就好比厨房里的铲子、勺子、筷子、叉子、菜刀，按需取用即可，而且随着技术的演变，用户场景的丰富，它也会与时俱进，这就涉及到下面要讲的**事件类型**。

## 事件类型

事件的种类可谓相当繁多，有可见的，有不可见的，大致可分为如下几类：

- UI：load、select、resize、scroll等
- 焦点：blur、focus、change等
- 鼠标：click、mousedown/up、mouseenter/leave、clientX/clientY等
- 键盘：keydown/up、keypress
- 触摸：touchstart、touchend、touchmove等
- 手势：gesturestart、gestureend、gesturechange等
- 设备：orientationchange、deviceorientation等

上面这些，大都是人为操作，还有些事件是网页状态带来的，比如：网页加载完成、提交表单、网页出错等。

除此之外，还有变动事件，复合事件，HTML5新加入的一些事件，不再一一列出。

完整列表可在这里查看[Web Events](https://developer.mozilla.org/en-US/docs/Web/Events)

网页上几乎每个动作都能够触发某个事件，网页代码也时刻准备响应这些事件，有时候它可能给我们带来麻烦，但多数时候是能够为我所用来解决问题的，这里没有多少难以理解的地方，就不再赘述。

## 事件冒泡/捕获

如果说事件当中有几大谜题，冒泡/捕获定能占据一席，按照最简单的逻辑，当满足了“元素、条件、执行内容”三者之后，事件应该就完整了，那冒泡和捕获又是什么意思？为什么会存在呢？

实际上，他们是**描述事件触发时序问题**的术语。

事件捕获指的是从总的父级到触发事件的那个节点，即自上而下地触发事件。相反的，事件冒泡是自下而上地触发事件。

用两个图来展示：

**事件冒泡**

![事件冒泡](/img/event/event_maopao.png)

**事件捕获**

![事件捕获](/img/event/event_buhuo.png)

如此说来，这两个过程看似浅显易懂，没那么难理解，可是它们为什么存在？

找了挺多资料，都只说它们是什么，怎么用，却没有说设计意图，不过还是看到一种可能合理的解释。

我们可以这样想，网页上的元素都是层层嵌套的，比如，一个body，套个div，再套个p，p里面再加个按钮，它们相当于若干个矩形进行叠放，那么，如果我点一下按钮所在的区域（注意，我强调了区域，即不先入为主地认为点的是按钮），那**到底是点了谁呢**？是按钮本身，还是包含它的p，或者是在点整个页面呢？

给不出准确答案吧？因为你的手指就在那里，你的想法，或者说，我想让你的点击起到什么效果，得有个设定的方法或者机制吧？之前就说了，用户的任何一种行为都会对应某种结果，那么，浏览器的开发者自然不会让这种模棱两可、无法确定的东西真的没法解决，于是就给出了两种事件流的形式，冒泡或者捕获，开发者可选择是否用、用哪个、怎么用，这就提供了较大的可控性和灵活性。

## 事件对象

JS领域有一句很有名的话——一切皆为对象，如果单看”对象“这个知识点，似乎一点都不难，每个人都能随口说出几种创建和使用对象的方法，但是，JS当中的很多对象都是很抽象的，不知怎地，它就存在那里了，事件对象便是其中之一。

先来看段代码：

    function bgChange(e) {
    var rndCol = 'rgb(' + random(255) + ',' + random(255) + ',' + random(255) + ')';
        e.target.style.backgroundColor = rndCol;
        console.log(e);
    }  

    btn.addEventListener('click', bgChange);

不难看出，这段代码可通过点击某元素来改变其背景色，但有个莫名的小东西出现了，就是”e“，这里的e是什么？又能做些什么呢？

你一定见过它，或者，你见过跟它长得很像的event、evt，这是个固定的指定名称的参数，被称为“事件对象”，**它被自动传递给事件处理函数，以提供额外的功能和信息**。

初看这种写法，我也很纳闷，有什么事不能明着来，非要暗里作怪？但人家就是这么设计的，我们只能尽量去理解。

那么，事件对象有什么用途呢，可多着嘞，你一定也见过，譬如：

- **阻止默认行为**
  什么是默认行为？链接跳转、表单提交等都是，这样写就可以加以阻止：
  e.preventDefault();

- **阻止事件冒泡**
  事件冒泡是一种事件的执行机制，有些时候会造成意向不到的结果，需要阻止，像这样：
  e.stopPropagation();

- **事件委托**
  上面一个应用是阻止事件冒泡，但有些时候，它又能给我带来好处，正所谓凡事都有两面性。
  事件委托的意思是，当一个父元素有很多子元素，每个子元素都将执行同样的事件，那么，没必要给它们重复绑定，只要将事件监听器设置其父元素之上即可，举例如下：

<div class="output_wrapper" id="output_wrapper_id" style="font-size: 16px; color: rgb(62, 62, 62); line-height: 1.6; word-spacing: 0px; letter-spacing: 0px; font-family: 'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;"><pre style="font-size: inherit; color: inherit; line-height: inherit; margin: 0px; padding: 0px;"><code class="hljs php" style="margin: 0px 2px; line-height: 15px; font-size: 11px; font-weight: normal; word-spacing: -3px; letter-spacing: 0px; font-family: Consolas, Inconsolata, Courier, monospace; border-radius: 0px; overflow-x: auto; padding: 0.5em; background: rgb(30, 30, 30); color: rgb(220, 220, 220); display: block !important; white-space: pre !important; word-wrap: normal !important; word-break: normal !important; overflow: auto !important;">&nbsp;&nbsp;&nbsp;&nbsp;&lt;ul&nbsp;id=<span class="hljs-string" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(214, 157, 133); word-wrap: inherit !important; word-break: inherit !important;">"parent-list"</span>&gt;<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;li&nbsp;id=<span class="hljs-string" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(214, 157, 133); word-wrap: inherit !important; word-break: inherit !important;">"post-1"</span>&gt;Item&nbsp;<span class="hljs-number" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(184, 215, 163); word-wrap: inherit !important; word-break: inherit !important;">1</span>&lt;/li&gt;<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;li&nbsp;id=<span class="hljs-string" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(214, 157, 133); word-wrap: inherit !important; word-break: inherit !important;">"post-2"</span>&gt;Item&nbsp;<span class="hljs-number" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(184, 215, 163); word-wrap: inherit !important; word-break: inherit !important;">2</span>&lt;/li&gt;<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;li&nbsp;id=<span class="hljs-string" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(214, 157, 133); word-wrap: inherit !important; word-break: inherit !important;">"post-3"</span>&gt;Item&nbsp;<span class="hljs-number" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(184, 215, 163); word-wrap: inherit !important; word-break: inherit !important;">3</span>&lt;/li&gt;<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;li&nbsp;id=<span class="hljs-string" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(214, 157, 133); word-wrap: inherit !important; word-break: inherit !important;">"post-4"</span>&gt;Item&nbsp;<span class="hljs-number" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(184, 215, 163); word-wrap: inherit !important; word-break: inherit !important;">4</span>&lt;/li&gt;<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;li&nbsp;id=<span class="hljs-string" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(214, 157, 133); word-wrap: inherit !important; word-break: inherit !important;">"post-5"</span>&gt;Item&nbsp;<span class="hljs-number" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(184, 215, 163); word-wrap: inherit !important; word-break: inherit !important;">5</span>&lt;/li&gt;<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;li&nbsp;id=<span class="hljs-string" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(214, 157, 133); word-wrap: inherit !important; word-break: inherit !important;">"post-6"</span>&gt;Item&nbsp;<span class="hljs-number" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(184, 215, 163); word-wrap: inherit !important; word-break: inherit !important;">6</span>&lt;/li&gt;<br>&nbsp;&nbsp;&nbsp;&nbsp;&lt;/ul&gt;<br>&nbsp;&nbsp;&nbsp;&nbsp;<span class="hljs-comment" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(87, 166, 74); font-style: italic; word-wrap: inherit !important; word-break: inherit !important;">//&nbsp;Get&nbsp;the&nbsp;element,&nbsp;add&nbsp;a&nbsp;click&nbsp;listener...</span><br>&nbsp;&nbsp;&nbsp;&nbsp;document.getElementById(<span class="hljs-string" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(214, 157, 133); word-wrap: inherit !important; word-break: inherit !important;">"parent-list"</span>).addEventListener(<span class="hljs-string" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(214, 157, 133); word-wrap: inherit !important; word-break: inherit !important;">"click"</span>,&nbsp;<span class="hljs-function" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(220, 220, 220); word-wrap: inherit !important; word-break: inherit !important;"><span class="hljs-keyword" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(86, 156, 214); word-wrap: inherit !important; word-break: inherit !important;">function</span><span class="hljs-params" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(220, 220, 220); word-wrap: inherit !important; word-break: inherit !important;">(e)</span>&nbsp;</span>{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="hljs-comment" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(87, 166, 74); font-style: italic; word-wrap: inherit !important; word-break: inherit !important;">//&nbsp;e.target&nbsp;is&nbsp;the&nbsp;clicked&nbsp;element!</span><br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="hljs-comment" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(87, 166, 74); font-style: italic; word-wrap: inherit !important; word-break: inherit !important;">//&nbsp;If&nbsp;it&nbsp;was&nbsp;a&nbsp;list&nbsp;item</span><br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="hljs-keyword" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(86, 156, 214); word-wrap: inherit !important; word-break: inherit !important;">if</span>(e.target&nbsp;&amp;&amp;&nbsp;e.target.nodeName&nbsp;==&nbsp;<span class="hljs-string" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(214, 157, 133); word-wrap: inherit !important; word-break: inherit !important;">"LI"</span>)&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="hljs-comment" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(87, 166, 74); font-style: italic; word-wrap: inherit !important; word-break: inherit !important;">//&nbsp;List&nbsp;item&nbsp;found!&nbsp;&nbsp;Output&nbsp;the&nbsp;ID!</span><br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;console.log(<span class="hljs-string" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(214, 157, 133); word-wrap: inherit !important; word-break: inherit !important;">"List&nbsp;item&nbsp;"</span>,&nbsp;e.target.id.replace(<span class="hljs-string" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(214, 157, 133); word-wrap: inherit !important; word-break: inherit !important;">"post-"</span>,&nbsp;<span class="hljs-string" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(214, 157, 133); word-wrap: inherit !important; word-break: inherit !important;">""</span>),&nbsp;<span class="hljs-string" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(214, 157, 133); word-wrap: inherit !important; word-break: inherit !important;">"&nbsp;was&nbsp;clicked!"</span>);<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;});<br></code></pre></div>


这是个列表，事件监听器位于ul上，但最终选择执行的目标元素是li。

当然，事件对象不止这些作用，它还有很多属性可以操作，譬如：

- event.target.nodeName 　　
- event.target.id　　　　　　
- event.target.className　　
- event.target.innerHTML
等。

在此不再赘述，具体可自行试验和研究。

## 总结

在使用事件的过程中，除了知道用什么，怎么用，还需要注意它可能引起的内存与性能问题。譬如：

- 要限制一个页面当中的事件处理程序数量，否则，数量过多会占用太多内存，造成反应不灵敏，建立在冒泡机制上的事件委托可以帮助减少处理程序的数量。
- 在浏览器卸载页面之前最好移除页面中的事件处理程序。

事件的细节十分之多，两篇文章远远说不完，所以，这里只是概述，并未深入，但还是希望能对耐心读完的你有所帮助！

接下来还会逐个涉及JS领域的其他知识，欲知后事如何，我们下回继续。


参考链接：[Events](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Building_blocks/Events)
