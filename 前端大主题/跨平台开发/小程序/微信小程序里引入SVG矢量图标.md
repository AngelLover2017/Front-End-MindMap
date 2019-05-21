<h1>引言</h1>
	
因为微信小程序的限制，引入外部图片或者矢量图，只能通过设置背景图片`background-image : url("base64转码后的代码");`的方式来进行操作。同时还是因为微信小程序的限制，我们要先把svg的xml编码转码为base64编码

	首先，说明以下我们常见的svg矢量图是什么？下面引用百度百科的话：
	svg是基于可扩展标记语言（标准通用标记语言的子集），用于描述二维矢量图形的一种图形格式
可能还是比较迷糊，那我们来看看，用记事本打开一个svg，里面的编码是什么：
```
<?xml version="1.0" standalone="no"?><!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 
1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd"><svg 
t="1532946882675" class="icon" style="" viewBox="0 0 1024 1024" version="1.1" 
xmlns="http://www.w3.org/2000/svg" p-id="826" 
xmlns:xlink="http://www.w3.org/1999/xlink" width="200" 
height="200"><defs><style type="text/css"></style></defs><path d="M914.75 
466.75L832 384V256a64.19 64.19 0 0 0-64-64H640l-82.75-82.75a64.19 64.19 0 0 
0-90.51 0L384 192H256a64.19 64.19 0 0 0-64 64v128l-82.75 82.75a64.19 64.19 0 
0 0 0 90.51L192 640v128a64.19 64.19 0 0 0 64 64h128l82.75 82.75a64.19 64.19 0 
0 0 90.51 0L640 832h128a64.19 64.19 0 0 0 64-64V640l82.75-82.75a64.19 64.19 0 
0 0 0-90.5zM512 736L320 384h96l96 192 96-192h96z" p-id="827" 
fill="#F36778"></path></svg>
```
好了，你看到了`<?xml`/`svg11.dtd`，那我们就明白了，这是一种由特定的DTD约束的xml文件，www标准组织定义了这个标准或者说约束，来描述定义svg，所以我们可以知道svg其实就是xml的一个小子集。

<h1>步骤</h1>
好了，下面介绍一个网站，用来将xml文件编码转码为base64编码：
https://www.sojson.com/image2base64.html


1、在`↑`的网站的选择组件中选择需要转换的svg，然后在网站下面的转换框中复制转换成功的base64格式的代码

![这里写图片描述](https://img-blog.csdn.net/20180804104804393?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZ2VsTG92ZXIyMDE3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

2、将复制后的代码粘贴到`this`=> [`background-image : url("this")`]，然后对应的`view`或者`text`中加入样式即可

WXSS
![这里写图片描述](https://img-blog.csdn.net/20180804105400284?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZ2VsTG92ZXIyMDE3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

WXML
![这里写图片描述](https://img-blog.csdn.net/20180804105556261?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZ2VsTG92ZXIyMDE3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

效果图
![这里写图片描述](https://img-blog.csdn.net/20180804105758988?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZ2VsTG92ZXIyMDE3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

<h1>PS</h1>
	顺便安利一下这个网站，虽然界面不怎么美观，但是功能确实良心，作为前端开发的辅助工
	具网站还是蛮OK滴~


# 做个引流😜
博主最近开了一个微信公众号，以全栈技术为初衷，里面会分享我自己写的认为比较好的原创文章，博客会部分同步，感谢关注！
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190322164049429.jpg)