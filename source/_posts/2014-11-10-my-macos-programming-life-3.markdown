---
layout: post
title: "我的macos编程之旅-AutoLayout"
date: 2014-11-10 09:00:17 +0800
comments: true
categories: macos objective-c
---

开始了Mac OS编程之后，仿佛又回到了Visual Basic编程年代，拖拖拽拽几个控件，app的UI就设计好了。但我至今还记得当年面临一个头疼的问题，就是应用程序在不同分辨率下的展现问题。为什么会头疼呢？那时主流的屏幕分辨率是800x600，但有些更好的显示器分辨率为1024x768，如果我们按照1024x768的分辨率设计UI，那么在800x600的分辨率下就显示不全，如果按照800x600的分辨率设计UI，那在1024x768下就没法充分利用屏幕空间。所以当时的app通常会指定最佳分辨率，低了显示不全，高了就只利用屏幕的一部分空间。

回到**365看图**软件上来，按照IKImageViewDemo设计的UI，存在一个问题：当我拉大窗口，图片显示区域并不会随之变大，整个界面变得很不协调。原因呢在于UI采用了绝对布局，控件的大小和位置是固定的。当然我也可以通过编程的方式，根据窗口的大小，计算图片显示区域的大小，然后动态的设置大小和位置，但这比较繁琐。在Android中，通过设计出线性布局、相对布局等布局方式来解决这一问题，而绝对布局是不推荐使用的。Mac OS是如何解决这一难题的呢？答案就是AutoLayout。

### AutoLayout <sup>[1]</sup>

自动布局系统通过数学化描述元素之间的关系来布局应用程序用户界面。这种关系称作约束，可以定义在单个的元素上，也可以定义在元素之间。使用自动布局，您可以创建动态灵活的界面来适应窗口大小的改变、设备横竖屏变化，还可以应对本地化带来的麻烦。

约束是自动布局的基础，它描述了界面元素布局的规则，比如您可以创建一个约束指定元素的宽度，或者与另一个元素的水平距离。您也可以添加或者删除约束，甚至修改约束的属性，来改变界面布局。

当您添加了多个约束后，不可避免的可能会存在冲突。所以，在计算用户界面上元素的运行时位置时，自动布局系统会综合考虑所有的约束，然后找出一种最大满足所有约束的布局。

自动布局从XCode 5开始引入，适用于ios 6.0和mac os 10.7及以上系统。当一个新工程创建时，自动布局缺省是开启的。

### 添加约束

添加约束的最简单方法就是通过拖拽来完成，方法如下：按住Ctrl键，然后拖动画布上的控件，类似于创建outlet连接。您可以拖拽元素到自身、到容器，或者到另一个元素。

![autolayout](http://mogoweb.qiniudn.com/mogoweb_2014_mac_autolayout_trailing_2x.png)

如上图中，您可以根据需要选择"保持在容器垂直方向的中央“，也可以选择”尾部与容器保持一定的距离“。

在**365看图**中，我选择将IKImageView的四边与容器保持一定的距离，这样，当窗口放大和缩小时，IKImageView就能同步放大与缩小。

### 更多

关于约束，还有许多地方值得探讨：比如编辑/删除约束，多条约束出现冲突该如何处理，如何通过编程的方法增加约束，等等。现在我只使用了一个基础的用法，更多的资料，可以参考apple的一篇官方文档：[Auto Layout Guide](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/Introduction/Introduction.html)。

[1]  https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/Introduction/Introduction.html

