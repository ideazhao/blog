title: 【TypeScript逐点击破】类与修饰
date: 2021-12-26
tags: [TypeScript]
categories: [TypeScript]
toc: true
---

类的概念在后端语言（如Java）中存在很久了，但在JavaScript中很长一段时期是没有的，直到TypeScript/ES2015 登上舞台。

对JavaScript熟悉的会知道，Class出现之前，我们可以用构造函数和原型通过不同变种来模拟类的行为，但每种都各有优劣，发挥特点的同时存在弊端，代码也不够简洁，容易陷入混乱。

TypeScript 是 JavaScript 的超集，是包含关系，它们有相当部分是一样的，且TypeScript进行了一些扩展，特性更多，所以本篇直接介绍 TypeScript 的类。

## Class定义

Class的灵活度很大，这取决于你需要使用它做什么。比如，如果仅仅是定义，这样就够了。

```
class Calculator{

}
let calculator:Calculator = new Calculator()
```

这段代码可能眼熟，new 我们在聊构造函数时见过，用于实例化一个对象，在这里的用途是一样的，你可能会问了，这里也没有构造函数呀，其实可以有，它长这样：

```
class Calculator{
    constructor(){
        console.log('构造了一个对象')
    }
}
```

但”可以有“的意思是也”可以没有“，即非必须。当没有的时候，会获得一个隐式的构造函数，所以并不影响new实例化对象。

只是这样的定义没有意义，它什么都没做，类需要有丰富的成员来实现强大的功能。

## 实例成员

### 属性

刚刚定义了一个计算器的类，它是空的，现在增加一个代表精度的属性。

```
class Calculator{
    precision:number = 2
}
```

添加属性的动作看上去像变量声明，实例属性的定义就是这么做。

对类的使用比较熟的可能更习惯于下面这种方式：

```
class Calculator{
    constructor(precision){
        this.precision = precision
    }
}
```

但类的属性可以在类中直接定义，和构造函数不同。

可以这样访问：

```
let calculator:Calculator = new Calculator()
let precision:number = calculator.precision
```

也可以给属性赋值：

```
calculator.precision = 3
```

既然可以后赋值，是否代表定义时可以不赋值？对的。

```
class Calculator{
    precision:number = 2;
    maxValue:number;  // 最大值
    minValue;   // 最小值
}
```
不同于属性precision，新增的两个属性在定义时都未被指定初始值，因此当新的计算器对象被实例化时，maxValue和minValue两个属性的值会被默认为undefined。而且minValue的类型会默认为 any。

不显式定义类型不是好习惯，值为undefined也不好，所以，最好在定义时指明类型，也马上设置初始值。

```
let calculator:Calculator = new Calculator()
calculator.maxValue = 100
calculator.minValue = 10
```

但每次都这么做略显繁琐，有什么好方法能够既不需要事先赋值，又在实例化的时候方便初始化？

### 构造函数

了解背后机制的都知道，构建对象实例的时候引擎做了一些事情，比如：新建对象，绑定this，返回对象。在类中是同样的过程，所以，如果我们想在类实例化时初始化属性，可以这么做。

```
class Calculator{
    precision:number = 2;
    maxValue:number;  
    minValue:number;

    constructor(maxValue:number,minValue:number){
        this.maxValue = maxValue
        this.minValue = minValue
    }
}

let calculator:Calculator = new Calculator(100,10)
calculator.maxValue  // 100
calculator.minValue  // 10
```

### 方法

很少有类是不需要方法的，因为要给调用方提供处理数据的能力。这个概念不是很难理解，只举一个简单的例子：

```
class Calculator{
    precision:number = 2;
    
    add(a:number,b:number):number{
        let res = (a + b).toFixed(this.precision)
        return +res
    }
}
let calculator:Calculator = new Calculator()
calculator.add(1.233,1.456)  // 2.7
```

### 访问器

相比以上三位，访问器看似不是主干功能，很多人可能从来没写过，单纯的类只要属性和方法就行了。

访问器的存在，**使得对象实例的属性修改得以被监测**。

当真这么神奇？

```
class Calculator{
    _precision:number = 2;
    
    get precision:number(){
        console.log('获取_precision的值')
        return this._precision
    }
}
let calculator:Calculator = new Calculator()
console.log(calculator.precision)
```
运行这段代码，你将看到：
```
获取_precision的值
2
```
是否像打开了一个神奇的通道，你可以在访问这个属性的时候做任何想做的事。

当然，还有另外一个”武器“。

```
class Calculator{
    _precision:number = 2;
    
    set precision(value:number){
        console.log('设置_precision的值')
        this._precision = value - 1
    }
}
let calculator:Calculator = new Calculator()
calculator.precision = 4
console.log(calculator._precision)
```
运行这段代码，你会发现是如下输出：

