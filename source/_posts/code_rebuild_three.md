title: 重构：编程大师的实操指南  
date: 2024-11-10
tags: [书籍]
categories: [说书匠]
toc: true
---

> 每个系列一本前端好书，帮你轻松学重点。
>
> 本系列来自前ThoughtWorks首席科学家**马丁福勒**所编写的  **《重构：改善既有代码的设计》（第二版）**

前面的文章，我们抛出了项目代码中的种种问题，想必你也曾试图改善和优化过，但有时不得法，改不好。

本篇文，介绍一些主要问题的改善方法。

## 见名知意

难以理解的命名无疑是头等问题。

你见过最奇葩的名字是什么？笔者见过以自己名字命名的，用拼音的，用汉字的，用符号的，用简写的。

一个好名字能让人一眼看出变量的用途，但起个好名字并不容易，我们常常会有“先起个名字用着”的想法，这会留后患，当名字和内容不符，会让人产生困惑，从而花更多时间去理解。

起名字的技巧：

**语义化**：left、work、goods、about—避免生僻单词

**组合**：userinfo-update、form-login—表达更复杂的意思

**名、动区分**：userName（名）、getUserName（动）—区分是什么和干什么

**交互关联**：refreshClick、linkRedirect—用途和触发条件关联

以上为常用技巧，同时要统一规范，否则会充斥着各种风格，反而引起混乱。

如果实在想不到好名字，可以到这个站点寻求帮助（https://unbug.github.io/codelf/）

## 局部提炼

#### 变量

代码中难免会涉及结果值是由其他变量计算得来，就会存在计算表达式，表达式可能非常复杂而难以阅读。

这种情况下，局部变量可以帮助我们将表达式分解为容易管理的形式，调试时也更方便。

```
// 提炼前
return order.quantity * order.itemPrice -
Math.max(0, order.quantity - 500) * order.itemPrice *
    0.05 +
      Math.min(order.quantity * order.itemPrice * 0.1, 100);
// 提炼后
const basePrice = order.quantity * order.itemPrice;
const quantityDiscount = Math.max(0, order.quantity - 500) * order.itemPrice * 0.05;
const shipping = Math.min(basePrice * 0.1, 100);
return basePrice - quantityDiscount + shipping;
```

#### 函数

我们需要精简过长的函数，理由是什么？

有人认为是长度，减轻阅读负担；有人认为是复用，只要被用过不止一次的代码，就应该单独一个函数。

作者认为，最合理的观点是“**将意图与实现分离**”：如果你需要花时间浏览一段代码才能弄清它在干什么，就应该将其提炼到一个函数中。

```
// 提炼前
function printOwing(invoice) {
  let outstanding = calculateOutstanding();
  //print details
  console.log(`name: ${invoice.customer}`);
  console.log(`amount: ${outstanding}`);
}
// 提炼后
function printOwing(invoice) {
  let outstanding = calculateOutstanding();
  printDetails(outstanding);
}
function printDetails(outstanding) {
  console.log(`name: ${invoice.customer}`);
  console.log(`amount: ${outstanding}`);
}
```

## 参数对象

我们常会看见，一组数据总是结伴而行，出现在一个又一个函数，我喜欢代之以一个数据结构，如：对象。

**将数据组织成结构**可以使数据项之间的关系变得明晰，参数列表也能缩短，并且，所有使用该参数的函数都会通过同样的名字来访问，从而提升代码一致性。

```
// 基本变量
function amountInvoiced(startDate, endDate) {...}
function amountReceived(startDate, endDate) {...}
function amountOverdue(startDate, endDate) {...}
// 参数对象
const DateRange = {
 startDate：“”，
 endDate：“”
}
function amountInvoiced(DateRange) {...}
function amountReceived(DateRange) {...}
function amountOverdue(DateRange) {...}
```

## 派生聚合

在软件中，经常需要把数据“喂”给一个程序，让它计算出各种派生信息。

这些派生信息会在不同的地方用到，因此这些代码也会在不同的地方重复。

我更愿意把所有计算逻辑收拢到一处，这样始终可以在固定的地方找到和更新它们，也可以一定程度上避免漏改。

```
// 聚合前
function base(aReading) {...}
function taxableCharge(aReading) {...}
// 聚合后
function enrichReading(argReading) {
    const aReading = _.cloneDeep(argReading);
    aReading.baseCharge = base(aReading);
 aReading.taxableCharge = taxableCharge(aReading);
 return aReading;
}
```

这一步通常需要对输入的数据做深拷贝，确保变换不会修改原来的数据。

## 拆分阶段

每当看到一段代码在**同时处理两件不同的事**，就要想到把它拆分成**各自独立**的模块。

到了需要修改的时候，就可以单独处理模块，而不必同时在脑子里考虑两个不同的目标。

```
// 拆分前
const orderData = orderString.split(/\s+/);
const productPrice = priceList[orderData[0].split("-")[1]]; const orderPrice = parseInt(orderData[1]) * productPrice;
// 拆分后
const orderRecord = parseOrder(order);
const orderPrice = price(orderRecord, priceList);
function parseOrder(aString) {
  const values = aString.split(/\s+/);
  return ({
   productID: values[0].split("-")[1],
   quantity: parseInt(values[1]),
  });
}
function price(order, priceList) {
  return order.quantity * priceList[order.productID];
}
```

