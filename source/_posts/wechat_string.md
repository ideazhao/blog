title: 【轻聊前端】"字符串"江湖
date: 2021-03-27
tags: [分享]
categories: [JavaScript]
toc: true
---

> 现今各种框架、工具‘横行’，到处在讲原理和源码，更有跨端技术需要我们去探索，但如果基本功不好，学什么都是事倍功半，效果很不好，花费时间的同时打击自信心。此篇文章，为我所计划的【轻聊前端】系列第（六）篇，旨在系统地、逻辑性地把原生JavaScript知识分享给大家，帮助各位较为轻松地理清知识体系，更好地理解和记忆，我尽力而为，望不负期待。

“所有信息都是用字符串表示的。”

前面的文章里，我们说变量使得程序成为程序，那么也可以说，不论虚拟世界还是现实世界，其中传输的信息都是用字符串表示的，故而，字符串的使用及处理很重要。

## String

字符串，即JavaScript中的String类型，可以用“”或者‘’来直接定义，也可以使用String方法定义。

```
let str = "我是字符串";
let str2 = String("我也是字符串")
```

使用场景很多：

- 文件类型：当判断一个文件是否属于我们要求用户上传的文件类型时，可以在获取到文件的MIME值之后，去检查是否包含我们要求的类型字符串，比如“image、video”等。

- 输入格式：当需要用户输入一个手机号，或者邮箱时，检查格式是否合规。

- 拼接字符串：很多时候，展示的信息后端不是整个返回的，而是多个结果组合，就需要拼接。

- 索引：查询字符在字符串中的位置，或者进一步判断某字符串是否以特定字符开头或结尾。

- 截取：有时后端返回数据过长，没必要展示全部，比如时间，后端返回完整的“年-月-日 时：分：秒”，前端仅展示“年-月-日”。

等等，这些我们接下来都会介绍，当然，会更多。

## 属性

字符串的常用属性只有一个——length，用来检测字符长度，比如我们经常会限制用户输入“名称、描述”的长度，一是出于通常情况考虑，二是内容过长会影响页面展示，就可以检测输入字符串长度然后给提示。（现在这块内容不用专门做，原生或者组件库会支持）

## 方法

**获取字符**

第一种方法：charAt()

参数是字符所在的索引位。

```
let str = "hello world";
str.charAt(4)   //"o"
```
第二种方法：中括号

同样是用索引

```
let str = "hello world";
str[4]   //"o"
```

**字符串比较**

前面在讲数字的时候已经提及字符串的比较，因为数字也是字符的一种，当它们以字符串类型出现时，采用的就是字符串的规则。

当然，运算符是一样的，只是会比较它们的ASCII码。

```
'a' > 'b'  //false
'11' > '2' //false
'a' > 'A'  //true
```
哟，貌似又一个奇怪的东西混进来，第三个表达式‘a’>'A'是成立的，不都是a？但其实也不奇怪，每个字符都有自己的ASCII码值，值不同，就有大小之分，哪怕是同一个字母的大小写，这样的话，在某些可忽略大小写的情况，就可以使用下面的方法先转换后比较。

**大小写转换**

toLowerCase()/toUpperCase()

通过方法名即可看出，Lower是转换成小写，Upper是转换成大写。

```
let str = "heLLo World";
str.toLowerCase();  //"hello world"
str.toUpperCase();  //"HELLO WORLD"
```
但你可能又要问了，我怎么知道它输的是不是字母，就直接转？如果不是字母转个什么劲儿，有没有办法判断呢？

**查编码**

前面说了，字符串比较的是ASCII码，要比较，首先要有，既然有，是不是可以通过字符的ASCII范围来判断呢？

charCodeAt()

稍微一查即可知道：A-Z的ASCII码范围是“65-90”，a-z的ASCII码范围是“97-122”。

那么，只需要用输入的字符码和它们进行比较便知。

```
let str = "h".charCodeAt();
(str >= 65 && str <= 90) || (str >= 97 && str <= 122)  //true
```

