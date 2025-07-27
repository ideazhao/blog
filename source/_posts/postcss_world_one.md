title: 深入postcss学习（一）
date: 2019-03-27
tags: [学习、postcss]
categories: [总结]
toc: true
---

关注postcss已久，要按第一次知道，是几年前的事了，但从未对其深入研究过，一直是“有你没你都挺好”的状态，但其实这样并不好，就好比，你懂js，那你学jq、angular、react这些都没用么，显然不是，总有它的长处，不能因为自己的懒学和懒用，就认为其“无用”。

## postcss是什么？

它是个“工具箱”，箱子里可以放入多种不同功能的工具，而这些工具可以帮助开发者提升效率、降低成本，就这样。

## 它能做什么

或许你所听说过的东西它都能做，比如：

- autoprefixer——添加兼容代码前缀 
- stylelint——检测语法错误
- stylefmt——格式化
- cssnano——压缩代码
- postcss-sorting——整理代码
- postcss-sprites——生成雪碧图，等等

你不知道的，它也可以做到，比如：

- doiuse——检测所写代码的浏览器支持情况
- postcss-preset-env——允许你马上使用未来的css特性
- font-magician——生成图标字体规则，等等

当然，远不止这么多，会在后续逐渐深入的时候一一介绍。

## 怎么用

使用postcss的方法有很多，最便利的，还是结合工具，比如我们熟知的gulp、webpack等（此处以gulp为例）。

如果你会用到postcss，肯定已经不是新手，多余细节我们省去。

新建一个demo文件夹，比如就叫“postcss”。

进入目录，初始化

    npm init

没有特别要求的话，一路回车下去即可，会自动生成一个package.json文件，这个文件里会描述项目的概况和一些依赖信息。

随后需要安装gulp

    npm install -g gulp

完成后安装postcss

    npm install --save-dev gulp-postcss

因为使用的是gulp，所以安装的是 gulp-postcss。

这些都可以用命令行来完成，如果你觉得切换编辑器和命令行比较繁琐，推荐使用 Visual Studio Code ，它可以让你在编辑器中使用命令行。

到这里是否大功告成了呢？No!这仅仅是开始。

## 用起来

为什么说仅仅是开始，前面就提了，它只是工具箱，而非工具，没有工具，什么都做不了，所以，要往里面放工具。

万事开头难，咱们拿最简单的开刀。

    npm install --save-dev autoprefixer

这款插件无需赘述，看名字就知道是干嘛的，安装完之后，我们只需要在gulpfile里这样配置就好：

        var gulp = require('gulp');
        var postcss = require('gulp-postcss');
        var autoprefixer = require('autoprefixer');

        gulp.task('styles',function(){
            return gulp.src('src/*.css')
            .pipe(postcss([ autoprefixer ]))
            .pipe(gulp.dest('dest/'));
        });

然后我们要做的就是，新建一个文件夹，在里面放一个css文件，至于名称叫什么都行（这里是src），只要能够匹配。内容是这样：
（尽量选择一些大概率需要兼容的代码，否则你可能看不到任何变化）

        .box{
            transform: translate(50px);
            filter:blur(5px);
        }

完成后，在命令行里敲

    gulp styles

即可，如果一切顺利，会自动生成一个dest文件夹，里面有个处理后的css文件，变成了这样：

        .box{
            -webkit-transform: translate(50px);
                    transform: translate(50px);
            -webkit-filter:blur(5px);
                    filter:blur(5px);
        }

当然，这里生成什么不是固定的，可以根据项目需要进行浏览器类型和版本的设置，比如兼容IE、火狐、opera等。

# 小结

作为系列文章的第一篇，到此就差不多了，因为其他那些插件的使用也无非是类似的过程，如果需要，下篇的开头会给出简单示例，然后就进入可能多数人并不十分熟悉的领域，那才是你更感兴趣的部分，我猜。

下次见！~