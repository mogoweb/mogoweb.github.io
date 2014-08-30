---
layout: page
title: "365浏览器"
date: 2014-03-21 17:37
comments: true
sharing: true
footer: true
---

##一、chromium_webview

基于chromium核心的Android WebView封装，目标是在chromium核心的基础上提供Android WebKit API全兼容的接口。目标支持平台：Android 4.0以上。项目地址：https://github.com/mogoweb/chromium_webview

本项目是受这篇文章《[基于Chromium内核的Android WebView](http://www.ituring.com.cn/article/40831)》的启发而创建的，同时参考了[ChromeView](https://github.com/pwnall/chromeview)开源项目。

##二、365浏览器

365浏览器是一款拥有chromium核心的android浏览器。365浏览器是一个实验性产品，其目标是：

1. 拥有chromium一样强大而稳定的核心；
2. 拥有safari mobile一样优雅简洁的UI；
3. 支持android 4.0以上android平台；
4. 支持浏览器扩展；

经过一番权衡和抉择，决定采用开源项目TintBrowser作为浏览器UI，精力集中于浏览器内核精简与完善。TintBrowser项目采用GPL V3开源协议，所以365浏览器的UI也采用GPL V3开源协议，源码地址:[https://github.com/mogoweb/TintBrowser](https://github.com/mogoweb/TintBrowser)。安装包已经在腾讯开放平台上线，您也可以前往如下地址下载：

[http://android.myapp.com/myapp/detail.htm?apkName=com.mogoweb](http://android.myapp.com/myapp/detail.htm?apkName=com.mogoweb)

也可以通过手机扫描二维码下载

![](http://mogoweb.qiniudn.com/365browser_2014_qrcode.png)

修改记录:

* 2014-08-18 V2.0
    - 修改TintBrowser代码，将其中的WebView替换为Chromium WebView.
    - 修改package name
    - 修改几处中文翻译
    - UI主题修改为Holo.Light，并对界面做了一些微调
    - 产品中文名定为"365浏览器"
* 2014-08-21 V2.0.1
    - 修复Android ICS版本上显示白屏问题
    - 修复打开优酷视频，关闭窗口后无法再播放视频的问题。
