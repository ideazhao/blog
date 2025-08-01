title: 征服javascript学习笔记（三）——基本概念（下）         
date: 2016-03-20
tags: [javascript]
categories: [JavaScript]
toc: true
---

## 操作符

操作符有多种，包括算术操作符（加、减号）、位操作符、关系操作符和相等操作符，能够适用于很多值，例如：字符串、数字值、布尔值，甚至对象，在应用于对象时，相应的操作符通常会调用对象的ValueOf()或toString()方法，以便取得可以操作的值。

**一元操作符**

ES中最简单的操作符，只能操作一个值。

1、递增和递减

借鉴自C，各有两个版本：前置和后置。

顾名思义，使用的时候，就是放到前面或者后面，能起到什么作用

var age=29;
++age;
	
得到的结果为30.“--”符号亦然，会得到28

至于后置，那就是先输出结果后进行计算了，不再赘述。

2、一元加、减

一元加减应用到数值上面，和我们平时的认知没有不同，代表着正负数。

在对非数值应用时，就像Number()转型函数一样对值进行转换，布尔值true和false会被转换为1和0，字符串值会按照一定的规则进行解析，对象是先调用它们的valueOf()和toString()方法，再转换得到的值。

一元减操作符可以用于负数，一元加、减均可用于基本运算。

**位操作符**

位操作符用于最基本的层次上，按内存中表示数值的位来操作数值，ES中的所有数值都是以64位格式存储，但位操作符不直接操作64位的数，是将64位的值转换成32位的整数，然后执行操作，最后将结果转换回64位，对于开发人员来说，64位存储格式是透明的，因此整个过程就像只有32位的整数一样。

对于有符号的整数，32位中的前31位用于表示整数的值，第32位用于表示数值的符号：0表示正数，1表示负数。叫做符号位。

符号位的值决定了其他位数值的格式，其中，正数以纯二进制格式存储，31位中的每一位都表示2的幂，负数同样以二进制码存储，但使用的格式是二进制补码，计算一个数值的二进制补码，需要进行3个步骤：
（1）求这个数值绝对值的二进制码
（2）求二进制反码
（3）得到的二进制反码加1

1、按位非（NOT）

由一个波浪线（~）表示，执行按位非的结果就是返回数值的反码，是ES操作符中少数几个与二进制计算相关的操作符之一。

2、按位与（AND）

由一个和号字符（&）表示，有两个操作数，从本质上讲，就是将两个数值的每一位对齐，然后根据有0为0，两个都是1为1的规则，对相同位置上的两个数进行计算

3、按位或（OR）

由一个竖线符号（|）表示，也有两个操作数，遵循有1为1，同为0则为0的规则对相同位置的数进行计算。

4、按位异或（XOR）

由一个插入符号（^）表示，两个操作数，遵循：两位不同为1，相同为0的规则。

5、左移

由（<<）表示，会将数值的所有位向左移动指定的位数。

6、有符号的右移

由（>>）表示，会将数值向右移动，但保留符号位。有符号的右移操作和左移操作恰好相反。

7、无符号右移

由（>>>）表示，会将所有32位都向右移动。对正数来说，无符号右移的结果和符号右移相同，但对负数来说，情况就不同了，首先，无符号右移是以0来填充空位，而不是像有符号右移那样以符号位的值来填充空位，所以，对正数的无符号右移与有符号右移结果相同，但负数的结果就不一样了，其次，无符号右移操作符会把负数的二进制码当成正数的二进制码，而且，由于负数以其绝对值的二进制补码形式表示，因此会导致无符号右移后的结果非常大。

**布尔操作符**

3个：非（NOT）、与（AND）、或（OR）

逻辑非，一个！号表示，无论操作的值是什么类型，它都会返回一个布尔值，首先把操作对象转换成布尔值，再对其求反。当然，它也可以用作把一个值转换成其对应的布尔值，同时使用两个逻辑非操作符，实际上会模拟Boolean()转型函数的行为，就得到了这个值真正对应的布尔值。

