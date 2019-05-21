很久没有更博了，今天更新一篇文章，我们知道HTML标签很多，常用的标签大概有50-60个左右，但是你知道HTML标签总共有多少个吗？我统计了一下，大概是120个左右（有一些被废弃，有一些HTML5中新增）。想要完全记住这120个HTML标签当然可以，但是如果长时间不用，自然也是会忘记的。本博为了巩固前端基础知识，近期看了一遍所有的HTML标签，然后发现其实这些标签可以大概分为 <big>8</big> 类 ：

1. 元信息类标签 ： 放在<head></head>标签之内，用来描述文档基本信息和配置等标签
2. 语义类标签   ： 加了标签和没加标签，在文档中的直观外形并没有什么变化（或者完全可以使用样式表替代），但是却赋予了文档元素不同的语义的标签
3. 样式类标签   ： 相比于语义类标签，这类标签没有语义纯粹是为了为文档内容加入某种样式的标签
4. 媒体型标签   ： 为了使文档形式多样化，而可以嵌入不同与文字的媒体形式的标签
5. 表单类标签   ： 特指在表单处理业务场景下所使用到的标签
6. 表格类标签   ： 与表格相关的标签
7. 布局类标签（或结构类标签）   ：  这类标签是指定义了文档一些特定结构或布局的标签
8. 链接类标签   ： 指能够搭建起文档内和文档外的链接途径的标签

在开始看我下面的整理的表格之前，你可以先猜一下，我们常用的标签大概分布在哪里？哪部分的标签占比最大？

<small>*由于Markdown控制表格属实麻烦 ， 而且牵扯到大量HTML标签，Markdown支持所有Html标签，所以下面的表格是多张截图*</small>


![在这里插入图片描述](https://img-blog.csdnimg.cn/2019030320134961.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190303201420135.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZ2VsTG92ZXIyMDE3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190303201456932.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZ2VsTG92ZXIyMDE3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190303201523820.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190303201546430.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZ2VsTG92ZXIyMDE3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190303201606902.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZ2VsTG92ZXIyMDE3,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190303201629997.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019030320170517.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190303201726616.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190303201743329.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZ2VsTG92ZXIyMDE3,size_16,color_FFFFFF,t_70)

除此之外，还有`<object> ,<param>`比较特殊，用来定义对象和对象属性，不能归为任何一类，也不常用。

通过上面的统计，其实我们可以回答刚才的问题了。我们可以看到，占比最多的HTML标签是 <strong>语义类标签</strong>，但是这部分的标签却是使用较少的。我们常用的标签是 <strong>表单类</strong>，<strong>表格类</strong>，<strong>布局类</strong> 和 <strong>样式类</strong> 。这也可以说明一个问题，我们平时在使用HTML标签的时候，重样式布局，但是考虑语义的很少。其实使不使用 语义 要看具体的需求，对于那些功能型的重交互的网站（或应用程序），语义化的作用不大。但是如果是类似博客，评论这样的需求，语义化的作用举足轻重，做好语义化可以让搜索引擎更好的爬取到你的网站，对内容SEO有很大作用，同时也可以让浏览器更好更合理的展现你的内容给读者用户。

## 最后说件重要的事
我最近刚开了一个个人的微信公众号“**BearKeeper**”，上面会分享一些原创文章，博客有时候也会同步更新，希望关注。
                                       ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190304092139140.jpg)