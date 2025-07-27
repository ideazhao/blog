title: this指向知多少      
date: 2020-05-08
tags: [JavaScript，学习]
categories: [总结]
toc: true
---

”this“在JavaScript中很常见，用起来也很”香“，每当我们想访问一个值或者设置一个值，常会用到它，原生和框架都是，但它同时又让人困惑，使我们写的代码达不到预期效果，甚至引起bug，本文我们就来看看，this的指向遵循哪些规律。

## this等于自己？

先看几个看似正常却错误的理解。

### 指向自身

比如下面这段代码：

```js
function add(num){
  //记录add被调用的次数
  this.count++;
  console.log("计数" + num)
}
add.count = 0;

add(1)
add(2)
console.log(add.count) // 0
```

可以看到，结果跟预期的不一样，count未发生改变。

实际上，这段代码无意间创建了一个全局变量 count，在执行add的时候，并未改动add的count。

### 指向所在作用域

还是先看代码

```js
function one(){
  var a = 2;
  this.two();
}
function two(){
  console.log(this.a)
}
one();  // a is not defined
```

这种情况略特殊，因为并不总是错的，这里one调用two能够成功，但却无法因此利用two里面的this来访问one里面的a，这是做不到的。

看了两个错误用法，怎样是对的呢？接着往下看。

## this是什么

this指代变量或者方法和对象之间的从属关系，但它是在运行时进行绑定，而不是编写时，这取决于函数调用时的多种条件，所以，**this的绑定和函数声明的位置无关，取决于函数调用的方式**。

上面这句话不难理解，因为在哪调用函数似乎显而易见，其实不然，在某些编程模式下，调用位置可能被隐藏，这时我们就要顺着调用的路径找到调用位置，然后判断它符合哪种绑定规则。

## 绑定规则

### 默认绑定

```js
function get(){
  console.log(this.a);
}

var a = 2;
get();  // 2
```

这种属于独立的函数调用，应用的是函数的默认绑定，this指向全局对象。

怎么判断默认绑定呢，因为它是使用不带任何修饰的函数引用进行调用的。但也要注意，只有在非严格模式下，this才绑定到全局，否则会提示undefined。

### 隐式绑定

看调用位置是否被某个对象拥有或者包含。

```js
function get(){
  console.log(this.a);
}

var obj = {
  a:2,
  get
}
obj.get()  // 2
```

这里我们把get函数放在了obj内，但严格来说，它仍不属于obj，只是被obj包含和调用，这个时候，this就指向了obj，this.a 就和 obj.a等价了。

只是这种情况需要注意的是，它有个就近的规则，就是它只属于离this最近的一层。像下面这段：

```js
function get(){
  console.log(this.a);
}

var obj2 = {
  a:3,
  get
}

var obj1 = {
  a:2,
  obj2
}

obj1.obj2.get(); // 3
```

这段代码中，this绑定在了obj2上，而不是一直向上追溯。

###  隐式丢失

一个常见的问题就是被隐式绑定的函数丢失了绑定对象，导致应用默认绑定，this就到了全局或者undefined。

第一种情况：**方法传递**

```js
function get(){
  console.log(this.a);
}

var obj = {
  a:3,
  get
}

var getNum = obj.get;

var a = "global";

getNum(); // global
```

这里我们会发现，我们把obj的get方法给了getNum，却不是想象中的效果，其实我们上面就说了，这里的get并不真实属于obj，而只是在调用时，this被绑定到了obj，不信你可以像下面这么改一下：

```js
var a = "global";
var getNum = obj.get();
console.log(getNum)  // 3
```
又是3，跟期望的一致，这就是细微差别导致结果的不同。

第二种情况：**回调**

```js
function get(){
  console.log(this.a);
}

function doGet(get){
  get();
}

var obj = {
  a:3,
  get
}
var a = "global";
doGet(obj.get);  // global
```

这种情况跟上面的类似，因为参数传递就是一种隐式赋值，这个时候，执行get方法的时候，还是全局的get，this绑定的就是全局对象了。

回调很常用，所以，因为回调函数而丢失this的情况也常见，甚至于，调用回调函数的函数可能会修改this，这就让代码行为更加地难以捕捉。

所以有什么好办法弥补这些不确定问题的发生么。

## 显式绑定

通过上面的例子可以看到，要想把一个函数的this绑定到对象上，需要下面两个条件：

- 函数作为对象的属性
- 通过属性间接调用函数

如果不想这么做呢？

### 一、call/apply

