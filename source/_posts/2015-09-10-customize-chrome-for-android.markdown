---
layout: post
title: 打造自己的chrome for android
date: 2015-09-10 22:01:54 +0800
comments: true
categories: 
  chromium, android
---

[注] 这是两年前写的一篇旧文，现翻出来，根据现在的实际变化作了修订。

chromium移植已经接近尾声，正在冲刺beta版本。不过越往后面，越是一些难啃的骨头。虽然背靠chromium这座大山，但是网页的复杂性超乎想象。更郁闷的是，有些BUG在chrome for android上没有，但在我们的浏览器上存在。因此经常会有这样的质疑：人家的chrome浏览器好好的，你做的浏览器为什么会有这样的问题。面对这样的质疑，真是有苦说不出。在有些人看来，别人都把源代码开放出来了，超过他们是理所当然的。没有办法，碰到难啃的骨头只能迎难而上了。好在chrome for android（V25之后的版本）开始支持自行定制了，虽然没法调试全部的代码，但是部分代码还是可以调试的。这样在分析我们和chrome浏览器在代码执行路径上的差异，也许能够提供一种思路。下面就谈谈如何构建自己的chrome for android。

<!--excerpt-->

1. 使用您的android设备下载chrome for android，如果能够翻墙的话就在Google Play下载，国内的应用商店也一般有，这里就不详细说了。

2. 打开您的android设备上的chrome for android，地址栏中输入chrome://version

3. 记下Build ID(版本号ID)后的那一长串数字。

4. 在PC机上访问 http://storage.googleapis.com/chrome-browser-components/<BUILD\_ID>/index.html。其中<BUILD\_ID>为步骤3中的ID。下载该页面所列的文件。以下将以$CHROME_PREBUILT指代所存放的目录。

5. 下载与chrome for android版本对应的chromium源码，请参考[http://dev.chromium.org/developers/how-tos/get-the-code#TOC-Check-out-the-source-for-a-branch-or-specific-release]上的指导将代码切换到正确的release分支。下面就以$CHROMIUM_SRC指代chromium源码的src目录

6. build出自己的libchromeview.so库

    - cd $CHROMIUM_SRC 
    - mkdir chrome-android-prebuilts 
    - cp $CHROME\_PREBUILT/chromeview\_target.gyp chrome-android-prebuilts/libchrome.gyp 
    - mkdir -p out/Release 
    - cp $CHROME\_PREBUILT/libchrome\_android_prebuilt.a out/Release 
    - CHROMIUM\_GYP\_FILE="chrome-android-prebuilts/libchrome.gyp" build/gyp_chromium 
    - ninja -C  out/Release libchrome_prebuilt

7. 解包官方chrome的apk
    *   使用USB连接设备 
    *   cd $CHROME_SRC 
    *   mkdir out/apk 
    *   cd out/apk 
    *   adb pull $(adb shell pm path $CHROME_PACKAGE | sed &#8216;s/package:\([^\r ]\+\).*$/\1/g&#8217;) 
    *   将pull出的apk重命名为Chrome.apk 
    *   apktool d Chrome.apk 

    注1：您也可以从网上下载Chrome for android，但要确保下载正确的版本

    注2：apktool 可以从<http://code.google.com/p/android-apktool/> 下载

8. 更新应用程序包

    *   cp $CHROME\_PREBUILT/change\_chromium_package.py . 
    *   chmod a+x change\_chromium\_package.py 
    *   ./change\_chromium\_package.py -u Chrome -p desired\_package\_name -a desired\_app\_name 
    *   cp $CHROMIUM\_SRC/out/Release/lib.target/libchromeview\_prebuilt.so libchromeview.so 
    *   arm-linux-androideabi-strip libchromeview.so 
    *   cp libchromeview.so $CHROMIUM_SRC/out/apk/Chrome/lib/armeabi-v7a 

9. 重新打包和安装apk

    *   apktool b Chrome Chromium_unaligned.apk 
    *   签名apk，为了简便起见，可以使用debug key： 

    > jarsigner -sigalg MD5withRSA -digestalg SHA1 -keystore PATH\_TO\_ANDROID\_SDK/.android/debug.keystore -storepass android Chromium\_unaligned.apk androiddebugkey

    *   zipalign -f -v 4 Chromium_unaligned.apk Chromium.apk 
    *   adb install -r Chromium.apk 

到此，自有品牌的chrome浏览器就此诞生，您可以更换logo，修复chromium的bug等等。

