title: 对象拷贝与应用剖析
date: 2020-04-19
tags: [JavaScript,总结]
categories: [总结]
toc: false
---

## 来由

首先铺垫一点背景知识，JavaScript中值的类型分为基本值和引用值。基本值存储变量的实际值，而引用值存储的是一个地址，该地址指向内存中的某个位置，这个位置才是存储变量的实际值。

所以，如果存在以下操作：

```js
var a = {}; 
var b = a;  

a.name = 'idea';

a.name  // 'idea'
b.name  // 'idea'

b.age = 18;

b.age  // 18
a.age  // 18

console.log(a == b);// true
```

可以看出，直接”把a交给b“的操作会让两者相互影响，因为它给的是地址，二者共用一个地址，而拷贝本身的意思是，多一个副本出来，我可以随便操作而不影响原先的那个，这就是为什么需要有”额外“的拷贝方法存在。

## 实现

方法有很多，有原始，有先进，随着ES6+的出现，由语言本身帮助我们包装了很多强大又方便的方法，比如数组去重，一个`Set`就搞定，对象拷贝同样，但我们还是要看一看，一些方法的实现原理是怎样的。

上一段解释了”拷贝“的意思，再来解释一下”浅拷贝“和”深拷贝“是什么？

- 浅拷贝：顾名思义，只复制一层，如果对象有子对象，并没有复制。
- 深拷贝：父子对象全部复制。

### 浅拷贝

有些文章把”赋值“操作也算浅拷贝，就像上面举的例子那样，这么说也对，也不对，对的是，它的确是多一个对象出来，只是这个对象和父对象共用同一个地址，改变b会连累到a。

那么真正的浅拷贝是怎样的？看代码：

```js
function shallowCopy(src) {
    var target = {};
    for (var prop in src) {
        if (src.hasOwnProperty(prop)) {
            target[prop] = src[prop];
        }
    }
    return target;
}
```

思路就是，**建立一个新的空对象，然后遍历源对象的自有属性，并将它们逐个赋给新建的对象，最后返回新对象**。

做个实验：

```js
var obj1 = {
    'name' : 'idea',
    'age' :  18,
    'car' : ["奔驰","宝马"]
};

var obj2 = shallowCopy(obj1);

obj2.age // 18

//修改一下

obj2.age = 20;
obj1.age // 18  并未被改变

//再尝试修改

obj2.car[2] = "卡宴";
obj1.car  //  ["奔驰", "宝马", "卡宴"]
```
像之前说的一样，在浅拷贝中，源对象的外层拷贝成功，子对象并未彻底拷贝。

**原生新方法**

`Object.assign()`

这是ES6新增的一个方法:

>用于将所有可枚举属性的值从一个或多个源对象复制到目标对象。并返回目标对象。

所以下面的操作可以实现浅拷贝。

```js
const obj2 = Object.assign({}, obj1);
```

它还可以做到下面这种类似合并的效果。

```js
var o1 = { a: 1 };
var o2 = { b: 2 };
var o3 = { c: 3 };

var obj = Object.assign(o1, o2, o3);
console.log(obj); // { a: 1, b: 2, c: 3 }
console.log(o1);  // { a: 1, b: 2, c: 3 }
```

“浅拷贝“说差不多了，来看看”深拷贝”。

### 深拷贝

明白了浅拷贝，就能推测出深拷贝应该是怎样。思路如下：

- 判断数据类型，基本类型直接返回
- 不是基本类型，是对象或者数组，则新建空对象/空数组
- 执行浅拷贝步骤，将src的每一项复制给target
- 重复以上步骤处理src的子属性

可以看出，深拷贝实际也是使用了浅拷贝的方法，只是将拷贝的动作一层一层进行到底。代码如下：

```js
//判断类型
function getType(obj){
    let isType = Object.prototype.toString.call(obj);
    if(isType =='[object Object]'){
        return 'Object';
    }else if(isType =='[object Array]'){
        return 'Array';
    }else{
        return 'nomal';
    }
}
//拷贝方法
function deepCopy(obj) {
    if (getType(obj)=='nomal') {
        return obj ;  // 基本类型直接返回
    }else{
        var newObj = getType(obj)=='Object'?{}:[];  // 对象或者数组
        for(var key in obj){
            if(obj.hasOwnProperty(key)){
                newObj[key] = deepCopy(obj[key]) ;  // 子对象递归调用deepCopy方法
            }
        }
    }
    return newObj;
}
```

还拿上面的对象做实验，做同样的修改，看结果怎样。

```js
var obj2 = deepCopy(obj1);
obj2.car //  ["奔驰", "宝马", "卡宴"]
obj1.car //  ["奔驰", "宝马"]
```

可以看到深拷贝的效果出来了~

当然，你可以使用ES6+的语法进行重写，不过思路是一样的，不再重复。

**其他方法？**

- JSON解析

说实话，第一次看到这个方法时着实觉得有想法，但转念又一想，程序是死的，人是活的，正是人的想象力赋予技术更多可能性。来看代码：

```js
var src ={
  name:"idea",
  age :18,
  friend :['小志','小鸟','小明']
}
var target = JSON.parse(JSON.stringify(src))
target.age = 20
target.friend.push('小聪');
src.age  // 18
src.friend // ['小志','小鸟','小明']
```
它的原理就是利用JSON.stringify 将js对象序列化（JSON字符串），再使用JSON.parse来反序列化(还原)js对象。

实现很简单，但在有些情况下可能出错，比如对象中有Date、RegExp、Error、函数或者循环引用的时候，所以，虽然看起来简单，应该在合适场景下使用才好。

- lodash的_.cloneDeep()

不少人推荐这种方法，因为它支持循环引用和大量的内置类型，细节处理好，一般不会出错，相比之下用着放心。

用法就是引用cloneDeep.js文件，直接使用就好：

```js
var target = cloneDeep(src);
```
想研究源码的可以戳这里[cloneDeep](https://github.com/lodash/lodash/blob/master/cloneDeep.js)

## 总结

本文旨在分享”常见常新“的对象拷贝问题，以及”浅“、”深“拷贝的对比和代码实现。

这个问题之所以常见，其一是有用，其二，涉及的知识点较多：

- 引用类型
- 类型判断
- 对象遍历
- 自有属性
- 递归等

能通过这样一个问题考察基础掌握情况。

当然，实现方式有多种，能力有大小，很多人在实际开发中都是用库封装好的代码解决，确实方便快捷有保障，不过了解一下它的背景和原理还是有好处的，希望对你有帮助。

### 题外思考

拷贝我们聊完了，但可以跳出去重新想一下，我们拷贝是为了什么呢？b对象使用a对象的属性和方法，既然这样，直接继承不也是可以达到效果吗？而且继承之后可以改变属性且不影响原型对象。那么二者分别适用什么场景呢？留给大家衡量吧~

下篇见！~