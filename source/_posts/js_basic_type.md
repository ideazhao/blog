title: JavaScript类型检测和转换
date: 2019-10-31
tags: [学习、javascript]
categories: [总结]
toc: true
---

JavaScript的知识点中，类型是易忽视的，关乎细节，你可能因之犯错，但同时也可加以利用，要看熟悉程度。

类型无外乎这几种—数值（number）、字符串（string）、布尔值（boolean）、undefined、null、对象（object）。

同时，object是广义的对象，它又可以包含狭义的对象、数组(array)和函数(function)。

先来看”检测“。

## 检测

方法有三种：

- typeof 运算符
- instanceof 运算符
- Object.prototype.toString 方法

### typeof

typeof最直观，数值、字符串、布尔值分别返回number、string、boolean。

    typeof 666 // "number"
    typeof '666' // "string"
    typeof true // "boolean"

函数返回function。

undefined返回undefined。

利用这一点，typeof可以用来检查一个没有声明的变量而不报错。实际编程中通常这么使用

    if (typeof v === "undefined") {
      // ...
    }

对象返回object。

    typeof window // "object"
    typeof {} // "object"
    typeof [] // "object"

此处可印证我们前面所说的类型，数组本质上是一种特殊的对象。

null返回object。

总结一下就是，**除了数组和null检测是object，其他类型原样返回**。

null的类型是object，这是历史原因造成的。JavaScript 第一版只设计了五种数据类型，没考虑null，只把它当作object的一种特殊值，后来为了兼容老版本就没改变。

那么问题来了，想知道是不是数组怎么办？？

### instanceof

instanceof登场了，它是作为一种java的运算符被引进的，能够弥补typeof有时无法返回确切类型的缺陷，比如：

        var oStringObject = new String("hello world"); 
        typeof oStringObject;//object
        oStringObject instanceof String;// true

当然，我们所提的数组它也能做到

        var _array = [1,2,3];
        typeof _array; // object
        _array instanceof Array; //true
    
这是为什么呢？来看看MDN的解释：

>instanceof 运算符用于检测构造函数的 prototype 属性是否出现在某个实例对象的原型链上。

也就是说，**它能够用于检测到原型链中任一环节的对象类型**，你应该注意到“构造函数”这个字眼，对，除了内置对象，它还可以检测自定义对象。

        function Car(make, model, year) {
            this.make = make;
            this.model = model;
            this.year = year;
        }
        var mycar = new Car("Honda", "Accord", 1998);
        mycar instanceof Car;    // 返回 true

到此，基本应用你应该了解了，更多不再细说。

### Object.prototype.toString()

可以简称它`toString()`，每个对象都有一个`toString()`方法，默认情况下，`toString()` 方法被每个 Object 对象继承。如果此方法在自定义对象中未被覆盖，`toString()` 返回 "[object type]"，其中 type 是对象的类型。

那么怎么使用呢？

        var toString = Object.prototype.toString;

        toString.call(new Date); // [object Date]
        toString.call(new String); // [object String]
        toString.call(Math); // [object Math]
        toString.call(undefined); // [object Undefined]
        toString.call(null); // [object Null]

为了每个对象都能通过 Object.prototype.toString() 来检测，需要以 Function.prototype.call() 或者 Function.prototype.apply() 的形式来调用，传递要检查的对象作为第一个参数。

当然，既然有未被覆盖的情况，就说明可以覆盖，比如可以这样覆盖：

        Object.prototype.toString = function objectToString() {
            // 自定义
        }

至此，检测类型的方法简单介绍完了，来看转换。

## 转换

转换意味着变化，这个变化是运行时发生的，也是JavaScript语言的特点之一——**动态类型**。

某种程度上，它因为动态类型被吐槽，可能导致数据“失控”或者bug难以定位，所以近两年TypeScript挺身而出，本文不讨论。

类型转换通常体现在“**数值、字符串、布尔值**”这三种原始类型上。

### 显式转换

主要使用`Number()`、`String()`和`Boolean()`三个函数。

- **Number()**

用于转换的有这么几个来源：

    数字——123
    数字组成的字符串——“123”
    数字和其他字符相结合——“123abc”
    空字符——“”
    布尔值——true、false
    undefined
    null

相对来讲，数值是一种格式严格的类型，它不应该将本身不是数字的东西转换成数字（布尔值除外），至少目前为止，没有那个规则和机制，而且，`Number()`是整体解析，不会去拆分，所以，以上几个例子的结果如下：

    123、“123”——123
    “123abc”、undefined——NaN
    ""、null、false——0
    true——1

除此之外，还有两个方法可以进行数值转换，但**只对字符串有效**，它们是`parseInt()`、`parseFloat()`，顾名思义，将字符串转换为整型或者浮点型数字，相比之下，它们的要求比`Number()`更宽松。

