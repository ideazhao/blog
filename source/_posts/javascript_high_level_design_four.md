title: JavaScript 高级程序设计(第 5 版)：好的编程就是掌控感
date: 2025-07-27
tags: [书籍]
categories: [说书匠]
toc: true

---

> 每个系列一本前端好书，帮你轻松学重点。
>
> 本系列来自曾供职于 Google 的知名前端技术专家**马特·弗里斯比**编写的 **《JavaScript 高级程序设计》(第 5 版)**

程序员最讨厌的，是看一段晦涩难懂的代码。

读懂它所花的时间可能远大于应有的开发量。

我们期望，看到勺子就用来舀汤，看到铲子就用来炒菜。一切都**符合直觉，** 这就是一种“掌控感”。

这种掌控，需要对编程的各种方式足够熟悉。

前面的文章，介绍了 JavaScript 的变量，以及多个变量组成的集合。帮大家建立了对数据的认知，但好像还没有真正开始编程。

## 怎么开始

程序由什么组成，入手点是什么？

相比繁多的语法，这才是编程真正的门槛。

比如：

```javascript
let a = 1,
  b = 2;
a + b; // 3
```

没人能从这段代码中理解编程，因为不知道 a、b 代表什么，能干什么，它只有纯粹的数学意义。

但如果说，清华小学，一年级 500 人，二年级 300 人，问两个年级加起来多少人？

```javascript
let gradeOneStudentCount = 500，
gradeTwoStudentCount = 300；
gradeOneStudentCount + gradeTwoStudentCount  // 800
```

现在你知道，它可以用于计算一所学校两个年级的总人数，**代码被赋予了实际意义**。

程序就是对现实世界的映射，你只有在实现需求，解决问题时，才能体会程序是怎样把数据连接起来的（由此可见，语义化的命名也是多么重要）。

## 流程与逻辑

真正的程序当然不止加减法这么简单，但也没那么复杂。

设想一下，如果不是计算一、二年级的人数，而是二十年级，能否得到？

答案是显然的，不存在二十年级。

程序拿到“20”这个数值进行判断和查找，就称为“**流程**”。

“存不存在，合不合理，允不允许”，就称为“**逻辑**”。

JavaScript 中提供了多种方法进行流程控制和逻辑判断。

**if...else**

```javascript
let gradeLevel;
if (gradeLevel >= 1 && gradeLevel <= 9) {
  // 合理
  if (gradeLevel <= 6) {
    // 小学生
  } else if (gradeLevel > 6 && gradeLevel < 9) {
    // 初中非毕业班
  }
}
```

这是最常见的一种判断，叫“如果...否则”。

虽然有时你会见到有非常多的 if...else，被人诟病不优雅、维护性差，但在多数时候，它仍是调度大段代码结构最清晰的表达方式。

需要注意的一点，else 是排除了 if 之后的所有情况，可能因为逻辑疏漏造成误伤，产生 bug。

**switch**

```javascript
switch (expression) {
  case value1:
    statement;
    break;
  case value2:
    statement;
    break;
  default:
    statement;
}
```

switch 是除了 if 之外最常见的分支处理方式，它适合多个"同类不同值"的情况，与其使用多个 if(expression == "xx")，不如使用 switch 来得干练。

不过，同样需要注意，不要漏了 break 和 default。

漏了 break，即便值已被命中，仍会接着往下走，发生误命中；漏了 default，当出现特殊情况，前面都没有命中时，没有一个可以兜底的值。

**do...while**

```javascript
do {
  statement;
} while (expression);

let i = 0;
while ((i = 10)) {
  i += 2;
}
```

do while 和 while 看起来像循环，不满足条件就一直执行，到满足为止。

这种在项目中使用较少，较常见于算法实现，如：排序、二叉树查找之类。

以上三种，属于较“重”的逻辑工具，通常用来调度主干逻辑，还有三种更“轻”且好用的控制方式，推荐大家熟练掌握。

**条件操作符**

根据前置表达式的 true 或者 false 决定取值。

