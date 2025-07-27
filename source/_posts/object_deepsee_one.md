title: 纵观JS对象的“简”与“繁”（上）
date: 2019-01-22
tags: [学习、javascript]
categories: [总结]
toc: true
---

JS这门语言，曾被不少开发者视为玩乐的语言，没有厚度和技术含量的语言，但发展到现在，想必没有人敢再这么说，它能做的事越来越多，所以，这门语言看似知识结构简单，但却在代码的行与行之间藏着很多细节和玄机。

“对象”在JS中是个很有意思的东西，它随处可见，说简单可以很简单，但也可以复杂到让人头皮发麻。

# 简单
如何简单？

    var person = new Object();

这样就可以创建一个名为“person”的对象。

但事实上，没有人会在意它的简单，因为简单的东西往往承担不了重任。

所以，我们可以从另一个角度去理解它的复杂，就是强大——每一种形式，每一个特性，都为了解决更多问题而生。

# 复杂
像上面那样，我们可以轻松创建一个对象，进一步，为其添加一些属性和方法：

    person.name = "idea";
    person.age = 18;
    person.run = function(){
        alert("I can run!");
    }
    
但其实这里我们就会发现需要写很多的“person”，代码不够简洁。

可以进行如下改进，也就是另外一种写法——"对象字面量"：

## 对象字面量
    var person = {
        name : "idea";
        age : 18;
        run : function(){
            alert("I can run!");
        }
    }
它有两个优点：**简化代码、凸显封装性**。

如果你对js不熟，但对css预处理还算熟悉，这就像是less或者sass的嵌套。（后面更深入的东西我还会拿css类比，以帮助理解。

由此，我们可以像这样创建对象：

    var person1 = {
        name : "tom";
        age : 18;
        run : function(){
            alert("I can run!");
        }
    } 
    var person2 = {
        name : "lili";
        age : 16;
        run : function(){
            alert("I can run!");
        }
    } 
    ...

但其实我们好像又发现一点不那么好的地方，这样以来，我们每创建一个新的实例，都要写这么一大块的代码，显然是冗余的，于是，有人发动脑筋，想了一种办法——“工厂模式”。

## 工厂模式
对于工厂模式，可以这么理解，我们需要制作100个同类的产品，但不需要为每件产品都弄一个模子，而是一个模子可以反复用，生产很多很多产品。

代码就像这样：

    function createPerson(name, age, job){
        var o = new object();
        o.name = name;
        o.age = age;
        o.job = job;
        o.sayName = function(){
            alert(this.name);
        }
        return o;
    }

    var person1 = createPerson("tom",18,"teacher");
    var person2 = createPerson("lili",16,"doctor");

这段代码的亮点在最后，我们可以只用一行代码就创建一个对象，并赋予其属性值。

看起来很不错，但它存在一个难以觉察的不足——我们好像无法获知这个对象的类型。

这么说其实并不准确，每个对象都有其类型，大不了是Object，但这个结果给不了我们更有价值的东西。所以，要引荐出一个对象世界里的重要角色——“构造函数”。

## 构造函数
先看代码：

    function Person(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = function(){
        alert(this.name);
        }
    };

    var person1 = new Person("tom",18,"teacher");
    var person2 = new Person("lili",16,"doctor");

从面上看，这段代码跟上面那段有这么几点不同：

- 没有显式创建对象
- 属性和方法直接给了this
- 没有返回
- 创建新实例的时候，使用了“new”关键字
- 名称首字母大写


为什么说它重要，上面我们只是创建了一个自定义的构造函数，其实js当中有很多内置的构造函数，我们会无数次地使用，比如Array、Date、String等等。

构造函数和普通函数有什么不同？好像只是首字母大写？

这么说也没错，它可以当做普通函数使用，就像这样：

    Person(“tom”,18,“teacher”)；

但当它被这样使用了之后，就是作为构造函数：

    var person1 = new Person("tom",18,"teacher");

会经历完全不同的过程。

构造函数看起来很好用，但它还有需要改进的地方吗？往下看。

上面那段代码里，有这么一行：

    this.sayName = function(){  
        alert(this.name);  
    }

它会为新创建的实例新建一个sayName方法，别忘了，方法是赋予对象的函数，函数本身也是对象，所以，person1和person2是两个不同的对象实例，同样，它们的sayName方法也不同（虽然看起来是一个样子）。

这样以来，name、age和job都共用了，完成同样任务的方法却没有共用，每一个新的实例都会创建一次，显然，这不是最理想的，有办法解决吗？

我们可以做这样的尝试：

    function Person(name,age,job){
    this.name = name;
    this.age = age;
    this.job = job;
        this.sayName = sayName;
    };

    function sayName(){
        alert(this.name);
    }

    var person1 = new Person("tom",18,"teacher");
    var person2 = new Person("lili",16,"doctor");

和之前的区别就在于，我们把sayName方法提到了函数体的外面，意味着，它是个全局的函数，而不属于某一个。

这个时候，你会发现一个有趣的现象，如果你写下这么一行代码：

    alert(person1.sayName() == person2.sayName());

它会弹出三个值："tom、lili、true"。

首先是里面两个分别执行，然后是外层，这说明，它们俩共用了同一个，不需要担心多余创建的那个函数实例。

终于皆大欢喜！

可是...慢着，好像哪里不对？

你可能记得，说对象字面量的时候，我们提到了封装性，你会发现，这里的sayName方法在函数体的外部，如果有很多个，就会有很多个方法散落在外部，这感觉很糟，这不是我们要的，so，必须找到一个办法解决它，就像上面做的那样。

## 原型模式
我们终于迎来了这个重磅的概念，关于这个概念，往往让人觉得是复杂的，抽象的，高深莫测的，其实并不，让我们来看看它具体的表现：

    function Person(){

    }
    Person.prototype.name = "alien";
    Person.prototype.age = "23";
    Person.prototype.job = "teacher";
    Person.prototype.sayName = function(){
        alert(this.name);
    };

    var person1 = new Person();

    var person2 = new Person();


我们创建一个函数，但里面什么都没有。

接下来用到了“prototype”，其实每个函数都有一个prototype属性，这个属性是一个指针，所谓指针，就是建立A和B相关联的一个中介，prototype指向一个对象，这个对象，可以为我们提供所有同一类型实例能够共享的属性和方法，听起来是不是很熟悉？——它能为我们带来前面提到的所有。

但这么说似乎仍然不好理解，所以，你应该对CSS很熟悉，prototype，就相当于为所有可能的子元素，提供了一个父元素，这个父元素的所有属性，都能为子元素所共享，同时，它也不限制子元素的行为。

即，person1和person2具备Person.prototype所指对象的所有属性和方法，并且能够对其进行覆盖或者添加自身特有的新属性和方法。

看到这，我们是不是应该准备欢呼“大结局”了呢？的确，已经有不少内容了，可是，事情从来都不像我们想想的那么简单。

来思考一下，原型虽好，但它是否让我们走向了另一个极端？所有的属性和方法我们都需要共享吗？往往并不是，很多时候，对于某个属性，我们只需要私有即可，而不必共有，那么，什么样的属性私有更合适？既具备私有，又能共有的方案，存在吗？

想知道答案，且看下回分解~
