title: 【轻聊前端】强大的DOM（上）
date: 2021-12-04
tags: [分享]
categories: [JavaScript]
toc: true
---

> 现今各种框架、工具‘横行’，到处在讲原理和源码，更有跨端技术需要我们去探索，但如果基本功不好，学什么都是事倍功半，效果很不好，花费时间的同时打击自信心。此篇文章，为我所计划的【轻聊前端】系列第（十一）篇，旨在系统地、逻辑性地把原生JavaScript知识分享给大家，帮助各位较为轻松地理清知识体系，更好地理解和记忆，我尽力而为，望不负期待。

在系列的第一篇文里，我们说了“编程”是什么，而Web编程自然需要两个元素都在——’编程‘、’Web‘。

前面已经聊了"编程"的若干元素。

- “变量”，用于存储数据；
- “对象”，用于封装数据；
- “数组”，用于存储一系列同类数据；
- “函数”，用于封装行为。

作为程序，这些就够了，但很多人从学习JavaScript到写web页面都会有个坎——“学会了语法，写网页从何处下手？”。

要使编码能够实际作用于网页，和网页进行交互，还需要另外两样东西：DOM 和 BOM。

> JavaScript = ES + DOM + BOM。

BOM——浏览器对象模型（Browser Object Model）、DOM——文档对象模型（Document Object Model）。

BOM用于承载网页，DOM用于展示网页。

本文的主角就是 DOM。

## 什么是 DOM

从表现看，它决定了**网页中的内容**；从编码看，它包含了**属性和能力**。

前一种很好理解，后面一种也似曾相识，这不就是对象？所以，DOM就是**网页结构和编程接口的结合体**。

有一点要说明的是，尽管上面提了 JavaScript 的公式，但 DOM 并不专属于 JavaScript，而是跨平台、语言无关的。之所以跟 JavaScript 产生关联，是因为 JavaScript 中提供了 DOM API。

任何HTML或XML文档都可以用DOM表示为一个由节点构成的层级结构。节点分很多类型，每种类型对应着文档中不同的信息或标记，也都有自己不同的**特性、数据和方法**，而且与其他类型有某种关系，这些关系构成了层级，让标记可以表示为一个以特定节点为根的树形结构。就是我们常说的**DOM树**了。

DOM很强大，但在很长一段时间里都令人头疼，**使用繁琐、版本多、兼容难做、性能损耗大**...给人的印象就是，DOM是原始且不好的东西，最好敬而远之。

所以，大家谈论比较多的是怎样”减少“跟原生DOM打交道，比如几年前的Jquery/Zepto，近几年的Vue、React。前者对DOM操作进行了很好地兼容和封装，使用更方便，后者则引入”虚拟DOM“和更合理的diff算法，代码中极力避免直接操作DOM，而是操作数据，框架根据数据变化来处理从”虚拟DOM“到”真实DOM“的转换。

但是，这并不代表不需要DOM了，更不是人们忽略它的理由，甚至有些场景下操作DOM是唯一途径，所以，熟练掌握DOM相关的知识依旧是现代前端开发应该做到的。

## Document

在浏览器中，文档对象document表示整个HTML页面，它是window对象的属性，因此是一个全局对象。

### 文档信息

document对象包含一些页面共有信息，如：URL、domain和referrer。

- URL包含当前页面的完整URL
- domain包含页面的域名
- referrer包含链接到当前页面的那个页面的URL。如果当前页面没有来源，则referrer属性包含空字符串。

这些信息都可以在请求的HTTP头部信息中获取，只是在JavaScript中通过这几个属性暴露出来而已。

在这些属性中，只有domain属性是可以设置的。但出于安全考虑，不能设置URL中不包含的值。

当页面中包含来自不同子域的窗格`<frame>`或内嵌窗格`<iframe>`时，设置document.domain是有用的。因为跨源通信存在安全隐患，所以不同子域的页面间无法通过JavaScript通信。此时，在每个页面上把document.domain设置为相同的值，就可以访问对方的JavaScript对象了。

### 文档写入

document对象有一个古老的已经不太常用的能力，即向网页写入内容。对应4个方法：write()、writeln()、open()和close()。

其中，write()和writeln()方法都接收一个字符串参数，可以将字符串写入网页中。write()简单地写入文本，而writeln()还会在字符串末尾追加一个换行符（\n）。这两个方法可以用来在页面加载期间动态添加内容。如果想用脚本最快地在浏览器输出点什么，write()是首选。

但有一点需要注意，在页面渲染期间可以通过document.write()向文档输出内容，但如果**在页面加载完之后再调用document.write()，输出的内容会重写整个页面**，比如onload事件的回调函数中，要格外小心。

