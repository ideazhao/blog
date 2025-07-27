title: HTML并不简单：小角色的大用途  
date: 2024-10-08
tags: [书籍]
categories: [说书匠]
toc: true
---

> 每个系列一本前端好书，帮你轻松学重点。
>
> 本系列来自阅文集团前端专家**张鑫旭**所编写的 **《HTML并不简单》**

近几年，音视频处理是风口，很多小伙伴开始涉足，但业务不相关的开发不一定有机会接触。

那么Web端的音视频涉及哪些东西，前端开发又能在其中充当什么角色呢？本文带领大家了解一些基本知识。

进入音视频话题之前，先看看跟它们关系密切的图像。

# 图像

#### figure

`figure`是专为图像显示设计的，与之配套的是`figcaption`，这个组合特别适合常见的图片配文字场景。

```
<figure>
    <img src="example.jpg" alt="">
    <figcaption>示意图</figcaption>
</figure>
```

#### srcset

对于不同分辨率下使用不同图片，不少开发可能倾向于先获取分辨率，再做条件渲染的方案，或者采用媒体查询，但其实`img`元素本身也可胜任，就像这样：

```
<img src="w128.jpg" srcset="w256.jpg 2x,w512.jpg 3x">
```

这段代码中，`srcset`属性中出现的字符x代表像素密度，含义是当浏览器的屏幕密度为1的时候，显示w128.jpg；不小于2的时候，显示w256.jpg；不小于3的时候，显示w512.jpg。

就是在保证最佳显示效果的同时尽可能调用尺寸最合适的资源，以节约网络带宽和内存开销。

#### picture

除了分辨率，另一个比较关心的就是格式，浏览器会在不同时期支持新的图片格式，例如WEBP、AVIF。然而这些格式存在兼容性问题，开发人员想使用它们该怎么办呢？

`<picture>`元素可以帮忙，在<picture>中使用`<source>`元素将最希望使用的图片格式放在最前面即可，最好通过type属性进行格式的指定:

```
<picture>
    <source srcset="example.avif" type="image/avif">
    <source srcset="example.webp" type="image/webp">
    <img src="example.jpg" alt="">
</picture>
```

这样写，浏览器就会从上到下的去检查是否支持，如果都不支持，则会加载用`<img>`元素兜底的图片。

#### 懒加载

默认情况下，`<img>`元素只要设置了`src`地址，哪怕元素是隐藏的，浏览器也会请求对应的图片。

这会造成很大的资源浪费，因为很多网页用户仅停留几秒，看看首屏的内容就离开了，屏幕外的图片资源白白加载。所以就有了“图片懒加载”，也就是图片在用户不可见的地方就不加载，真正浏览的时候才去加载。

过去图片懒加载都是使用JavaScript代码实现的，而如今非常简单，给`<img>`元素设置`loading="lazy"`就可以了：

```
<img src="example,jpg" loading="lazy">
```

#### crossOrigin与跨域

支持`crossOrigin`属性的元素非常多，例如`<img>、<script>、<video>`等，但多用在`<img>`元素上。

Web截图、PDF导出等需求都需要将`<img>`元素绘制到Canvas上并转换，可如果图片元素跨域了，那么无论是`getImageData()`方法还是`toDataURL()`方法，在执行的时候都会报错。这时候给`img`添加上`crossOrigin`属性就可能使问题得到解决。

```
<img src="book.jpg" alt="" crossOrigin="anonymous">
```

#### elementtiming属性

相比文本内容，媒体资源往往会成为页面加载的性能瓶颈。

为了排查分析，传统做法是用开发者工具去查看资源的加载时长。但这样非常低效，有没有更好的方法？

目前，浏览器提供了原生的API，可以对页面中的图文元素进行性能监控——`PerformanceObserver API`，看起来是个JavaScript API，但统计元素的渲染和加载时长靠的是一个HTML属性——`elementtiming`。

```
<img src="book.jpg" alt="" elementtiming="image">
```

`elementtiming`属性可以设置在任何有图文内容的元素上，包括`background-image`、视频元素，然后浏览器就会记住这些元素的加载时长、渲染时长等各种信息。配合PerformanceObserver API，就可以获得这些信息了。

# 音频元素

`<audio>`元素的常见使用较简单，因为通常没有可视化的需求，多用于背景音乐，或者音频提取处理等，例如运营活动页面，用户在看页面时伴随着背景音乐：

```
<audio src="example.mp3" loop></audio>
```

