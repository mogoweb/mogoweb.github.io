---
title: "Android 4.4 WebView实现分析"
layout: post
date: 2014-01-16 19:22:09 +0800
comments: true
categories: chromium, android
---

随着Android4.4的发布，Android WebView改成由Chromium驱动了。浏览Android4.4源代码，可以看到之前版本中的external/WebKit目录被移除掉了，取而代之的是chromium_org。也就是说chromium已经完全取代了之前的WebKit for Android。虽然chromium完全取代了以前的WebKit for Android，但Android WebView的API接口并没有变，与老的版本完全兼容。这样带来的好处是基于WebView构建的APP，无需做任何修改，就能享受chromium内核的高效与强大。下面就Android 4.4中WebView的实现进行分析。

先来看看frameworks/base/core/java/android/webkit下的文件，里面有我们熟悉的WebView、WebSettings、WebViewClient等类，其中最主要的就是WebView了，相关的类关系如下：

![][1]

从图中可以看出，设计者抽象出了一个WebViewProvider接口，WebView本身并不做事，所有的处理就是交给WebViewProvider。而WebViewProvider只是一个接口，由实现者决定采用哪种引擎。从Android4.1开始，就采用了这种设计架构，开始为chromium核心的WebView做准备。在Android 4.4之前的版本，由WebViewClassic实现WebViewProvider接口。在Android4.4中，则是WebViewChromium实现WebViewProvider接口。WebViewFactory也采用了相似的结构，决定了如何实例化WebViewFactoryProvider，WebViewFactoryProvider有一个关键的接口createWebView，创建具体的WebViewProvider。（注：AOSP master源码基于WebKit的实现仍然保留，但在Android4.4中已经移除，由于采用了灵活的架构，所以在两种核心之间非常容易切换）
<!--more-->
目前的代码结构如下图所示，在ContentAPI之上，Chromium的WebView实现封装了一个新的类AwContents，该类主要基于ContentViewCore类的实现，不同的是，AwContents需要基于一个原来存在于“chrome/”目录下的模块（图中的BrowserComponents），但是AwContents不应该依赖该目录，所以，将chrome中的一些所谓的浏览器模块化是Chromium的一个方向。目前，一些模块以及从chrome中抽取出来了，参见“components/”。

![][2]

AwContents提供的不是WebView的API，所以，需要一层桥接部分，将AwContents桥接到WebView，这就是图中的桥接模块，该模块位于Android源代码中的frameworks/webview/chromium/java/com/android/webview/chromium/目录下，WebViewChromium和WebViewChromiumFactory类作为WebView的具体实现，依赖于Chromium项目的AwContents模块，整体模块图如下：

![][3]

AwContents基于Content之上，专门针对Android WebView需求而进行的一个封装，这个封装只针对Android平台。

WebView同样也是基于ContentAPI(web contents, ContentViewCore等)，在这点上来说，它同Content Shell和Chromium浏览器没有大的不同，区别在于它们对很多Delegate类的实现不同，这是ContentAPI用于让使用者参与内部逻辑和实现的过程。具体来说，它主要有以下两个方面的不同：

1. 渲染机制

因为WebView提供的是一个View控件，那么View控件的容器可能接受储存在CPU中的结构（如bitmap），也可能是储存在GPU内存中的结构（如surface），所以它需要提供两种不同的输出结果。

Chromium引入了一种新的合成器UberCompositor++，该合成器支持输出到GPU和CPU内存两种方式。对于Compositor的结果输出到给定View的GPU内存这种方式，关键点在于实现AwContents.InternalAccessDelegate接口的requestDrawGL方法。与requestDrawGL实现有关的代码不多，只涉及到DrawGLFunctor.java和GraphicsUtils.java，以及与之关联的native实现draw\_gl\_functor.cpp, graphic\_buffer\_impl.cpp和graphics\_utils.cpp，naitve代码位于frameworks/webview/chromium/plat\_support目录下。这部分代码不多，但牵涉到了Android源码，并不是只使用了Android SDK和NDK的API。

2. 进程

目前WebView只支持单进程方式，未来不排除支持多进程方式。单进程意味了没有办法使用Android的isolated UID机制，因此，某种程度上来讲，安全性降低了，而且页面的渲染崩溃会导致使用WebView的应用程序崩溃。 