既然字母可以判断，我们的母语中文是否可行？当然。

字母有范围，汉字也有范围，汉字的编码都大于255，只需要检测字符串中字符是否有大于255的即可。

```
let str = "灵".charCodeAt();
str > 255   //true
```

同理，数字也可，这里不再赘述。

好，上面说了怎样检测是不是“字母、文字”，那如果用户啥也没输，啪啪敲几个空格呢？通常情况下空格是没有实际意义的，不需要输入，有办法知道么，知道了又怎么办？

**是否存在**

indexOf()/includes()

当我们需要检测一个字符串中是否包含某个特定字符，可以使用这两个方法。

indexOf()返回字符索引，includes()返回布尔值。我们就来检测一下空格。

```
let str = "hello world";
str.indexOf(' ');  //5
str.includes(' '); //true

//检测不存在的字符
str.indexOf('p');  //-1
str.includes('p'); //false
```
可以看出indexOf检测存在的字符会返回正确索引，不存在的返回-1，includes返回true/false。

indexOf()还有一个兄弟方法，lastIndexOf()，返回字符串中某个字符最后一次出现的索引值，也说明了indexOf()返回的是字符第一次出现时的索引。

```
let str = "hello world w abc w";
str.indexOf('w'); //6
str.lastIndexOf('w'); //18
```

那么除了第一次和最后一次，中间出现的能不能查到呢？也是可以的，这就要多加一个参数：

indexOf(searchValue [, fromIndex])

```
str.indexOf('w',7); //12
str.lastIndexOf('w',7); //6
```

同理，includes()方法也是可以有起始查询位置的参数的，但一般情况下用不到。

看到这些方法，是否想到上面提到的检测文件类型？通常文件的MIME类型是“image/png”形式，只需要检测“image”即可判断图片类型。

好，这个方法让我们知道了有没有空格，然后怎么办？

**替换**

字符串为我们提供了使用现有字符串替换原字符串的能力。

replace(regexp|substr, newSubStr|function)

第一个参数可以是原字符串，也可以是正则表达式，第二个参数可以是新字符串，也可以是个函数。

这里我们只要用到字符串。如果不需要空格，用“”把它替换掉不就可以了？

```
let str = "hello world";
str.replace(' ','');  //"helloworld"
```
这感觉类似我们用编辑器或者用word时的全局替换功能，只不过是用JavaScript代码实现，在有些时候很实用，不需要找到某个位置的字符去改变，可以直接替换。

但是，去除空格只是replace能够处理的情况之一，还有专门去除空格的方法。

**去除空格**

trim()/trimStart()/trimEnd()

字符串本身具备几个去除空格的方法。
trim()常用来为用户输入的数据去除两端的空格。

比如：

```
let str = "  hello world  ";
str.trim();  //"hello world"
```
可以看到，字符串两边的空格被去除，但中间的没有。

这个方法在一些老旧浏览器里不支持，可以回退到上面说的replace()方法进行兼容。

而另两个Start和End则分别是从“左侧”或“右侧”去除空格。

现在，为提高用户输入数据的合理性，trim相关方法预处理几乎成了标配。

这里遇到了“左侧”和“右侧”的概念，那字符串中有没有判断最左侧或者最右侧是不是某字符的方法呢？必须。

**以什么字符开头/结尾**

startsWith(searchString[, position])
str.endsWith(searchString[, length])

这两个方法很好理解，看字面意思便知，一个是开头，一个是结尾，直接看效果。

```
let str = "深圳市aaa.png";
str.startsWith("深圳市");   //true
str.endsWith(".png");  //true
```
两个方法有两个代表不同意义的第二参数，开头的，可以给起始查询位置，而末尾的可以给个长度，可以查到指定长度的字符串是否以某字符串结尾。

看到这里，是不是觉得，我们判断文件类型的时候也可以用这个方法？理论上有可行性，但有两点不足：

