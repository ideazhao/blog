title: 【算法和数据结构】—栈     
date: 2020-03-17
tags: [JavaScript、算法]
categories: [算法]
---

上一篇我们说了"列表"，是个相对简单的结构，如果数据存储的顺序不重要，也不必对数据进行查找，列表就很合适，但如果有特定的要求，“列表”就显得简陋了，本文开始，介绍一系列"真正"的结构。

## 栈是什么

关于“栈”有个经典的比喻，就是“放盘子”，我们平时在家吃饭洗盘子，都是先放最底下一个，然后一个一个往上摞，拿盘子也是从上面拿，即“存、取”都是从最上面进行操作——“LIFO(last in first out)”，这样的操作就很快，也方便实现。

在讲JS的时候，不会很经常地提及“栈”这个东西，但它却默默地在背后发挥作用，比如：表达式求值、处理函数调用等。

## 栈的实现

存储数据的底层结构这里还是选择“数组”。

```js
    function Stack(){
      this.dataStore = [],  //  初始化一个空栈
      this.top = 0,  //  记录栈顶位置，有元素加入则发生变化
      this.push = push,   //  入栈
      this.pop = pop,   //  出栈
      this.getTop = getTop    //  获取栈顶元素
      this.length = length    //  获取元素个数
      this.clear = clear    //  清空栈
    }
```

## 方法

`push()`

```js
    function push(ele){
      this.dataStore[this.top++] = ele;
    }
```

这里特别注意 top++ 的位置，放在了后面，这样新入栈的元素就放在了top代表的当前位置，然后top指向下一个位置。

`pop()`

pop方法和push相反。

```js
    function pop(ele){
      return this.dataStore[--this.top];
    }
```

直接将长度减1，便将栈顶元素剔除了

`getTop()`

```js
    function getTop(){
      return this.dataStore[this.top-1];
    }
```

`length()`

有时需要获取长度，知道有多少元素

```js
    function length(){
      return this.top;
    }
```

`clear()`

清空栈

```js
    function clear(){
      delete this.dataStore;
      this.top = 0;
      this.dataStore = [];
    }
```

## 试一试

```js
    var plates = new Stack();
    plates.push("瓷的");
    plates.push("红的");
    plates.push("我的");
    console.log("个数:"+ plates.length());  //  个数:3
    console.log(plates.getTop());  // “我的”
    console.log(plates.clear());  
```

demo顺利运行，得到我们想要的结果。

## 栈的应用

那么栈可以应用在哪类需求里？

### 递归

“栈”经常被用于实现编程语言，递归便是其中一种，我们尝试实现。

递归有很多典型使用场景，有一种叫“**阶乘**”。

可以是这样：

```js
    function factorial(n){
      if(n == 0){
        return 1;
      } else {
        return n*factorial(n-1);
      }
    }
```

用“栈”就是这样

```js
    function fact(n){
      var s = new Stack();
      while(n>1){
        s.push(n--);
      }
      var result = 1;
      while(s.length() > 0){
        result*=s.pop();
      }
      return result;
    }
```

我们来试一下

```js
    fact(5);  // 120
```

OK。

虽然从代码量来看，第二种反而更多，不如前面简洁，但这只是一个很简单的例子，而且二者算法不同，栈仍然有着它更加强大的用途。

### 函数设计

我们知道，函数的定义就是为了封装一个可复用的代码块。那么在重复应用的时候，参数是怎样传入的呢？
函数内一般都会定义局部变量，而不是单使用全局变量，那么局部变量存在哪里合适，而且能够在各种情况下都不发生冲突？

“栈”就可以解决这些问题，函数调用的时候，将参数和变量都压到一个栈中，这样避免产生太多的空间占用，也可以利用栈指针的偏移来完成存取，只要对应的函数栈指针是不同的，就不会发生冲突。

## 小结

关于“栈”的介绍到此告一段落，为便于理解，并没有很高深的东西，但不代表它不强大，相反，如果能够在合适的场景加以利用，就会帮到大忙，大家在实践中共同摸索吧~

这篇同样比较简单，但学习就是要由简到繁，由浅入深嘛，下篇见！~


    
