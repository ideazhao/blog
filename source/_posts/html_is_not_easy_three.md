title: HTML并不简单：自带交互的元素  
date: 2024-10-01
tags: [书籍]
categories: [说书匠]
toc: true
---

> 每个系列一本前端好书，帮你轻松学重点。
>
> 本系列来自阅文集团前端专家**张鑫旭**所编写的 **《HTML并不简单》**

上一篇我没聊过瘾，相信你也没看过瘾，这一篇接着聊。

就刻板印象来说，HTML是用于展示的，最多会联系上语义化、无障碍。

跟交互有什么关系，这不是JavaScript该做的事？

非也，HTML里有不少元素自带交互，你能想起哪些？`input、select`，就是表单嘛，还有？

当然，往下看。

## details

这个单词很常用，见名知意，详情，但元素并不常用，按照语义来说，它可以用在展示不全内容的地方。

`details`通常和`summary`同时使用，一个概括，一个详情，看起来很合理。

所以，`details`的常规应用就是：

    <details open>
        <summary>这是摘要</summary>
        <p>这里是具体的描述，标签相对随意</p>
    </details>

效果如图：

![](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/d216e1ea6a284ae99de5a6ba0e7ab207~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg54G15oSfX19pZGVh:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNjQxNzcwNTIxMzY4NjA2In0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1746237656&x-orig-sign=FZkxM8plk50bsrAMpZdfccs4Y0Y%3D)

通过点击摘要的部分，可以展开和闭合详情，是不是很常见，也没想到会原生支持？

`details`是这个交互的核心，而`summary`是可以缺省的，如果没有，也可以通过切换`open`属性决定内容的显隐。

其中的`p`元素只是示例，可以根据需要，替换成其他元素。

最常见的使用场景就是悬浮菜单、多项菜单折叠、多级嵌套的树形效果等。

## dialog

这个也很常见，通常我们创建对话框组件就会用这个名字，而它其实也是原生元素之一，专用来实现对话框。

    <dialog open>
        <div>打开一个对话框</div>
    </dialog>

它的内部没什么限制，能放任意HTML元素，同时，支持使用`open`属性或者`dialog.open()`方法打开。

但有一点要注意，它的默认打开位置不是屏幕中心，是靠近按钮的位置，需要改变位置得使用CSS进行设置。

说到这里，似乎`dialog`跟`div`没有区别，没有比它优势的地方，当然不。

#### form和自动关闭

通常，对话框出现后，会在某个时机被关闭，往往是点击一个按钮，通过JavaScript代码进行关闭，而`dialog`内置了一种特性，不使用JavaScript代码也能让对话框关闭。

    <dialog>
        <p>描述文字</p>
        <form method="dialog">
            <button>我知道了</button>
        </form>
    </dialog>

以上这段代码里，`form`元素设置了`method`属性，值为`‘dialog’`，这时候直接点击button即可实现对话框的关闭。

不过有一点要注意，就是`button`必须是`submit`性质的，不能是普通的`type=‘button’`或者`type=‘reset’`等，当然，还有一种方法，是给`button`设置`formmethod="dialog"`也能关闭弹窗。

#### 关闭来源

这是个比较简单，但可以节省代码的特性，弹窗中常有多个按钮能关闭弹窗，如“确认”、“取消”，我们想知道用户点的是哪个，通常通过传参来判断，但也可以用另一种方法，直接给按钮设置`value`属性，然后用`dialog.returnValue`的值进行判断。

#### 顶层特性

弹出框最尴尬的事情是什么？就是它被弹出的时候却发现页面上有别的元素比它层级更高，这个问题常用的解决方案是暴力设定`z-index`，给一个极大值来做保证。

如果使用原生dialog，这种做法也不必了，因为它有个`showModal()`方法，这个方法有点厉害，它能够让弹框自动获取焦点，且弹框外元素无法再获取焦点，同时，它的层级是最高的，无需任何CSS样式设置，此特性的英文名叫`“top-layer”`。

## 原生弹层

虽然`dialog`元素好用，但对于一些敏捷交互，还是略重，比如：点击按钮预览图片。

