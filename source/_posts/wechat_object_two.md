title: 【轻聊前端】有“对象”之后
date: 2021-06-12
tags: [分享]
categories: [JavaScript]
toc: true
---

> 现今各种框架、工具‘横行’，到处在讲原理和源码，更有跨端技术需要我们去探索，但如果基本功不好，学什么都是事倍功半，效果很不好，花费时间的同时打击自信心。此篇文章，为我所计划的【轻聊前端】系列第（八）篇，旨在系统地、逻辑性地把原生JavaScript知识分享给大家，帮助各位较为轻松地理清知识体系，更好地理解和记忆，我尽力而为，望不负期待。

上一篇我们讲如何创建对象，但不是有了对象就万事大吉，对象无处不在，要懂得如何爱她，不对，使用它~

对象本身无须创建，JavaScript就有对象，它们是面向对象编程的基石。

重点是有了对象之后，能做什么？简列如下：

- 通过不需定义的，从Object或宿主对象继承而来的方法，操作自定义的对象/值
- 使用自定义对象封装从业务层面抽象出来的角色
- 利用对象特性形成解决某问题的“设计模式”
- 从语言层面改变对象的表现和行为
等等

挑重点来看。

## 语言内置

把任何一种类型的值转成字符串，可使用toString()方法。

想获取字符串或数组的长度，可访问length属性。

把其他值转为浮点型或整型，可用parseFloat()/parseInt()。

这些均不需要开发者定义，直接使用。

除此之外，还有一些特定类型的方法，比如：数字处理方面的Math对象，日期处理方面的Date对象，都是常用的内置对象，都有丰富的方法。

## 抽象、封装

抽象，跟具体相对，跟实物相对，我们常会看到拿人举例，比如：姓名、性别，但这样还不是特别利于理解，很少有业务需要定义人，大多都是更具体的角色，比如：普通用户、Vip用户、学生、老师、商家、买家等，也不定是跟人相关，比如：商品、班级。只要是一个具备独立意义的角色，都可以将其封装起来，至于怎么用，用属性还是方法，按需即可。

这个抽象、封装的过程，就是对象的“创建”。

上篇文章，我们已经简要介绍了几种创建对象实例的方式，但最后一种class的方式并未深究，现在就来看看class的一些细节。

## class的细节

首先，我们把完整定义的代码再拿过来：

```
class Person{
    constructor(name,age,work){
        this.name = name;
        this.age = age;
        this.work = work
    }
    get getName(){     
        return this.name
    }
    set setAge(newAge){
        this.age = newAge;
    }
    run(){
        console.log(this.name + "会跑")
    }
    static sayName(){
        console.log("say"+this.name)
    }
}
//可以像这样创建实例：
let person1 = new Person("idea",18,"程序员")

//可以这样继承
class Man extends Person{}
let man1 = new Man("idea",18,"男程序员");
```
它有什么细节？

**和构造函数的区别**

- 函数表达式有提升，class没有
- class中的代码默认以严格模式执行
- 在使用new创建类的实例时，constructor就是用来实例化的函数，其过程与使用构造函数一致
- constructor参数可选，括号可选
- 构造函数如果不使用new，就是普通的函数调用，类不能直接使用，会报错
- 使用typeof检测class，它依然是function
- 不能在类中添加原始值或对象作为数据，但可以在类外部进行添加

**class继承**

前面提到过class可使用extends实现继承，继承而来的类叫“派生类”。

它可以继承任何拥有[[Construct]]和原型的对象，所以，它不仅可以继承类，还可以继承构造函数。即下面两种写法都是合法的，由此也做到了向后兼容。

```
class Person{}
class Engineer extends Person{}

function Person(){}
class Engineer Person{}
```

派生类都会通过原型链访问到类和原型上定义的方法。

派生类的方法可以通过super关键字引用它们的原型。

使用super的几个规则：

- 派生类的构造函数必须使用super()调用父类构造函数，否则不能在构造函数中使用this关键字。这是为了确保父类先于子类得到初始化。
- 如果没有在子类中定义构造函数，解释器会自动创建。这个隐式定义的构造函数会取得传给它的值，然后把这些值再传给super()。
- 在静态方法中可以通过super调用继承的类上定义的静态方法。
- 不能单独使用super

class已经成为现在定义对象和创建实例的主流方式，知道了这些细节，再多加练习，必会游刃有余。

