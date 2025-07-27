title: 【轻聊前端】有“对象”之前
date: 2021-04-24
tags: [分享]
categories: [JavaScript]
toc: true
---

> 现今各种框架、工具‘横行’，到处在讲原理和源码，更有跨端技术需要我们去探索，但如果基本功不好，学什么都是事倍功半，效果很不好，花费时间的同时打击自信心。此篇文章，为我所计划的【轻聊前端】系列第（七）篇，旨在系统地、逻辑性地把原生JavaScript知识分享给大家，帮助各位较为轻松地理清知识体系，更好地理解和记忆，我尽力而为，望不负期待。

“一切”都是对象，那对象有什么，又能做什么？

有些话题总是经久不衰，比如：原型链、this、深/浅拷贝，这些都跟对象有关，而对象的故事远不止于此，我们就来适当聊一聊。

## 对象是什么

它是一组属性和方法的集合，属性——“有什么”，方法——“能干什么”。

使用场景大致分为两种：

**语言层次**，JavaScript的运行依赖对象系统，其中的每一种数据类型同时是一类对象，有着自己的属性和方法，供编写程序时使用。

```
let str = "hello world";
str.length  //11
str.substr(0,5) //"hello"
```

**业务层次**：根据业务类型不同，我们可以从具体需求中抽象出多个角色，每种角色都可以封装成一个对象，有自己的属性和方法。

```
let teacher = {
    name:"莉莉",
    age:30,
    work:"老师"
}
```

## 创建对象

对象需要创建才能用，创建对象的方式有多种，接下来我们逐一介绍，讲一下前世今生。

- 本尊

聊类型的时候，我们就见过对象的本尊——Object。使用new运算符就可以创建一个对象，创建之后可以为其添加属性和方法：

```
let person = new Object();
person.name = 'idea';
person.age = 18;
person.work = '搬砖';
person.run = function(){
    console.log("我会跑")
}

```

有了对象，就能在需要的时候使用它们，比如，前端请求后端传递过来的数据通常是对象形式，就可以取到它的属性值放到页面上展示，当然，前端可以创建对象来存储某种意义上的个体，进行传递或者其他操作。

但这种创建形式不够简洁，写了很多重复的对象名称，一种更加直观也更常用的方法，叫”对象字面量“：

```
let person = {
    name :'idea',
    age :18,
    work :'搬砖',
    run(){
        console.log("我会跑")
    }
}
```

书写简洁对工程师的开发体验也是重要的，所以，这种写法在开发中很常见。

单这么看的话，对象就是个容器，不复杂，但是，这两种方法都有一个缺点：**创建具有同样接口的多个对象需要重复编写很多代码。换句话说，只能一个个地生创建，没有实现封装、复用、继承**。

## 更好的创建与继承

- 工厂模式

谈到封装和复用，我们可以十分自然地想到函数，如果能够用一个函数来封装整个创建对象的过程，类似一个小的对象“加工厂”，就解决了上面说的问题，这就是“工厂模式”的由来。

```
function createPerson(name,age,work){
    let obj = new Object();
    obj.name = name;
    obj.age = age;
    obj.work = work;
    obj.run = function(){
        console.log(this.name + '会跑')
    }
    return obj
}
let person1 = createPerson("张三",16,"程序员")
let person2 = createPerson("李四",18,"产品经理")
```
这个方案解决了类似对象创建的重复书写问题，是个不错的方案，但它有点不完整的地方，即“仅仅”封装了行为，创建出的对象和来源并没有什么关系，即person1、person2和createPerson是没有关系的。

很多时候建立它们之间的联系都是重要的，比如继承属性和方法，所以可以继续改良。

- 构造函数

什么是构造函数——**用于构造某种对象实例的函数**。

ECMAScript中的构造函数是用于创建特定类型对象的，就像我们可以用 new String()创建字符串，new Number()创建数字。

String()和Number()是内置函数，这里我们讨论的是自定义的构造函数。

它的写法和工厂模式相似，却有着本质的不同，譬如上面的对象我们可以这样写：

```
function Person(name,age,work){
    this.name = name;
    this.age = age;
    this.work = work;
    this.run = function(){
        console.log(this.name + '会跑')
    }
}
let idea = new Person('灵感',18,'前端砖工');
idea.name //'灵感'
idea.age  //18
```
它有如下特点：

- 首字母大写，更易与普通函数作区分
- 没有显式创建对象，属性和方法直接赋值给this
- 没有return

那么本质不同是什么？这就要了解构造的过程，具体如下：

（1）在内存中创建一个新对象。

（2）这个新对象内部的[[Prototype]]特性被赋值为构造函数的prototype属性。

（3）构造函数内部的this被赋值为这个新对象。

（4）执行内部代码，给新对象添加属性。

（5）如果构造函数返回非空对象，则返回该对象；否则，返回刚创建的新对象。

