title: JavaScript模块化历程      
date: 2020-04-26
tags: [JavaScript、模块化、总结]
categories: [总结]
toc: false
---

现代前端，模块化已成必备，不仅有多种工具类的库，还被写进规范当中，不论大小公司，代码都遵循模块化思想在开发，它已经成为独立于技术的一种经验和能力。

本文我们就来聊聊这个话题。

## 需求

网页刚出现的时候，只是很简单的文档，样式简单，极少的交互，极少的设计元素，一个页面不会依赖很多文件，逻辑代码少。

但随着Web技术的发展，网页变得越来越丰富，于用户来说是绝对的福音，但对开发者来说，问题逐渐凸显。

- 代码多，命名冲突概率大
- 代码冗余，请求过多拖慢速度
- 文件间依赖增多，易出现引用错误，导致代码运行出错
- 难复用，重写？copy一份？
- 修改或者改版时，要去成百上千行代码里找，难维护

综上所述，不论是从开发还是体验角度，都需要解决方案。

## 模块

无数实践证明，小的、组织良好的代码远比庞大的代码更易理解和维护。因此，优化程序的结构和组织方式，把它们分成小的、耦合相对松散的片段，不失为更明智的做法，这些片段就称为模块。

还好，JavaScript有一种原生的组织方式——函数。

### 函数

函数用来做什么？封装具备一定功能的代码，它里面可以包裹所有类型的东西，且有自己独立的作用域，然后在需要的地方调用即可。譬如：

```js
function f1(){
  //...
}
function f2(){
  //...
}
function f3() {
  f1();
  f2();
}
```
这样做功能明了，实现了代码的清晰组织和分离，但是它们散落在整个文件当中，可能污染全局命名空间，且维护成本较高，继续探索。

### 对象

对象可以有属性，而属性既可以是数据，也可以是方法，这就能够很好地满足需求，而且对象的属性通过对象来访问，相当于设定了一个命名空间。

```js
let myModule = {
  name: '张三',
  getName() {
    console.log(this.name);
  }
}
```
这么一来，在一定程度上解决了命名的问题，但是它的属性仍然是暴露的，可以被外部更改。比如：

```js
myModule.name = '李四';
myModule.getName() // ‘李四’
```

还是不够理想。

### 匿名闭包