#### Android 4.4中的chromium

Android 4.4代码树中的chromium源码位于external/chromium\_org，而不是external/chromium, external/chromium是一个老版本的chromium，主要提供chromium net网络栈。external/chromium\_org下的chromium版本为r227252，对应的Chromium发行版本为30.0.1599.101， Blink版本为r159105。

AOSP源码中的默认浏览器基于chromium核心，但Browser代码和之前的版本并没有差别，这也看出WebView API的兼容性相当好。而google官方则宣布以后android系统中不再提供浏览器，只有Chrome for Android。

#### Android4.4中的chromium移植分析

从Android4.4的chromium_org git库log可以看到，从chromium源码获取分支后，只cherry-pick了两个提交，而且cherry-pick的代码也是来自chromium项目，信息如下：

commit 3311b27cb6fdbe7af035a7d769944a17328c6227

Author: Ben Murdoch <benm@google.com>

Date:&#160;&#160; Mon Oct 21 12:33:52 2013 +0100

&#160;&#160;&#160; Cherry pick&#160; [Android] MediaPlayerBridge should pass Bridge to getAllowedOperations

&#160;&#160;&#160; Cherry pick of https://codereview.chromium.org/32213003/

&#160;&#160;&#160; Bug: 11311417

&#160;&#160;&#160; Original description:

&#160;&#160;&#160; [Android] MediaPlayerBridge should pass Bridge to getAllowedOperations

&#160;&#160;&#160; Right now the Java side is expecting a MediaPlayer object to be passed

&#160;&#160;&#160; to the MediaPlayerBridge.getAllowedOperations function, but native

&#160;&#160;&#160; is actually passing it a MediaPlayerBridge. Update the code to be

&#160;&#160;&#160; consistent &#8211; Java now expects the MediaPlayerBrdige and uses it to

&#160;&#160;&#160; extract a MediaPlayer.

&#160;&#160;&#160; Change-Id: I5ac618c833c6c3ff015d43405a9a4d507fce9ba5

commit 41a8683eef4139670e32f284a160dc3702d9a5c2

Author: Ben Murdoch <benm@google.com>

Date:&#160;&#160; Mon Oct 21 11:04:45 2013 +0100

&#160;&#160;&#160; Cherry pick android: fix base::Time::FromLocalExploded() crash.

&#160;&#160;&#160; Cherry pick of https://codereview.chromium.org/27472003/

&#160;&#160;&#160; Bug: 11313033

&#160;&#160;&#160; Original description:

&#160;&#160;&#160; android: fix base::Time::FromLocalExploded() crash.

&#8230;skipping&#8230;

&#160;&#160;&#160; Record Chromium merge at DEPS revision 30.0.1599.101

&#160;&#160;&#160; This commit was generated by merge\_to\_master.py.

&#160;&#160;&#160; Change-Id: Ie9e9e034f3e5d9a3fe3edf87595c7be467d3136b

结合前面的分析，可以得出如下结论：

1）AwContents都是基于Android SDK/NDK开发，并没有使用Android Source里面的未公开API和库。

2）Android 4.4中使用的chromium，直接取自chromium开源项目，虽然它使用了自己的git库：[https://android.googlesource.com/platform/external/chromium_org](https://android.googlesource.com/platform/external/chromium_org)

3）Android4.4中的chromium并没有针对android平台做一些特殊修改，和chromium开源项目的代码一致

4）与Android source密切相关的代码位于ChromiumWebView。

如前面所述，在实现reqeustDrawGL时，使用到了Frameworks 未公开的API，native部分代码也用到了Android source中的代码。所涉及的类和Android Source如下：

    1. HardwareCanvas
    2. ViewRootImpl
    3. DrawGlInfo.h
    4. GraphicBuffer.h
    5. GraphicsJNI.h

5）整个chromium移植代码中，与android source密切相关的代码极少，绝大部分都是基于SDK和NDK而开发。

 [1]: http://mogoweb.qiniudn.com/mogoweb_2014_android_4_4_webview_classes_diagram.png
 [2]: http://mogoweb.qiniudn.com/mogoweb_2014_android_4_4_webview_awcontents.png
 [3]: http://mogoweb.qiniudn.com/mogoweb_2014_androd_4_4_webview_components.png