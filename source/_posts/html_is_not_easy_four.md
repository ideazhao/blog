title: HTML并不简单：小角色的大用途  
date: 2024-10-08
tags: [书籍]
categories: [说书匠]
toc: true
---

> 每个系列一本前端好书，帮你轻松学重点。
>
> 本系列来自阅文集团前端专家**张鑫旭**所编写的 **《HTML并不简单》**

笔者第一次接触移动端适配的时候，完全没概念，什么样是做了适配，什么样是没做？不明白。

直到我把按照PC端写的页面用手机打开，傻眼了，怎么那么小，什么也看不清，然后我就开始搜，怎么做移动端适配。

找到的第一个十分有用的答案，是下面这行代码：

```
<meta name="viewport" content="width=device-width,initial-scale=1.0">
```

这段代码的用途是什么，如果没有这里的`width=device-width`，在移动端，网页默认会缩放，按照980px大小的宽度渲染，以便在屏幕内完整显示，这就解释了为什么内容会变小。

如果要更完整，内容正常展示的同时，不允许用户进行缩放，则是下面这样：

```
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
```

由此可见，不止我们所认为的，用于承载页面内容的元素才有用，其他一些“不起眼”的元素也很有用。

### 自动刷新

现在给一个需求，让页面每2秒钟刷新一次，你会怎么做？可能会想到定时器，每2秒调用一次`reload()`方法。但其实只要这样就可以：

```
<meta http-equiv="refresh" content="2;url='xxx'">
```

其中的关键是`http-equiv`，`http-equiv`的用途很多，除此之外，它还可以以这样的方式禁用缓存：

```
<meta http-equiv="cache-control" content="no-cache">  
```

### SEO

随着移动互联网时代各种APP盛行，SEO对于站点的重要性没有以往那么高了，但不代表它不重要，仍有很多用户是来自搜索引擎，SEO同样是由HTML来承担的。

对于一个页面而言，最重要的信息就是“标题”“描述”和“关键字”，简称`TDK`，因为这些信息会直接展示在搜索结果列表页。

```
<title>你并不精通HTML</title>
<meta name="description" content="你并不精通HTML配套学习站点">
<meta name="keywords" content="HTML、HTMLAPI">
```

上面的代码就是本书配套官网首页的`TDK`设置，搜索引擎会将这些信息展示在搜索结果中。

有自己搭建个人博客站点的朋友，SEO是非常值得做好的，因为平时大家查知识点，搜问题解决方案，还是会使用搜索引擎。

### Open Graph协议

这个看起来会陌生，但只是大家平时较少注意，用了也没有深究。

Open Graph协议直译为开放图谱协议，也被称为OG标签，任何页面只要遵守该协议，在被分享到社交媒体SNS网站上时，社交网站就会提取最有效的信息呈现给用户。

基本用法如下：

```
<meta propery="og:title" content="文章标题">
<meta propery="og:type" content="article">
<meta propery="og:url" content="https://www.htmlapi.cn">
<meta propery="og:image" content="https://www.htmlapi.cn/assets/example.jpg">
```

社媒会提取其中的标题、url和图片等，展示在发布的信息中，对于分享的呈现效果很重要，日常我们做`Facebook、Twitter`等分享都会用到。

### 安全区域

开头已经聊了移动端适配，这里补充一下安全区域，这个概念怎么来的呢？

主要是由于IOS系统更新过程中所出现的刘海摄像头、底部触摸横条等，对手机屏幕展示区域的占用和影响。

不被影响的区域就称为“安全区域”，处理安全区域，需要在`meta`中设定`viewport-fit=cover`，只有设置了`viewport-fit=cover`，类似下面的环境变量函数才能生效。

```
padding-top: env(safe-area-inset-top);
padding-bottom: env(safe-area-inset-bottom,0.5vh);
```

以上代码，就是使用CSS属性来为我们的网页内容预留安全区域。

### 状态栏颜色

在比较注重体验的站点中，你会发现网页状态栏的颜色和主体的风格颜色是一致的，这样的好处是更沉浸，更像APP，怎么做的呢？只要这样就可以：

```
<meta name="theme-color" content="#x2014">
```

同时，它还可以搭配亮色或者暗色模式来使用：

```
<meta name="theme-color" media="(prefers-colors-scheme:light)" content="skyblue">
```

### Favicon图标

每有新项目上线，大概率最后一个被发现的bug，就是站点的标签上缺少品牌logo，要么是空的，要么是`Vue`或者`React`的logo，显得开发人员很粗糙。

怎么设置呢，很简单，预先准备好`ico`图片，像下面这样设置即可。

```
<link rel="icon" href="favicon.ico" type="image/x-icon">
```

### 资源预加载

预加载是大家比较熟悉的一种性能优化手段了，为了提升网站的体验，会优先提前加载一部分资源，代码像这样。

```
<link rel="prefetch" href="gitbook/style.js">
<link rel="preload" href="gitbook/style.js" as='script'>
```

上面这段代码中，`prefetch`是预获取，`preload`是预加载，好像差不多，区别是什么：

`prefetch`的原理是缓存，且获取的是尚未到达的下一个页面的资源，优先级较低。

`preload`加载的是本页面即将使用的资源，优先级高于其他页面，另外，`preload`在使用的时候推荐设置`as`属性，精准指定需要加载的元素类型。

凡事都不是只有好处没有弊端，这两者的使用有个“陷阱”，就是到底要设置哪些资源，可以设置多少资源？容易因为把握不好度而产生浪费，原则就是，能够显著提高加载速度的，最主要的资源，才设置，否则不需要设置。

### 脚本依赖

现在大家都已经习惯了ES6风格的模块化开发，较少去关心脚本加载的顺序和依赖关系，可以交给打包工具去做，但有时候还是需要自行操作的，比如：链入一个三方广告、埋点脚本等。

默认情况下，外链的多个JavaScript文件会按照前后顺序一个一个加载，前面的文件没加载完，不会加载后面的文件，包括后续的页面渲染，这就是为什么传统最佳实践中总有一条是“脚本放底部”。

但是，如果这些资源，它们只是“要用”，但没那么重要，这样的结果就有些得不偿失。

此时，我们就可以将对JavaScript文件的引用改成异步的方式，使用`async`属性或者`defer`属性都可以达到这一目的。

```
<script src="a.js" defer></script>
<script src="b.js" async></script>
```

既然都可以，只要一个不就行了？区别自然是有的。

`defer`有“依赖”的意思，如果希望保证加载顺序，就不能随便设置，只能使用`defer`。

`async`就是单纯的异步，“你走你的，我走我的”，a、b各不相干，谁先执行完成是不确定的。

需要注意的是，`defer`属性会推迟对`DOMContentLoaded`事件的执行，直到所有设置了`defer`属性的JavaScript文件都加载完毕。

### type=‘module’

`type="module"`是一种新的<script>类型，这种类型是伴随着ES6 `export`和`import`语法一起出现的。

一旦JavaScript文件设置了`type="module"`，代码的上下文环境就不再是全局对外的，而是私有的，要想外部可访问，要么使用`export`语法暴露，要么使用全局对象暴露。

另外，设置了`type="module"`的元素是异步加载与执行的，且能保证顺序，和defer属性的表现类似。

### 小结

这篇文介绍了一些通常存在于HTML文件头或者尾的“小角色”，它们往往跟内容和业务逻辑无关，但是能帮助我们更好的提升体验，或者为解决问题提供相当便捷的方案，十分值得关注和研究。

更多干货，我们下篇见！
    
欢迎关注公众号：前端说书匠。好文第一时间接收不迷路！~
