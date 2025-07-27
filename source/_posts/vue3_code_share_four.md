title: Vuejs技术内幕：数据响应式之3.x版
date: 2025-03-09
tags: [书籍]
categories: [说书匠]
toc: true
---

> 每个系列一本前端好书，帮你轻松学重点。
>
> 本系列来自ZOOM前端架构师，前百度、滴滴资深技术专家**黄轶**所编写的 **《Vue.js技术内幕》**

Vuejs的响应式是其能够得以流行的核心之一，上一篇我们介绍了2.x版本的实现原理，本篇看3.x。

在Vuejs 3.x中，响应式设计的变化可谓十分明显，说是换了个框架都不为过。有专门的API，可按需调用，代码组织也做了调整，不再跟框架紧密绑定，而是可以在项目中单独使用。

就像这样：

```
// 安装
npm install @vue/reactivity

// 导入使用
import { reactive, ref } from '@vue/reactivity';
 
const state = reactive({ count: 0 });
const count = ref(0);
```

这极大地提升了Vue相关工具使用的灵活性。

通过这段代码，已经看到了Vue.js 3.x中响应式API，就是 **reactive** 和 **ref**，本篇围绕 **reactive** 展开。

## reactive API

Vue.js 3.x中，组件书写的模式变为了Compostion API，上篇文的实例代码可改写为：

```
<template>
  <div>
   <p>{{ state.msg }}</p>
   <button @click="random">Random msg</button>
  </div>
</template>

<script>
  import { reactive } from 'vue'
  export default {
    setup(){
      const state = reactive({
        msg:"msg reactive"
      })
      const random = function(){
        state.msg = Math.random()
      }
      return {
        random,
        state
      }
    }
  }
</script>
```

可以看到，Vue.js 3.x的响应数据不是一股脑地塞进data对象中，而是使用reactive按需定义。

那么对响应式的研究，就转变为对API的拆解：

```
function createReactiveObject(target,false,mutableHandlers,mutableCollectionHandlers,proxyMap){
  
  // 如果不是对象类型，则直接返回
  if(!isObject(target)){
    return target
  }

  // target已经是proxy类型的对象，直接返回
  if(target.__v_.raw && !(isReadOnly && target.__v_.isReactive)){
    return target
  }

  // 使用Proxy创建响应式对象
  const proxy = new Proxy(targetType === 2 ? mutableCollectionHandlers : baseHandler);
  
  // 缓存已代理的对象
  proxyMap.set(target,proxy)
  return proxy

}
```

以上代码的关键自然是 **new Proxy**的调用，在调用之前做了一些判断，避免规则之外的行为或者做重复的工作：

-   入参是否为对象或者数组
-   入参是否已经被处理为响应式
-   入参是否为应该处理的类型

你肯定好奇，targetType === 2 代表什么，这个东西隐藏在getTargetType方法中，这个方法中Object、Array等返回1，Map、Set等返回2，否则返回0，如Date、Promise等类型。

## 响应式三要素

知道了怎么走到Proxy，就可以进行更深层次的探究，探究之前，先对全局有个把握。

本质上，它还是在劫持和处理数据，所以，依标准三件套：**依赖收集、派发更新、副作用函数**。

先看看处理器对象，它来告诉proxy拦截哪些行为，主要是 mutableCollectionHandlers 的部分。

```
const mutableCollectionHandlers = {
  get,
  set,
  deleteProperty,
  has,
  ownkeys
}

// 就是做这个用的
new Proxy(mutableCollectionHandlers);
```

这个对象代表拦截了哪些操作，显然包括了get、set和delete。

接下来就分析常用的get、set函数的实现。

#### 依赖收集

依赖收集发生在数据访问阶段，即get，看看get中做了哪些处理。

```
function createGetter(isReadonly = false){
  return function get(target,key,receiver){
    if(key === '__v_isReactive'){
      return !isReadonly
    }
    // 求值
    const res = Reflect.get(target,key,receiver)
    // 依赖收集
    if(!isReadonly){
      track(target,'get',key)
    }
    return res
  }
}
```

代码中真正起作用的，是track方法。

需要说明的一点是，在Vue2.x中，响应式数据如果仍是对象，会递归执行，但Vue3.x中会在访问数据时才决定要不要执行，这会带来一定性能上的提升。

那么，track方法做了什么：

```
// 当前激活的activeEffect
let activeEffect
// 原始数据对象的map
const targetMap = new WeakMap()
function track(target, type, key){

  let depsMap = targetMap.get(target)
  // 每个target对应一个depsMap 
  if(!depsMap){
    targetMap.set(target,(depsMap = new Map()))
  }

  let dep = depsMap.get(key)
  if(!dep){
    // 每个key对应一个dep集合
    depsMap.set(key,(dep = new Set()))
  }
  if(!dep.has(activeEffect)){
    // 收集当前激活的activeEffect作为依赖
    dep.add(activeEffect)
    // 当前激活的effect收集dep集合作为依赖
    activeEffect.deps.push(dep)
  }

}
```