针对此类场景，一个全新的HTML属性就出现了—`popover`，不仅主流浏览器都支持，而且是一个全局属性，也就是说，它比dialog更加灵活，使用方法如下：

    <button popovertarget="imagePopover">点击我</button>
    <div popover id="imagePopover">
        <img src="example.jpg" alt="">
    </div>

三个要点：

1、弹出元素添加`popover`属性\
2、触发弹出的元素设置`popovertarget`属性\
3、popovertarget属性值等同于弹出元素的`id`值。

点击按钮之后就会看到打开了图片的一个弹出框。

并且，它还自带点击外层内容自动关闭的特性，不用我们另外去写clickOutside之类的东西了。

既然涉及交互，它可以设置样式和响应事件么，可以，用`:popover-open`伪类可匹配显示的弹层元素，而`beforetoggle`和`toggle`这两个事件可感知弹层显隐。

## fieldset

`fieldset`可以帮助我们天然实现如下的样式：

![](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/537666d5cb634323a4599b3d7cd6e72d~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg54G15oSfX19pZGVh:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNjQxNzcwNTIxMzY4NjA2In0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1746237656&x-orig-sign=N%2BBgvHF0vMGOqBXmGLxM4ivTnBk%3D)

多天然？你说自己实现也简单呐，不就是一个绝对定位加个背景色搞定嘛，但它能更简单。

就像这样：

    <fieldset>
        <legend>标题</legend>
    </fieldset>

不需要CSS，是不是简单得出奇？努力拼不过天赋的典范\~

除此之外，它还有个特性很实用，大家平时肯定碰到过这样的需求，用户填写完表单选项后，点击按钮开始处理文件，此时表单需要处于禁用状态，否则用户可能二次操作，这个行为是不可控的，一般怎么做呢，给每个表单分别设置禁用，但这样不论对于开发还是维护都显得繁琐。

如果，我们将表单元素都包裹在`fieldset`元素内部，只需要设置一个属性，就可以将整批元素一起禁用，代码如下：

    <fieldset disabled>
        <input type="text">
        <input type="text">
    </fieldset>

##### 是不是后悔没早点get这个知识点？

## progress进度条

在上传或者下载文件的时候，通常都要用到进度条，我们习惯引用组件，但如果不用组件，一定要自己写一套来模拟吗？

其实原生HTML已经有了`progress`元素，基本使用方法如下：

#####

    <progress value="50" max="100"></progress>

只要给它设置`value`和`max`的属性，就能展示成一个进度条，如果没有设置`value`，就是循环等待状态，可用`CSS:indeterminate`对这种状态做样式设置。

## meter

如果说前面几个元素，大家还有印象，只是没用过，这个元素怕是很多人没见过，它是用来干什么呢。

`meter`直译是“计量器”的意思，计量器的特点：有刻度，可计量。

这两个特点你会想到什么？用不同长度或者颜色来表示“程度”对吧。

不卖关子，密码强度就是它的一个典型适用场景。

    <meter value="20" min="0" max="100" low="30" high="60" optimum="80"></meter>

这么多属性，简直量身定做，这样以来，不用javaScript，仅用CSS就能实现对它的控制了。

其中，`low`表示值过低，`high`表示值过高，值是高还是低的标准，则由`optimum`来决定，`optimum`在哪个区间就说明哪个区间是正常的，其他区间都为异常。

用于设置其样式的是这一批伪类：

    -webkit-meter-inner-element
    -webkit-meter-bar
    -webkit-meter-even-less-good-value
    -webkit-meter-optimum-value
    -webkit-meter-suboptimum-value

大致效果如下：

![](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/36624f14ca554c1bacaa25d8f98f37cf~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg54G15oSfX19pZGVh:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNjQxNzcwNTIxMzY4NjA2In0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1746237656&x-orig-sign=XzdHFfApxJttbcPsyfgli0TuIOo%3D)

大家最好自行写个demo体会一下。

#### 小结

又是收获满满的一篇！两篇文章看过，是不是不敢再小看HTML了？​

近些年标准制订者一直在努力推进添加各种能力和特性，不仅HTML，JavaScript和CSS也一样，知识库的更新速度远超不少开发伙伴的学习速度，所以我们要保持持续的关注和学习为好。

欢迎关注公众号：前端说书匠。好文第一时间接收不迷路！\~