逻辑与，由两个和号（&&）表示，两个操作数。遵循“同为真则真，有一个假则为假”的规则，可以应用于任何类型的操作数，在有一个操作数不是布尔值的情况下，逻辑与操作就不一定返回布尔值。

逻辑或，由（||）表示，两个操作数，遵循“同假为假，其他为真”的规则。

**乘性操作符**

乘法 *
除法 /
求模 %

**加性操作符**

加法 +
减法 -

**关系操作符**

小于（<）、大于（>）、小于等于（<=）或大于等于（>=）

**相等操作符**

相等和不相等 “==”和“!=”
全等和不全等 “===”和“!==”

**条件操作符**

a>b?1:2;

若a大于b则为1，不大于则为2

**赋值操作符**

用“=”表示，如：a=3；将右侧的值赋给左侧

每个主要的算术操作符都有对应的复合赋值操作符，如：*=、/=、%=、+=、-=、<<=、>>=.

**逗号操作符**

使用逗号操作符可以在一条语句中执行多个操作，如下例：
var num1=1,num2=2,num3=3;
逗号操作符多用于声明多个变量，除此之外，逗号操作符还可以用于赋值，在用于赋值时，逗号操作符会返回表达式的最后一项，如下例：
var num=(1,2,3,4,5); 
最终num的值为5.

## 语句

首先，我们要知道语句是用来做什么，比如，条件判断，我们常常需要做比较，某个条件成立的情况下执行某个动作，又或者循环，网页中每种类型的信息都可能是多个，那么对多个进行判断或者操作，就可能会用到循环，当然，也跟数组有关，后面会有，这里不探讨。

1、if

	if(a){
		do this
		} else {
		do this
		}

虽然在比较简单的情况下，比如一个简单的判断之后执行一个动作，那么它们可以被写在一行上，但是，业界普遍推崇的最佳实践是始终使用代码块，不管是简单还是复杂

2、do-while

是一种后测试循环语句，只有在循环体中的代码执行之后，才会测试出口条件，即循环体内的代码至少会被执行一次：

	var i=1;

		do{
		i+=2;
		} while (i<10)
	
		alert(i);

只要变量i的值小于10，语句就会一直执行下去。

3、while

跟do-while相对，它是前测试语句，先判断，后执行。

	var i=0;
		while(i<10){
		i+=2;
		}

4、for 

一种前循环测试语句，具有在执行循环前初始化变量和定义循环后要执行的代码的能力。

包括初始化变量定义，条件表达式，和循环的方式三个参数设置，例如：

	 var count=10;
	 	for(var i=0;i<count;i++){
	 	alert(i);
	 	}
	 
其实你可以发现，它跟下面的语句功能相同

	var count=10;
		var i=0;
		while(i<count){
		alert(i);
		}
	
for循环只是把循环有关的代码集中到了一个位置，故而，while做不到的，for也做不到。需要指出的是，在for循环的变量初始化表达式中，可以不用var，也可以在外部进行初始化。

for语句有极大灵活性，是ES中最常用的一个语句。

5、for-in

是一种精准的迭代语句，可以用来枚举对象的属性，譬如示例：

	for(var propName in window){
		document.write(propName);
	}
	
上述例子，使用for-in循环来显示了BOM中windows对象的所有属性，注：ES对象的属性没有顺序，因此，通过for-in循环输出的属性名顺序是不可预测的。

6、label

使用label语句可以在代码中添加标签，以便将来使用。

	label:statement

示例：

	start:for(var i=0;i<count;i++){
		alert(i);
	}

此例中定义的start标签可以在将来由break或continue语句引用，加标签的语句一般都要与for语句等循环语句配合使用。

7、break 和 continue

此二者用于在循环中精确的控制代码的执行，其中，break会立即退出循环，强制执行循环后的语句，而continue语句虽然也会立即跳出循环，但退出循环后会从循环的顶部继续执行。

8、with

with语句是将代码的作用域设置到一个特定的对象中，定义with语句的目的主要是为了简化多次编写同一个对象的工作，如下

		var qs=location.search.substring(1);
			var hostName=location,hostname;
			var url=location.href;

