title: Webpack上手指北      
date: 2020-04-18
tags: [Webpack,总结]
categories: [总结]
toc: false
---

近几年，打包构建工具层出不穷，前有Grunt、Gulp，后有Webpack、Rollup等，它们各有优势，均受到部分开发者的欢迎，本文聊聊Webpack的使用。

## 模块化

不论是新工具还是新技术，都是为了解决问题而存在，那么构建工具解决什么问题呢？

众所周知，最初的网页十分简单，没有多少交互，代码量也少，但随着技术的发展，JavaScript已经不仅仅用来实现简单的表单提交等功能，引入多个js文件到页面中成为常态，但这种做法有很多缺点：

1）需要手动维护文件的加载顺序。且多个js之间通常有依赖关系，难以清晰分辨谁依赖了谁。

2）每一个`<script>`标签都需要向服务器发送请求，过多的请求会严重拖慢渲染速度。

3）每个`<script>`标签都暴露在全局作用域，如果没有任何处理而直接在代码中进行变量或函数声明，就会造成全局污染。

模块化解决了这些问题：

- 通过分离模块，导入和导出，可以清晰地看到模块间的依赖关系。

- 借助工具进行打包，页面中只需要加载合并后的资源文件，减少了网络开销。

- 模块之间作用域隔离，彼此不会有命名冲突。

## 使用Webpack的理由

打包工具众多，为什么选择Webpack？Webpack具备以下几点优势。

1）默认支持多种模块标准，包括AMD、CommonJS，以及最新的ES6模块。这对于某些同时使用多种模块标准的工程非常有用，Webpack会帮我们处理好不同类型模块之间的依赖关系。

2）完备的代码分割解决方案。可以分割打包后的资源，首屏只加载必要的部分，不重要的放到后面动态加载。这对于体积较大的应用尤为重要，可有效减小资源体积，提升首页渲染速度。

3）可以处理各种类型的资源。除JavaScript以外，还可以处理样式、模板、图片等，而开发者需要做的仅仅是导入它们。比如你可以从JavaScript文件导入一个CSS或者PNG，而这一切最终都可以由下面讲到的loader来处理。

4）庞大的社区支持。除了Webpack核心库以外，还有无数开发者为它编写周边插件和工具，大多数的需求你都可以找到现有解决方案。

## 配置与方法

看看怎么使用。

依赖Node，这几乎是前端开发的标配，不多说。

学习一种东西的时候最怕看到各种陌生概念，这里先捋一捋。

为了便于理解，按照工作流程的阶段：

`entry`:流程的入口，告诉webpack从哪个文件开始打包，方式包括：字符串、对象、函数等。

**字符串写法**：

```js
const config = {
  entry: './entry/file.js'
};

module.exports = config;
```

**对象写法**：

```js
const config = {
  entry: {
    app: './src/app.js',
    vendors: './src/vendors.js' //第三方库入口
  }
};
```

**多页面**：

```js
const config = {
  entry: {
    pageOne: './src/pageOne/index.js',
    pageTwo: './src/pageTwo/index.js',
    pageThree: './src/pageThree/index.js'
  }
};
```

至于函数的方式，可以有逻辑处理的发挥空间，最终返回的还是字符串或者对象。

`chunk`:字面意思是代码块，存在依赖关系的模块在打包时被封装为一个chunk
`bundle`:由chunk得到的打包产物

所以三者的关系如图：

![](/img/webpack_use/chunk.jpg)

>先消除一个可能的误解，并非是一个entry项对应一个chunk，对应一个bundle，都可能是多个，后面会再说。

`output`:设置输出路径和文件

**单入口**

```js
const config = {
  output: {
    filename: 'bundle.js',
    path: '/public/assets'
  }
};

module.exports = config;
```

**多入口**

```js
{
  entry: {
    app: './src/app.js',
    about: './src/about.js'
  },
  output: {
    filename: '[name].js',
    path: __dirname + '/dist'
  }
}
```

这里就遇到了上面提到的”多对多“的关系。

其中filename是输出资源文件名，不仅可以是名称，还可以是相对路径，而且目录不存在也没关系，会自动创建。

可以看到，filename 使用了`[name]`，这是因为多个文件需要生成多个对应的文件，`[name]`类似模板语言，意图是根据入口文件动态生成文件名，以确保唯一性。