track接收三个参数：target（原始数据）、type（依赖类型）、key（访问的属性）

-   targetMap，用于存储原始数据（target）
-   depsMap，用于存储依赖（dep）
-   dep，用于存储副作用函数（effect）

副作用函数，就是**每次数据发生变化时所会执行的函数**，所谓“副作用”，就是**使某些跟响应数据关联的状态发生变化**。

因此，每次执行track函数，都会把当前激活的activeEffect作为依赖，收集到与target相关的dep中，记住这一点很重要。

收集完依赖，下一步，就是在值发生改变的时候派发通知进行更新。

#### 派发通知

派发通知发生在数据更新阶段，通过执行set来实现。

```
function createSetter(){
  return function set(target,key,value,receiver){
    let oldValue = toRaw(target[key])
    const hadKey = isArray(target) && isIntergerKey(key)? Number(key) < target.length : hasOwn(target,key)
    // 设置值
    const result = Reflect.set(target,key,value,receiver){
      if(target === toRaw(receiver)){
        if(!hadKey){
          trigger(target,'add',key,value)
        } else if(HashChanged(value,oldValue)){
          trigger(target,'set',key,value,oldValue)
        }
      }
    return result
  }
}
```

set函数主要做了两件事：通过Reflect.set实际设置属性值，并确保操作符合规范，然后通过trigger函数派发通知。

最核心的部分是trigger，我们进一步看看这个函数：

```
const targetMap = new WeakMap()
function trigger(target,type,key){
  const depsMap = targetMap(target)
  if(!depsMap){
    return
  }
  const effects = new Set()
  const add = (effectsToAdd) => {
    if(effectsToAdd){
      effectsToAdd.forEach( effect => {
         effects.add(effect)
      });
    }
    // SET/ADD/DELETE操作之一，添加对应effects
    if(key!== void(0)){
      add(depsMap.get(key))
    }
    const run = (effect) => {
      if(effect.options.scheduler){
        effect.options.scheduler(effect)
      } else {
        effect()
      }
    }
    effects.forEach(run)
  }
}
```

trigger函数主要做了四件事：

1、从targetMap中获取target对应的依赖集合depsMap

2、创建effects集合

3、根据key将depsMap中的effects添加到集合中

4、遍历effects，执行副作用函数

接下来，整个流程只剩下揭开**副作用函数**的庐山真面目。

#### 副作用函数

介绍副作用之前，先清楚它是怎么工作的。

通过一个简单的示例了解：

```
const counter = reactive({
  num:0
})
function logCount(){
  console.log(counter.num)
}
function count(){
  counter.num++
}
logCount()
count()
```

这个示例中，先通过logCount的执行访问了counter.num，然后，希望在执行count函数时对num修改，同时自动执行logCount函数。

怎样实现这样的效果呢？秘诀就在之前看到过的activeEffect，它是一个来存储当前执行函数的全局变量，当get时，把这个函数设置为activeEffect，当set时，再触发这个activeEffect，就可以了。

实现原理如下：

```
let activeEffect;
function wrapper(fn){
  const wrapped = function(...args){
    activeEffect = fn
    fn(...args)
  }
  return wrapped
}
const wrappedLog = wrapper(logCount)
wrappedLog()
```

Vue源码的核心实现如下：

```
const effectStack = []
function createReactiveEffect(fn,options){
  const effect = function reactivedEffect(){
    try{
      // 入栈
      effectStack.push(effect)
      activeEffect = effect
      // 执行原始函数
      return fn()
    } finally {
      // 出栈
      effectStack.pop()
      // 指向栈的最后一个effect
      activeEffect = effectStack[effectStack.length-1]
      effect.options = options
      return effect
    }
  }
}
```

这段代码中，当执行trigger时，真正执行的effect就是reactivedEffect。

按照前面所说，它只做两件事，让全局的activeEffect指向它，执行被包装的原始函数fn。

除此之外，有个额外的东西是effectStack，干嘛的？

这是因为，需要考虑到函数的执行是会出现嵌套的，如果仅仅是赋值，activeEffect可能出现错误，使用栈结构之后，每次执行函数入栈，执行完出栈，就不会出现这种错误。

## 小结

至此，Vuejs 3.x响应式实现流程介绍完毕。

我们会发现，它的机制和Vuejs 2.x是类似的，但实现上的差异不小，并不单单是用Proxy代替了Object.defineProperty，而是改变了实现的逻辑：

-   使用惰性响应式，减少了初始化开销。
-   对嵌套对象的处理更加高效。
-   对数组的支持更高效。
-   使用Map存储依赖关系，结构更高效。

这几块内容的解读，不单是让大家能弄懂Vue，更重要的是对框架设计消除陌生感，对知识点本身，对代码实践，有更好的认识，体会相对复杂项目应该是什么样子。

下一篇，我们将展示算法的魅力。

更多好文第一时间接收，可关注公众号：**前端说书匠**
