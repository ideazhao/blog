title: 【TypeScript逐点击破】函数类型
date: 2021-12-12
tags: [TypeScript]
categories: [TypeScript]
toc: true
---

TypeScript的核心是类型，但除了JavaScript中熟识的常规类型之外，它还包含了一些拓展类型，函数类型就是其中之一。

你可能会说，不对，JavaScript中也有函数类型——Function，的确，但严格来说它不是独立的类型，属于Object，而且这个function的约束仅仅在于“是不是函数”、“能不能被合法调用”，除此之外，别无它用，而TypeScript显然需要更多。

更多的什么？函数体是不会被限制的，因为它对外部来说就是个黑盒，须给予充分的自由度，那么开发者就只需要关心“入参”和“返回值”。

## TypeScript中的函数

看函数类型之前，先看TypeScript中是怎么定义函数的。

```
function add(a:number,b:number):number{
    return a + b
}
```

这就是一个简单、完整，带类型限制的函数。

哪些限制，我们可以想一想，在JavaScript中：

- 函数的参数是必须的么，非必须
- 定义参数之后，传参是必须的么，非必须
- 定义参数之后，传参类型固定么，不固定
- 执行函数之后，返回值类型确定么，不确定

如此多的不确定会带来什么？——潜在风险。

聊函数的时候我们就说过，参数是函数发挥强大威力的关键因子，无参会大打折扣，而一旦有了参数，却不可控，也很容易发生期望之外的情况，这是我们不想看到的，所以，定义函数时对参数和返回值进行约束是有利的。

如上代码所示，在TypeScript中定义了一个函数，如果不按要求传参，或者返回值类型和定义的类型不同（也不兼容）时，都会出现编译报错，这就是TypeScript中函数的重要区别。

你可能又会问，TypeScript这种限制是否过于死板？如果有一个函数，它需要允许不同情况传不同的参数，有解？有。

**可选参数**

比如，登录功能，我们需要用户输入用户名、密码，可选地输入年龄，就可以这样定义。

```
function login(name:string,password:string,age?:number){
    //登录逻辑
}
```

只需要在参数名称后加个‘？‘，就可以实现可选参数的定义，这个参数可传可不传，不会导致编译错误。

但有一点需要注意，可选参数只能跟在常规参数的后面，放前面同样会报错。

除此之外，跟ES6类似，TypeScript中也有默认参数、剩余参数的概念，此处不再赘述。

到这里，只是在讲TypeScript中怎样“定义函数”，还没说到“函数类型”。

## 理解“函数类型”

知道了TypeScript中函数的不同点，“函数类型”又是什么，该怎么理解？

其实也不难，可关注以下几点：

- 函数定义只是对参数和返回进行限制，而不是对函数本身的限制
- 函数类型，是用函数的形式来定义“类型”，终归还是“类型”
- 仅作为类型定义存在，不会在编译后的代码中存在
- 会在函数使用过程中给予提示

一点点看：

在 JavaScript 中，常会有这样的操作：

```
function add(a,b){
    return a + b
}
let addFunc = add
```

那么问题来了，TypeScript中怎样给addFunc定义类型？你可能想，数字是number，字符串是string，函数就是这样：

```
let addFunc:function = add
```

但这是不行的，TypeScript的编译时类型并没有function，此时该“函数类型”登场了。

```
type add = (a:number,b:number) => number
```

它由"type"关键字和一个类似“函数表达式”的东西构成。

type代表定义一个类型，后面的表达式包含了“名称”，“参数列表”，“参数类型”和“返回值类型”。

函数类型其实就是 (a:number,b:number) => number 这么一段，’add‘是类型别名。

这样以来，上面的赋值就可以写成这样：    

```
let addFunc:add = add
```

是不是一下清晰了。

为什么用 type 标识，而不是 let、var、const，因为它只是代表一个类型，不是常量，也不是变量，并且编译完成后是会被“干掉”的。

那它的实际作用又是什么？只用来指定函数变量的类型？这就涉及到上面所列的最后一点了。

当定义了这样一个类型之后，IDE会在全程提醒我们应该给函数的参数提供什么值，需要返回什么类型的值，只有当函数具备类型的时候，IDE才能提醒我们这些详细的信息。而这样的类型我们可以多次复用，达到同一种类型约束。

到这“函数类型”已经介绍完了，既然聊到函数，就顺便说下另一个紧密相关的话题——“重载”。

## 重载

重载是JavaScript中没有的概念，重载函数常用来实现功能类似而所处理的数据类型不同的问题。简单理解就是“一函多用”。JavaScript中的同名函数只会被覆盖。

TypeScript中多个类型不同但名字相同的函数被称为函数重载。

和其他语言不同的是，TypeScript中重载的函数不能包含自己的函数体，而只能共用一个实现函数的函数体。示例如下：

```
function add(a:number,b:number):number;  //加法运算
function add(a:string,b:string):string;  //字符串拼接
function add(x:any,b:any):any(   
    return x + y  //加法或字符串拼接的实现
)
```

以上函数重载实现了一个，既能执行数字相加，也能执行字符串拼接的add方法，如果你想，还能做更多。

但在实现函数的重载时，不是没有任何限制，需遵守以下三个规则：

（1）实现函数和重载函数必须同名；
（2）实现函数的类型必须兼容于任何一个重载函数的类型；
（3）重载函数和实现函数之间不能有其他代码。

以上三点其实很好做到，而且看起来挺不错的样子，大家在实际项目中可以尝试用起来，或有奇效。

## 总结

对TypeScript函数类型的介绍到此告一段落，虽然有些东西未提到，比如：类型兼容、参数数量兼容等，但这些较容易理解，就不详细展开，如有兴趣，可以进一步探讨。

这篇文是TypeScript的第二篇，相当于JavaScript话题没结束之前，将TypeScript提前了，因为不想继续往后拖，并行也没大问题。

这系列文也只是重点关注二者不同的部分，便于转换理解，咱们下篇见。