JavaScript中的函数都有一些有用的特性，可以用来解决这个问题，比如：call 和 apply，因为可以直接指定this的绑定对象，所以称之为显式绑定。

可看如下代码：

```js
function get(){
  console.log(this.a);
}

var obj = {
  a:3,
}
get.call(obj); //  3
```

通过get.call()，我们把this强制绑定到了obj上。

从绑定this的角度看，call和apply的区别只是参数的形式不同，call可以直接写参数，而apply需要以数组的形式传参。

遗憾的是，这仍无法解决上面提到的绑定丢失问题，但它的一种变通方法可以解决。

```js
function get(){
  console.log(this.a);
}

var obj = {
  a:3,
}

var getNum = function(){
  get.call(obj);
}

getNum()  // 2
```

可以看到，这里创建了一个函数给getNum，然后函数内部进行显式地绑定，这样以来，不论再怎样调用getNum，get的this都不会变了。

### 二、bind

因为上面提到的绑定方式较为常用，ES5直接提供了一个内置的方法——Function.prototype.bind。

```js
function get(){
  console.log(this.a);
}

var obj = {
  a:3,
}

var getNum = get.bind(obj);

getNum()  // 3
```
bind()会返回一个硬编码的新函数，把指定的参数设置为this的上下文并调用原函数。

### 三、API调用上下文

有一些库，或者ES新版本的内置函数，会提供一个可选参数，它的作用和bind()类似，确保回调函数使用指定的this，比如：

```js

let arr = [1,2,3];

function get(el){
  console.log(el,this.id);
}

var obj = {
  id:"item",
}

arr.forEach(get,obj);
// 1 "item"  2 "item"  3 "item"
```

类似的这些函数实际就是通过call()或者apply()实现了显式绑定，这样可以少写一些代码。

## new绑定

我们很熟悉的一句话：”没有对象就new一个“。

JavaScript 中有一些内置对象函数和自定义函数的构造调用，都会用到new关键字，它会经历以下过程：

- 创建一个新对象
- 新对象执行prototype连接
- 新对象绑定到函数调用的this
- 如果函数没有返回其他对象，就返回这个对象

就会有如下代码的效果：

```js
function Get(a){
  this.a = a;
}
var getNum = new Get(2);
console.log(getNum.a)  //  2
```

这是大家熟悉的用法，只是中间经历的过程不是表面那么简单，需要理解一下。

new是本文提到的最后一种可以影响this绑定的方法，下面看看这些规则的优先级。

## 规则优先级

显然的，默认绑定优先级是最低的，它可以轻易被改变，所以，我们主要关心隐式绑定和显式绑定的优先级。可按照如下顺序进行判断：

- 是否通过new调用来绑定，是，则绑定新创建的对象
```js
var getNum = new Get(); 
```
- 是否直接或间接通过call、apply、bind绑定，是，绑定指定对象。
```js
var getNum = get.call(obj);
```
- 是否在某个上下文对象中调用，是，则绑定那个上下文对象
```js
var  getNum = obj.get();
```
- 如果都不是，使用默认绑定。

## 箭头函数

曾有人问我，ES6当中比较喜欢哪个设计，我没多想，就说箭头函数，然后他问我，它跟之前的函数有什么不同，this绑定就是它们的不同之一。

**箭头函数不使用this的那几种规则，而是根据外层函数/全局作用域来决定**。

```js
function get(){
  return (a) =>{
    console.log(this.a);
  }
}
var obj1 = {
  a:3,
}

var obj2 = {
  a:2,
}

var getNum = get.call(obj1);
getNum.call(obj2); // 3,不是2
```

因为get内部创建的箭头函数会捕获调用get时的this，getNum也会跟着一起绑定到obj1，且这种绑定无法被修改。

这种就像bind方法一样，确保函数被绑定到指定对象，它取代了传统的this机制。

实际上，在ES6之前，我们常用另外一种方法来实现它。

```js
function get(){
  var self = this;  //  就是这里
  console.log(self.a);
}
var obj = {
  a:2
}
get.call(obj); //2
```

## 总结

this绑定是个看似简单，又有一点复杂的东西，除了默认绑定和构造函数调用可以比较自然地理解，其他都绕了那么一点弯儿，但也不用怕，只要经过反复地思考和实践，掌握它们就是本能反应了。

本文尽量全面，仍难免疏漏，鉴于篇幅太长会增加学习负担，索性没有提及某些特殊情况，多数场景已经够用，欢迎交流探讨。

下篇见！~