```
document.write('hello world')
```

聊完全局，来看看具体的节点。

## DOM节点

每个页面都是个元素树，由很多节点组成，所有节点类型都继承Node类型，都共享基本属性和方法。

### 节点类型

每个节点都有自己的类型归属，比如：元素节点、文本节点，类型值nodeType和常用节点类型对应关系如下：

- 元素：1
- 属性：2
- 文本：3
- 注释：8

可以通过如下代码，拿到页面中一个div的节点类型：

```
let div = document.getElementsByTagName("div")[0];
div.nodeType  //1
```
需要注意的是，空格和换行也会被算作节点当中，被当做文本节点处理。

除了nodeType，nodeName 与 nodeValue也可以用来对节点进行更具体的查询，只是并不总返回合理的值，最好先对节点类型做判断再进行操作。

### 节点关系

**子节点**

childNodes是元素子节点的集合，每个节点都有一个childNodes，包含一个NodeList的实例。NodeList是一个类数组对象，用于存储可以按位置存取的有序节点。

NodeList对象独特的地方在于它是一个对DOM结构的查询，因此**DOM结构的变化会自动地在NodeList中反映出来**。

可以使用两种方式访问nodeList中的元素——中括号、item()。

```
let firstChild = parent.childNodes[0]
let firstChild = parent.childNodes.item(0)
```

多数开发者倾向于使用中括号，更像访问数组项。

**父节点**

每个节点都有一个parentNode属性，指向其DOM树中的父元素。

**兄弟节点**

使用 previousSibling 和 nextSibling 可以在节点间导航，第一个节点的 previousSibling 属性是null，最后一个节点的 nextSibling 属性也是null。

**首、尾节点**

firstChild 和 lastChild ，分别指向childNodes 中的第一个和最后一个子节点。

利用这些关系指针，几乎可以访问文档树的任何节点，而这种便利性是childNodes的最大亮点。

**判空**

还有一个方法是hasChildNodes()，返回true说明节点有一个或多个子节点。相比查询childNodes的length属性，这个方法无疑更方便。

### 元素定位

不论是查询还是操作DOM，首先需要获取目标元素，所幸，它为开发者提供了多种方法。

- getElementById()：接收元素的ID作为参数，若页面中存在多个相同ID的元素，返回第一个。

- getElementsByTagName()：接收元素的标签名作为参数，返回包含零个或多个元素的NodeList。

- getElementsByName()：接收name属性值作为参数，返回具有给定name属性的所有元素。

除此之外，document对象上还有一些特殊的属性，代表特定的集合，它们是：

document.anchors：包含文档中所有带name属性的`<a>`元素

document.forms:包含文档中所有`<form>`元素

document.images:包含文档中所有`<img>`元素

document.links:包含文档中所有带href属性的`<a>`元素

看到这，你肯定觉得少了点什么，既然可以通过Id、TagName、Name来进行元素定位，却没有class？是的，早期的标准里并没有class定位的方法，直到HTML5版本，通过class定位元素的方法才推出。

- getElementsByClassName()：接收元素的类名作为参数，返回包含零个或多个元素的NodeList，该方法加入标准的时间较晚，早年尚需考虑兼容问题，现在则无需顾虑。

除此之外，还有一个值得关注的是**classList**属性，根据业务逻辑更改元素的样式，操作class是很常见的需求，HTML5之前，我们可以操作className，但className是个字符串，需要把它拆分、修改、再赋值，略繁琐。classList提供了更简单也更安全的实现方式。

- add（value），向类名列表中添加value。如果已存在，什么也不做。
- contains（value），返回布尔值，表示给定的value是否存在。
- remove（value），从类名列表中删除指定的value。
- toggle（value），如果类名列表中已经存在value，则删除；不存在，则添加。

用过Jquery的朋友应该对这种操作很熟悉，但早年的原生没有这么便捷的操作，现在可以了。

有了classList属性之后，除非是完全删除或重写元素的class属性，否则className属性就用不到了。IE10及以上版本和其他主流浏览器都实现了classList属性。

既然提到Jquery，不得不提 Selectors API，上述定位元素的方法还都比较传统，在新的W3C标准里，已经提供了类似Jquery的选择器定位能力——CSS查询API。

- querySelector()：接收CSS选择符参数，返回匹配该模式的第一个后代元素。

- querySelectorAll()：接收一个用于查询的参数，返回所有匹配的节点，是一个NodeList的静态实例。