- 很多图片的文件路径都不是文件类型结尾，可能会加时间戳之类的东西，就不准了。
- 每种文件类型都有很多扩展，如果要求很松，就要写很多映射，还可能变动，健壮性和维护性都比较差。

所以还是根据具体场景选择更适合的方法为好。

**头部/尾部填充**

既然有了查询首尾的能力，也应该具备添加首尾的能力。

padStart()/padEnd()

在当前字符串首部或尾部填充指定的字符串， 直到达到指定的长度。

有什么可能的使用场景呢，可以通过其特点观察，指定字符串就不说了，指定长度，什么时候需要指定长度？

有固定位数的时候——电话号码、身份证号，邮政编码等。

当我们需要给其中的某些位填上“*”号使其不可见，就可以先将字符串裁剪，然后在其中一个的首部或尾部填充上足够的“*”，达到应有的位数，然后再拼接起来即可。

除此之外，还可以用另一个方法做到同样的事——repeat()。

可以将指定字符串重复一定次数，即将上面的字符串截取过后，得出需要填充的位数，给repeat处理完进行拼接，也可。

这里只是简单说一下可能的使用场景，实际当中或许很少用，也或许有更好的方法和更适合的场景。

好，我又偷偷地引入了两类新方法——裁剪、拼接。

**字符串裁剪**

这是很重要也很常用的方法之一。

它有几小种：

- slice()——截取位置区间。
- substr()——截取指定数量。
- substring()——返回指定下标之间的字符。

slice()方法接收一个必选参数和一个可选参数。看示例：

```
let str = "hello world";
str.slice(5);  // " world"
str.slice(5,7);  //" w"
```
如果只传一个参数，则截取参数位置到末尾的部分，传了两个，则是两参数位置之间的部分。值得注意的一点是，它支持传负值。

```
str.slice(-6)  // " world"
```
和传5的时候结果一样，可以这么记，如果传正的，就从前面数，负值从末尾开始数，截取的长度就是前后的差值。当然，需要同正同负。比如(3,5)、(-6,-2)，正着截长度为2，反着截长度为4。

substr()方法从指定位置上截取指定数量的字符。

```
let str = "hello world";
str.substr(5);  // " world"
str.substr(5,2);  //" w"
```
可以看出，如果没有指定位数参数，它和slice()方法的效果是一样的，而指定了位数参数之后，就不同了。

它跟slice还有一点不同是，因为是指定位数，就可以不同正负了。

```
str.substr(-2,2);  // "ld"
```

substring()方法乍一看和slice()方法干的是同一件事，有什么不同？

- 截取算法

```
let str = "hello world";
str.slice(5,2);  //""
str.substring(5,2);  //" w"
```
可以看出，slice方法的截取有计算顺序，当位置参数为正，从后往前截不会得到结果，同理，位置参数为负时，从前往后不会有结果。

而substring纯粹返回两个下标之间的字符，无关截取顺序。

- 位置算法

```
let str = "hello world";
str.slice(-2,3);  //""
str.slice(3,20);  //"lo world"

str.substring(-2,3);  //"hel"
str.substring(3,20);  //"lo world"
```
可以看出，slice在位置为负时会从末尾向前计数，从而无法得到结果，substring不会反向计数，而是会当做从头开始来返回字符串。当位数超出字符串长度时二者的结果是相同的。

通过以上讨论，字符串操作在某种程度上有类似效果，日常不会全都用到，需要时选择一种更适合的就好。

**字符串拼接**

终于来到最常见需求之一的字符串拼接了。

首先想到的应该是“+”运算符，这个前面已经提过，最便利，有多少加多少，但字符串有自己的连接方法——concat()。

```
let str = "hello";
str.concat('lily','and','lucy');  //"hellolilyandlucy"
```
虽然如此，多数时候还是推荐使用“+”运算符。

但是，在使用较长字符串，且需要连接变量时，用另一种方法会更好——**模板字符串**。

模板字符串是ES6之后新加入的方式。以前我们会这样写：