```javascript
variable = boolean_expression ? true_value : false_value;
let canRead = age > 6 ? "go to school" : "can't go to school";
```

**短路操作符**

分两种：

&&（与）只有 value 为 true 才执行后续表达式。

||（或）valueA 为 true 就取 valueA，否则取 valueB。

当然，这里说的 true 或者 false 不单指它的值，类型转换的结果有同等效力，像 “a”、1 都会被转为 true，还可以是一个比较表达式。

代码如下：

```javascript
variable = value && expression;
let canRead = age > 6 && "go to school";

variable = valueA || valueB;
let canRead = age > 6 || "can't go to school";
```

**空值合并操作符**

第一个“??”，作用类似于逻辑或（||），但要求更严格，逻辑或（||）会将 0 与 "" 视为 false，而空值合并，当且仅当表达式的值为 null 或者 undefined，才取后面的值。

```javascript
variable = expression ?? other_value;
const displayName = user.name ?? "匿名用户";
```

第二个可选链 ?. 作用类似逻辑与（&&） ，尤其是在属性访问有多层嵌套时。

```javascript
variable = res?.data?.status`
// 以及与 `??` 更好的协作
const foo = a?.b?.c?.d ?? 'default';
```

上述这三类表达式，其实用 if 都能实现，但它们会让代码更简洁。

## 异步

除了条件判断的方式，代码里还有一种手段是控制执行时机。

多数情况下，代码编写的顺序就是执行顺序，这种机制称为“同步”。

异步的意思是：“**暂时没有完成，但不影响其他事情的进行**”。

最形象的类比是点餐，你的点单动作就是发起一条异步指令，点完单你可以去做别的，餐品准备好了再食用。

**定时器**

```javascript
// 一次
setTimeout(()=>{
	// 执行这里的代码
}，1000)

// 多次
setInterval(()=>{
	// 执行这里代码
}，1000)
```

setTimeout 用于让代码过一段时间后执行，其中的时间单位为 ms，比如：1000，就是 1s。

setInterval 是每隔一段时间执行一次，会一直持续下去，直到定时器关闭，时间单位也是 ms。

这两种定时器曾经在脚本动画方面担当重要角色，但也有其不足，如：时长不够精准、不能跟浏览器任务调度完美契合等，于是有了如下这个角色。

**requestAnimationFrame**

requestAnimationFrame 的使用很简单，就像函数调用一样，把你要执行的代码传进去，不断地调用，就会不断地执行。

```javascript
let animationId;
function animate() {
  // 动画执行
  console.log("Animating...");
  // 触发下一帧
  requestAnimationFrame(animate);
}
// 开始动画
animationId = requestAnimationFrame(animate);
// 3s 后停止
setTimeout(() => {
  cancelAnimationFrame(animationId);
  console.log("Animation stopped!");
}, 3000);
```

这个 api 有什么特别之处？

需要额外提个概念—**屏幕刷新率**，直观感受就是我们浏览网页、玩游戏时，刷新率越高，体验越流畅。

requestAnimationFrame 就是浏览器提供的，用于匹配屏幕刷新率的 api，它“知道”浏览器在什么时候适合进行下一次渲染，这样就能跟浏览器引擎的工作节奏完美契合，同时避免 CPU/GPU 的资源浪费。

不论定时器，还是 requestAnimationFrame，都像是与浏览器进行某种约定，等到约定的时机执行相应的动作。

本书中，有一个更新也更强大的异步机制就被译为了“**期约**”，它就是重磅选手—Promise。

**Promise**

这个翻译曾引起不小的争议，因为这个词不常用，读着也不顺口，但含义上又是恰当的。

Promise 的意思是，发起一个指令，指令多久能完成不确定，是成功还是失败也不确定，但终归会有个结果，有结果了会通知“你”，就相当于程序中做了个约定。

```javascript
// 简单创建Promise
const myPromise = new Promise((resolve, reject) => {
  // 异步操作
});

// 使用 Promise
myPromise
  .then((result) => {
    console.log(result); // 成功时执行
  })
  .catch((error) => {
    console.error(error); // 失败时执行
  });