```
设置_precision的值
3
```
我们给precision赋值是4，而在set里进行了拦截和改动，将值减1，导致结果变成了3。

看完这段演示，几点小tips：

- get 和 set分别叫”读取器“和”设置器“，统称”访问器“
- 它们可以单独使用
- 成对使用的时候，名字要一致
- 访问器的本质仍是函数，但不能像函数那样去调用，而应该像访问属性一样去使用

类的几大成员聊完，下面聊聊“修饰”。

## 修饰

修饰是什么意思呢，可以理解为在定义成员的同时给它打上了某种标签，改变了默认行为，一一来看。

### 静态成员

关键字：**static、类访问**

类的静态成员和实例成员是一个相对的概念。我们还拿计算器类举例。

- 静态属性

```
class Calculator{
    _precision:number = 2;
    static maxValue:number;  
    static minValue:number;

    add(a:number,b:number):number{

        // 新增代码 
        if(a > Calculator.maxValue || a < Calculator.minValue){
            console.log('操作数a超过了计算范围！')
            return false
        }

        let res = (a + b).toFixed(this._precision)
        return +res
    }
}
```

这段代码给类增加了两个静态属性，静态属性在使用上和实例属性的区别在于，实例属性在类的内部必须通过关键字this访问，在类的外部必须通过类的实例访问；而静态属性通常都需要通过封装它的类访问。

接下来，只需要如下操作，就能统一设置计算范围。

```
Calculator.maxValue = 100
Calculator.minValue = 10
```
可以总结为：**实例属性仅属于单个实例，而静态属性可以被所有实例共享**。

你可能注意到，上面说访问静态属性用了”通常“二字，而非”必须“，那就自然存在另一种情况 —— 在静态访问器和静态方法中，也可以使用this来访问静态属性。

- 静态访问器

```
class Calculator{
    _precision:number = 2;
    static _maxValue:number;  
    
    static get maxValue:number(){
        console.log('获取_maxValue的值')
        return Calculator._maxValue
    }

    static set maxValue(value:number){
        console.log('设置_maxValue的值')
        this._maxValue = value
    }
}
```

上面这段代码，我们添加了两个静态访问器，并在访问器中访问了类的静态属性_maxValue。

我们使用了两种方法：Calculator 和 this。

关键字this在静态访问器（及稍后要聊的静态方法）内指向的不是类的实例，而是类本身。

因此，以上静态访问器中的Calculator._maxValue和this._maxValue是等价的，都是计算器类的静态属性_maxValue。

静态访问器遵守和实例访问器一样的规则：

1、静态读取器应该被指定返回类型
2、静态设置器必须且只能接收一个参数的同时不能被指定返回类型
3、静态读取器的返回类型必须和静态设置器的参数的类型相同
4、函数体内不能访问类的实例成员，只能访问类的静态成员

- 静态方法

有了静态属性和访问器作为铺垫，静态方法理解起来就容易很多了，同样是在普通方法前增加static关键字，且通过类本身访问。

省略上面写过的代码：
```
class Calculator{
    precision:number = 2;
    static _maxValue:number = 100;  
    
    static resetMaxValue(value:number){
        this._maxValue = value
    }
}

let calculator:Calculator = new Calculator()
console.log(Calculator._maxValue) // 100
Calculator.resetMaxValue(80)
console.log(Calculator._maxValue) // 80
```

静态成员，强调的是成员是否需要动态变化和共享，除此还有一些关于“可访问性”的修饰符。

### 可访问性

- public

类成员的默认状态，添加等同于不添加，不赘述。

- protected（受保护的）

**防止类中的方法被无意义地使用**

```
class Calculator{
    //省略其他代码

    //新增检查操作数的方法
    checkNumber(a:number,b:number):boolean{
        if(a > Calculator.maxValue || a < Calculator.minValue || b > Calculator.maxValue || b < Calculator.minValue){
            return false
        }
        return true
    }
}

class SimpleCalculator extends Calculator{
    add(a:number,b:number):number{
        let numLegal:boolean = this.checkNumber(a,b)
        if(!numLegal){
            throw '非法操作数'
        }
        let res：string = (a+b).toFixed(this.precision) 
        return +res
    }
}
//假定两个类均被正确创建和导入
let calculator:Calculator = new Calculator(2)
calculator.checkNumber(1,2)

let simpleCalculator:SimpleCalculator = new SimpleCalculator(2)
simpleCalculator.add(1,2)  //3 
```

这段代码正常运行，但一个明显的问题——calculator实例的创建和使用，我们继承是为什么，能够在子类中复用父类已有的东西，所以这里的操作是没有必要的，为了防止这类事情的发生，就有了protected，我们只需要对以上代码做如下改动：

