---
title: "[译]Android WebView"
layout: post
date: 2013-10-24 15:27:02 +0800
comments: true
categories: chromium, android
---
原文地址：<https://crosswalk-project.org/#wiki/android-webview>

* 架构&组件

  - AOSP中的Android WebView将基于AwContents，其位于Content API和Content View之上。 
  - 与Content API不同，它还依赖于从最初的chrome browser分离出来的组件，组件位于components/下，包含auto\_login\_parser，navigation\_interception，visitedlink\_browser，visitedlink\_renderer和web\_contents\_delegate\_android，这些都是用于页面。 
  - 无渲染进程（进程内渲染），无沙盒机制（sandbox机制） 
  - 图形架构和Content Shell有很大的不同，且正在开发中，将支持软件和硬件输出。 

* API

  - API类似于EFL WebView，不会暴露内部的browser/renderer/gpu进程。 
  - 能够加载URL，接收加载过程中的事件和回调。 
  - 可通过addJavaScriptInterface扩展JavaScript API，允许JavaScript调用Java代码，反之这不支持。 
  - 提供了若干API接收页面状态改变通知和页面回调。 

* 消息循环和事件处理

  - 整合利用Android消息处理 
  - native代码设置定时器到Java侧的消息循环 

* 图形 

  - 渲染使用chromium合成器，开启了硬件加速
  - 合成器输出支持软件和硬件Surface
  - 硬件加速的架构和content shell不同，和ChromeOS类似。现在还处在早期开发阶段，使用了UberCompositor，支持软件输出

* WebAPI扩展 

  - API addJavaScriptInterface可用于JavaScript API扩展，但支持JavaScript调用Java代码。
  - 相反方向的调用（Java -> JavaScript）不支持
  - addJavaScriptInterface通过NPObject机制（之前用于NPAPI插件）支持进程外访问，虽然在WebView中不再需要。
  - 来自Java侧的事件通知貌似还不支持

* API细节和涵盖范围 

  - WebViewClient用于页面状态改变通知
  - WebChromeClient包含参与渲染逻辑的回调
  - 原生代码/JS交互
  - 页面加载
  - 导航&历史记录
  - 文字搜索
  - 事件处理
  - 轨迹球、触摸、键盘
  - 不支持鼠标事件
  - 设置：编码
  - 页面信息：标题、favicon
  - HTTPS/SSL
  - 页面加载状态、缩放、重定向、URL重载，等等

* 源码 

  - src/android_webview 
    * android_webview_java.jar, libwebviewchromium.so和webviewchromium.pak是本模块的编译目标.
  - Google的工程师正在Android开源项目里开发基于Chrome核心的新WebView. 
    * 官方 站点:
    <https://android.googlesource.com/platform/frameworks/base.git/+/master/core/java/android/webkit/> 
    * 镜像:
    [https://github.com/android/platform_frameworks_base/tree/master/core/java/android/webkit][1]
    * 第三方开发者也在基于android_webview模块开发ChromeView
        - ChromeView有着和Android Webview一样的API. <https://github.com/pwnall/chromeview-src>

 [1]: https://github.com/android/platform_frameworks_base/tree/master/core/java/android/webkit