上面几行代码都包含location对象，如果使用with语句，可以写成如下：

	with(location){
		var qs=location.search.substring(1);
		var hostName=location,hostname;
		var url=location.href;
	}
	
但是，大量使用with会导致性能下降，同时也给调试代码带来困难，大型应用程序中不建议使用with语句。

9、switch

switch语句和if语句关系密切，同样可用于多种条件的判断，代码示例如下：

	switch(i){
		case:25:
		alert("25");
		break;
		case:35:
		alert("35");
		break;
		case:45:
		alert("45");
		break;
		default;
		alert("Other");
	}
通过为每个case后面添加一个break语句，可以避免同时执行多个case代码的情况，若确实需要合并几种情况，要在代码中写清楚注释。

可以在switch中使用任何数据类型，而且每个case的值不一定是常量，可以是变量甚至表达式。

switch语句在比较值时使用的是全等操作符，因此不会发生类型转换。

## 函数

提到函数，不得不说的一个词是**封装**，它可以封装任意多条语句，可以在任何地方，任何时候调用执行，使用function关键字来声明，后跟一组参数及函数体。譬如：

	function sayHi(name,message){
		alert("hello"+name+","+message);
	}
	
可以通过其函数名进行调用，后面加上一对圆括号和参数，多个参数逗号分开。

	sayHi("idea"，“you are good”)；
	
ES中的函数在定义时不必指定是否返回值，实际上，任何函数在任何时候都可以通过return语句后跟要返回的值来实现返回值，如：

	function sum(num1,num2){
		return num1+num2;
	}

这个sum()函数的作用是两个值的和，除了return语句，没有任何声明表示该函数会返回一个值，调用示例如下：

	var result=sum(5,10);

**函数会在return语句之后立即退出，若return后面还有代码，将不会被执行。**

当然，一个函数可以在函数体的不同代码块中包含多个return语句。另外，return语句可以不带任何返回值，这时，函数在停止执行后将返回undefined值，用于需要函数提前终止又不需要返回值的情况。

推荐做法是，要么让函数始终返回一个值，要么永远不要返回值，否则会给代码调试带来不便。



**理解参数**

ES函数的参数和其他语言的函数参数不同，它不在乎传进来多少参数，也不在乎参数的数据类型，定义的函数参数个数和调用是传递的参数之间没有必然联系，之所以会这样，是因为ES中的参数内部是用一个数组来表示的，函数接收到的始终都是这个数组，而不关心数组中包含哪些参数，如果这个数组中不包含任何元素，也无所谓，包含多个元素，也没问题，实际上，在函数体内，通过arguments对象来访问这个参数组，从而获取传递给函数的每一个参数。

其实argument对象只是与数组类似，前面例子可以这样写

	function sayHi(){
		alert("hello"+arguments[0]+","+arguments[1]);		
	}
它不包含命名参数，但函数功能依旧，这个事实说明了ES函数的一个重要特点，命名的函数只是提供了便利，但不是必需的。当然，另外一点是，arguments对象可以和参数一起使用，它的值永远和对应命名参数的值保持同步。

需要记住的最后一点：没有传递值的命名参数将自动被赋予undefined值，这就跟定义了变量却并未初始化一样。

**没有重载**

ES函数不能像传统意义那样实现重载，而在其他语言中，可以为一个函数编写两个定义，只要两个定义接受的参数的类型和数量不同即可。如前面所述，ES中的函数没有这些，其参数是由包含零活多个值的数组来表示的，所以，重载是不可能做到的。

如：

	function addSomeNumber(num){
		return num+100;
	}
	
	function addSomeNumber(num){
		return num+200;
	}
	
	var result=addSomeNumber(100);  //300
	
此处，函数被定义了两次，但后定义的函数覆盖了先定义的函数，所以最后的值为300.


好了，关于基本概念，到此就告一段落了，还是有不少内容的，接下来依然路途遥远，但请相信只要我们坚持下去，它就不再那么遥远，定会越来越精彩，一起继续加油吧！~
	


	



