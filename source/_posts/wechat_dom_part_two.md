title: 【轻聊前端】强大的DOM（下）
date: 2022-02-02
tags: [分享]
categories: [JavaScript]
toc: true
---

> 现今各种框架、工具‘横行’，到处在讲原理和源码，更有跨端技术需要我们去探索，但如果基本功不好，学什么都是事倍功半，效果很不好，花费时间的同时打击自信心。此篇文章，为我所计划的【轻聊前端】系列第（十二）篇，旨在系统地、逻辑性地把原生JavaScript知识分享给大家，帮助各位较为轻松地理清知识体系，更好地理解和记忆，我尽力而为，望不负期待。

上一篇，我们介绍了DOM节点、节点关系以及操作节点相关的知识。

那些知识是网页结构存在的基础，基础有了，DOM对网页来说有什么作用，开发者如何加以使用呢？这就是本文要讨论的。

## 状态

什么是状态，当我们通过一个链接打开页面，会有资源请求的过程，资源包括哪些？HTML、CSS、JavaScript，页面相关的图片、文字、音视频等。当资源从服务器获取回来之后，就开始在浏览器窗口展现，这个展现的过程会对应不同状态，正在加载、加载完成，亦或是发生变化，这样的不同时机。

知道了这些，我们可以在合适的时机做想做的事情。

- DOMContentLoaded事件

这个事件可按照字面意思理解和记忆，DOM内容已加载，什么是DOM内容，即HTML文档。所以，**DOMContentLoaded事件的触发时机是初始的HTML文档被完全加载和解析完成之后**，无需等待样式表、图像和子框架的完全加载。简单示例如下：

```
document.addEventListener('DOMContentLoaded',function(){
    console.log('DOMContentLoaded');
});

```

- load事件家族

之所以称家族，是因为load有正向、反向的过程和一个中间态。

load：正向，**整个页面及所有依赖资源如样式表和图片都已完成加载时，将触发load事件**。简单示例如下：
```
window.addEventListener('load', (event) => {
  console.log('page is fully loaded');
});
```
下面这种写法也很常见：

```
window.onload = (event) => {
  console.log('page is fully loaded');
};
```

beforeunload：中间态，当浏览器窗口关闭或者刷新时，会触发beforeunload事件。

unload：反向，当文档或一个子资源正在被卸载时, 触发unload事件。

在面试中，会出现询问 DOMContentLoaded 和 load 事件有什么区别，需要注意。

- readyState
readyState属性有两个可能的值：

loading，文档正在加载；

complete，文档加载完成。

当文档的 readyState 属性发生改变时，会触发 readystatechange 事件。

实际开发中，最好是把document.readState当成一个指示器，以判断文档是否加载完毕。在这个属性得到广泛支持以前，通常要依赖onload事件处理程序设置一个标记，readState是新的替代方案。

## 尺寸

我们常常需要获取网页中元素的尺寸，进行不同情况的区分展示，或者响应一些交互动作。下面分类介绍一下：

### 偏移尺寸

 - offsetHeight，元素在垂直方向上占用的像素尺寸，包括高度、水平滚动条高度（如果可见）和上、下边框的高度。
 - offsetWidth，元素在水平方向上占用的像素尺寸，包括宽度、垂直滚动条宽度（如果可见）和左、右边框的宽度。
 - offsetLeft，元素左边框外侧距离包含元素左边框内侧的像素数。
 - offsetTop，元素上边框外侧距离包含元素上边框内侧的像素数。

这里的偏移，是元素相对于计算的起点所产生的，在网页的坐标体系中，以左上角为基准，所以，offsetLeft 和 offsetTop，代表**从元素的边框外侧计算，距离包含元素的像素数**。而 offsetWidth 和 offsetHeight 则是在位置基础上的宽高区域延展。

它们都是只读的属性，有什么用途呢？举个典型的例子，一个方块，在一个区域内运动，怎样判断这个方块接触到边界？接触边界并不会有什么事件发生，此时判断偏移量即可。

### 客户端尺寸

客户端尺寸只有两个相关属性：clientWidth和clientHeight。

这个值可理解为元素本身所占的宽高，不包含margin、border、滚动条等，只包含内容及padding区域。

### 元素尺寸——getBoundingClientRect()

上面的内容已经涉及了元素的宽高和位置，这个方法用来干什么的呢？

首先，client 并未涉及位置，而offset所涉及的位置又是相对包含元素的。那么，如果想获取到元素相对于视口的位置该怎么办呢？