```
let name = 'Tom';
let age = 10; 
let intro = "我的名字叫" + name + ',' + '我今年' + age + '岁了';
```

模板字符串将字符串用反引号进行包裹，且可以解析变量。

```
let intro = `我的名字叫${name},我今年${age}岁了`;
```
相比之前有几个优点：

- 书写更简洁，不需要特别注意使用加号和引号，出错概率低。
- 空格和缩进都会保留在输出中，以前的方法需要拼接相应字符。
- 可以在${}中使用JavaScript表达式进行计算，比如${a/b}。

**转换为数组**

split([separator[, limit]])

这个方法是字符串操作中的另一个常见用法，为什么，因为很多时候，我们在页面上输入的，或者从后端拿到的数据，都会是字符串形式。

像“1，3，5，6，7”或者“张三，李四，王五”

如果仅仅作为文本来展示问题不大，但如果需要进行其他的处理，比如：排序、过滤、列表渲染等，字符串的能力就捉襟见肘了，而把它们转换成数组，再用数组相关的能力，就很容易了。

```
let numStr = '1,3,5,6,7';
numStr.split(',');  // ["1", "3", "5", "6", "7"]  

let numStr = '1-3-5-6-7';
numStr.split('-');  // ["1", "3", "5", "6", "7"] 
```
这个方法还有一个不太常用的第二个参数limit，用于指定分隔符的限制。

```
numStr.split(',',3);
(3) ["1", "3", "5"]
```

**对象字符串化**

前面讲运算符和类型的时候，说过对象也是可以转换为字符串的，什么方法呢？toString？valueOf?

可以看一下：

```
let obj = {
    a:'b'
}
obj.toString()  //"[object Object]"
obj.valueOf()  //{a:'b'}
```
看起来toString完不成这个任务，valueOf凑效了，但是

```
typeof obj.valueOf()  //"object"
```
它并不是字符串，仍是对象，那怎么把对象真的转换成字符串呢？

JSON.stringify()

```
JSON.stringify(obj)  //"{"a":"b"}"
```
这样就真正转换为字符串类型了。

除此之外，数组也是类似的。

这经常发生在需要深拷贝一个对象，或者给后端发送请求时将对象转换为JSON串的情况。

## 正则匹配

replace()/match()/search()

前面讲了很多字符串的查找、替换和裁剪操作，但在某些时候，简单的操作，或者固定的值无法满足复杂的条件，就需要用到另一个重要武器——正则。

正则表达式是一种字符规则，可以用少量的书写规则匹配一大类字符的组合情况，以助更高效地解决问题。

replace()示例：

replace()方法上面已经用过，只是用一个字符串替换另一个字符串，但它也可以用正则来匹配替换，实现更强大的功能。

```
var reg = /apples/gi;
var str = "Apples are round, and apples are juicy.";
var newstr = str.replace(reg, "oranges");   // oranges are round, and oranges are juicy.
```
以上代码，表示以不区分大小写的情况全局将“apples”替换为“oranges”。这里只是个简单示例，更为强大的效果，需要运用更多正则的匹配技巧，不展开。

match()示例：

```
let str = "123Abc";
let reg = /[A-Z]/g;
str.match(reg);  //["A"]
```
match()方法返回一个数组，而且是返回能够和正则表达式匹配的部分。这就比去检查字符的类型再输出指定位置的字符要便利得多。

search()示例：

```
var str = "123AbB";
var reg = /[A-Z]/g;
str.search(reg)  //3
```
search()方法返回符合条件的第一个字符的索引，从功能上，和indexOf()方法很像，但它是用规则进行匹配，覆盖范围更大，适合检查某一类字符。

## 总结

到此，关于“字符串”的讨论可以告一段落。

相比“数字游戏”，“字符串”江湖的丰富程度不遑多让，甚至更复杂多样，也能看出字符串在编程领域有多重要，使用的场景多么繁多，这可能也是开发者为其准备更多处理工具的原因。

内容略多，可慢慢消化，并在不断地使用过程中逐渐熟练吧~

下篇见！


