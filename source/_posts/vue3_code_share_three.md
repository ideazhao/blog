title: Vue.js技术内幕：数据响应式之2.x版  
date: 2025-01-18
tags: [书籍]
categories: [说书匠]
toc: true
---

> 每个系列一本前端好书，帮你轻松学重点。
>
> 本系列来自ZOOM前端架构师，前百度、滴滴资深技术专家**黄轶**所编写的 **《Vue.js技术内幕》**

上一篇我们聊了组件渲染，你好奇的第二个问题，一定是**响应**。即“数据驱动”。

数据驱动的本质是数据变化引起页面变化，让开发者只关注数据操作。

响应式的处理，需要应对多种场景，而且其中带有不易察觉的“巧思”，所以，本文只聊一个话题，就是Vue 2.x的响应式实现。

## 核心与框架

大家对于Vue,js 2.x使用的API和流程应该耳熟能详。API 就是 Object.defineProperty，流程则是：**在数据被访问时收集依赖，数据被修改时更新依赖**。

图示如下：

![image.png](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/cb82d46e371f4314a0d8b2eb6541ac4f~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg54G15oSfX19pZGVh:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiNjQxNzcwNTIxMzY4NjA2In0%3D&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1746238866&x-orig-sign=KHkL0aGmF%2BeaTq6u7ZiX2eO3jUE%3D)

具体过程，以一个简单的示例来看：

    <template>
      <p>{{ msg }}</p>
      <button @click="random">Random msg</button>
    </template>
    <<script>
    export default {
      data(){
        return {
          msg:"msg reactive"
        }
      },
      methods: {
        random(){
          this.msg = Math.random()
        }
      },
    }
    </script>

不得不说，Vue 2.x的流行是有原因的，笔者已经写了几年的Vue3 和 React，敲这段代码依然非常自然，符合直觉。

开发者只需要把变量定义在data中，改变变量的方法定义在methods中，然后在模板中绑定这个变量，页面就会渲染定义好的变量，点击按钮，执行函数，就能改变页面的渲染。

代码和表现都很简单，但不论是简单还是复杂，都会经过同一流程。

## 处理流程

流程分为三步：**数据劫持、依赖收集、派发更新**。

说法比较抽象，直接看代码：

    // 核心：定义对象的响应式属性
    function defineReactive(obj: any, key: string) {
      let value = obj[key];
      let dep = new Dep();

      // 深度代理
      let childOb = observe(value);
      Object.defineProperty(obj, key, {
        get() {
          // 依赖收集 dep.depend()
          if (Dep.target) {
            dep.depend();
            if (childOb) {
        // value 是对象或者数组，childOb 才会有值
        // 这里是针对数组的依赖收集
              childOb.dep.depend();
            }
          }
          return value;
        },
        set(newVal) {
      if (!hasChanged(value, newVal)) {
             return;
           }
           // 新值进行响应式
           observe(newVal);
           value = newVal;
           // 通知更新
           dep.notify();
        },
      });
    }

这个方法里，我们见到了两个关键角色：**dep、observe**。

还看到 observe(value)、Dep.target，dep.depend()，dep.notify()，你可能已经懵了，这些代码在干什么？

## 底层机制

搞懂这些操作，需要先做两件事：

1、理解底层机制

2、理顺机制与代码结合的过程

响应式机制使用了“观察者”模式，它是设计模式的一种。

> 设计模式，是由前人所总结的，用来解决某类问题的方案。可以理解为数学或者物理中的公式，同类问题均能套用，此谓“模式”。

在这里，它解决的问题是**哪些数据要响应，数据变化之后哪些地方要跟着变**。

所以，需要有一个角色对数据做**观察（Watcher）** ，还需要有一个角色来收集对数据有**依赖的集合（subs）** ，然后在变化发生的时候**通知更新（notify），** 这些事情都是在dep中完成的。

说到这，就明了。

在拿到数据之后，首先创建了一个Dep实例dep。

    let dep = new Dep();

    export default class Dep {
      // 静态变量，保存 Watcher 类型对象
      static target: ?Watcher;
      subs: Array<Watcher>;     // 订阅者数组 元素即 Watcher对象
      constructor () {
        this.subs = []
      }
      // 添加订阅者
      addSub (sub: Watcher) {
        this.subs.push(sub)
      }
      // 依赖收集
      depend () {
        if (Dep.target) {
          Dep.target.addDep(this)
        }
      }
      // 通知订阅者 更新事件
      notify () {
        for (let i = 0, l = subs.length; i < l; i++) {
          subs[i].update()
        }
      }
    }

