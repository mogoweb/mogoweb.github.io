---
layout: post
title: "Chromium for Android开源了"
date: 2015-09-25 09:08:49 +0800
comments: true
categories: 
  chromium android
---

最近一段时间都在忙其它的事情，没有关注chromium的最新进展。这几天浏览 http://www.chromium.org 上的文档，发现android build目标增加了一个：chrome_public_apk。编译方法：


> #Build the full browser

> ~/chromium/src$ ninja -C out/Release chrome_public_apk
>
> ~/chromium/src$ build/android/adb_install_apk.py out/Release/apks/ChromePublic.apk

搜索了一下相关新闻，大约在2015年5月份开始，chromium中开始加入了chrome for android的源码。从2012年开始，chromium中开始出现Android的移植代码，但只有Content API和ContentShell，离一个完备的浏览器还有一定的距离。当然，基于Content API开发浏览器也是可行的，当年就是这么干的。Chromium for Android和Chrome for Android并非完全一样，就如同桌面版Chromium浏览器和Chrome浏览器之间的区别。

下面放上一些Chromium for Android浏览器的截图，慢慢品味吧：

![welcome](http://7d9je4.com1.z0.glb.clouddn.com/mogoweb_2015_chromium_for_android_welcome.png)

欢迎界面

![account](http://7d9je4.com1.z0.glb.clouddn.com/mogoweb_2015_chromium_for_android_account.png)

帐号同步界面

![main](http://7d9je4.com1.z0.glb.clouddn.com/mogoweb_2015_chromium_for_android_main.png)

浏览器主界面

![menu](http://7d9je4.com1.z0.glb.clouddn.com/mogoweb_2015_chromium_for_android_menu.png)

浏览器菜单

![settings](http://7d9je4.com1.z0.glb.clouddn.com/mogoweb_2015_chromium_for_android_settings.png)

浏览器设置

![tabs](http://7d9je4.com1.z0.glb.clouddn.com/mogoweb_2015_chromium_for_android_tabs.png)

浏览器标签页


