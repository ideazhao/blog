title: Vue.js技术内幕：组件渲染  
date: 2024-12-16
tags: [书籍]
categories: [说书匠]
toc: true
---

> 每个系列一本前端好书，帮你轻松学重点。
>
> 本系列来自ZOOM前端架构师，前百度、滴滴资深技术专家**黄轶**所编写的 **《Vue.js技术内幕》**

随着前端框架流行，组件化开发几乎成标配。

其一，它是更清晰的代码组织方式；

其二，有很多适配框架的组件库诞生，提供了极大的便利，甚至一些非专业人员都能轻松构建出体验尚可的网页，大大降低了门槛，提升了效率。

对于框架，你好奇的第一个问题一定是**渲染**，因为我们写的并不是常规意义上的HTML，而且没有手动操作DOM，**从模板到页面到底发生了什么？**

（友情提示：本文较长，时间有限的朋友在看完“为什么设计vnode”后可先跳到最后阅读“小结”）

## template

在Vue中，组件通常以这样的形式呈现：

```
<template>
  <h1 class="title">{{ msg }}</h1>
</template>
```

这个组件要渲染到网页上，需要经历三个过程：**创建vnode—渲染vnode—生成DOM**。

## vnode

`vnode就是用来描述DOM的JavaScript对象，可以用来描述不同类型的节点，如：普通元素节点、组件节点。

像h1这种原生HTML元素，会被vnode描述为：

```
const vnode = {
  type:"h1",
  props:{
    "class":"title",
    style:{
      width:"100px",
      height:"50px"
    }
  },
  children:"我是标题"
}
```

开发者自定义的组件节点：

```
<custom-component msg="test"></custom-component>
```

则被vnode描述为：

```
const CustomComponent = {
  // 组件对象
}
const vnode = {
  type:CustomComponent,
  props:{
    msg:"test"
  }
}
```

除了这两种，还有纯文本vnode，注释vnode等，Vue.js 3.x内部针对vnode的type做了详尽的分类，以便在挂载阶段做相应处理。

#### 为什么设计vnode

既然不直接操作DOM，就要有东西可以描述DOM形成之前的数据，引入vnode，可以把渲染过程抽象化，抽象化意味着可控性和通用性，为实现跨平台打下了基础，比如：服务端渲染、小程序平台渲染等。

此处有大家关心的两个问题：

1、**用了vnode是否一定不再操作DOM？**

2、**vnode的性能是否一定比操作DOM好？**

答案是，不一定。

vnode既然是一个中间产物，就必然付出额外的代价来创建和分析，特别对于大组件的创建和更新，耗时仍会较长，页面会卡顿，虽然框架的diff算法足够优秀，仍免不了操作DOM，所以**这不是vnode的绝对优势所在**。

## createApp

到这里本该聊创建vnode，但上来就介绍冗长的方法不易于接受，还是先从熟悉的东西入手。

```
import { createApp } from 'vue'
import App from './App.vue'