## 属性的细节

### 可计算属性

前面讨论的所有对象，属性都是固定的，比如：name、age。但有时候可能并不固定，是个变量。

引入可计算属性之前，如果想使用变量的值作为属性，必须先声明对象，然后使用中括号语法来添加属性。比如：

```
const nameKey = "name";

let person = new Object();
person[nameKey] = 'idea';
```

有了可计算属性就可以这样写：

```
let person = {
    [nameKey] :'idea',
}
```
区别就在于能不能在字面量中使用，中括号包围的对象属性键告诉运行时将其作为JavaScript表达式而不是字符串。

既然是表达式，就不仅仅是变量这么简单，还可以更复杂，比如调用函数，把返回结果作为键等，具体不赘述。

### 操作干涉

不论是访问属性，调用方法，还是继承，我们都是在使用对象，在多数场景中，这就够了，那是否可以在更底层去做点文章呢？有没有应用场景？

诚然，的确不常用，却能实现一些“奇妙”的效果，defineProperty便是其中之一，它因应用于Vue2.x的数据响应而受到更多关注。

**defineProperty**

通过字面理解，“定义属性”，就知道它可以用来定义已知对象的属性。

它接收三个参数“要添加属性的对象、属性的名称和一个描述符对象”，仍以上面的对象为例。

```
let person = {}
Object.defineProperty(person,'name',{
    writable:true,
    value:'idea'
})
```
对象是person，定义了一个可写，值为“idea”的属性name。

不急着往下看，就看看这个writable，如果设置为false会是什么效果。

```
Object.defineProperty(person,'name',{
    writable:false
})
person.name = "lili"
console.log(person.name)  //’idea‘
```
非严格模式下，writable为false的属性，赋值行为会被忽略，而严格模式下会直接报错。

那么自然会想到，当用 new 运算或者字面量形式创建的对象，并非没有对属性的特性进行描述，而是有相应的默认值。

像这种跟数据属性相关的，可称为“数据属性”，或者它更像一种配置。

**配置**

除了 writable 和 value，还有：

- Configurable：是否可以通过delete删除并重新定义，是否可以修改它的特性，以及是否可以把它改为访问器属性。
- Enumerable：是否可以通过for-in循环返回。

上面说到默认值，这也是值得注意的：以常规方法定义对象，“writable、Configurable、Enumerable”默认都为true，而使用defineProperty来添加的时候，默认的值是false。

看完“数据属性”，再看看“访问器属性”。

**getter/setter**

访问器属性不包含数据值。而是包含一个获取（getter）函数和一个设置（setter）函数，不过这两个函数不是必需的。

在读取访问器属性时，会调用getter函数，返回一个有效的值。在写入访问器属性时，会调用setter函数并传入新值，这个函数必须决定对数据做出什么修改。

访问器属性依然有四个特性，其中“Configurable、Enumerable”同数据属性一样，不再赘述，就说下
getter 和 setter。

```
let person = {
    age_:16
}
Object.defineProperty(person,'age',{
    get(){
        console.log(this.age_)
    },
    set(newAge){
        if(this.age > 18){
            console.log("可正常访问")
        } else {
            console.log("未成年，不可访问")
        }
    }
})

person.age //16
person.age = 10 // '未成年，不可访问'
```
可以看出，在访问对象属性的时候，进了get方法，打印了年龄数据，设置年龄值的时候，进入了set方法，因为age小于18，输出了“未成年，不可访问”。

通常情况下，这两个方法是不需要用到的，但既然可以进入到访问或赋值的环节，就能在其中加一些额外的操作来达到自己的目的，比如监听数据变化渲染内容等。

以上便是关于defineProperty对对象的一些操作了。

## 代理和反射

ES6之后，又有了一种更加强大的对对象进行“干涉”的方法，即代理。

什么是代理，**代理为开发者提供了拦截并向基本操作嵌入额外行为的能力**。

乍一听，跟defineProperty类似，但不同之处在于，它不是在对象上直接做文章，而是为想要操作的对象，即“目标对象”，指定了一个代理对象。

目标对象既可以直接被操作，也可以通过代理来操作，默认情况下，在代理对象上执行的所有操作都会无障碍地传播到目标对象。因此，在任何可以使用目标对象的地方，都可以通过同样的方式来使用与之关联的代理对象。**在对目标对象的各种操作影响目标对象之前，可以在代理对象中对这些操作加以控制**。