数据就是data，Dep.target是Watcher，dep.depend()就是在收集依赖，我们结合三段代码来看。

它们分别来自：defineReactive、Dep、Watcher。

    // defineReactive中
    if (Dep.target) {
      dep.depend();
    }

    // Dep中
    // 添加订阅者
    addSub (sub: Watcher) {
      this.subs.push(sub)
    }
    // 依赖收集
    depend () {
      if (Dep.target) {
        Dep.target.addDep(this)
      }
    }
    // 通知订阅者 更新事件
    notify () {
      for (let i = 0, l = subs.length; i < l; i++) {
        subs[i].update()
      }
    }

    // Watcher中
    class Watcher {
      addDep(dep) {
        dep.addSub(this);
      }
      update() {
        const oldValue = this.value;
        this.value = this.get(); // 重新获取
        this.callback.call(this.vm, this.value, oldValue); // 触发回调
      }
    }

从上到下，分别调用了：dep.depend()、Watcher.addDep(this)、dep.addSub(this);

玄机就在理解这两个this，第一个是dep，第二个是watcher

这个流程下来做了三件事：

1、创建dep实例

2、Watcher掉用自身的addDep方法，把dep实例加进去

3、dep实例调用自身的addSub方法，把watcher作为订阅者加入到订阅者列表中

总结：**把跟当前数据相关的观察者存储到了所创建的dep实例的subs数组当中**，这就是收集依赖。

这个流程清楚了，更新就好说，需要更新的时候，调用观察者的update()方法，想想观察者放哪儿去了？dep的subs里，那么执行下面的代码就可以了。

    // defineReactive中
    // 通知更新 dep.notify()
    dep.notify();

    // Dep中
    // 通知订阅者 更新事件
    notify () {
      for (let i = 0, l = subs.length; i < l; i++) {
        subs[i].update()
      }
    }

    // Watcher中
    update() {
      const oldValue = this.value;
      this.value = this.get(); // 重新获取
      this.callback.call(this.vm, this.value, oldValue); // 触发回调
    }

一句话总结，找到跟当前数据相关的watcher，执行update。

## observe

到这里我们刻意忽略了一个角色——observe，我们在defineReactive方法中见过它。

    function defineReactive(obj: any, key: string) {
      let value = obj[key];

      // 深度代理
      let childOb = observe(value);
    }

它起到什么作用呢？可以看出，从data中取值之后，它可以对值进行更深层次的代理，所以其实它才是响应式数据开始处理的入口，而不是defineReactive。数据先经过observe，做完了对数据类型的判断，才进入defineReactive，

看下面两段代码，就都串起来了。

    // 数据响应式的入口函数
    export function observe(value: any) {
      if (isPlainObject(value) || isArray(value)) {
        // 当值为对象或数组时，进行响应式处理
        return new Observer(value);
      }
    }

    class Observer {
      constructor(value: any) {
        if (isArray(value)) {
          // 数组，需要特殊处理，进行劫持数组方法
          (value as any).__proto__ = arrayMethods;
          this.observeArray(value);
        } else {
          // 对象
          const keys = Object.keys(value);
          for (let i = 0; i < keys.length; i++) {
            const key = keys[i];
            defineReactive(value, key);
          }
        }
      }

      /**
       * 将数组的每一项进行响应式处理
       * @param value
       */
      observeArray(value: any[]) {
        for (let i = 0, l = value.length; i < l; i++) {
          observe(value[i]);
        }
      }
    }

至此，Vue.js 2.x的响应式机制梳理完成。

## 小结

响应式的过程，说复杂也复杂，需要一步步精妙的设计及考虑不同细节，才能囊括日常开发的不同情况，但说简单也简单。

我们从头再做一次梳理：

1、observe接收data，判断data中的变量是一般的值，还是普通对象，还是数组；

2、如果是普通对象，对其进行劫持，如果是数组，则进入数组的处理流程；

3、响应式机制应用了“观察者模式”，需要观察者角色，负责收集观察者的角色，及发生变化时通知观察者更新的动作；

4、收集依赖和执行更新的过程使用了Object.defineProperty API中的getter和setter特性；

5、收集依赖在getter中进行，Dep负责收集Watcher；

6、更新动作在setter中进行，Dep负责通知Watcher更新。

OK，下篇文，我们聊Vue.js 3.x的实现过程。

更多好文第一时间接收，可关注公众号：**前端说书匠**