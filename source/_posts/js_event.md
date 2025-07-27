title: 拨开JS事件的迷雾（一）  
date: 2018-08-31
tags: [学习、javascript]
categories: [总结]
toc: true
---

## 怎么实现？

当我还很菜的时候（好像就是现在~），参加过一次面试，面试官问我，给一个元素绑定事件有几种方法，虽然我JS不熟，但觉得，没吃过猪肉还没见过猪跑吗？张口就来。

>行内onclick啊，bind啊，还有那个什么Event啊...

我努力在脑海中搜索见过的“猪”，他一边听一边微微点头，好像是懂了，可我自己都不确定自己说的是什么（捂脸），他没有笑出声来就算很nice了。

那次过后，我决定把没弄清楚的给好好学习下，就是这篇文的由来，算个总结，也给仍一团糊涂的朋友一点帮助。

既然是拨开“迷雾”，就不再主动制造障碍，像历史、深层次原理、某种事件类型的细节等就不再罗列，以免增加理解和记忆负担，内容较基础，高手可绕道~

## 事件

事件，即发生了某种行为，日常浏览网页的时候，不论是用户操作，还是开发者主动控制，都会有很多事件发生，比如：加载、点击、滚动、触摸等。所以它牵扯到几个方面因素：

在哪里发生——**绑定**
在何时发生——**监听**
发生了什么——**类型**
发生的过程——**流程**

## 绑定

俗话说，在哪里跌倒，就从哪里爬起来，先聊绑定。

直接上码：

    <button onclick="alert(你点到我了!)"></button>

这行代码给button绑定了一个click事件，弹出“你点到我了！”，这是最直接的方式，也是我们最初接触的方式，但显然这种结构和行为混在一起的方式是不太好的，来一点优化。

继续上码：

    <button onclick="tips()"></button>
    <script type="text/javascript">
        function tips(){
            alert("你点到我了！");
        }
    </script>

有了什么变化？我们把所要执行的事件内容提了出来，单独封装，这样以来，当执行内容较多时，不会让HTML显得很杂乱，而且可复用，易维护，还能怎样优化？

以上两种方式，都需要HTML的参与，即结构和行为相耦合，下面介绍不动HTML的DOM事件处理程序。

**一、DOM0级事件**

接着上码：

    //添加

    <button id=“btn”></button>
    <script type="text/javascript">
        var btn=document.getElementById("btn");
        btn.onclick=function(){
            alert("你点到我了！");
        }
    </script>

    //删除

    btn.onclick=null;


这里是将一个函数赋值给一个事件处理程序的属性，这种方法，是DOM0级事件处理程序，出现的时间较早。

它的优点是：**简单、跨浏览器**，当使用更高级的方式而浏览器不支持的时候，可以回退到这种方式。

**二、DOM2级事件**

你没看错，不是没有DOM1级，而是DOM1级没有定义事件处理相关的东西，所以这里直接跳到2级。

So，我们来看看DOM2级的标准之下，上面的代码怎么写：

    <button id=“btn”></button>
    <script type="text/javascript">
        var btn=document.getElementById("btn");
        btn.addEventListener("click",function(){
            alert("你点到我了！");
        },false);
    </script>

发现了什么？它们之间只有一处不同，即事件处理程序的绑定方法——**addEventListener**。其标准用法如下：

    addEventListener("事件名",事件处理程序,布尔值)

前面两个很直观，但第三个布尔值是什么意思？其实它决定了事件处理程序什么时候调用，true是捕获阶段，false是冒泡阶段。多数情况下都会在冒泡阶段调用，这样能最大限度兼容浏览器，至于捕获和冒泡是什么以及有什么用处，后续单独聊。

使用DOM2级方法的主要好处，是可以添加多个事件处理程序。比如：

    btn.addEventListener("click",function(){
            alert(this.id);
    },false);

    btn.addEventListener("click",function(){
        alert("你点到我了！");
    },false);

它们会依次按顺序执行。

既然有添加，当然要有移除，移除事件的方法就是**removeEventListener()**。和添加时传入的参数相同，这意味着，这种方法添加的匿名函数将无法移除。

**兼容？**

我们聊很多技术的时候都会做这样的区分：现代浏览器、老版浏览器。这是事物发展的必然规律，事件也不例外，那么除了上面提到的还有什么不同？——IE。

大家知道，IE通常都是特立独行的，它**添加**和**删除**事件处理程序的方法分别是：

    attachEvent() 和 detachEvent()

同样需要有“事件名”和处理函数两个参数，但跟addEventListener()的区别是：

- **事件名称需要加“on”，比如“onclick”；**
- **没了第三个布尔值，IE8及更早版本只支持事件冒泡；**
- **仍可添加多个处理程序，但触发顺序相反。**

还有一点需要注意，DOM0和DOM2级的方法，其作用域都是在其所依附的元素当中，attachEvent()则是全局，即如果像之前一样使用this.id，访问到的就不是button元素，而是window，就得不到正确的结果。

那么，跨浏览器添加事件怎么写呢？这类代码通常具有通用性，所以可以将它封装成一个对象以备多人多项目复用，代码如下：

