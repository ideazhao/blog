title: 【JS启示记】—原型（链）
date: 2020-03-19
tags: [学习、JavaScript]
categories: [总结]
---

在笔者对JS还很懵懂的时候，曾认为“原型（链）”是个高深难懂的概念，面试题经常看到，网上也随处可见有关它的文章。

可当我抱着一颗严肃而又敬畏的心去研究的时候，才发现，就只是这样？

对原型的理解固然很重要，但它算不上最难，甚至称不上“难点”，仅仅是一个必须懂的东西而已，不信？听我一一道来。

## 为何存在

首先你应该已经知道，JavaScript当中，几乎所有数据类型都能当做对象来用，为什么是“当做”，而不是“是”？因为像“数字、字符串”这些，它本来就不是对象（跟显式定义的对象相比），但如果你要像对象那样使用，比如：

```js
    var num = 123;
    num.toString(); // "123"
```

它也能顺利进行，而且示例的这种用法极为常见，为什么？

这里经历了这么一个过程：

- 内置包装类临时构造了一个Number()对象实例
- Number()实例具有继承自Object()的方法toString()

本次代码执行就由继承而来的toString()来完成。

好，其实到此为止，我们就看到了一条完整“原型链”的东西。

> num——>Number——>Object

JavaScript本身并非具备完善面向对象特性的语言，它借助原型链来实现**继承**。

## 从对象谈起

既然原型链跟对象有关，就从对象说起，当然，这不是介绍对象的文章，基础略过。

把对象分为三类：

- 直接量和通过Object创建
- 内置包装类（显式或隐式都算）
- 自定义构造函数创建

不论哪一类，道理都一样，只是“原型”和“链”的长度不同而已。

第一种最短，它不是由其他对象创建而来，是没被“污染”的，最“干净”的顶层对象，既没有“包装类”那些系统已经定义的自有方法，又没有开发者定义的属性和方法。

说完对象，下面来看它们是怎么链起来的。

**内置包装类**

```js
  var str = new String();
```

链是这样的

> str——>String——>Object

**构造函数**

```js
   function Origin(){
     this.prop = prop;
     this.method = method;
   }

   var obj1 = new Origin();
```

这时候链是这样的

> obj1——>Origin——>Object

到此，虽然示例很简单，你应该明白了什么。

## 类比理解

如果还没懂（甚至上面提到的东西不知所云），也完全没关系，因为单纯搞懂原型链是怎么回事并不必要懂JS，**优秀的思想和模式都是通用的**。

我们拿另一项Web技术CSS来做个类比。

- Object，相当于我们页面啥都还没写的时候，body所具备的样式；
- 内置包装类，即标题、ul、input等有自带样式的元素；
- 构造函数，就是“啥也没有”的div、p、span之类；

当你直接写个div在页面上，再填几个文字进去，它同样有颜色和大小，但是你可以在div上定义新的规则将其覆盖，这就类似于对象属性的继承和覆盖。

当你写个标题或者表单元素，它们会比上面的文字多一些特殊的自有样式，这就类似于String或者Array对象，它们除了继承自Object，还具备特有的属性和方法，当然，你也可以自定义把它们覆盖掉（但通常不建议这么做）。

```html
    <body>
      <div>
        <p></p>
      </div>
    </body>
```

> 这里的链就是  p——>div——>body

看到这里，是不是舒服多了？来加个餐。

## 检查、设定

搞懂了”原型链“，顺带分享几个”检查“和”设定“原型的方法。

### 检查

`constructor`

  这是个不常被提到，但还挺管用的属性。

```js
  var date = new Date();
  date.constructor
```

如果只是这么写会有问题，它会把整个函数体返回，我们取其名字即可：

```js
  date.constructor.name //  Date
```

但是，constructor并非不可修改，你可以给 Prototype 链中的任意对象添加名为constructor的属性或者对其进行修改，所以它引用的目标可能跟想象的有出入，不推荐使用。

`instanceof`

这个方法常用来判断数据类型，但它也可以用来判断某对象是否在另一个实例的原型链上，返回布尔值，比如上例

```js
date instanceof Date  //  true
```

自定义构造函数同样适用

```js
  function Person(){
    //...
  }
  var liming = new Person();
  liming instanceof Person;  //  true
```

  `__proto__`和`Object.getPrototypeOf()`

```js
  date.__proto__ == Date.prototype; // true 
```

  但`__proto__`不建议使用，因为它是一个内部属性，而不是开放API，且并未被写入新版本的ES正文中，可能存在浏览器实现的差异，已经有了等效的方法，就是`Object.getPrototypeOf()`

```js
  Object.getPrototypeOf(date) == Date.prototype // true
```

  `isPrototypeOf()`

  这种方法较新，也更直观和方便，在浏览器支持的情况下，可以优先选择。

```js
  Date.prototype.isPrototypeOf(date); // true
```

知道了检查方法，再看指定。

### 指定

聊两个点：

一，把某对象指定为另一个对象的原型
二，在原型上定义属性/方法

指定的目的是“继承”，因为并非所有的对象创建完毕之后就不再改变了，也或者我们需要它继承另外一个对象不一样的东西，这时候就要变换/指定其他原型，我猜你会想到这么个方法。

```js
a.prototype = b.prototype;
```

这个方法可以让a引用b，但它的机制可能不是你想要的，当你执行类似 a.prototype.myMethod = ... 的赋值语句时也会修改到 b.prototype 对象本身。

所以，想要创建一个更加稳妥的关联对象，要使用如下方法

`Object.create()`

```js
a.prototype = Object.create(b.prototype)
```

这个语句的意思是：“创建一个新的 a.prototype 对象并把它关联到 b.prototype”。

你应该会有个疑问，既然有这个语句，就代表a原本是存在的，那么这样做之后，a.prototype原本关联的对象哪儿去了？

是的，两者的关系被剪断了，它被抛弃了，这也是此方法唯一的瑕疵。

还有一个更标准且可靠的方法来修改对象的Prototype关联，往下看。

`Object.setPrototypeOf()`

ES6开始可以直接修改现有的 a.prototype 

```js
Object.setPrototypeOf( a.prototype, b.prototype );
```

### 共享

定义原型链属性/方法的一个目的是共享，如

```js
    function People(name) {
        this.name = name
    }
    People.prototype.age = 23 // 岁数
    // 创建两个实例
    var People1 = new People('lucy');
    var People2 = new People('lily');
```

这种其实就是**组合”构造函数和原型”模式**，既共享了年龄，又独有了name，这是一种很常见的方式，是比较好的实践经验。

## 小结

此文重点分享”原型链“，但求简单易懂，无法兼顾广度和深度，鉴于JS和对象本身的细节很多，难免有遗漏或描述不妥，如需更多了解，可查阅官方文档、《JavaScript权威指南》、《你不知道的js》或其他优质资源，欢迎交流。