浏览器在每个元素上都暴露了getBoundingClientRect()方法，该方法返回一个DOMRect对象，包含6个属性：left、top、right、bottom、height和width。这些属性给出了元素在页面中**相对于视口**的位置。

如果想知道一个元素相对视口的位置，特别是动态情况下，可以借助此方法获取，然后再结合视口的宽高，元素的宽高，进行下一步的交互或样式变化即可。

### 滚动尺寸

前面介绍了一个“偏移”，这里的关键词就是“滚动”。

既然是滚动，只有内容超出容器，且允许滚动的时候，它们才有实际意义。

scrollWidth，没有滚动条出现时，元素内容的总宽度。

scrollHeight，没有滚动条出现时，元素内容的总高度。

scrollLeft，内容区左侧隐藏的像素数，设置这个属性可以改变元素的滚动位置。

scrollTop，内容区顶部隐藏的像素数，设置这个属性可以改变元素的滚动位置。

滚动属性和偏移属性还有一大明显不同，即它是**可写**的。

这个用途就很广了，比如，我们可以通过判断元素滚动到了什么位置，设置不同的样式表现，常见的是头部固定，或者侧边菜单固定的效果。还可以在用户打开某个特定页面之后直接滚动到某个位置等。

除了设置scroll位置的值之外，还可以使用scrollTo()方法进行滚动，但这脱离了DOM的范围，待后续文章再讨论。

好的，到这里我们已经讨论了滚动相关的话题，但是，都是借助不同元素间的关系以及属性，通过计算判断得来的，还有个更新、更便利的方案。

## 区域滚动——scrollIntoView()

DOM规范中没有涉及的一个问题是如何滚动页面中的某个区域，为填充这方面的缺失，不同浏览器实现了不同的控制滚动的方式，在所有方法中，HTML5选择了标准化scrollIntoView()。

这个方法便利到什么程度呢？如果你想将一个id为box的元素滚动到与视口顶部对齐，只需要这样。

```
<div id='box'>
  此区域滚动
</div>
const box = document.getElementById('box')
box.scrollIntoView()
```

这里需要注意两点：

1、容器要可滚动，且DOM加载完成  
2、剩余空间足够滚动到目标位置

就能达到正确的效果。

scrollIntoView()方法存在于所有HTML元素上，具体参数如下：

- alignToTop，一个布尔值。
  
  true：窗口滚动后元素的顶部与视口顶部对齐。
  
  false：窗口滚动后元素的底部与视口底部对齐。

- scrollIntoViewOptions，一个选项对象。
  
  behavior：定义过渡动画，可取的值为"smooth"和"auto"，默认为"auto"。
  
  block：定义垂直方向的对齐，可取的值为"start"、"center"、"end"和"nearest"，默认为"start"。
                                  
  inline：定义水平方向的对齐，可取的值为"start"、"center"、"end"和"nearest"，默认为"nearest"。
  
两个参数皆为可选，但同时存在时Options不生效；不传参数等同于alignToTop为true。

有了这个方法，将元素滚动到可视区的操作就变得非常简单了。

## 自定义属性

HTML5允许给元素指定非标准的属性，即自定义属性，什么是标准属性，如：id、class、name、style、src等，非标准属性是什么，有什么用途？

使用前缀data-命名的属性就是自定义属性，这些属性既不包含与渲染有关的信息，也不包含元素的语义信息。除了前缀，命名没有限制，data-后面跟什么都可以。

主要用于在前端的元素上存储一些较为轻量的数据，以便交互时使用，从而不需要多次重复地获取。

怎么用呢，可以通过元素的dataset属性来访问。dataset属性是一个DOMStringMap的实例，包含一组键/值对。元素的每个data-name属性在dataset中都可以通过data-后面的字符串作为键来访问。
（例如，属性data-myname、data-myName可以通过element.dataset.myname访问。

```
<div data-myname='idea' id="myName"></div>
const myName = document.getElementById('myName')
console.log(myName.dataset)
```

![dataset.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a9d59d1daec949bb95b7bb22003c5677~tplv-k3u1fbpfcp-watermark.image?)

自定义属性在某种程度上增强了HTML结构的能力。

## 事件绑定

事件是个大话题，后面会作为单独一篇文来讲，但事件和DOM是关联的，这里提一下。

事件是用户和网页交互的桥梁，需要一个“接口”进行连通，即绑定事件。

