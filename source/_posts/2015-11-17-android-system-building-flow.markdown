---
layout: post
title: "Android源码阅读与理解（二）：Android系统Build流程详解"
date: 2015-11-17 08:51:50 +0800
comments: true
categories:   
  android 系统分析
---

个人认为，成为一个靠谱的Android系统工程师，必须具备以下技能：

* 翻墙
* 会使用Linux系统
* 阅读英文资料

翻墙技能就不用说了，因为不翻墙，Android源码难以获取，更不用谈体验墙外精彩的世界了。关于第二点，最好的Android系统开发环境就是Linux（推荐ubuntu），如果能够熟练使用Linux系统可以节省不少折腾环境的时间。最后英文阅读能力也很关键，因为碰到问题Google, [StackOverflow][1]非常有用，上面的回答有不少是用英文写的。

在这里略过下载Android源码，最好的方法是参考[官方文档][2]。简单说一下我的环境：

* Ubuntu 14.04 LTS 64位
* Android 4.4.2源码
* Sublime Text 2
* Intel(R) Core(TM) i7-2630QM CPU @ 2.00GHz, 8G RAM, 1TB hd

源码下载后，执行如下命令即可编译Android系统(后面的文章将使用$ANDROID_SRC指代Android源码所在目录，如非特别指明，假设命令行在Android源码目录下执行):

1. source build/envsetup.sh
2. lunch
3. make -j4

下面就解析以上三个步骤分别做了什么事情。

## 设置环境变量

打开$ANDROID_SRC/build/envsetup.sh文件，我们可以看到文件中定义了一些shell函数，其作用是初始化编译环境，并引入一些辅助的Shell函数，这其中就包括第二步使用lunch函数。这些shell函数可以看做shell命令，方便我们进行一些build任务，比如常用的mm。下面就列出一些常用的函数：

**表 1. build/envsetup.sh 中定义的常用函数**

---

| **名称** | **说明** |
|:--------------|:-----------------|
|lunch          | 选择产品，格式为lunch BUILDID-BUILDTYPE，如果不指定，会弹出一个列表选择 |
|tapas          | build未打包到image的app，命令行后可以跟一个或者多个app的名称
|croot          | 切换到源码树的根目录 |
|m              | 在源码树的根目录执行 make |
|mm             | Build 当前目录下的模块 |
|mmm	            | Build 指定目录下的模块 |
|mma            | Build 当前目录下的模块及所有依赖的模块 |
|mmma           | Build 指定目录下的模块及所有依赖的模块 |
|cgrep          | 在所有 C/C++ 文件上执行 grep |
|jgrep          | 在所有 Java 文件上执行 grep |
|resgrep        | 在所有 res/*.xml 文件上执行 grep |
|godir          | 转到包含某个文件的目录路径 |
|printconfig    | 显示当前 Build 的配置信息 |
|add_lunch_combo| 在 lunch 函数的菜单中添加一个条目 |

除了定义辅助Shell函数，在脚本的结尾处，遍历device和vendor目录下的vendorsetup.sh文件，vendorsetup.sh文件定义与设备相关的环境变量，比如*/device/generic/armv7-a-neon/vendorsetup.sh*就定义了如下：

> add_lunch_combo mini_armv7a_neon-userdebug

这会在lunch菜单中增加一个选项：mini_armv7a_neon-userdebug

## lunch函数详解

Android支持不同的硬件，比如不同的CPU架构（ARM、MIPS，X86），不同厂商的外设（如蓝牙、wifi、基带等），另外可能还需要分Debug版本和Release版本，这都是在lunch中进行选择的。lunch命令的格式为lunch BUILDID-BUILDTYPE，其中BUILDTYPE取值如下：

**表 2. BUILDTYPE说明**

---

| **BUILDTYPE** | **说明** |
|:--------------|:-----------------|
|user           | 相当于Release版本，无root权限，不打包调试工具，适合最后的产品发布 |
|userdebug      | 和user类似，但多了root权限，可以调试，适合于产品调试 |
|eng            | 开发阶段的配置，另外还打包了许多调试工具，适合于产品初期阶段 |

BUILDID则是一组特性集合的代码，这个通常与具体的平台相关。除了Android源码预置的选项外，还可以通过add_lunch_combo命令添加自定义的组合。当我们要开发一款新的Android产品时，首先就需要在Build系统中添加对于该产品的定义。

产品定义文件通常位于device目录下，vendor目录已经不建议使用（nexus系列的产品定义就是放在vendor目录）。device目录下通常根据公司名及产品名分为二级目录，比如generic的目录结构如下：

```
 device
   |-- generic
         |-- armv7-a-neon
         |-- common
         |-- goldfish
         |-- mini-emulator-armv7-a-neon
         |-- mini-emulator-mips
         |-- mini-emulator-x86
         |-- mips
         |-- x86
```

通常一个产品定义至少包含4个文件：AndroidProducts.mk, BoardConfig.mk, vendorsetup.sh及版本定义文件（文件名不固定）。关于如何添加产品定义，请参考[<<理解 Android Build 系统>>][3]一文的**添加新的产品**一节。

## build Android系统

最后一步执行*make -j4*才开始真正的进行编译操作，j后面的数字代表同时编译的job数，这个数字可根据CPU核心线程数而定，通常可指定为CPU核心线程的1~2倍，数字越大，代表同时进行编译的任务越多，整个编译过程也会越快。如果不指定目标，默认就会实用“droid”目标，build出完整的Android系统镜像。

Android build系统使用了GNU make，这点很让人意外，因为GNU make实在太难用了，Makefile也很难阅读。不过Google的天才工程师引入了模块化、写了很多实用函数，修改/添加起来还算容易。

### Build输出目录结构

所有的编译产物都将位于 /out 目录下，该目录下主要有以下几个子目录：

* out/host/：该目录下包含了host端的工具和库，例如：emulator，adb，aapt等。
* out/target/common/：该目录下包含了针对设备的共用的编译产物，主要是 Java 应用代码和 Java 库。
* out/target/product/<product_name\>/：包含了针对特定设备的编译结果以及平台相关的 C/C++ 库和二进制文件。其中，<product_name\>是具体目标设备的名称。

### Build生成的镜像文件

Build 的产物中最重要的是三个镜像文件，它们都位于 out/target/product/<product_name\>/ 目录下。这三个文件是：

* system.img：包含了 Android OS 的系统文件，库，可执行文件以及预置的应用程序，将被挂载为根分区。
* ramdisk.img：在启动时将被 Linux 内核挂载为只读分区，它包含了 /init 文件和一些配置文件。它用来挂载其他系统镜像并启动 init 进程。
* userdata.img：将被挂载为 /data，包含了应用程序相关的数据以及和用户相关的数据。

另外还有cache.img,启动模拟器时可使用-cache参数来挂载该文件，指定/cache内容。一般来说我们不直接使用userdata.img，而是使用userdata_qemu.data，用来存放用户数据，可读写，android启动后mount到 /data。只有使用-wipe-data参数启动模拟器是时候才会用到userdata.img，它会使用userdata.img的内容覆盖userdata-qemu.img。

## 参考文档
1. [Android官方文档][1]
2. [理解 Android Build 系统][3]
3. [Android编译系统详解(一)——build/envsetup.sh][4]
4. [Android模拟器镜像文件介绍][5]

[1]: http://www.stackoverflow.com
[2]: http://source.android.com
[3]: http://www.ibm.com/developerworks/cn/opensource/os-cn-android-build/index.html
[4]: http://www.cloudchou.com/android/post-134.html
[5]: http://www.devdiv.com/android_-blog-1-1488.html