这种方式在项目中有不少应用，是利用了闭包的特性——私有数据和共享方法，关于这一点，我们在[闭包](https://ideazhao.com/2020/03/22/js_closure/)中有提及。

代码如下：

```js
//module.js
(function(window) {
  let name = 'idea'
  //操作数据的函数
  function getName() {
    console.log(`${name}`)
  }
  //暴露方法
  window.myModule = { getName } 
})(window)
```

这个时候，可以通过`myModule.getName()`来获取`name`，但无法通过`myModule.name`访问`name`。

```js
myModule.getName() // "idea"
myModule.name  // undefined
```

看起来很不错，同时解决了两个问题，但如果这个模块需要依赖另一个模块呢？

也有办法，别忘了，匿名函数也是函数，可以传参~

```js
//module.js
(function(window,$) {
  let name = 'idea'
  //操作数据的函数
  function getName() {
    console.log(`${name}`);
    $('body').css('color', 'red');
  }
  //暴露方法
  window.myModule = { getName } 
})(window,jQuery)
```

当然，这里的jQuery是另一个地方定义好的模块，通过这种方式引入，就可以在myModule内部使用，而且这就是现代模块实现的思想来源，只是方式不同。

说了上面那么多，模块的实现也有了一些改变和优化，但还有一个东西是没有变的，文件的组织方式，像上面的代码，在页面中会是这样：

```js
<script type="text/javascript" src="jquery-1.10.1.js"></script>
<script type="text/javascript" src="module.js"></script>
<script type="text/javascript">
  myModule.getName()
</script>
```
我们解决了命名冲突，数据保护，和引入依赖，但没有解决依赖与文件加载顺序的强相关性，另外还存在请求过多的问题。

所以，仍然需要更好的方案来解决这些问题。

## 模块化规范

上面都是开发者运用语言本身的特性不断摸索出来的成果，各有用武之地，但又都存在不足。

接下来我们介绍几个应用较广的、更理想的模块化规范。

### CommonJS

CommonJS规范中，每个文件就是一个模块，有自己的作用域，模块的变量、函数、类，都是私有的，外部不可见。

```js
// num.js
var a = 5;
var b = 3;
var add = function (a,b) {
  return a + b;
};
```

既然数据受保护，想用怎么办？——主动暴露。

```js
module.exports.a = a;
module.exports.b = b;
module.exports.add = add;
```

`module.exports`就是暴露的方法。

另一个文件中需要这样引用：

```js
//index.js
var num = require('./num.js');  // ./代表相对路径
console.log(num.a); // 5
console.log(num.b); // 5
console.log(num.add(a,b)); // 8
```

`require`命令负责读入并执行一个JavaScript文件，并返回该模块的exports对象，如果没有找到会报错。

CommonJS的特点可总结如下：

- 所有代码运行在模块内部，不污染全局。
- 模块可以多次加载，但只会在首次加载时运行一次，结果被缓存，再次加载直接读取缓存，想让模块再次运行，必须清除缓存。
- 模块的加载顺序，按照代码出现的顺序。

关于CommonJS，多数人可能知道怎么用，却不知道原理是什么，它看起来跟普通的js文件一样，区别在哪？

简单剖析一下：

首先，像很多Web工具一样，它依托于Node，Node内部提供一个Module构造函数，所有模块都是Module的实例。

```js
function Module(id, parent) {
  this.id = id;
  this.exports = {};
  this.parent = parent;
  // ...
```
每个模块内部，都有一个module对象，该对象有以下几个常见属性：

- module.id 识别符，通常是带有绝对路径的模块文件。
- module.parent 返回一个对象，表示调用该模块的模块。
- module.children 返回一个数组，表示该模块要用到的其他模块。
- module.exports 表示模块对外输出的值。

这么看，应该知道它跟普通js还是有区别，只是这种区别是无感知的，对开发十分友好了。

CommonJS很好地解决了之前提出的所有问题，但还有一点瑕疵，它是同步的，在Node服务端应用当中，模块一般存在本地，加载较快，同步问题不大，在浏览器中就不合适了，所以还需要异步模块化方案。

### AMD

AMD（异步模块定义）是为浏览器环境设计的，它定义了一套异步加载标准来解决同步的问题。

语法如下：

```js
define(id, dependencies, factory);
```

- id 是模块的名字，字符串，可选。
- dependencies 是依赖模块列表，数组，可选。
- factory 包裹了模块的具体实现，是“函数”或者“对象”，如果是函数，返回值就是模块的输出接口或者值。

看个示例：

**有模块依赖**

```js
//  定义
define('myModule', ['jquery'], function($) {
    // $ 是 jquery 模块的输出
    $('body').text('hello world');
});
// 使用
require(['myModule'], function(myModule) {});
```

**模块输出**

```js
define(['jquery'], function($) {
    var HelloWorld = function(selector){
        $(selector).text('hello world');
    };
    // HelloWorld 是该模块输出的对外接口
    return HelloWorld;
});
```

**RequireJS**

RequireJS是一个遵守AMD规范的工具库，用于客户端的模块管理。它通过define方法，将代码定义为模块；通过require方法，实现代码的模块加载，使用时需要下载和导入项目。

```js
//无依赖模块
// msg.js
define(function() {
  let msg = 'www.baidu.com'
  function getMsg() {
    return msg.toUpperCase()
  }
  return { getMsg } // 暴露模块
})

//有依赖模块
// showMsg.js
define(['msg'], function(getMsg) {
  let name = 'idea'
  function showMsg() {
    alert(msg.getMsg() + name)
  }
  return { showMsg } // 暴露模块
})
```
使用的时候只需要像下面这样:

```js
<!-- 引入require.js并指定js主文件的入口 -->
<script data-main="js/main" src="js/libs/require.js"></script>
```

### CMD

CMD规范可说是站在巨人的肩膀上，它整合了CommonJS和AMD规范的特点，专门用于浏览器模块异步加载。

直接看代码：

```js
//无依赖
define(function(require, exports, module){
  exports.xxx = value  
  module.exports = value
})
```

exports 参数是 module.exports 对象的一个引用。只通过 exports参数来提供接口有时无法满足开发者的所有需求。 比如当模块的接口是某个类的实例时，需要通过 module.exports来实现。

```js
//有依赖
define(function(require, exports, module){
  //同步引入
  var module = require('./module')
  //异步引入
  require.async('./module', function (module) {
  })
  //条件引入
  if (status) {
      var x = requie('./x');
  }
  //暴露模块
  exports.xxx = value
})
```

相比之下，CMD规范推崇代码职责更单一，且没有全局require，看起来，头部较轻，所需模块就近引入。

**SeaJS**

CMD规范的集大成者是SeaJS，笔者之前所在的组就是使用SeaJS作为模块加载器。它的使用同RequireJS类似，需要下载和引入页面。

```js
<script type="text/javascript" src="js/libs/sea.js"></script>
<script type="text/javascript">
  seajs.use('./js/modules/main')
</script>
```
看完以上几个方案，是时候介绍原生实现了。

### ES6模块

ES6之前没有专门的模块机制，为提供这些能力，ES6引入两个关键字。

- export——设定模块的对外接口。
- import——将其他模块的功能导入。

它设计思想是尽量静态化，使得编译时就能确定模块的依赖关系以及输入和输出的变量。

```js
/** math.js **/
var basic = 0;
var add = function (a, b) {
    return a + b;
};
export { basic, add };
/** 引用模块 **/
import { basic, add } from './math';
function test(ele) {
    ele.textContent = add(66 + basic);
}
```

导入和导出都既可以是单独的，也可以是集合形式，此处只列集合形式。

除此之外，它还提供了一些不一样的东西：

- as 

```js
// name.js
let myName = "idea";
export { myName as exportName }  // 别名导出

import { exportName as name } from "./name.js";  // 别名导入
console.log(name);  //  idea
```

它提供了更改模块导出或者导入接口名称的能力，但名称之间必须是一一对应的关系。

- export default

```js
// xxx.js
var a = "My name is idea!";
export default a; // 只能有一个

// 可以使用任意变量接受
import b from "./xxx.js"; 
```

这种称为默认导出，就是什么都不管，默认就是它，跟构建工具当中的默认任务类似，直接跑就是。

不过它看起来方便，却可能引起一些潜在的问题，这些问题超出本文讨论范围，相信大家能够以最好的方式去使用它们~

以上两种方法都非刚需，看各自的编码喜好和需要使用。

不论语法，从形式上看，ES6的模块跟CommonJS很像，那它俩有什么区别？

- CommonJS模块输出的是一个值的拷贝，ES6模块输出的是值的引用。即CommonJS中的值的改变，对引用了它的模块没有影响了，ES6模块则仍然会有影响。

- CommonJS模块是运行时加载，加载的是一个对象，运行时才会生成；ES6模块是编译时输出接口，在代码的静态解析阶段就会生成。

- 运行机制不同，ES6模块是动态引用，不会缓存值，模块变量绑定其所在的模块。

不论怎样，模块化已经被加入规范，且看起来更简洁和直观，很多地方都能看到它的应用，其一是不需要再使用第三方工具，其二，即使浏览器没来得及支持，我们也有类似Babel的工具来辅助使用。条件允许的话，还是可以愉快地用起来~

## 总结

内容已经很长，很多东西仍不是很详细，但相信你能够对模块化的发展有个大概的了解，也能感受到它的魅力。

按理说，对于任何一种工具或者工作方式，我们不必知道它是怎么来的，只需知道当下哪种最好用以及怎么用，就可以了，但对它们产生的背景和发展历史有所了解能让我们更加有的放矢，在没人替我们做选择，或者遇到问题时，能有自己的判断和解决方法。

就聊到这儿了，不保证完全正确，欢迎交流，下篇见！~

参考：

[前端模块化详解](https://github.com/ljianshu/Blog/issues/48)
[AMD规范](http://shouce.jb51.net/webpack/amd.html)
《JavaScript忍者秘籍》等