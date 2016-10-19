---
layout: post
title: "浏览器插件扩展研究"
date: 2013-09-26 08:55:28 +0800
comments: true
categories: 
  android, browser plugin
---
首先说明一下，本文探讨的并非NPAPI和PPAPI这种插件技术，而是一种扩展浏览器功能的方法，并且范围仅限于Android平台。插件技术在软件系统中得到了广泛的应用，程序员熟悉的Eclipse就是因为有着极好的插件扩展机制，几乎可以作为所有的程序设计语言的IDE。一般人熟悉的媒体播放器，也使用到了插件技术，以支持不同的编码和格式。甚至photoshop中的滤镜，也算的上是插件。

百度百科上对插件的定义为：

> 插件(Plug-in,又称addin、add-in、addon或add-on,又译外挂)是一种遵循一定规范的[应用程序接口][1]编写出来的程序。

插件和应用程序的最大区别在于插件必须依赖于应用程序才能发挥自身功能，仅靠插件是无法正常运行的。相反地，应用程序并不需要依赖插件就可以运行。一般而言，插件具有以下特征：

* 独立于应用程序部署，可以被应用程序动态加载。比如Eclipse的插件，可以直接放到plugins目录，就可以被Eclipse识别并动态加载。视频解码器可以单独安装，媒体播放器可以根据视频格式自动加载。 
* 具有自描述元数据，比如插件名称、icon、版本、描述等，能够以统一的方式被应用程序检索到。 
* 遵循应用程序的应用程序接口，插件一般只针对特定的应用程序而开发。

<!--excerpt-->

要实现插件机制，需要解决如下问题：

* 自注册。要求插件能够独立于应用程序记录某些信息，让应用程序能够找到插件，并且加载。比如Windows下可以通过注册表。 
* 动态加载。要求应用程序能够动态的加载插件，而不是静态绑定。比如Java应用程序可以通过反射机制动态。 
* 插件管理。应用程序一般要求能够安装、卸载、禁用插件，显示插件信息。 

### Android插件系统设计

Android是采用现代设计思想而设计的移动操作系统，其天然的组件架构为设计插件系统提供了极大的便利。我将按照如下思路设计插件：

* 插件都设计为Service。通过AIDL定义插件和应用程序之间的应用程序接口，插件实现应用程序定义的接口，应用程序通过Android IPC机制调用接口。插件和应用程序位于不同的进程控件，避免由于插件崩溃而影响到应用程序的运行。 
* 插件可以有UI，也可以是纯实现功能的后台服务。插件编译为单独的apk包，可独立于应用程序部署/卸载。 
* 应用程序通过Android Package Manager发现插件。为了区分应用程序所属的插件，可以定义一个特殊的Intent（比如aexp.intent.action.PICK_PLUGIN），并在插件的AndroidManifest.xml中定义Intent filter。 
* 利用Android的元数据(meta-data)定义插件信息，比如名称、描述、版本等等。
* 应用程序监听 ACTION\_PACKAGE\_ADDED, ACTION\_PACKAGE\_REMOVED和ACTION\_PACKAGE\_REPLACED事件来动态的更新插件列表。 
* 利用Android的Receiver机制，实现应用程序和插件之间的事件交互。 

插件的例子请看参考文档，无法翻墙的同学看[这里][2]。

### 浏览器插件框架设计

Chrome浏览器的extensions技术非常强大，让广大的开发人员使用html/css/js就能扩充Chrome浏览器的功能。Google甚至还打造了chrome web store，上面已经有成千上万个插件，有很多新奇和实用的插件，不得不佩服程序员的创造力。不过Chrome for android尚不支持extensions，在开发Browser，曾考虑过enable chromium的extensions开关，后来有些问题实在无法解决，只能放弃。

为了让插件能够更好的扩充浏览器功能，浏览器也需要开放一些接口和事件给插件，比如：

* tabs操作，包括新建tab、关闭tab，在tab上加载指定的URL 
* bookmark 
* history 

如果想要设计一份完备的浏览器编程API，建议参考[Chrome extensions文档][3]，虽然是以js提供的，但非常完备，具有极高的参考价值。

### 参考文档

1. [MY LIFE WITH ANDROID :-) Plugins][4]

2. [MY LIFE WITH ANDROID :-) plugins with user interface][4]

 [1]: http://baike.baidu.com/view/592964.htm
 [2]: https://app.box.com/s/37j3ds11u9kv0avz6mpc
 [3]: http://developer.chrome.com/extensions/extension.html
 [4]: http://mylifewithandroid.blogspot.com/
