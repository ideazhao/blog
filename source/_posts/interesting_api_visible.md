title: 有趣的API—Page Visibility
date: 2020-03-27
tags: [学习，javascript]
categories: [总结]
toc: true
---

>语言为技术服务，技术为需求服务。

我们在日常浏览网页的时候，经常会遇到这样的情况，有些页面元素，当你在当前网页的时候，它正常进行，切换到其他页面的时候，或者最小化的时候，它就停了。比如：

- 当视频加载到可播放状态，根据用户是否停留在当前页面来决定是否自动播放；

- 视频正在播放时，用户有事离开页面了，是否要暂停播放；

- 需要播放给用户的一次性开场动画，当用户打开页面之后就切换了，是否暂停。

等等。

如果首次遇到，可能会觉得哪里跟之前不一样，但仔细想一下，这种实现不仅有趣，且是合理的。

怎么实现呢？

## 实现方式

相当简单，有这么几个主要的API：

### document.hidden

表示页面是否隐藏的布尔值，页面隐藏，包括：页面在后台标签中或者最小化。

### document.visibilityState

4个可能状态的值：

- 页面在后台标签中或最小化
- 页面在前台标签中
- 实际的页面已经隐藏，但用户可看到页面的预览（win7系统的应该有体会，鼠标放在任务栏，无需打开就可看到小窗页面）
- 页面在浏览器外预渲染

### visibilityChange

这是一个事件，当文档的可见性发生变化时触发。

## 注意点

### 第一个是浏览器兼容

如果需要兼容较低版本，就需要在代码中做检测，然后向后兼容。

```js
var hidden, visibilityChange;
if (typeof document.hidden !== "undefined") {
    hidden = "hidden";
    visibilityChange = "visibilitychange";  //标准
} else if (typeof document.mozHidden !== "undefined") {
    hidden = "mozHidden";
    visibilityChange = "mozvisibilitychange";  //火狐
} else if (typeof document.msHidden !== "undefined") {
    hidden = "msHidden";
    visibilityChange = "msvisibilitychange";  //IE
} else if (typeof document.webkitHidden !== "undefined") {
    hidden = "webkitHidden";
    visibilityChange = "webkitvisibilitychange";  //Chrome
}

// 添加监听器
document.addEventListener(visibilityChange, function() {
    console.log("当前页面是否被隐藏：" + document[hidden]);
}, false);
```

### 第二个是实现差异

visibilityState 在不同浏览器（版本）可能有不同实现方式.

比如IE，是表示状态的数字。

- document.MS_PAGE_HIDDEN(0)
- document.MS_PAGE_VISIBLE(1)
- document.MS_PAGE_PREVIEW(2)
- document.MS_PAGE_PRERENDER(3)

而在chrome里，是字符串。

- “hidden”
- “visible”
- “prerender”

## 小结

本文介绍了一个相对较新，也不那么常用的API属性和方法，但如果你需要，它就会很方便地帮到你。

下篇见！~