比如：“123abc”，会转换成 123，但也是有规则的，它不会跳跃着识别，**首次碰到非数字字符后**，就停止转换，如：“123abc456”，会转换成“123”，而“abc123”则是NaN。

`Number()`方法的参数是对象时，将返回NaN，除非是包含单个数值的数组，Number([3])会转换为3。

- **String()**

相比`Number()`稍显复杂的规则，`String()`温和了很多，因为，不论是数字、字母、其他符号还是空格，都可作为字符串的一部分。于是，有着下面这样的转换结果：

    String(123) // "123"
    String('abc') // "abc"
    String(true) // "true"
    String(undefined) // "undefined"
    String(null) // "null"
    String([1, 2, 3]) // "1,2,3"

很美好，似乎一切都是原来的模样，仅仅是类型成为了字符串。

当然，对象依然特殊。

    String({a: 1})  // "[object Object]"

除此之外，还有一个`toString()`方法也可用于字符串转换，只是用法不同。

    var order = 100;
    order.toString(); //"100"

- **boolean()**

Boolean()函数可以将任意类型的值转为布尔值。它的转换规则相对简单，除了以下五个值的转换结果为false，其他的值全部为true。

        Boolean(undefined) // false
        Boolean(null) // false
        Boolean(0) // false
        Boolean(NaN) // false
        Boolean('') // false

true和false这两个布尔值不会发生变化。

        Boolean(true) // true
        Boolean(false) // false

对象也老实返回布尔值，但依然特殊——**所有对象，包括空对象和false对应的布尔对象new Boolean(false)，转换结果均为true**。

        Boolean({}) // true
        Boolean([]) // true
        Boolean(new Boolean(false)) // true

至此，你可以这么理解：**具备正常值（0除外）的变量类型和对象的布尔转换都是true，其他为false**。

### 隐式转换

隐式转换在JS代码中也是随处可见，我们可能不会写很多显示转换，隐式转换却在自动执行着。

遇到以下三种情况时，JavaScript 会自动转换数据类型。

- 不同类型的数据运算

- 非布尔值类型求布尔值

- 非数值类型使用一元运算符

自动转换的规则是：**预期什么类型的值，就调用该类型的转换函数**。

有点玄乎，下面具体介绍。

- **自动转换为布尔值**

预期为布尔值的地方，系统内部会自动调用Boolean函数将其转换，比如很常用的条件判断语句。

        if ( !undefined && !null && !0 && !NaN && !'') {
            console.log('true');
        } // true

下面两种写法也常见，它们内部调用的也是Boolean函数。

        // 写法一
        expression ? true : false

        // 写法二
        !! expression

- **自动转换为字符串**

JavaScript 遇到预期为字符串的地方，就会将非字符串的值自动转为字符串。具体规则是，先将复合类型的值转为原始类型的值，再将原始类型的值转为字符串。

字符串的自动转换，主要发生在**加法**运算时。当一个值为字符串，另一个值为非字符串，则后者转为字符串。

        '5' + 1 // '51'
        '5' + true // "5true"
        '5' + false // "5false"
        '5' + {} // "5[object Object]"
        '5' + [] // "5"
        '5' + function (){} // "5function (){}"
        '5' + undefined // "5undefined"
        '5' + null // "5null"

这里的加法我加粗了，即它体现的不再是数学里的“加”，而是“连接”，相信大家不陌生，不再赘述。

这种自动转换很容易出错。

        var obj = {
        width: '100'
        };
        obj.width + 20 // "10020"

开发者可能期望返回120，但是由于自动转换，实际上返回了一个字符10020。

- **自动转换为数值**

JavaScript 遇到预期为数值的地方，就会将参数值自动转换为数值。系统内部会自动调用Number函数。
除了加法运算符（+）有可能把运算子转为字符串，其他运算符都会把运算子自动转成数值。

        '5' - '2' // 3
        '5' * '2' // 10
        true - 1  // 0
        false - 1 // -1
        '1' - 1   // 0
        '5' * []    // 0
        false / '5' // 0
        'abc' - 1   // NaN
        null + 1 // 1
        undefined + 1 // NaN

上面代码中，运算符两侧都被转成了数值。

注意：null转为数值时为0，而undefined转为数值时为NaN。

一元运算符也会把运算子转成数值。

        +'abc' // NaN
        -'abc' // NaN
        +true // 1
        -false // 0

PS：由于自动转换具有不确定性，且易出错，所以，如果你知道或者想要使用某种类型，就可以考虑使用Boolean、Number和String函数进行显式转换。

## 总结

至此，我们就絮絮叨叨地，把看似简单，实则很多细节的“类型”相关说完了，乍一看并不难理解，很多都很直观，但最“烦人”的，就是那些不易察觉，也不易记忆的东西，需要我们反复琢磨和熟悉，才能牢牢地记住，避免出错。

我们很快会再见面，下一篇是什么呢，可以期待一下。