```
    protected checkNumber(a:number,b:number):boolean{
        //...
    }
```
这样以来，checkNumber 就只能被封装了它的类和子类使用，否则会报错。

- private（私有的）

**类本身可用**

乍一看，跟 protected 有点像，”私有的“感觉也会”被保护“，但protected的保护是有范围的，它赋予”子类“使用权，”私有“则更彻底，只有类本身可以使用。

假如将上面的代码做如下改动：

```
class Calculator{
    public _precision:number = 2;
    private static _maxValue:number = 100;  

    static get maxValue:number(){
        console.log('获取_maxValue的值')
        return Calculator._maxValue
    }

    protected checkNumber(a:number,b:number):boolean{
        //...
    }
}
```

把 maxValue 变成私有的，它只能被Calculator访问，如果这时候想改变它。

```
Calculator.maxValue = 200
```
会抛出错误。

但如果不想改变它，只是读取，还是可以做到，这就是定义get的意图所在。Calculator.maxValue 会返回 100。

这样以来，就等于定义了一个”只可读不可写“的属性。

类似原理，你还可以定义一个”只可写不可读“的，但貌似没有什么实际意义，不再赘述。

private看起来挺安全了，但足够安全吗？未必，毕竟它还是能够在类的内部被改变的，想要更安全，我们需要下一个”工具“。

- readonly（只读）

**实例创建后无法更改**

readonly并不是修饰符，虽然看起来像，它能和修饰符一起配合使用。

```
class Calculator{
    protected readonly precision:number = 2;

    construtor(precision:number){
        this._precision = precision
    }
}
```

这段代码中，我们给 precision 加了双层保护，一是 protected，把 precision 限制在了类和子类，然后是 readonly，使其成为”只读“属性。

只读属性的值只能在两种情况下被改变：

1、定义时
2、构造函数重新赋值

这在很大程度上保护了精度属性的安全。

需要注意的是：**修饰符可以修饰类的构造函数和所有成员，readonly只能修饰类的属性**

- 参数属性

只剩最后一个”无关紧要“的属性了，参数属性的意思就是，可在参数里面声明的属性。

**被访问修饰符修饰的构造函数参数**就是参数属性。

```
class Calculator{

    construtor(public precision:number){
        this.precision = precision
    }

}
```

它确实是构造函数的参数，但同时也是当前类的实例属性，并且这个属性的值将会是它作为参数接收到的值。

从运行效果看，它和上面的代码并无两样，只是更加简洁便利的定义。除此之外，类成员和函数参数的特点它都具备，比如：私有、可读、可选、默认值等。

## 抽象类

**不被实例化的类**

依然拿上面的计算器类来举例，其实它就是一个没有必要实例化的类。

注意，这里说的是没必要，不是不可以，为什么没必要呢？我们看看抽象类可以怎么做：

```
//计算器抽象类
abstract class Calculator{

    // 定义计算方式
    abstract method:string; 

    // 传入操作数
    constructor(protected a:number,protected b:number){
        this.a = a
        this.b = b
    }

    // 获取计算结果
    abstract getResult():number; 
}

// 加法实现类
class Add extends Calculator{
    method:string = '加法'
    getResult():number{
        return this.a + this.b
    }
}

// 减法实现类
class Subtract extends Calculator{
    method:string = '减法'
    getResult():number{
        return this.a - this.b
    }
}
```

这段代码给大家简单呈现了抽象类的定义和子类的“实现”，虽然有点刻意，没有充分的合理性和必要性，只是示例一下抽象类是如何作用的。

这时候Calculator是不能被实例化的，只能由子类提供具体“实现”，这就减少了不必要的实例创建。

当然，抽象类的存在并不只是阻止创建实例，它的另一大作用是帮助我们快速地定义相关的特征和行为的规范，以父类定义的属性和方法为规范，创建有具体场景意义的子类。

你还会发现，这里的“实现”和类继承的“重写”类似，它们都是在子类中定义父类已有的成员，但重写是指在子类中定义父类中已有的非抽象成员，不是子类的必须行为；而实现是指在子类中定义父类中已有的抽象成员，是子类的必须行为。

## 总结

类是个不小的话题，很长一段时期内，前端开发者都无法直接使用。

庆幸的是，在行业前辈们孜孜不倦的追求中，语言正变得越来越友好，越来越强大。

简单复盘，本文主要讲了类及成员的定义、访问器、静态成员、可访问性修饰符和抽象类。

程序可以是简单的，但通常是复杂的，在业务开发中，既要应对多种多样的逻辑，又要注重代码的管理，如果在语言层面提供便于管理的“工具”自然是最好的，幸好“类”提供了不少，让我们一起努力精进，使它们发挥出最大的能力吧！