```

基本的 promise 就这么简单，发起任务，然后等待“成功”或“失败”的结果。

但需要说明，promise 不单是一个普通的 api，更是一种事务处理方式，所以，用到 promise 的地方并不一定直接创建 promise，通常会说：“异步执行”或者“返回一个 promise”。

比如，用于发起网络请求的 fetch，剪贴板操作 clipboard、音视频操作 WebRTC 等，它们的使用方式像这样：

```javascript
fetch("https://api.example.com/data")
  .then((response) => response.json())
  .then((data) => console.log(data))
  .catch((error) => console.error("Error:", error));
```

有 then、catch，但没有 promise，因为 promise 被封装到了内部。

promise 方案之所以能够获得认可，被广泛采用，因为它是更为现代、设计更加合理的异步机制，能够避免因多重依赖形成的“回调地狱”等问题，还有其他特性，后续会单开一个话题分享。

## 编程范式

最后聊聊“范式”，什么是范式？

你一定听过“面向对象、面向过程、函数式”等。

范式，可以认为是风格，或者一种解决问题的方式，就像出行的时候可以选择步行、骑车、地铁。

下面就给大家展示，用三种方式，实现同一种需求的表现。

**面向对象**

以对象为主，通过将数据（属性）和行为（方法）封装在对象中，模拟现实世界的事物和行为。

核心思想是抽象、封装、继承和多态。

```javascript
class EvenSquareCalculator {
  constructor(arr) {
    this.numbers = arr;
  }
  isEven(num) {
    return num % 2 === 0;
  }
  sum() {
    return this.numbers
      .filter((num) => this.isEven(num)) // 过滤偶数
      .map((num) => num ** 2) // 计算平方
      .reduce((sum, x) => sum + x, 0); // 累加
  }
}
const calculator = new EvenSquareCalculator([1, 2, 3, 4, 5]);
console.log(calculator.sum()); // 输出: 20
```

**面向过程**

核心思想是通过步骤化分解问题，将程序转化为一系列线性执行的指令。

```javascript
function sumOfEvenSquares_procedural(arr) {
  let sum = 0;
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] % 2 === 0) {
      // 检查偶数
      sum += arr[i] * arr[i]; // 累加平方
    }
  }
  return sum;
}

const array = [1, 2, 3, 4, 5];
console.log(sumOfEvenSquares_procedural(array)); // 输出: 20 (2² + 4²)
```

每个人的编程都是从“面向过程”开始的，因为它简单直接。

弊端就是逻辑分散，复用性差，随着代码量的增加，维护难度上升。

**函数式**

以函数为主，强调无副作用、不可变数据和声明式代码。

核心思想是用表达式（而非语句）描述计算逻辑。

```javascript
const sumOfEvenSquares_functional = (arr) =>
  arr
    .filter((x) => x % 2 === 0) // 过滤偶数
    .map((x) => x * x) // 计算平方
    .reduce((sum, x) => sum + x, 0); // 累加

console.log(sumOfEvenSquares_functional([1, 2, 3, 4, 5])); // 输出: 20
```

这里呈现的函数，是数组中自带的原生方法，也可以是自定义函数，关于函数，后面会有专门一篇文介绍。

**如何选择？**

这么多方案，怎么选，哪种最佳？

人们常沉迷于寻找“最佳”，实际上，不论是编程，还是现实，都不是非此即彼的淘汰赛，而是彼此互补的团队赛。

先坐地铁，再骑车，再步行，可以吗？很常见。

成熟的工匠，会去挖掘每种招数的长处，然后用在适合的地方。

## 小结

从你敲下第一个字母，定义第一个变量开始，就已经在为向计算机发出指令做准备。

随着代码量的增多，你是否始终能够很好地理解和掌控代码的走向，不论对于你，还是他人，都非常重要。

如果不想你的代码背上越来越多的技术债，学习和掌握各种编程方法就是必要的。

除了本文讨论的内容，让你的代码变得更好的手段还有“算法”和“设计模式”，我们当然会涉及这些内容，但那是后面的事了。

欢迎持续关注，下篇再会。
