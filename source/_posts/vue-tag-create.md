
title: Vue标签生成组件
date: 2021-03-23
tags: [开源]
categories: [组件,Vue3]
toc: true
---

今天为大家带来一个基于Vue的标签生成组件。

### 背景

文章类页面一般都需要有标签来归类和检索，就会在编辑文章时需要创建标签的功能，标签是一个个独立的文字块。

### 需要实现的功能：

- 输入框
- 某种特定的操作触发生成标签
- 校验输入的内容是否符合标准
- 判断标签总字数是否超出
- 删除标签

本文的主角便是实现这样一个功能的Vue组件——**vue_tag_create**。

其实笔者遇到这样一个需求的时候，找过组件库，但并不完全符合要求，也找过有没有现成的包，同样体验不好，文档也不完善，索性自己写。

### 目前实现的功能：

- 特殊字符校验
- 超出字数校验
- 添加标签
- 删除标签
- “删除图标”自定义
- “占位文字”自定义
- 错误提示布局选择

使用示例：

```
npm install vue_tag_create -S

import vue-tag-create from "vue_tag_create"

<vue-tag-create 
  :limit='20' 
  :inline="true" 
  :iconClass="iconClass" 
  :placeholder="placeholder">
</vue-tag-create>
```

注：目前已可用，不必安装多余依赖，Vue3版本未验证，应该也可以。

但是，还需要更多来自开发者的反馈，继续优化之后方可正式投入使用，更多功能和更好的体验等你来一起定义，欢迎[vue_tag_create](https://github.com/linggan100/npm_package/tree/master/vue_tag_create)提issue。