不论是标签、id还是class，都属于CSS选择器，所以，以往需要用三种不同方法实现的，新的API只需一种，除此之外，它还支持后代元素等选择，无疑是一大便利。

需要注意的是，querySelectorAll返回的是一个静态的“快照”，而非“实时”的查询。这样的底层实现避免了使用NodeList对象可能造成的性能问题。

还有最后一位成员——matches()，它接收一个CSS选择符参数，如果元素匹配该选择符返回true，否则返回false。

### 元素遍历

有时候，我们需要遍历子元素并对其进行操作，每个元素都可以拥有任意多个子元素和后代元素，元素本身也可以是其他元素的子元素。

而childNodes属性包含元素所有的子节点，可能是其他元素、文本节点、注释或处理指令。不同浏览器在识别这些节点时的表现有明显不同。

如下一个经典示例：

```
<ul>
    <li></li>
    <li></li>
    <li></li>
</ul>
```

在解析以上代码时，ul元素会包含7个子元素，3个是li元素，还有4个Text节点（li元素周围的空格）。如果把元素之间的空格删掉，则所有浏览器都会返回同样数量的子节点。

这种情况下操作元素节点，过往我们要通过nodeType判断一次节点的类型，然后再进行后续操作。

现在有两个新的选择；

1、Element Traversal规范

Element Traversal API为DOM元素添加了5个属性：
- childElementCount，子元素数量；
- firstElementChild，第一个Element类型的子元素；
- lastElementChild，最后一个Element类型的子元素；
- previousElementSibling，前一个Element类型的同胞元素；
- nextElementSibling，后一个Element类型的同胞元素。

2、children

> children 和 childNodes 有什么区别？

你没看错，猝不及防就是一道面试题，笔者就遇到过，不往下看你能答出来么？

既然有了childNodes，可以拿到元素的所有子元素，为什么还需要children。事实上，正是IE9之前的版本与其他浏览器在处理空白文本节点上的差异，才有了children属性的出现。

children属性是一个HTMLCollection，**只包含元素**类型的子节点。相当于帮开发者过滤掉了其他类型的节点。

在现代浏览器中，所有DOM元素都会有这些属性，有了 Element Traversal API 和 children属性，开发者就不用担心误选节点导致操作错误的问题了。

### 操作节点

定位和遍历都是找到节点，可称之为“查”，要与DOM进行交互，还得会“增、删、改”。

- 创建——createElement()

最常用的创建DOM是创建元素，像这样：

```
let newNode = document.createElement('div')
```

- 尾部增——appendChild()

在childNodes列表末尾添加节点，appendChild()方法返回新添加的节点。

```
let resNode = parentNode.appendChild(newNode)
```

需要注意的是，**如果把文档中已经存在的节点传给appendChild()，则这个节点会从之前的位置转移到新位置，即一个节点不会在文档中同时出现在多个地方**。

- 前增——insertBefore()

如果想把节点放到childNodes中的特定位置而不是末尾，可以使用insertBefore()方法。这个方法接收两个参数：要增加的节点和参照节点。调用这个方法后，要增加的节点会变成参照节点的前一个同胞节点，并被返回。

```
let resNode = parentNode.insertBefore(newNode,targetNode)
```
既然有前增，怎么实现后增呢？这也是一道经典面试题哦~

- 替换——replaceChild()

appendChild()和insertBefore()在插入节点时不会删除任何已有节点。相对地，replaceChild()方法接收两个参数：要插入的节点和要替换的节点。要替换的节点会被返回并从文档树中完全移除，要插入的节点会取而代之。

```
let resNode = parentNode.replaceChild(newNode,targetNode)
```

- 移除——removeChild()

要移除节点而不是替换节点，可以使用removeChild()方法。这个方法接收一个参数，即要移除的节点。被移除的节点会被返回。

```
let resNode = parentNode.removeChild(oldNode)
```

- 克隆——cloneNode()

所有节点类型还共享了两个方法。第一个是cloneNode()，会返回与调用它的节点一模一样的节点。cloneNode()方法接收一个布尔值参数，表示是否深复制。在传入true参数时，会进行深复制，即复制节点及其整个子DOM树。如果传入false，则只会复制调用该方法的节点。

```
let resNode = targetNode.cloneNode(true)  //深复制目标节点
```

ps：cloneNode()方法不会复制添加到DOM节点的JavaScript属性，比如事件处理程序，只复制HTML属性，以及可选地复制子节点，除此之外则一概不会复制。

- normalize()

最后一个方法是normalize()。它的唯一的任务就是**处理文档子树中的文本节点**。