所以`<audio>`元素默认是隐藏的，如果希望有播放界面，则需设置controls属性：

```
<audio src="example.mp3" controls></audio>
```

音频元素同样支持对音频格式的渐进使用：

```
<audio>
    <source src="audio.ogg" type="audio/ogg">
    <source src="audio.mp3" type="audio/mpeg">
</audio>
```

# 视频元素

`<video>`元素的原本用途是播放视频，但大多数的网站都不是视频网站，所以它也常用于实现特效，例如：官网首页或者运营活动页，用视频作为背景，使页面效果更具冲击力和表现力，也都是用`<video>`实现的。

具体方法是将视频素材覆盖在背景素材上，然后设置视频的混合模式为`screen`。

```
<figure>
    <img src="night.jpg" alt="">
    <video autoplay loop playsinline src="fire.mp4"></video>
</figure>
```

```
video{
    mix-blend-mode:screen;
}
```

#### playsinline

`playsinline`属性可以让视频像图片一样内嵌在网页中。如果不设置该属性，用户在触碰视频的时候会调用系统内置的播放进行交互，显然，把视频当作动效的这种场景是不需要出现播放进度或者下载按钮的，所以`playsinline`属性比看起来实用得多。

#### 格式

适合在Web中传播的视频格式只有两个，WEBM 和 MP4。

WEBM 格式最大的优点是开源的编码格式，但iOS Safari对WEBM格式的视频的支持不太好，因此，目前在Web中主流的视频格式就是MP4格式，并且是H.264编码的视频格式。

`<audio>`元素支持的属性和方法`<video>`元素也都支持，实用程度差不多，唯一不同的就是`<video>`元素多了一个`poster`属性，这个属性的作用很简单，设置视频的预览封面图。

```
<video src="video.mp4" poster="poster.jpg"></video>
```

需要注意的是，封面图的尺寸要尽可能小，因为一旦视频成功预加载数据，封面图就会被视频第一帧的画面替换掉，图片如果太大，封面图还没加载好，视频画面就加载出来了，就失去它的意义。

如果的确对视觉表现有要求，节省开发成本考虑，可以直接使用 `Video.js`(https://github.com/videojs/video.js) 。

#### 自动播放

不论音频还是视频，都有一个`autoplay`属性可设置，但目前所有主流浏览器都将这个功能做了禁用。

目前，对于Web网页，如果你希望媒体资源自动播放声音，必须要点击或者触摸页面，或者以键盘访问页面，否则浏览器是不会自动播放的。

#### 倍速播放

这个功能，很多爱追剧的朋友会用到，它看起来高大上，其实很简单，使用`playbackRate`属性控制一下就好了，`playbackRate`属性是一个可读写的控制音视频播放速率的属性。

```
video.playbackRate = 1.0;
```

值为1就是正常播放，小于是放慢，大于是加快，但也是有可用范围的，Gecko内核浏览器是0.25到4.0，超出范围就静音了。

#### 画中画

这个功能也很常见，当我们在播放视频，页面往下滚动，直到看不到视频，这时候视频窗口会脱离原先位置，展示在右下角。

开启这个模式比想象中简单得多，只需要这样：

```
video.requestPictureInPicture();
```

退出也只需要一行代码：

```
document.exitPictureInPicture();
```

#### 外挂字幕

你很可能没想到，前端可以对视频的字幕加以干涉，这种需求出现在视频本来没有字幕，我们加字幕，或者本来有，我们需要对音频做处理，然后改掉它的字幕。

做到这一点是需要提供字幕文件的，文件后缀名是`.vtt`，被称为WebVTT格式，是专门的Web字幕格式。使用很简单，用一个`<track>`元素即可：

```
<video id="video">
    <source src="example.mp4" type="video/mp4">
    <track src="example.vtt" default>
</video>
```

# 小结

本篇文大致带大家了解了图片和音视频相关的一些HTML层面知识，实际中的音视频处理技术点肯定不止这些，比如会用到Canvas，音视频解析，信息提取，还可能用到FFmpeg等，这里没有深入介绍，大家根据自己的业务需求进行拓展。

篇幅考虑，本篇是对《HTML并不简单》这本书的最后一篇解读，除了精选出的这些内容外，书中还提及语义化、表单、表格、无障碍、MathML等知识，感兴趣的朋友可移步至书中进一步阅读。

欢迎关注公众号：前端说书匠。好文第一时间接收不迷路！~