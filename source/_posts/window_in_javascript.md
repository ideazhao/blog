title: Window对象在前端领域的角色     
date: 2019-05-30
tags: [学习、javascript]
categories: [总结]
toc: true
---


## 特殊的window

提起window，在网页当中很常见，比如像这样：

    window.onload=function(){
    //执行函数体
    }


这段代码的意思是当网页内容加载完成后要做什么。

在js的领域，window对象有着双重角色，既是用来访问浏览器窗口的接口，又是Global对象。

也正因为此，所有全局作用域中声明的变量、函数都会变成window对象的属性和方法。

比如这样：

    var age = 29;
    function sayAge(){
        alert(this.age);
    }
    alert(window.age); //29
    sayAge();         //29
    window.sayAge();  //29


但二者也不是完全等同，全局变量不能通过delete删除，而直接在window上定义的属性可以。

## 历史遗留
早期的网页中会使用较多的窗口和框架，在同一个窗口内展示有嵌套形式的网页，以及各种弹窗alert、confirm、prompt等，给用户输入或者确认信息，但随着网页开发技术和设计的发展，它们已经难寻踪迹，所以这里不重点说。

## location对象

**基本属性**

location是最有用的BOM对象之一，它提供了当前窗口所加载文档的信息和导航功能。它的主要用途都跟url有关：

- hash：返回url中#符号后面的字符，如果没有，返回空。
- host/hostname：返回服务器名称或者端口号之类。
- href：返回完整url
- port：返回指定端口号
- protocol：返回使用的协议
- search：返回url的查询字符串，即从问号开始到末尾的所有内容

**位置操作**

location可以通过多种方式改变浏览器的位置，最常用的是assign()方法，比如：

    location.assign("http://www.baidu.com");


这样就会马上打开新的url，并在浏览历史中增加一条记录，下面两行代码等效：

    window.location = "http://www.baidu.com";
    location.href = "http://www.baidu.com";

最常见的是location.href。

当然，修改其他属性也可以改变当前加载的页面，通过任何一种方式修改url之后，都会有一条新记录生成，用户可以通过点击后退按钮导航到前一个页面，不过，有时候我们不希望这种操作发生，可以使用replace()方法。像下面这样：

    location.replace("http://www.baidu.com");

它只接收一个参数，即导航到的url，且不会生成记录，用户不能返回前一个页面。

与位置有关的另一个方法是reload()，作用就是它的字面意思，重新加载当前页，但这里有一点点小讲究，如果仅仅是reload，不带参数，页面会从浏览器缓存中重新加载，如果强制从服务器重新加载，则需要传递参数，像这样：

    location.reload(true);

## history对象

history保留着用户上网的记录，每个浏览器窗口、标签页，都有自己的history对象与特定的window对象相关联,出于安全考虑，开发人员一般无法得知用户浏览过哪些网页，但仍然有办法实现前进后退功能，方法就是go()。例如这样：

    history.go(-1);
    history.go(1);

其参数不仅是数字，也可以是一个字符串，浏览器会跳到历史记录中包含该字符串的第一个位置，可以是前进，也可能是后退。比如

    history.go("baidu.com");

另外，前进或者后退也有更直接一些的方法back()、forward()。

除此之外，history还有个length属性，保存历史记录的数量，如果你想确定用户是否一开始就打开了你的页面，可以用到它。

    if(history.length == 0){
        //干你想干的事
    }

history对象不是特别常用，但在某些特殊用途的设计当中，还是要请它出马来搞定问题。

## 总结
window对象在移动互联网浪潮下的地位已经不像PC端那么重，更多涉及的是功能和检测方面，涉及交互较多，其他方面则交由功能更丰富的自定义的代码来实现了。

尽管如此，window对象也不止这么简单，还有一个重要的navigator对象，但说到它内容有有点多，后续单独一篇文章跟大家分享。

欢迎交流！~
