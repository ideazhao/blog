title: Console很飒，不止log
date: 2020-03-31
tags: [学习，JavaScript]
categories: [总结]
toc: true
---

日常工作中，难免存在为了代码调试而试验性地输出一些东西，曾经都爱用alert()，高兴了弹个数据，但让人头疼的是，它会中断代码执行，得手动关掉，所以有人就用 document.write()，这样也有弊端，它会把内容写到网页上，我们是调试代码，这样显得多余，种种原因，console.log()就成了香饽饽，它不会影响代码运行，且只会把内容输出到控制台，挺好。

## 别把console不当对象

JavaScript语言中，到处是对象的运用，有很多内置和自定义的对象闪耀光芒。

我们一般不会用console做什么实质的事情，更别提业务逻辑，所以，会忽略它本身也是对象，不过，它不属于原生js，而是宿主（通常是浏览器）的内置对象，用来在控制台输出一些代码执行情况的信息，既然是对象，就有方法，log仅仅是其中之一。

## console的方法

**提示类**

这类方法，它们的目的和效果类似，都是提示，只是提示的类型不同。

`log`
`debug`
`info`
`warn` 
`error`

既然类型不同，就应该在UI上有所区别，这是浏览器做得比较贴心的一点。

![](/img/console/console_info.png)

有了这个区分，甚至不需要看图标，扫一眼就能知道是哪一类。

除此之外，它们有各自的职责所在。

`info`用于输出信息或者通知，跟log很像，但没log常用和强大。

`debug`输出“调试”的信息，且控制台配置为显示时才能看到。它常出现在代码里，就像这样：

```js
function func(){
  debugger;
  console.log("bug"); 
}
```

这段代码当运行到 debugger 的地方就会停下，这样可以辅助定位问题，程序是在前面出错，还是后面出错，最终精确到哪一行出错。

当然，相比手动书写，我们经常是在控制台里通过可视化工具进行操作，更方便，也更灵活。

除此之外，更常见的是下面这两种。

`warn` 和 `error`

这两个方法是对代码执行情况最直接的反馈，不需要手动去写，控制台就会有很多警告和报错...我们当然不希望看到，但一旦出错，有提示信息总比没有好。

warn 代表代码中有不太推荐的写法或者将来可能存在的错误，但在当下不影响运行。

error 就没有那么友善了，轻则某段代码无效，重则页面停止解析执行。

常见的是如下几类：

- SyntaxError解析错误
  
这种算低级错误，比如，漏写个括号，少写个引号，引号写成了中文的等等，属语法解析层面。表现为：

  >Uncaught SyntaxError: Unexpected ...

如果使用的编辑器没有提醒功能，找起来还挺费劲儿的，通过控制台来帮助定位就很方便了。

- ReferenceError引用错误

这个错误也常见，比语法解析稍微高级一点，但也不值得骄傲~它代表引用一个不存在的对象，或者不合法的赋值，有时是真的忘记定义，有时可能就是单词拼错。

  >Uncaught  ReferenceError: a is not defined

- TypeError类型错误

变量或参数不是预期类型时发生的错误。

  >Uncaught TypeError: a is not a function(…)

这种错误常出现在你以为某个方法或者对象已经存在或者引用，却由于疏漏不能正常引用的情况。

通常错误信息会自动在控制台输出，但如果你需要在其基础上自定义输出并呈现给用户，有这么几个可使用的属性和方法：

- message 描述信息
- name 类型名称
- toString 包含错误信息的字符串

比如：

```js
try {
    adddlert("Hello!");
}
catch(err) {
    console.log(err.name);  //  ReferenceError
    console.log(err.message);  //  adddlert is not defined
}
```

此处仅列三种极为常见的异常，更多种类可自行查阅资料。

`assert`

assert 方法在很多地方被翻译成”断言“，譬如一本很不错的书《JavaScript忍者秘籍》中，就随处可见它的身影。

它接收两个参数，第一个是表达式，第二个是字符串。只有当第一个参数为false，才会输出第二个参数，否则不会有任何结果。