当然，`[name]`只是模板变量之一，还有可以控制客户端缓存的`[hash]`、`[chunkhash]`等，可以精确地让相应文件在客户端得到更新。就像这样

```js
output:{
  filename:'[name]@[hash].js'
}
```

`loader`:字面意思是装载器，可以理解为”预处理器“，Webpack本身只认识JavaScript，对其他类型，比如：样式、图片等，必须预定义一个或多个loader对其进行转译，所以loader赋予webpack处理不同资源的能力，丰富了扩展性。

用法示例如下：

首先说明，loader不是webpack自带的，需要安装，比如我们处理样式。

>npm install --save-dev css-loader style-loader

```js
module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader','css-loader']     
      }
    ]
  }
```
可以看出，loader的套路很简单：

1) 文件类型
2) 对应loader

需要说明的是：

1) css-loader 本身仅处理样式的加载，并不负责将样式插入页面，所以通常配合 style-loader 一起使用，预处理器同样。
2) use数组当中的loader顺序是有讲究的，它是从后往前进行处理，搞错了顺序同样无法达到想要的效果。

`plugin`: loader是做某些类型文件的模块转换工作，相比之下，plugin是用来完成一些loader职责之外的任务，更多，更丰富，也增强了webpack的能力。

用法如下：
```js
plugins: [
    new HtmlWebpackPlugin({template: './src/index.html'})
]
```

它的可配置项有很多，这里不再列举，只要知道它是一个具有 apply 属性的 JavaScript ，可以携带参数，所以要向plugin属性传入 new 实例。

## 配置文件

说到这儿，你可能觉得都是零散的概念，它们应该放在哪儿？

有一种”最佳实践“叫配置分离，即配置文件单独放在一个文件当中，node有package.json，webpack也有，我们上面提到的所有，都应该写在一个叫`webpack.config.js`的文件当中。譬如：

```js
//webpack.config.js
var path = require('path');

module.exports = {
  mode: 'development',  //模式，分为 development 和 production
  entry: './index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'index.bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader','css-loader']     
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({template: './src/index.html'})
  ]
};
```
最基本的配置就是这样了，如果看做是一条生产线的话，就是从入口文件进，从dist输出。

实际开发中会加入更多逻辑和工具，代码量会大大增加，但使用方法类似。

[更多配置](https://www.webpackjs.com/configuration/)

## 常用loader

说到这儿，你可能会问，我知道webpack拿loader作为工具，但有哪些工具可用呢？下面介绍几个：

- babel-loader

用途大家都很熟，把ES6+代码编译为ES5，可以让大家在浏览器支持度不是很充分的情况下用新方法编码。

推荐安装：`babel-loader @babel/core @babel/preset-env`

- ts-loader

用于连接Webpack和Typescript。

- html-loader

前面说过，Webpack只认识js，这个loader用于将HTML文件转化为字符串并格式化，通过js加载。

- file-loader

打包文件类型资源，返回publicPath。
对publicPath稍作解释，前面我们看到过path，它是资源打包路径，而publicPath是资源引用路径，即页面当中做引用的静态文件（图片等）。

- vue-loader

这个应该很多人在用，处理vue组件，除此之外，可能还要安装vue-template-compiler以及前面提到的样式处理loader等。

[更多loader](https://www.webpackjs.com/loaders/)

## 常用插件

- html-webpack-plugin

自动生成html文件，并且引用相关的 assets 文件。

- mini-css-extract-plugin

分离CSS，将CSS提取到单独的文件中，支持CSS和sourceMap的按需加载。

- uglifyjs-webpack-plugin

对js进行压缩混淆。

- Hot Module Replacement

浏览器自动更新，实时预览修改效果

- CommonsChunkPlugin/SplitChunks

把多个chunk中的公共部分提取出来，减少重复打包，缩小资源提及，可以更有效利用客户端缓存。

[更多插件](https://www.webpackjs.com/plugins/)

## 总结

文章已经有点长，但仍有很多东西没有讲到，没有深入，旨在跟大家分享webpack的用途以及大致用法，更详细内容及工具可移步[官网](https://www.webpackjs.com/)查看。

但对于建立初步的认识应该足够，剩下的，各公司或者业务的特点都不一样，大家可根据具体情况进行定制，总之，工具是为我所用的，是为开发者提供便利的，而不是拦路虎，应该学会并掌握它。

下篇见！~