如果要给一个按钮绑定点击事件，可以这样：
```
<input type='button' value='click me!' onclick="console.log('clicked!')"/>
```
可以这样：

```
<input type='button' value='click me!' id="myBtn"/>
let btn = document.querySelector("#myBtn")
    btn.onclick = function(){
      console.log('clicked')
    }
```
还可以这样：

```
btn.addEventListener('click',()=>{
          console.log('clicked')
        },false)
```

当然，这个方法在IE中略有差别：

```
btn.attachEvent('onclick',()=>{
          console.log('clicked')
        })
```
通常情况下，为了处理兼容和复用，可以封装一个addHandler()方法，将所有代码都在这个方法中处理即可。当然了，现代框架会最大程度的帮开发者节省这些额外成本，很少需要自己再写兼容代码了。

事件就聊这些，点到为止，下面介绍DOM中的最后一员大将。

## 节点观测——MutationObserver

MutationObserver是什么？概念上，它很接近事件，可以理解为当DOM发生变动会触发Mutation Observer事件。

但是，它与事件有一个本质不同：事件是同步触发，也就是说DOM发生变动立刻会触发相应的事件；Mutation Observer是异步触发，DOM发生变动后并不会马上触发，而是等到当前所有DOM操作都结束后才触发。这样设计是为了应付DOM变动频繁的情况。

使用MutationObserver可以观察整个文档、DOM树的一部分，或某个元素。还可以观察元素属性、子节点、文本。

MutationObserver的实例要通过调用MutationObserver构造函数并传入一个回调函数来创建。

新创建的MutationObserver实例不会关联DOM的任何部分。要与DOM关联起来，需要使用observe()方法。这个方法接收两个必需的参数：要观察的DOM节点，以及一个MutationObserverInit对象。

MutationObserverInit对象用于控制观察哪些方面的变化，是一个键/值对形式配置选项的字典。

每个回调都会收到一个MutationRecord实例的数组。MutationRecord实例包含的信息包括发生了什么变化，以及DOM的哪一部分受到了影响。因为回调执行之前可能同时发生多个满足观察条件的事件，所以每次执行回调都会传入一个包含按顺序入队的MutationRecord实例的数组。

默认情况下，只要被观察的元素不被垃圾回收，MutationObserver的回调就会响应DOM变化事件，从而被执行。要提前终止执行回调，可以调用disconnect()方法。

代码示例如下：
```
// 获取目标节点
const targetNode = document.getElementById('ele');

// 配置（需要观察什么变动）
const config = { attributes: true, childList: true, subtree: true };

// 变动时执行的回调
const callback = function(mutationsList, observer) {
    for(let mutation of mutationsList) {
        if (mutation.type === 'childList') {
            //...
        }
        else if (mutation.type === 'attributes') {
           //...
        }
    }
};

// 创建一个观察器实例并传入回调函数
const observer = new MutationObserver(callback);

// 以上述配置开始观察目标节点
observer.observe(targetNode, config);

// 停止观察
observer.disconnect();
```

需要说明的是，可以通过多次调用observe()方法来复用一个MutationObserver对象观察多个不同的目标节点。这种情况下，MutationRecord的target属性可以标识发生变化事件的目标节点。

说了这么多，它能用来干什么呢？举个大家熟悉的例子：Vue中的nextTick。

大家知道，Vue中从数据变化到更新DOM需要过程，想要在DOM更新后执行某个动作，常用到nextTick 方法，而 MutationObserver 就可以实现这样的效果，所以MutationObserver是拿来实现nextTick的方法之一，为什么说之一，上面说过，框架会替开发者做很多工作，比如某功能的最优方案，次优方案，兼容方案等，考虑到MutationObserver的兼容性，会使用其他替代方案来适配不支持的情况，这里不是重点，不再赘述。

## 总结

本文是DOM篇的下半部分，聊到除DOM节点关系和操作之外的能力，并没有介绍完全，但大体的轮廓已经显现，重要的是，理解DOM是什么，它和哪些东西有关联，我们想要一个效果，想做一个功能的时候，它能为我们提供什么。

它可以是文档，是视口，是容器，是元素，是内容，是接口，是关系，是信息，是工具...是什么完全由我们的需求而定。不论我们使用原生编程，框架编程，还是UI组件库编程，这一点都不会发生改变。

DOM包含很多细节，是值得不断挖掘的宝藏，这两篇文算是开了个头，鉴于水平有限，也可能某些表达不精准，欢迎交流和指正。下篇见！~