```js
console.assert(1 > 2, "条件不成立")
// Assertion failed: 条件不成立
```

**计算**

`console.count`

```js
function func(){
  console.count("order");   // 此处如果不填，默认是 default
}

for(let i = 0;i<3;i++){
  func();
}
```

这段代码输出

>order:1
>order:2
>order:3

可以看到，它能起到统计代码执行次数的作用。

`console.time()`和`console.timeEnd()`

这两个方法也常被提到，就是测试一段代码的执行时间，在进行代码优化之后，对比哪种写法的执行效率更高。
刚好，我们前面就有一段代码，索性来测试一下它的运行时长。

```js
console.time();
function func(){
  console.count("order");   // 此处如果不填，默认是 default
}

for(let i = 0;i<3;i++){
  func();
}
console.timeEnd();
```

输出在最后多了一行

>default: 0.349853515625ms

说明这段代码执行用了大概0.35毫秒。

## 花样

console还有什么花样？有的。

`trace`

追踪函数调用轨迹，比如，有个求和方法：

```js
function add(x,y){
    console.trace();
　　return x+y;
}
//  假设调用如下:
var result = add3(1,1);
function add3(x,y){return add2(x,y);}
function add2(x,y){return add1(x,y);}
function add1(x,y){return add(x,y);}
```

控制台就会这样显示：

![](/img/console/console_trace.png)

`table`

语法格式是：`console.table(tabledata, tablecolumns)`

第一个是填充到表格的数据，可以是数组或者对象，必填，第二个是标题栏名称，选填。

```js
var table = [
     {"name": "张三", "sex": "F"},
     {"name": "李四", "sex": "M"},
     {"name": "王二", "sex": "F"},
     {"name": "麻子", "sex": "F"},
];
console.table(table);
```
![](/img/console/console_table.png)

相比常规输出，这种格式开发者会觉得更加舒适。

`占位符`

占位符，是用某种特定规则的符号替代真正的内容来占位，看代码。

```js
  console.log('%d年%d月%d日',2020,03,30);  //  2020年3月30日
```

这里的 %d(还有%i) 代表整数，它还支持字符串（%s）、浮点数（%f）和对象（%o）。

```js
let person = {
  name:'idea',
  age:18
}
console.log("%o可以用来创建人的类",person);  //  {name: "idea", age: 18}可以用来创建人的类
```

占位符跟函数中的参数作用差不多，方便了数据的引用和操作。

`样式`

什么？你说的是那个样式吗？对，是那个样式~

语法是：`console.log("%c内容",样式）`

这里%c起到了关键作用，它的功能是**根据提供的样式代码对输出内容进行格式化**。

```js
console.log("%c3D Text"," text-shadow: 0 1px 0 #ccc,0 2px 0 #c9c9c9,0 3px 0 #bbb,0 4px 0 #b9b9b9,0 5px 0 #aaa,0 6px 1px rgba(0,0,0,.1),0 0 5px rgba(0,0,0,.1),0 1px 3px rgba(0,0,0,.3),0 3px 5px rgba(0,0,0,.2),0 5px 10px rgba(0,0,0,.25),0 10px 10px rgba(0,0,0,.2),0 20px 20px rgba(0,0,0,.15);font-size:5em");
```

控制台里就能看到如下炫酷的效果：

![](/img/console/console_css.png)

有点意思吧，不过这个效果是拿来做试验的，略显招摇，没有实际用途，如果你想自定义输出的字体，改个字号和颜色就好。

## 总结

作为一名开发者，我确实曾以为 console 就只能 log，也觉得控制台那些乱七八糟的东西没什么用，没规律可循，更觉得某些人直接在控制台里写代码是在卖弄技巧，后来就是被“啪啪”打脸的过程，所以，哪块知识都不宜小看，不断挖掘和探索才是正解~

本文就到此了，鉴于笔者水平有限，可能有遗漏或者错误，欢迎指正和交流~