可以看出，它不仅看起来和工厂模式像，背后做的事情也像，创建了一个新对象，添加属性和方法，再把这个对象返回。一点不同是在第2步，**新对象的[[Prototype]]特性被赋值为构造函数的prototype属性**，这就为实例和构造函数建立了联系。

```
idea.constructor == Person  //true
```
所以，使用构造函数模式创建对象实例，既起到封装、复用的效果，又能为实例和构造函数之间建立联系。

但是，并不代表构造函数是完美的，我们发现构造函数里有方法，方法本身是函数，函数也是对象，这样就等同于，每次创建一个实例的时候，都新建了一个Function实例。

```
let lisi = new Person("李四",18,"产品经理")

lisi.run == idea.run  //false
```
两个实例是相互独立的，但其实它们做的是同一件事，这就有些浪费资源。

怎么办？有一种方法是，把函数定义放在构造函数的外部，你是new的时候发生的事情，我把你放在new操作的外部，不参与不就行了？

从效果上是可行的，但有几点弊端：

- 污染全局作用域
- 如果需要多个方法，就要在全局定义多个方法，变得较混乱
- 这些方法只是跟单一对象相关，跟其他都无关，归属不清晰

综上，这个方案是得不偿失的，不建议采用，那有没有更好的方式？

不急，我们先来看另外一种。

**原型模式**

“每个函数都会创建一个prototype属性”。知道这一点，是理解原型模式的前提。

prototype是个对象，对象中包含用特定引用类型创建的实例所共享的属性和方法。

实际上，这个对象就是通过调用构造函数创建的对象的原型。所以，可以在它上面定义的属性和方法，从而被对象实例共享。看代码：

```
function Person(){}
person.prototype.name = 'idea';
person.prototype.age = '18';
person.prototype.work = '程序员';
person.prototype.run = function(){
    console.log(this.name);
};
```
这样以来，当我们想创建对象的时候，可以：

```
let person1 = new Person();
let person2 = new Person();
person1.run == person2.run  //true 
```
方法run得到共享。

我们来稍微探讨一下这个效果是怎么产生的。

前面说过，只要创建一个函数，就会按照特定的规则为这个函数创建一个prototype属性。默认情况下，所有原型对象自动获得一个名为constructor的属性，指回与之关联的构造函数。对前面的例子而言，Person.prototype.constructor指向Person。然后，可能会给原型对象添加其他属性和方法。

每次创建新实例，实例的内部[[Prototype]]指针就会被赋值为构造函数的原型对象。需要指出的是，并没有访问[[Prototype]]特性的标准方式，但多数浏览器会在每个对象上暴露__proto__属性，通过这个属性可以访问对象的原型。实例便是通过__proto__链接到原型对象，同一个构造函数的不同实例，通过__proto__共享原型对象，而Person.prototype指向原型对象，所以，定义在原型对象上的属性和方法就得到了共享。

在这个基础之上，Object类型还有一个setPrototypeOf()方法，可以向实例的[[Prototype]]写入新值。这样就可以重写一个对象的原型继承关系.

```
let person = {
    age : 18
}

let person1 = {
    name:'idea'
}
Object.setPrototypeOf(person1,person)
person1 // {name: "idea"}
person1.age  //18
```

这个方法接受一个目标对象和一个原型对象，将二者建立联系，使得目标对象继承原型对象的属性。

但是，修改继承关系是个风险稍高（或者比较损耗性能）的操作，毕竟你不知道会有多少对象与之关联，如果的确需要在创建对象实例的时候为其指定原型这样的操作，有另一种方法——Object.create()。

Object.create()方法创建一个新对象，使用现有的对象来提供新创建的对象的__proto__。

```
let person = {
    age : 18
}
let  person1 = Object.create(person); 
person1.age  //18
```

由此，还可以了解一个叫“干净”的空对象的东西。我们建立空对象可用这些方法：

```
let obj = {}
let obj = new Object()
let obj = Object.Create(null)
```
如果你不是对对象完全陌生，应该会知道，通过前面两种方式创建的对象，虽然看起来没有添加属性和方法，但它们有继承自Object的属性和方法，而第三种，它没有属性，也没有方法，所以称作“干净”的空对象。

以上这些是我们讨论“原型模式”延伸出的小知识点，重点仍在模式本身。

“原型模式”看似很棒，也不是没有问题。首先，它弱化了向构造函数传参的能力，所有实例默认都是相同的属性值，这显然造成不便，但这不是原型的最大问题。最大问题源自它的共享特性，所谓”成也萧何，败也萧何“，共享对函数来说比较合适，对原始值属性也能接受，如果涉及引用值，问题就暴露了。

