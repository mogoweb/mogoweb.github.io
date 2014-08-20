---
layout: post
title: "关于chromium_webview项目"
date: 2014-06-10 08:56:06 +0800
comments: true
categories: chromium, android
---
chromium_webview的目标是在chromium核心的基础上提供Android WebView API全兼容的接口，希望达到的效果是基于Android WebView API开发的应用程序，只需将android.webkit包名替换为com.mogoweb.chrome即可完成从WebKit内核到Chromium内核的迁移。目标支持平台为Android 4.0以上。项目地址：[https://github.com/mogoweb/chromium_webview][1]。

## 项目的起源

从2011年开始，我转向从事chromium的定制化工作。2012年在移动手机平台上开发了一款浏览器Browser1.0，基于WebKit内核和Android WebView API。2012年下半年开始进行chromium的移植工作，其时google发布了Chrome for Android的第一个版本V0.16，还是beta版本。虽然浏览器的内核发生了很大的变化，但是我们期望上层的代码不需要做修改，于是在Content API的基础上，按照Android WebView API进行接口封装。最后做出来的浏览器美其名曰“双内核”，也就是WebKit加Chromium双内核。
<!--more-->

由于Chromium渲染架构的缘故，在Content API之上封装的WebView API还存在问题：

1. 为了启用硬件加速，chromium for android引入了SurfaceView，但SurfaceView动态添加会引起黑屏。所以SurfaceView必须在应用程序的初始化代码中添加，不能象Android WebView那样随心所欲的使用。
2. 采用了多进程架构，后果之一就是内存狂飙，更让人难以接受的是，访问新浪首页这样复杂的网页，还不如WebKit浏览器流畅。

在我们移植chromium的同时，google的工程师也没闲着，chromium代码下面开始出现android_webview的代码，接着代码越来越多，更新的也越来越频繁。有一天，在github上看到一个项目：[ChromeView Project][2]。这正是我所需要的基于chromium的WebView API封装,但这哥们估计也没啥闲功夫，项目好久都没有更新。于是我就在github上创建了chromium_webview项目，早期的AwContents实现是软件渲染，所以效率低下。

时间推到2013年11月，Android 4.4发布，其中很大的一个变化就是使用chromium替换了原来的webkit引擎。于是我又开始哼哧哼哧研究起AOSP中的Chromium WebView实现，研究了AOSP中的Chromium WebView实现后，定下了如下策略：

1. 照搬WebViewChromium及相关代码，如果代码中使用了未公开的API，使用反射机制实现。
2. 启用光栅化位图，消除对GraphicBuffer的依赖，从而不依赖系统的未公开C++接口。

经过一段时间的工作，chromium webview项目终于完整支持硬件加速。

## Chromium WebView的应用场景

有人要说了，现在Android系统都已经采用chromium引擎了，你还费老大劲整出这玩意儿，是不是吃饱了撑了。的确，做这个事情没给我带来一毛钱经济上的收益，反而搭进了我的业余休息时间，我做这件事的动力来自：

1. Android WebView采用chromium引擎不假，但那也得4.4及以上的版本。
2. 使用系统的WebView，失去了定制能力，比如要添加一个HTML标签就比较难。
3. 提高水平，结交朋友。

早些年做《无线城市》项目时，就发现Hybrid App还是有一些市场的。但使用系统的WebView可能存在性能低下、兼容性不良、无法定制的问题。所以chromium webview的最佳应用场景就是集成到APP作为渲染引擎，此外开发浏览器也是一个选择。

## Chromium WebView存在的问题

Chromium WebView刚完成了初步的移植，问题还非常多，在后续的时间，将会继续完善。目前看来，仍然有几大难题难以解决：

1. 由于渲染架构的原因，在Android 4.0 & Android 4.0.3上还只能使用软件方式渲染；
2. 光栅化位图的效率似乎比GraphicBuffer的效率低一些，长网页或复杂网页在快速滚动时，会出现短暂的白屏。

## 后续计划

1. 完善Chromium WebView API，修复BUG
2. 开启WebGL支持
3. 精简内核，优化内存占用
4. 将开源浏览器TintBrowser改成使用Chromium WebView，证明基于WebView API的应用能够无缝迁移到Chromium WebView。

## 更新

2014-08-18 

完成TintBrowser在Chromium WebView上的适配，并修改了若干UI，命名为365浏览器，已经在腾讯开放平台上上架，你可以从[这里](http://android.myapp.com/myapp/detail.htm?apkName=com.mogoweb)下载。

[1]: https://github.com/mogoweb/chromium_webview
[2]: https://github.com/pwnall/chromeview
