---
layout: post
title: "浏览器中实现阅读模式"
date: 2013-08-30 09:06:23 +0800
comments: true
categories: 
  chromium, webkit
---
这里对阅读模式的定义是：去除网页上与主题内容无关的图片、导航、广告等等，给用户提供一个清爽的网页。在PC浏览器中，chrome/firefox都有这样的扩展/插件。我个人用的最多的是chrome上的**印象笔记.悦读**插件，和evernote整合，非常好用。从IOS 6开始，safari浏览器也提供了**阅读器**功能，用户界面如下：

[<img style="border-right-width: 0px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px" title="safari_readmode_1" border="0" alt="safari_readmode_1" src="http://mogoweb.net/wp-content/uploads/2013/08/safari_readmode_1_thumb.png" width="274" height="484" />][1] [<img style="border-right-width: 0px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px" title="safari_readmode_2" border="0" alt="safari_readmode_2" src="http://mogoweb.net/wp-content/uploads/2013/08/safari_readmode_2_thumb.png" width="274" height="484" />][2] 

当网页中主题内容比较明确、且文字内容较多时，地址栏上就会出现“阅读器”按钮，点击“阅读器”按钮，就会提供给用户一个清爽的网页。更绝的是，如果网页内容包含多个分页，“阅读器”模式下还可以自动整合，无需费力的点击下一页了，相当贴心，如下图所示：

[<img style="border-right-width: 0px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px" title="safari_readmode_3" border="0" alt="safari_readmode_3" src="http://mogoweb.net/wp-content/uploads/2013/08/safari_readmode_3_thumb.png" width="274" height="484" />][3] 

如果要在浏览器中实现这一个功能，可以有三种方法：

<!--excerpt-->

1、服务器端对网页进行处理

其思路和百度转码相同，就是在服务器端对网页进行处理，UC浏览器能够做到省流量，也是采用了这一技术。不过百度转码主要目的是为网页瘦身，所以处理出来的效果惨不忍睹。国外有一家公司，提供了免费的readability API，其口号是：

> Transforms any article … into a calm, humane, readable experience

详情请访问<http://www.readability.com>。

使用国外免费的API，实现简单，无需架设服务器，但缺点也是明显的，速度不够理想，容易被墙，说不定哪天就不能使用了。

2、修改webkit，对DOM树进行处理

webkit中，有一个步骤就是解析html，形成DOM树，有了这个DOM树就好办了，有什么节点、节点属性、节点样式都一目了然。所以接下来要做的事情就是筛选主题内容，过滤无关紧要的节点。至于采用什么算法进行筛选，还没有找到现成的C++库，网上有一个C#库：[NReadability][4] 可供参考，如果对javascript比较熟的话，直接参考[readability][5]提供的js脚本也可以。

通过修改webkit实现阅读模式，速度快，更容易和浏览器界面进行整合（比如，safari中，只有文字较多的网页才会出现**阅读器**），也比较适合对js前端开发不熟悉的C++程序员，缺点是webkit比较复杂，需要对webkit了解深入，而且之后的维护也是个麻烦事情。

3、注入js，对DOM树进行处理

javascript具有操作DOM的能力，也可以应用样式表来修改节点的样式，所以通过注入JS脚本来实现阅读模式是一个不错的选择。chrome/firefox中扩展插件，或者一种称为[bookmarklet][6]的小片代码就是这样实现的。

回到chromium for android，ContentView提供了evaluateJavaScript方法为当前ContentView注入javascript，所以实现起来非常容易。

### TODO

在safari中，实现了多页拼接的处理。Readability在[一篇文章][7]中也声明实现了多页合并的处理，不过我对javascript研究的不够多，还没有弄清是如何实现的。如果有精通web前端开发的同学一起探讨，非常欢迎。

 [1]: http://mogoweb.net/wp-content/uploads/2013/08/safari_readmode_1.png
 [2]: http://mogoweb.net/wp-content/uploads/2013/08/safari_readmode_2.png
 [3]: http://mogoweb.net/wp-content/uploads/2013/08/safari_readmode_3.png
 [4]: https://github.com/marek-stoj/NReadability
 [5]: https://code.google.com/p/arc90labs-readability/
 [6]: http://zh.wikipedia.org/zh-cn/%E5%B0%8F%E4%B9%A6%E7%AD%BE
 [7]: http://blog.arc90.com/2010/08/02/readability-updated-to-support-multi-page-articles/
