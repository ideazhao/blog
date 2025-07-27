title: 静态页面模块化探索
date: 2019-03-24
tags: [学习、模块化、gulp]
categories: [总结]
toc: true
---

常关注的都知道，我有个站点叫”醉牛前端“，刚发出来的时候就有人说开发方式简陋，手工维护静态页面，不便于多人协作。

我就是受害者本人，所有东西都堆在一个页面当中，查看和维护都很费力，所以，可不可以分拆模块呢？

工作当中我们是这样做的：

    <!-- #include = "./file.shtml" -->

但是，懂的人能看出来，这不是静态页，后缀是‘shtml’，平时我们开发也要在本地撘个apache才能正常访问，那可不可以不要服务器呢？

答案是可以，不得不说程序员的伟大（除了我~）

我们可以使用其他工具，比如：gulp插件。

一、**gulp-content-includer**

关于安装不再赘述，自行查阅很快搞定，只需要来了解，这个插件怎么使用

**gulpfile**

        var gulp = require('gulp');
            var contentIncluder = require('gulp-content-includer');
            var rename = require('gulp-rename');

            gulp.task('default', function() {
                gulp.src("test_src.html")
                    .pipe(contentIncluder({
                        includerReg:/<!\-\-include\s+"([^"]+)"\-\->/g
                    }))
                    .pipe(rename('index_test.html'))
                    .pipe(gulp.dest('./'));
            });

**test_src**

        <article class="mainarea item">
            <!-- 手册 -->
            <!--include "./mod/handbook.html"-->
            <!-- 工具 -->
            <!--include "./mod/tools.html"-->
            <!-- 代码在线演示 -->
            <!--include "./mod/code_online.html"-->
        </article>

**index_test**作为最终生成文件（代码过多就不贴出来了）。

- 一个目标文件
- 一个正则匹配设定include的格式
- 一个输出文件

如果你的页面要求不多，只需要这样，可以直接把这段代码拿过去用，无痛接入。

项目地址：[gulp-content-includer](https://github.com/hellopao/gulp_plugin/tree/master/gulp-content-includer)

当然，既是插件，肯定不止这一种选择，再看另一个。

二、**gulp-file-include**

**gulpfile**

        var gulp = require('gulp'),
            fileinclude = require('gulp-file-include');

        gulp.task('fileinclude', function() {
            gulp.src(['./src/*.html'])//主文件
                .pipe(fileinclude({
                    prefix: '@@',//变量前缀 @@include
                    basepath: './src/_include',//引用文件路径
                    indent:true//保留文件的缩进
                }))
                .pipe(gulp.dest('./dist'));//输出文件路径
        });

乍一看，差不多，但从上面的代码细节看，更易用，且供选择的功能也更多，这里没有完全列出，它还能处理导航条（选中态）、面包屑等网页常见元素的功能。

贴一段导航条示例代码瞅瞅：

**index**

        @@include('./navbar.html',{
            "index": "active"
        })

**navbar**

        <ul class="navbar">
            <li @@if (context.index==='active' ) { class="active" }>
                <a href="index.html">首页</a>
            </li>
            <li @@if (context.about==='active' ) { class="active" }>
                <a href="about.html">关于</a>
            </li>
            <li @@if (context.contact==='active' ) { class="active" }>
                <a href="contact.html">联系我们</a>
            </li>
        </ul>

编译结果：

        <ul class="navbar">
            <li  class="active" >
                <a href="index.html">首页</a>
            </li>
            <li >
                <a href="about.html">关于</a>
            </li>
            <li >
                <a href="contact.html">联系我们</a>
            </li>
        </ul>

以上只作为简介，更多功能可参考官方文档深入研究。

项目地址：[gulp-file-include](https://www.npmjs.com/package/gulp-file-include)

# 小结

对于静态页面的模块化，可能多数人是不需要的，因为要么团队本身搭建有服务器，要么是个人开发的一个需要后台支持的项目，也有服务器，但知道有这么个可用的东西是有益的，需要的时候能派上用场，如果你有类似的经历和更好的解决方案，欢迎交流。