代理是使用Proxy构造函数创建的。接收两个参数：目标对象和处理程序对象。缺少其中任何一个参数都会抛出TypeError。

使用代理的主要目的是可以定义捕获器，也就是基本操作的拦截器。简单示例如下：

```
const target = {
    name:'idea'
}
const handler = {
    get(){
        return 'hello idea'
    }
}

const proxy = new Proxy(target,handler)

console.log(target.name)  //'idea'
console.log(proxy.name)   //'hello idea'
```
所有捕获器都可以访问相应的参数，基于这些参数可以改变被捕获方法的原始行为。

我们将上面的代理对象改造一下。

```
const handler = {
    //trapTarget : 目标对象
    //property : 属性
    //recevier : 代理对象
    get(trapTarget,property,recevier){
        return trapTarget[property]
    }
}
console.log(proxy.name) //'idea'
```
可以看到，我们在捕获器中返回了目标对象的属性，这就改变了代理对象的返回值，这只是一种现象，但它的强大，正是基于此可添加的“自定义行为”，或者“重建”。

所有捕获器都可以基于自己的参数重建原始操作，但并非所有捕获器行为都像get()那么简单。因此，通过手动写码的想法是不现实的。实际上，开发者并不需要手动重建原始行为，而是可以通过调用全局Reflect对象上（封装了原始行为）的同名方法来轻松重建。

```
const handler = {
    get(trapTarget,property,recevier){
        return Reflect.get(...arguments)
    }
}
console.log(proxy.name) //idea
```
基于上面那段代码，把return改了一下，效果是一样的。

再进一步，甚至可以写成这样：

```
const handler = {
    get:Reflect.get
}
```
最“过分”的，如果想创建一个可以捕获所有方法，然后将每个方法转发给对应反射API的空代理，甚至不需要定义处理程序对象。这样写就可以了：

```
const proxy = new Proxy(target,Reflect)
```
所以，代理和反射通常是成对出现的，也可以见识到它们能够发挥的作用了。


## 对象的操作

相比创建和继承，或者很少涉及的底层干预，实际编码中更高频的是对对象的操作。

比如：访问属性、调用方法、迭代、拷贝对象、合并对象等。先简单介绍几个。

**遍历**

方法有多种，但目的无非就三种，键、值、键值对。

他们对应的方法是Object.keys(),Object.values(),Object.entries()

相应的，它们会返回一个由给定对象的自身可枚举属性、值、键值对组成的数组，然后可进行迭代操作。

**解构**

过去，如果我们想拿到对象的某个属性赋给另一个变量，需要这么干。

```
let mike = {
    name: 'mike',
    age: 25
};
let name = mike.name
let age = mike.age
```
现在只需要这么干。

```
let {name, age} = node;
```
解构时，变量名可以不同，可以给默认值，也可以嵌套解构等，这里不赘述。

**合并**

合并另个对象可以怎么做？可以遍历赋值，但不够简洁。现在可以这么做：

```
let target = {}
let source = {}
Object.assign(target, source);
```

还可以使用展开运算符。
```
let result = {...target,...source}
```

**冻结、封闭**

ES6之后还添加了一些方法用来保护对象不被不明行为或恶意破坏。

Object.seal()

封闭一个对象，阻止添加新属性并将所有现有属性标记为不可配置。

属性不可配置的效果就是属性不可删除，以及一个数据属性不能被重新定义成为访问器属性，或者反之。但属性的值仍然可以修改。

Object.freeze() 

可以冻结一个对象。一个被冻结的对象再也不能被修改；不能添加新的属性，不能删除已有属性，不能修改已有属性的可枚举性、可配置性、可写性，以及不能修改已有属性的值。此外，冻结一个对象后该对象的原型也不能被修改。

可以看出，封闭只是在对象创建完毕之后对添加新属性以及改变配置进行限制，冻结则限制得更彻底，创建完不能以任何方式进行修改，只能访问。

## 总结

至此，有“对象”之前和之后的讨论就告一段落。

回过头，我们从第三篇文就在聊对象，到现在看，对象是什么呢？是概念？是方法？是思想？又或是数据的载体？似乎从哪个层面去理解都没问题，正因为这样，它既简单，又复杂，既强大，又难以捉摸。

关于它的话题还远未聊完，待时机合适我们继续探讨。