```
function Person(){}
Person.prototype = {
    bag:["thinkPad","iphone12","ipad","mac"]
}
let person1 = new Person();
let person2 = new Person();
person1.bag //  ["thinkPad", "iphone12", "ipad", "mac"]
person2.bag //  ["thinkPad", "iphone12", "ipad", "mac"]

//修改person1
person1.bag.pop()  
person1.bag   //  ["thinkPad", "iphone12", "ipad"]
person2.bag   //  ["thinkPad", "iphone12", "ipad"]
```
前面讲引用类型的时候说过，它存的是地址，而不是值本身，这里person1和person2的背包指向的是同一个地址，改一方，另一方也跟着改变，但这里是需要个性化处理的，毕竟，不是每个人都能用上mac...

由于这个弊端的存在，原型模式通常不会单独使用，就有了下面这种模式。

**组合继承模式**

通过上面的讲述，我们知道，构造函数的成员都是私有的，原型模式的成员都是共享的，既然有些成员需要共享，有些成员需要私有，将二者组合起来不就可以了？

我们可以把上面的代码改成这样：

```
function Person(name,age,work,bag){
    this.name = name;
    this.age = age;
    this.work = work;
    this.bag = bag;
}
Person.prototype.run = function(){
        console.log(this.name + '会跑')
}

let person1 = new Person("idea",18,"程序员",["thinkPad"]);
let person2 = new Person("lili",16,"产品经理",["mac"]) 

person1.run()  // idea会跑
person2.run()  // lili会跑

//有一天idea有钱了（虽然不知道哪一天）
person1.bag.push("mac")
person1.bag  //  ["thinkPad", "mac"]
person2.bag  //  ["mac"]
```
这种方法规避了构造函数和原型模式的不足，同时利用了各自的优点，曾经成为很流行的方式。

但ES6之后，有了更好用的替代方法。

**Class**

在正统面向对象语言中都有类（class）的概念，类是一个模板，它描述一类对象的行为和状态。即使用类可以创建很多同类对象。

ES6之前的JavaScript中是没有类的，只能通过其他方法来模拟，比如前面介绍的构造函数，或者组合继承。ES6之后推出了Class这种创建类的新工具。先看类的定义：

```
class Person{}
const Person = class {}
```
可以直接定义，也可以使用赋值表达式，这看起来是一种全新的方式，但大家应该在很多地方看到过，说class只是一种语法糖。什么是语法糖？语法层面的不同，用起来更舒服、更自然。它背后仍是构造函数与原型的组合模式。

类可以包含构造函数方法、实例方法、getter函数、setter函数和静态类方法，但都不是必需的。正像上面的示例那样，空的类定义照样有效。默认情况下，类中的代码都在严格模式下执行。

下面就定义一个完整的类来认识它。

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
```

然后可以像这样定义一个实例：

```
let person1 = new Person("idea",18,"程序员")
```

通过定义类，可以非常方便地定义应该存在于实例上的成员、应该存在于原型上的成员，以及应该存在于类本身的成员。

**实例成员**

每次通过new调用类时，都会执行类构造函数。在函数内部，可以为新创建的实例添加“自有”属性。即上面Person类的constructor做的事。

每个实例都对应一个唯一的成员对象，这意味着所有成员都不会在原型上共享。

**原型方法与访问器**

为了在实例间共享方法，类定义语法把在类块中定义的方法作为原型方法。比如person中的run方法。

类定义也支持获取和设置访问器。语法与行为跟普通对象一样。就像上面的getName和setAge。它们分别会在访问实例属性或者修改实例属性时被调用。

**静态类**

使用static关键字作为前缀。在静态成员中，this引用类自身。通常用于执行不特定于实例的操作，也不要求存在类的实例。

比如以上代码的sayName方法：

```
person1.sayName()  //Uncaught TypeError: person1.sayName is not a function
Person.sayName()  //sayPerson  
```
使用实例无法调用静态方法，而类本身可以，且返回的this是类本身，name为Person。

**类的继承**

继承很常用，可以省很多重复的定义，比如，上面我们定义了人，那么可以在人的基础上，再定义一个男人，这样，男人是可以继承人的属性和方法的。直接看代码：

```
class Man extends Person{

}

let man1 = new Man("idea",18,"男程序员");
man1.work   //"男程序员"
man instanceof Person   //true
```
可以看出，Man所创建的实例，继承了Person中的属性，且Person存在于man1的原型链上。

当然，类还有其他一些细节，这里不再赘述，它已经成为现在创建对象和实例最常用的方式，且在流行框架React当中应用广泛。

## 总结

此文标题是”对象“之前，因为对象是个很大的话题，篇幅所限，一篇或者两篇文章可能都是说不完的，所以分开说，以减轻大家的阅读负担。

本文仅介绍了从对象/类不存在到存在的这个环节，曾有过什么方法，现在主要使用什么方法，以及它们的特点，还有一些细节和对象API我们留到”之后“再详细介绍。

祝学习进步，一起加油。