const app = createApp(App)
app.mount('#app')
```

这样一段代码我们都很熟悉，一切都从第三、四行开始。

createApp做了什么？

```
const createApp = ((...args)=>{
  // 创建App对象
  const app = ensureRenderer().createApp(...args)
  const { mount } = app
  return app
}
```

进入其中，遇到的第一个方法是ensureRenderer。

```
function ensureRenderer() {
  return (
    renderer ||
    (renderer = createRenderer<Node, Element | ShadowRoot>(rendererOptions))
  )
}
```

它做的事就是**创建render，** 创建的方法为createRenderer。

```
export function createRenderer(options) {
  return baseCreateRenderer(options)
}
function baseCreateRender(options){
  function render(vnode,container){
    // 组件渲染核心逻辑
  }
  return {
    render,
    createApp:createAppAPI(render)
  }
}
```

createRenderer返回了一个render（渲染器）和一个createApp方法，这个createApp里面又是什么?

```
function createAppAPI(render){
  return function createApp(rootComponent,rootProps = null){
    const app = {
      _component:rootComponent,
      _props:rootProps,
      mount(rootContainer){
        // 创建根组件的vnode
        const vnode = createVNode(rootComponent,rootProps)
        render(vnode,rootContainer)
        app._container = rootContainer
        return vnode.component.proxy
      }
    }
    return app
  }
}
```

至此，我们终于见到了真正执行的createApp，它正儿八经地返回了一个app，app中包含根组件、根属性，挂载的方法，**在挂载方法中进行了vnode的创建和渲染**。

到这里，createApp的主流程就走完了，同时，自然而然地遇到了下一个话题——createVNode。

## 创建vnode

从上面的代码中，我们看到了这行

```
const vnode = createVNode(rootComponent,rootProps)
```

createVNode方法传入了两个参数：**根组件、根属性**。

其大概实现如下：

```
function createVNode(type,props = null,children=null,patchFlag=0,isBlockNode=false){
  // 判断type是否为空
  // ...
  // 判断type是不是一个vnode
  // ...
  // 对vnode的类型做编码
  const shapeFlag = ...
  return createBaseVnode(type,props = null,children=null,patchFlag=0,shapeFlag,isBlockNode,true)
}
```

这个方法会做很多判断，判断完之后，最终调用createBaseVnode方法进行普通元素的vnode创建流程。

内部代码较多，此处不赘述，但大家肯定想知道后面几个参数的意义。

patchFlag：编译过程中给节点打的特定标记，通过检查patchFlag的值，Vue.js可以快速了解虚拟节点需要进行的具体操作，从而优化更新过程。

isBlockNode：用于判断节点是否是块级节点的函数。块级节点通常是指在DOM中占据新的一行的元素，如div, p等。

假设有这样一个模板：

```
<template>
   <div>
     <p>hello world</p>
     <custom-component></custom-component>
   </div>
</template>
```

它编译后就是这个样子：

```
const _hoisted_1 = _createElementVNode('p',null,"Hello world",-1) 
export function render(){
  return (_openBlock(),_createElementBlock('template',null,[
    _createElementVNode('div',null,[
      _hoisted_1,
      _createVnode(_component_custom_component)
    ])
  ]))
}
```

其中：

_hoisted_1代表一个静态节点；

_createElementVNode是createBaseVnode的别名，用于创建普通元素的vnode；

createVNode创建组件vnode。

代码中通过层层嵌套体现了层级关系，第三个参数是子节点的vnode，div是template的子节点，p是div的子节点。

通过这样的过程，就构成了一棵vnode树，它和DOM树是一一映射的关系。因此，**vnode就是render函数执行的时候创建的**。

那么render函数是怎样执行的呢？

#### 组件挂载

还记得上面执行的createAppAPI吗？createVNode是在mount方法中执行的，所以，要从组件挂载说起。

```
const mountComponent = (initialVnode,container,anchor,parentComponent,parentSuspense)=>{
  // 创建组件实例
  const instance = (initialVnode, component = createCommentInstance(initialVnode,parentComponent,parentSuspense))
  // 设置组件实例
  setupComponent(instance)
  // 设置并运行带副作用的渲染函数
  setupRenderEffect(instance,initialVnode,container,anchor,parentSuspense)
}
```

主要参数的含义：

initialVnode，组件vnode；

container，组件挂载的父节点；

anchor，组件挂载的参考锚点；

parentComponent父组件实例。

它做了三件事：

创建组件实例（instance），设置组件实例，设置并运行带副作用的渲染函数。

这里需要对副作用渲染函数重点关注一下，因为前面的动作都是为了最终执行它，它是渲染的关键。

```
const setupRenderEffect = (instance,initialVnode,container,anchor,parentSuspense)=>{
  const componentUpdateFn = ()=>{
    if(!instance,isMounted){
      // 渲染组件生成子树，vnode
      const subTree = (instance.subTree = renderComponentRoot(instance));
      patch(null,subTree,container,anchor,instance,parentSuspense)
      initialVnode.el = subTree.el;
      instance.isMounted = true
    } else {
      // 更新组件
    }
  }
  const effect = new ReactivedEffect(componentUpdateFn,()=>queueJob(instance.update),instance.scope)
  const update = (instance.update = effect.run.bind(effect))
  update()
}
```

此函数创建了一个副作用实例effect，effect就是当数据发生变化时会重新执行的函数，数据的获取、更新、订阅都跟它有关。

当首次执行instance.update时，会执行componentUpdateFn函数，触发组件的首次渲染。

当数据发生变化时，componentUpdateFn会重新执行一遍，达到重新渲染组件的目的。

这里着重看首次渲染。

#### 首次渲染

主要做两件事情：

1、生成subTree；

2、把subTree挂载到container中。

**生成subTree**

就是setupRenderEffect函数中调用的renderComponentRoot函数。

```
function renderComponentRoot(instance){
  const { vnode,proxy,widthProxy,props,render,renderCache,data,setupState,ctx} = instance
  let result
  const proxyToUse = widthProxy || Proxy
  result = normalizeVnode(render.call(proxyToUse,renderCache,props,setupState,data,ctx))
  return result;
}
```

这个函数，接受了组件挂载方法中创建的组件实例，获取渲染上下文的数据、render函数等，执行render函数生成vnode。

生成子树之后，就要继续调用patch函数把子树vnode挂载到容器container中。

**挂载subTree**

就是setupRenderEffect函数中调用的patch函数。

```
const patch = (n1,n2,container,anchor = null,parentComponent = null,parentSuspense = null)=>{
  const { type,shapeFlag} = n2
  switch(type){
    // 处理各种类型节点元素
    case Text：
 // 处理文本节点
 break
    case Static：
 // 处理静态节点
    break
    default：
  if(shapeFlag & 1){
      // 挂载普通元素
      processElement();
  } else if(shapeFlag & 6){
     // 挂载组件元素
      processComponent();
  } else {
      // 其他情况
  }
  }
}
```

patch的本意是打补丁，这里有两个用途：

1、根据vnode渲染DOM；

2、根据新vnode更新DOM。

n1表示旧vnode，n2表示新vnode，当n1为null时，就是初次创建。

其中挂载分为两种类型的挂载，一是普通元素，一是组件。

**普通元素vnode**

普通元素主要关注mountedElement函数：

```
const mountElement = (vnode,container,anchor,parentComponent,parentSuspense,)=>{
  let el
  const {type,props,shapeFlag} = vnode
  // 创建DOM元素节点
  el = vnode.el = hostCreateElement(vnode,type,isSVG,props);
  if(shapeFlag & 8){
    // 处理子节点vnode是纯文本情况
 hostSetElementText(el,vnode.children);
  } else if(shapeFlag & 16){
    // 处理子节点vnode是数组的情况
  }
  if(props){
    // 处理class、style、事件等
  }
  hostInsert(el,container,anchor)
}
```

mountElement函数做了四件事：创建DOM元素节点，处理children，处理props，挂载DOM到container上。

到这里，就快接近终点了，准备创建元素，这是hostCreateElement函数在web环境下的定义：

```
const svgNS = "http://www.w3.org/2000/svg"
const doc = (typeof document!== 'undefined'?documentent:null)
function createElement = (tag,isSVG,is,props)=>{
  const el = isSVG? doc.createElementNS(svgNS,tag):doc.createElement(tag,is?{is}:undefined)
  if(tag==="select" && props && props.multipel!==null){
    el.setAttribute("multiple",props.multiple)
  }
  return el 
}
```

我们看到了熟悉的createElement，也看到一个新的参数isSVG。

其实这个参数前面就有，为了不加重理解负担，这里才放出来，也只有到这里才真正看到它的用途，因为我们难免会用到svg，而**创建svg和创建其他DOM元素需要用到不同的API**，所以要做区分。

createElement最终还是调用浏览器的DOM API document.createElement来创建DOM元素，因此Vue.js强调不操作DOM，只是希望用户不直接操作DOM，Vue.js本身并没有什么魔法，还是操作DOM。

创建完DOM节点，就要对子节点进行处理，子节点的类型有多种可能，可能是纯文本，也可能是vnode数组。

如果是纯文本，会调用DOM元素的textContent属性设置文本。

如果不是，则会执行mountChildren函数。

```
const mountChildren = (children,container,anchor,parentComponent,parentSuspense,isSVG,slotScopedIds,optimized,start = 0)=>{
  for(let i = start;i<children.length,i++){
    const child = (children[i] = optimized?cloneIfMounted(children[i]:normalizeVNode(children[i])));
    patch(null,child,container,anchor,parentComponent,parentSuspense,isSVG,slotScopedIds,optimized)
  }
}
```

mountChildren 函数会遍历children，递归执行patch函数，挂载每一个child，通过这种方式就可以构造完整的DOM树。

这里需要注意的是使用的patch函数，而不是mountElement函数，因为考虑到子节点可能是组件vnode。如果是，调用的就是processComponent函数。

**组件vnode**

```
const processComponent = (n1,n2,container,anchor,parentComponent,parentSuspense,isSVG,slotScopedIds,optimized)=>{
  if(n1 == null){
    // 挂载组件
  mountComponent(n2,container,anchor,parentComponent,parentSuspense,isSVG,slotScopedIds,optimized)
  } else {
   // 更新组件 
  }
}
```

processComponent函数中，会调用mountComponent函数挂载组件。

**DOM元素到container**

处理完所有子节点，会再回到当前节点，判断是否有props，比如：class、style、event等，做相关处理。

最终，执行insert函数把创建的DOM元素挂载到container上，

```
function insert(child,parent,anchor){
  parent,insertBefore(child,anchor || null)
}
```

insert内部通过调用DOM API来执行insertBefore。执行完之后，mountElement创建的元素就挂载到父容器container上了，由于insert是处理子节点后执行的，整个DOM挂载顺序就是**先子节点，后父节点，最终挂载到最外层容器**。

## 小结

读完全文，你可能还是懵的，因为源码中涉及的细节处理确实较多，相比于我们日常需要关注的业务逻辑，复杂度高了两个档次。

但随我再来梳理一下，应该就不懵了。

这个过程总共做了这几件事：

1、createApp的时候，拿到根组件、根属性，并把外层容器传到挂载方法；

2、挂载方法中，会根据template的结构，创建一个与之对应的vnode结构；

3、创建的过程中，需要区分是否有嵌套，是文本、注释、普通元素，还是组件元素，再或者是Vue内置组件，进行不同处理；

4、创建的顺序是由内而外进行，通过对子节点的递归判断，进行普通元素或者组件的创建；

5、创建vnode的同时会执行副作用渲染函数，为数据的渲染和更新做准备；

6、挂载的过程中，会先确定元素类型，再调用DOM 的 createElement 等API进行相关内容的创建；

7、完成创建后，回到当前节点，处理props；

8、最终使用insertBefore方法挂载到最外层容器。

相信看到这里，你已经很明白了（不准说没有~）

内容确实多，有句名言，叫“书读百遍其义自见”，还有一句名言，叫“困难的事总是值得做的”。

这部分的内容如果确实觉得不好理解，不用读百遍，10遍差不多了，定会有收获。

欢迎留言讨论和补充！

欢迎关注公众号：**前端说书匠**。好文第一时间接收不迷路！~
