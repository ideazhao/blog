title: 征服javascript学习笔记（六）——面向对象的程序设计之创建对象		        
date: 2016-09-21
tags: [javascript]
categories: [JavaScript]
toc: true
---

面向对象的语言有一个标志，那就是它们都有类的概念，而通过类可以创建任意多个具有相同属性和方法的对象。
每个对象都是基于一个引用类型创建的，这个引用类型可以是原生类型，也可以是开发人员定义的类型。

##创建对象

创建自定义对象最简单的方式，就是创建一个Object的实例，然后再为其添加属性和方法。

比如这样：

	var person = new Object();
	person.name = "idea";
	person.age = 28;
	
	person.sayName = function(){
		alert(this.name);
	}

##工厂模式

另外还可以采用对象字面量的方式，前面的文章里也有提到过，但是这些方式有个明显的缺点：使用同一个接口创建很多对象，会产生大量的重复代码。为解决这个问题，人们开始使用工厂模式的一种变体。比如：

	function creatPerson(name,age,job){
		var o = new Object();
		o.name = name;
		o.age = age;
		o.job = job;
		o.sayName = function(){
			alert(this.name);
		};
		return o;
	}
	var person1 = creatPerson("Nicholas",29,"programer");
	var person2 = creatPerson("Greg",27,"Doctor");

函数creatPerson()能够根据接受的参数来构建一个包含所有必要信息的Person对象，可以无数次调用这个函数，而每次它都返回一个包含三个属性一个方法的对象，它解决了创建多个相似对象的问题，但却没有解决对象识别的问题，即怎样知道一个对象的类型。这会是下面一个模式会解决的问题。









