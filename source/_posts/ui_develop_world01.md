title: 【UI开发大观】始于布局——页面布局      
date: 2017-10-13
tags: [总结]
categories: [UI开发大观]
toc: true
---

得布局者，得重构天下。这里说的重构，就是UI开发。

每个从事前端开发的人，起点都是静态页面的制作，HTML——CSS——JS，这个顺序不是谁规定的，而是一个自然的顺序，先有骨架，再有样子，然后再有行为（事件、数据、交互）。

随着现如今阅读设备的多样化，“多机型适配”成了网页制作中的一大工程。所以，虽然布局只是UI开发的一部分，我仍会说“**得布局者得重构天下**”。

## 一、概念
很多人开始接触网页制作，看到的可能是“div+css”，因为很多课程和书籍都是这个名字。

因为它俩是并列关系，所以，我疑惑的第一个问题就是，div是什么？有什么用途或者功能？得到的答案无非是，它就是一种元素，一个盒子。

这并没有解开我的困惑，css是一种语言，是一种技术，相应的，div也应该是，它居然不是，那为什么要把二者并列？

好的，上面有提到一个名词——“盒子”。我最初学习网页制作，第一个不能直接理解的名字就是“盒子模型”，因为“链接、标题、段落、列表”等等都是能够很容易联想到的网页信息形象，但是盒子跟网页能有什么关系？什么盒子？鞋盒子？

直到后来时间长了，才能慢慢地对以上两个问题释怀：

其一、之所以会有“div+css”的说法，是因为在那之前，大家都在使用table（表格）进行网页中大大小小的元素进行包裹、嵌套布局，当人们意识到了它的缺陷，就有了使用div来替换table进行页面当中没有明显语义的大区块结构构造。其本身没有特殊的意思和用途，但后来的发展却又走向另一个极端，div开始泛滥，人们好像总是在用新的错误去替代老的错误，然后再去反思和改进，或许这是人类进步的必经之路。

其二、盒子模型的提出，我认为更像是一种学术研究成果，当你真正搞懂了之后，才觉得这是个合理的比喻，但对于初学者反而不利，盒子和网页没有明显的对应关系，是网页当中的隐性骨架，当你了解到了“content、padding、border、margin”之后，才知道“盒子”的概念是怎么来的，才意识到这个不知所云的东西其实无处不在，只有当我们练就了庖丁的本领，才能看牛不是牛，所以，我不知道这种命名是更好，还是南辕北辙？

## 二、脉络
网页是二维平面，要么横排，要么竖排，**形态只有这两种，但是方法有很多种**。这句话几乎可以说是布局方法论的精髓。

我第一个上手实践的网页，是做毕业设计，当时还没有系统了解过布局的知识，边做边学，做到导航链接的时候，从形式上看，它是列表，所以我选择了ul，但我写完之后就觉得不对，我需要的是横排，它默认是竖着的，怎么让它横着呢？我在百度上搜，向网友求助，他说：这个不确定，要看你是什么情况了。我找到的第一个答案是使用float（浮动），然后我跟他说，原来浮动就可以呀，然后他说，不一定啊，你用span也可以啊。

当时我是略有不屑的，明明我找到了正确的方法，你还说什么span。

但事实上，我并没有很具体说我的代码是什么，所以他那样说没错。用a也可以，用display:inline-block;也可以，方法有多种，只是看需要或者应该用哪种。

我当时没有理解，也没有深究，这是很多人都有的一种不好的习惯，**容易看到学习成果，容易获得成就感，但也容易浅尝辄止**，结果只看到冰山一角，遇到新的问题又不知所措。

## 三、范畴
哪些表现属于布局？

会影响到元素位置和尺寸的属性，都属于布局。比如：宽、高、定位、浮动、内/外边距等等。

哪些因素会影响视觉效果？

首先就是宽高和间距，没有这些，网页内容就会全都挤在一起，美观暂不说，可读性极差，这也是为什么，人们常说按照规范的建议使用标签，因为就算不给网页加任何样式，只要代码写得够科学，浏览器给它提供的默认样式就会有不错的可读性。

其次是归类，怎么能够一目了然的看出，谁属于谁，谁和谁是并列的一类，这就需要视觉风格的层次感和一致性，通过排列顺序、文字大小、文字颜色、背景颜色、边框颜色等等来给内容归类。

第三是细节，UI层面的细节是很容易被忽视的，但也是设计师非常关心，会直接影响到最终效果的，比如：对齐、行高、透明度、渐变、阴影、纹理等等。让人欣喜的是，CSS3的技术能帮我们实现很多以前无能为力的效果，但我们面临的难题往往是人造成的，这个人是设计师。

可以说，以上三个大方向的判断和思考，就奠定了后面所有方案实施的基础，使用怎样的布局方式，怎么组织和规划代码，答案就逐个浮出水面。

这三个层面，能做到多少，做到什么程度，就基本能判断一个人的经验和用心程度。

## 四、挑战
兼容：

作为网页开发人员，需要面对不同厂商的浏览器对代码的解析和理解的差异。小到表现不一，大到是否支持。

过去是IE系列低版本广为诟病，战场转移到移动端后，又是安卓浏览器来背锅，一有问题，大家都会联想到安卓机，值得庆幸的是，安卓机的系统经过不断更新换代，能够支持大部分属性的机型已经占据了大部分的用户市场，使得我们在开发时不用太担心，但至少现在，我们仍然需要采取一些必要的错失以使我们的页面在少量低端机中不会出明显的问题。

适配：

如果这个世界不曾有智能手机的出现，那么适配就会变得很简单（排除其他设备出现的情况），但是没有如果，其实还应该感谢它，虽然因为它我们需要面对很多困难，但也因为有了它，让网页的浏览和使用变得更加便捷和有趣。

适配是个大的话题，因为**并没有一个单一的方法来完成它，但人们常常喜欢单一的去谈论它**，大到页面布局，小到图片的显示，文字的大小，除了要适应各种设备，还要考虑特定的元素适合使用哪种方法，后面的文章我会稍作展开探讨，这里不赘述。

## 五、不只是CSS
我在文章开头说，得布局者得重构天下，但也不全对，搞好了布局，只是搭好了框架，还有细节，还有代码维护、性能优化、团队合作，上下游沟通等。

所以，不能单凭代码能力去判定一个人的综合能力，做事效率，完成质量和合作舒适度，也不能看出一个人的提升空间有多大，故而，我们也不仅仅需要在代码层面提升自己，还要会总结，思考，不断反思和改善自己。

开篇先啰嗦这么多，后续咱们再聊。