<div class="output_wrapper" id="output_wrapper_id" style="font-size: 16px; color: rgb(62, 62, 62); line-height: 1.6; word-spacing: 0px; letter-spacing: 0px; font-family: 'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'Microsoft YaHei', Arial, sans-serif;"><pre style="font-size: inherit; color: inherit; line-height: inherit; margin: 0px; padding: 0px;"><code class="hljs lua" style="margin: 0px 2px; line-height: 15px; font-size: 11px; font-weight: normal; word-spacing: -3px; letter-spacing: 0px; font-family: Consolas, Inconsolata, Courier, monospace; border-radius: 0px; overflow-x: auto; background: black; color: rgb(234, 234, 234); padding: 0.5em; display: block !important; white-space: pre !important; word-wrap: normal !important; word-break: normal !important; overflow: auto !important;">var&nbsp;EventUtil={<br>&nbsp;&nbsp;&nbsp;&nbsp;addHandler:<span class="hljs-function" style="font-size: inherit; color: inherit; line-height: inherit; margin: 0px; padding: 0px; word-wrap: inherit !important; word-break: inherit !important;"><span class="hljs-keyword" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(195, 151, 216); word-wrap: inherit !important; word-break: inherit !important;">function</span><span class="hljs-params" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(231, 140, 69); word-wrap: inherit !important; word-break: inherit !important;">(element,type,handler)</span></span>{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="hljs-keyword" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(195, 151, 216); word-wrap: inherit !important; word-break: inherit !important;">if</span>(element.addEventListener){<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;element.addEventListener(<span class="hljs-built_in" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(231, 140, 69); word-wrap: inherit !important; word-break: inherit !important;">type</span>,handler,<span class="hljs-literal" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(231, 140, 69); word-wrap: inherit !important; word-break: inherit !important;">false</span>);<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}&nbsp;<span class="hljs-keyword" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(195, 151, 216); word-wrap: inherit !important; word-break: inherit !important;">else</span>&nbsp;<span class="hljs-keyword" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(195, 151, 216); word-wrap: inherit !important; word-break: inherit !important;">if</span>(element.attachEvent){<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;element.attachEvent(<span class="hljs-string" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(185, 202, 74); word-wrap: inherit !important; word-break: inherit !important;">"on"</span>+&nbsp;<span class="hljs-built_in" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(231, 140, 69); word-wrap: inherit !important; word-break: inherit !important;">type</span>,handler);<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}&nbsp;<span class="hljs-keyword" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(195, 151, 216); word-wrap: inherit !important; word-break: inherit !important;">else</span>&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;element[<span class="hljs-string" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(185, 202, 74); word-wrap: inherit !important; word-break: inherit !important;">"on"</span>&nbsp;+&nbsp;<span class="hljs-built_in" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(231, 140, 69); word-wrap: inherit !important; word-break: inherit !important;">type</span>]=handler;<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;},<br>&nbsp;&nbsp;&nbsp;&nbsp;removeHandler:<span class="hljs-function" style="font-size: inherit; color: inherit; line-height: inherit; margin: 0px; padding: 0px; word-wrap: inherit !important; word-break: inherit !important;"><span class="hljs-keyword" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(195, 151, 216); word-wrap: inherit !important; word-break: inherit !important;">function</span><span class="hljs-params" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(231, 140, 69); word-wrap: inherit !important; word-break: inherit !important;">(element,type,handler)</span></span>{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="hljs-keyword" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(195, 151, 216); word-wrap: inherit !important; word-break: inherit !important;">if</span>(element.removeEventListener){<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;element.removeEventListener(<span class="hljs-built_in" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(231, 140, 69); word-wrap: inherit !important; word-break: inherit !important;">type</span>,handler,<span class="hljs-literal" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(231, 140, 69); word-wrap: inherit !important; word-break: inherit !important;">false</span>);<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}&nbsp;<span class="hljs-keyword" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(195, 151, 216); word-wrap: inherit !important; word-break: inherit !important;">else</span>&nbsp;<span class="hljs-keyword" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(195, 151, 216); word-wrap: inherit !important; word-break: inherit !important;">if</span>(element.detachEvent){<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;element.detachEvent(<span class="hljs-string" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(185, 202, 74); word-wrap: inherit !important; word-break: inherit !important;">"on"</span>+&nbsp;<span class="hljs-built_in" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(231, 140, 69); word-wrap: inherit !important; word-break: inherit !important;">type</span>,handler);<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}&nbsp;<span class="hljs-keyword" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(195, 151, 216); word-wrap: inherit !important; word-break: inherit !important;">else</span>&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;element[<span class="hljs-string" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(185, 202, 74); word-wrap: inherit !important; word-break: inherit !important;">"on"</span>&nbsp;+&nbsp;<span class="hljs-built_in" style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; color: rgb(231, 140, 69); word-wrap: inherit !important; word-break: inherit !important;">type</span>]=null;<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;}<br>}<br></code></pre></div>


可明显看出，其使用了条件判断来检测浏览器是否具备那个方法，如果有，就用，没有，向更古老和通用的方法回溯。

怎么使用它呢？就像这样

    EventUtil.addHandler(btn,"click",handler);
    EventUtil.removeHandler(btn,"click",handler);

## 小结
    
至此，JS事件的添加方法以及怎样做兼容就大概介绍完了，这只是事件的一个方面，也已有不少内容。

在现今JS框架满天飞的时代，从较早的JQ到较近的React、Vue，我们似乎很少需要再手动书写原生方法，它们本身封装的方法替我们省去了很多重复繁杂的劳动，但去了解和学习这些知识仍是有好处的。

JS总体的知识点可谓相当庞杂，但再庞杂的东西都是由小的点组成，把它的要点和脉络抽出，进行个个击破，学习的过程就会显得轻松愉快，最终绘成一张大的版图。

欲知后事如何，我们下回继续。