## 更换算法

随着编程的推进，对问题有了更多理解，我们会发现原先的做法不够好了，这时应该将复杂的东西分解为简单的小块，或者删掉重写。

```
// 更换前
function foundPerson(people) {
   for(let i = 0; i < people.length; i++) {
     if (people[i] === "Don") {
       return "Don";
     }
     if (people[i] === "John") {
       return "John";
     }
     if (people[i] === "Kent") {
       return "Kent";
  } 
 }
 return ""; 
}
// 更换后
function foundPerson(people) {
  const candidates = ["Don", "John", "Kent"];
  return people.find(p => candidates.includes(p)) || '';
}
```

我个人经常会做这样的事，将条件判断更换为环境变量配置，或者更换为映射表，就会更清晰，也能减少代码量。

## 管道取代循环

开发中常常需要对列表项进行处理，比如：寻找目标项、过滤目标项、返回新列表等等。

如果这些操作用for循环实现，就要仔细甄别循环内部到底做了什么，结果是什么。

这时候，用管道取代循环就是更好的做法。

```
// 改造前
const names = [];
for (const i of input) {
 if (i.job === "programmer")
 names.push(i.name);
}
// 改造后
const names = input
 .filter(i => i.job === "programmer")
 .map(i => i.name)
```

最常见的管道非map和filter莫属，使用一组运算来描述迭代过程，入参和返回值都是一个集合。

改造后，可读性变得更强了，一眼就知道每一步的意图。

## 分解条件表达式

**多样的业务需求**产生的条件判断是导致复杂度上升的常见因素。

我们需要根据不同的条件做不同的事，然后，很快就会得到一个相当长的函数，常常让人弄不清楚每个分支因何而来，又做了什么。

根据经验，也可以将它分解为多个独立的函数。

```
// 分解前
if (!aDate.isBefore(plan.summerStart) && !aDate.isAfter(plan.summerEnd))
  charge = quantity * plan.summerRate;
else
charge = quantity * plan.regularRate + plan.regularServiceCharge;
// 分解后
if (summer())
  charge = summerCharge();
else
  charge = regularCharge();
```

别小看这样的改变，从运行层面没什么区别，但它能很大程度避免代码杂糅，也更直观地说明做了什么。

## 合并条件表达式

有时我们会发现这样一串条件检查：**条件各不相同， 最终行为却一致**。

如果发现这种情况，就应该使用“逻辑 或”和“逻辑与”将它们**合并为一个条件表达式**。

```
// 改造前
if (anEmployee.seniority < 2) return 0;
if (anEmployee.monthsDisabled > 12) return 0;
if (anEmployee.isPartTime) return 0;

// 改造后
if (isNotEligibleForDisability()) return 0;
function isNotEligibleForDisability() {
  return ((anEmployee.seniority < 2) || (anEmployee.monthsDisabled > 12) || (anEmployee.isPartTime));
}
```

当维护这段代码的时候，就不需要花太多时间去理解每一个判断的含义和职责，因为赋予了它们一个整体的意义。

## 卫语句替代嵌套判断

什么是**卫语句**？如果某个条件极其罕见，就应该单独检查该条件，并在该条件为真时立刻从函数中返回，这样的单独检查被称为“**卫语句**”(guard clauses)。

条件表达式可分为两种，分支都是正常行为，使用 if...else...；只有部分分支正常，其余是异常，就可以用卫语句。

```
// 改造前
function getPayAmount() {
  let result;
  if (isDead)
   result = deadAmount();
  else {
   if (isSeparated)
     result = separatedAmount();
   else {
     if (isRetired)
       result = retiredAmount();
     else
       result = normalPayAmount();
   }
}
  return result;
}
// 改造后
function getPayAmount() {
      if (isDead) return deadAmount();
      if (isSeparated) return separatedAmount();
      if (isRetired) return retiredAmount();
      return normalPayAmount();
}
```

## 函数取代参数

**不要用参数的类型来区分函数要做什么。**

这看似有反常理，参数不就是用来做这个的？

参数的使用可分成两种：一是决定输出结果的值，二是决定改变谁。

用函数取代参数，就是第二种情况：**决定改变谁**。

```
// 改造前
function setDimension(name, value) {
      if (name === "height") {
       this._height = value;
       return; 
      }
      if (name === "width") {
       this._width = value;
       return;
      } 
}
// 改造后
function setHeight(value) {this._height = value;}
function setWidth (value) {this._width = value;}
```

作者认为，看到函数就要知道它的用途，而不必去通过分析不同的参数来识别它什么时候做了什么。特别是当参数是布尔类型时尤为糟糕，你不知道true或者false到底意味着什么。

## 小结

本文介绍了共11种改善代码问题的方法和技巧，也是本系列的完结篇。

它们不是全部，但聪明如你，应该能看出这些方法存在共性，就是“**让代码更好读，更好用**”，所以，你能通过它们得到一些思考和启发，来改善自己项目中遇到的更多问题，才是最棒的。

下一本，你想看什么，可以在评论区告诉我。

欢迎关注公众号：**前端说书匠**。好文第一时间接收不迷路！~