title: 防抖、节流及应用    
date: 2020-05-15
tags: [JavaScript,学习]
categories: [总结]
toc: true
---

“防抖”和“节流”是很多公司的面试常见问题，有些人不知道是什么意思，有些知道意思，却难以分清两者的区别，还有一些人，明白二者区别，但没有遇到过合适的应用场景，以至于无法真正理解它们，本文就试图把这些问题逐个击破。

## 防抖

顾名思义，防止抖动，什么叫抖动，事件被高频触发，想象你在用鼠标点击按钮操纵一个小人让他有跳跃的动作，你狂点鼠标，小人狂跳，这就是最直观的抖动现象。

现在我们需要防止抖动，怎么做呢？当我们快速点击鼠标的时候，仅有一次动作是生效的，就可以解决这个问题。

怎么做呢？这就取决于鼠标点击的有多快，也就是我们设定一个时间，当两次点击的时间间隔小于那个时间，就只有最后一次点击才会生效。

这也就可以引出“防抖”的定义：**任务频繁触发的情况下，只有触发的间隔超过指定间隔，任务才执行**。

用代码来表现就是：

```js
function debounce(fn,delay) {
    let timer;
    return function(){
      let args = arguments;
      if(timer){
        clearTimeout(timer);
      }
      timer = setTimeout(()=>{
        fn.call(this,args);
      },delay)
    }
}
```

使用的话可以像下面这样：

```html
<div>
    <p class="text">1</p>
    <button class="btn">点击加一</button>
</div>
```

```js
let btn = document.querySelector(".btn");
let txt = document.querySelector(".text");

btn.addEventListener("click",debounce(()=>{
  console.log("执行一次");
  txt.textContent++;
},1000))
```

大家试着在浏览器里运行这段代码，就会发现，只要我们两次点击的间隔小于一秒，那么多出来的点击就是无效的，只会最后一次生效，也就是说，如果慢慢地点击



其中的奥秘就在于，每次点击时，只要timer还在，就清空定时器重新计时。

## 节流

可以这么比喻，节流就像排队，比如我们在车站候车，当听到检票进站的通知，不论你有多紧急，跑得多快，还是得跟着排队，一个个地过闸机口，你的通过速度不可能比闸机口开关的速度更快了。

引出定义：**指定时间间隔只执行一次任务**。

到这里你可能会困惑，防抖也有时间间隔，节流还是时间间隔，它俩啥区别？

好的，到这里，你遇到了区分它们的唯一一个难点。

防抖：超过指定时间间隔，不论点击多少次都只执行一次。
节流：指定时间间隔只执行一次，执行频率不受操作频率影响，但会把所有的都执行完。

所以我们继续看代码：

```js
function throttle(fn,delay){
  let flag = true;
  return function(){
    if(flag){
      flag =false;
    }
    timer = setTimeout(()=>{
      fn();
      flag = true;
    },delay)
  }
}
```
