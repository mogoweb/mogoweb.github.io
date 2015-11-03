---
layout: post
title: "基于chromium for android开发Android浏览器"
date: 2015-11-03 08:39:21 +0800
comments: true
categories:
    chromium android 
---

在上一篇文章<< [Chromium for Android开源了][1] >>中谈到了Google已经完全开源了Chromium for Android，这样我们就完全可以开发与Chrome for Android媲美的Android浏览器了。通常浏览器的一些新特性和新功能会先出现在Chromium for Android上，稳定后才会出现在Chrome for Android上，如果我们基于Chromium for Android开发浏览器产品，在新特性和新功能方面不会落后于Chrome浏览器.

Chromium开源项目的代码及其庞大，也相当的复杂。对于Android开发人员，特别是UI开发人员来说，没有一个合适的IDE环境，会是一个非常痛苦的事情。我们通常是这样开发的，在sublime text中阅读、修改代码，使用chromium的构建系统build出apk，安装到手机，进行调试。对于native代码来说，这样的开发方式也不会有太多麻烦，因为native部分的代码通常修改很少。但如果主要进行UI的定制，这种开发方式就非常痛苦了。

为了应对这个问题，考虑如下方案：

1. 采用Android Studio作为开发环境，从Chromium for Android抽取chrome模块的源码，加入Android project。
2. native代码在chromium环境中build，作为so加入Android project
3. 基础模块(base, content, net等)在chromium环境build为jar包，加入Android project
4. content, chrome, ui等模块的资源文件加入Android library project

资源文件为什么不能直接都添加到Android project呢？因为命名空间的原因，比如content模块的资源的命名空间为org.chromium.content, chrome模块的资源的命名空间为org.chromium.chrome，所以需要建立不同的Android library project, 指定不同的包名。最后整个项目的结构如下：

```
browser
  |_ app
  |    |_ libs
  |    |_ src
  |         |_ main
  |              |_ aidl
  |              |_ assets
  |              |_ java
  |              |_ jniLibs
  |              |_ res
  |_ libraries
       |_ androidmedia_res
       |_ chrome_res
       |_ content_res
       |_ datausagechart_res
       |_ ui_res
```

注意事项：

1. chromium项目的源码和资源有些是自动生成的，需要到out目录下去复制
2. pak和dat等文件需要加入到assets目录，而且不能压缩
3. aidl文件加入到main/aidl下，android studio会自动处理

实践证明，这种方案是行之有效的，请参考github上的项目: [365browser][2]。在script目录下有从chromium项目同步代码和资源的脚本。


[1]: http://mogoweb.net/blog/2015/09/25/chromium-for-android-already-open-source/
[2]: https://github.com/mogoweb/365browser