由于解析器实现的差异或DOM操作等原因，可能会出现并不包含文本的文本节点，或者文本节点之间互为同胞关系。在节点上调用normalize()方法会检测这个节点的所有后代，从中搜索上述两种情形。发现空文本节点，则将其删除；如果两个同胞节点是相邻的，则将其合并为一个文本节点。

- 批量操作

讲到这里，你会发现以上操作比较适合单个特定元素，而不适合批量操作，相比先创建一堆节点，再把它们以正确的顺序连接起来，直接插入一个HTML字符串要简单（快速）得多，即innerHTML属性。

浏览器在读取innerHTML属性时，会返回元素所有后代的HTML字符串，包括元素、注释和文本节点。在写入innerHTML时，则会根据提供的字符串值以新的DOM子树替代元素中原来包含的所有节点。

```
let newNode = document.createElement('div')
newNode.innerHTML = '<div><header></header><div></div><footer></footer></div>'
```

在写入模式下，赋给innerHTML属性的值会被解析为DOM子树，并替代元素之前的所有节点。

所赋的值默认为HTML，其中的所有标签都会以浏览器处理HTML的方式转换为元素，如果赋值中不包含任何HTML标签，则直接生成一个文本节点。

优点：这种方法的优势在插入大量html更便捷，HTML解析器在浏览器中是底层代码（通常是C++），比JavaScript快得多。

缺点：频繁操作造成性能损耗（可先构建字符串，将操作合并为一次来优化）；被替换的元素，如果有绑定的事件处理程序或其他对象的属性，这种关系会被滞留在内存中（最好在操作前手动删除）；可能留给用户xss攻击的机会（用户输入型数据避免以此种方式注入网页）。

说完大的操作，该看看对节点本身的操作了。

- 修改

我们将其归为两类——修改属性，修改样式。

其实样式也是属性之一，但从操作目的上二者略微不同。

先说样式，元素的样式可以是外部样式表的，也可以是行内的，如果是外部样式，通常通过操作class来改变，就是上文提到的classList，如果是行内，则通过style属性，大体是这样：

```
var div = document.getElementsByTagName("div")[0];
    div.style.width="100px";
    div.style.height="100px";
    div.style.backgroundColor="red";
```
先拿到目标节点，然后通过点操作法更改具体的样式，需要注意的是，在css中使用分割线“-”表示的属性，这里要换成驼峰式大小写（示例中的backgroundColor）。

如果是一次性批量改多个，还可以使用**cssText**属性，相对较快捷。

```
div.style.cssText = 'width:100px;height:100px;backgroud-color:red'
```
但是，style有个缺陷，在读状态下，它只能拿到行内样式，无法拿到真实表现的样式。什么是真实表现？即元素受多种来源样式规则影响所呈现出的样子。

有个方法可以帮我们做到这一点——getComputedStyle，这是个挂在全局的方法，使用方式如下：

```
getComputedStyle(ele,'可选参数')
```
该方法接收两个参数：要取得样式的元素和伪元素字符串（如":after"）。如果不需要查询伪元素，第二个参数可以传null。getComputedStyle()方法返回的就是元素的计算样式了。

因为计算样式是包含所有样式加一起的结果，甚至包含浏览器内部样式表中的信息，所以看起来会很多，如果只需要拿到部分项的值，使用点操作符即可，这里不赘述。

关于样式操作，日常使用就这些了，接下来看除样式之外的属性。

当然，也没什么特殊的，无非是get和set。

- 点操作法

普通属性也是属性，所以可以用点操作法，比如我们拿到了一个输入框元素，将其设置为禁用状态，就可以这样。

```
let ipt = document.querySelector('ipt')
    ipt.disabled = true
```

- get/set方法

```
let ipt = document.querySelector('ipt')
    ipt.getAttribute('disabled')  // false
    ipt.setAttribute('disabled',true)
    ipt.getAttribute('disabled')  // true
```

## 总结

内容够多了，但这也仅是完整DOM体系的冰山一角，文中列一些常见、常用的罢了。

本文标题是强大的DOM，因为网页上几乎所有东西都跟它相关，掌握了它，就相当于把住了网页的“脉”，需要什么元素，应该以怎样的方式去更改元素，怎样拿到特定元素所包含的数据和具备的能力，怎样更友好地和网页交互，都建立在对DOM的深入掌握之上的。

哪怕现今的编程倾向于框架化、模板化，数据驱动，也不能包含一切，框架没有的能力怎么办？我们想使用组件库中某个特定的能力怎么办？往上追溯都能看到一些DOM的影子，它就是根基。

因联系较多，这块分上下两部分聊，但也不一定面面俱到，欢迎交流。
