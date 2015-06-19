---
layout: post
title: "在Chromium for Android中能否用TextureView替代SurfaceView"
date: 2015-06-19 19:26:45 +0800
comments: true
categories: chromium android
---

在开发Browser 2.0时，碰到一个非常头疼的问题，往布局上添加SurfaceView时，会引起黑屏闪一下。为了解决这个问题，我们不得不在初始化阶段就把SurfaceView添加好，之后也不能调整大小、移动位置。当时就考虑是不是能够用TextureView替代SurfaceView，不过由于计划上的调整，没有做进一步的尝试。今天在网上看到[这篇文章][1]，正好解答了我的疑问，在此把文章翻译过来，供参考。


># Android SurfaceView和textureView
>
>本文将简要介绍SurfaceView和TextureView的不同之处
>
>## SurfaceView和TextureView
>
>SurfaceView和TextureView都继承自android.view.View类，他们都可以在另一个独立线程中绘制和渲染，这是和其它View的最大不同。Crosswalk采用这一分离绘制特性，独立的GPU线程显著提高了渲染效率。
>
>SurfaceView提供一个嵌入在视图层次上的专用绘制表面，您可以控制该表面（Surface）的格式和尺寸。SurfaceView负责将表面放置在屏幕上正确的位置。它的行为多少有些类似于传统桌面系统上的onscreen窗口，比如，X11系统中的XWindow可以是无边框的，嵌入在另一个XWindow中。
>
>SurfaceView存在如下两个缺点：
>
>* 不能应用动画、变换和缩放
>* 不能叠加（Overlay）两个SurfaceView
>
>TextureView看似更像一个通用的View，可以应用动画、变换和缩放，就如同TextView。TextureView只能用在硬件加速的窗口。但是，TextureView比SurfaceView更耗内存，而且可能会有1～3帧的延迟，请参考讨论[3][4]。
>
>## 使用可以动画的XWalkView
>
>Crosswalk Embedding API for Android很好的支持了SurfaceView和TextureView。XWalkView缺省使用SurfaceView，也允许您在以下情况下使用TextureView：
>
>* 您想让XWalkView支持动画和变换
>* 您想叠加两个XWalkView
>
>设置布尔标识ANIMATIBLE_XWALK_VIEW为true即可启用TextureView。
>
>1. http://developer.android.com/reference/android/view/SurfaceView.html
>2. http://developer.android.com/reference/android/view/TextureView.html
>3. https://groups.google.com/a/chromium.org/forum/#!topic/graphics-dev/Z0yE-PWQXc4

文中提到的讨论起源是在google groups中有人提出如下问题：

>在当前ContentViewRenderView实现中，使用SurfaceView作为合成表面(compositing surface)，如我们所知，SurfaceView是一个特别的视图，无法进行动画或者变换。
>
>TextureView可以做SufaceView同样的事情，比如，您可以在另一个独立线程中渲染，也可以从底下的SurfaceTexture创建一个egl表面(eglSurface)。更主要的是，TextureView可以进行动画和变换。
>
>我很好奇为什么不使用TextureView作为合成表面，是否TextureView在使用上有一些特别的考虑，比如性能问题？欢迎任何讨论。
>

Google groups有人做如下回复：

>事实上chrome过去使用TextureView作为合成表面，但我们出于几个原因切换到SurfaceView：
>
>* 由于失效(invalidation)和缓冲的特性，TextureView增加了额外1~3帧的延迟显示画面更新
>* TextureView总是使用GL合成，而SurfaceTexture可以使用硬件overlay后端，可以占用更少的内存带宽，消耗更少的能量
>* TextureView的内部缓冲队列导致比SurfaceView使用更多的内存
>* TextureView的动画和变换能力我们用不上

所以结论是Chromium for Android中可以使用TextureView替代SurfaceView作为合成表面，但带来的后果是占用更多的内存，性能下降。

[1]: https://github.com/crosswalk-project/crosswalk-website/wiki/Android-SurfaceView-vs-TextureView
[2]: http://developer.android.com/reference/android/view/SurfaceView.html
[3]: http://developer.android.com/reference/android/view/TextureView.html
[4]: https://groups.google.com/a/chromium.org/forum/#!topic/graphics-dev/Z0yE-PWQXc4