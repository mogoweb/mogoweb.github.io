---
layout: post
title: "在本地服务器上建立chromium镜像"
date: 2014-12-09 21:59:27 +0800
comments: true
categories: chromium
---
Android 5.0发布了好长时间，一直都没有来得及研究。前两周终于将android 5.0源码下载了下来，大致看了一下代码。我所关注的并非Material Design，而是Android 5.0的web引擎。从Android 4.4开始，采用了chromium/blink内核，Android 5.0自然也不例外，只是chromium的版本升级到了37.0.2062.116。想到我的chromium webview项目，仍然采用的是34.0.1847，因此打算也将chromium版本进行一次升级。

因为可恶的GFW，下载代码及其不顺利，虽然有VPN，但是对于几十G的chromium下载量，下载速度太慢，而且git不支持断点续传。在折腾了两三个星期之后，决定在公司的服务器上建立chromium的镜像。不知道从哪个版本开始，gclient开始全面转向git。由于git的天然的分布式特点，这为完全镜像chromium源码库提供了便利。

chromium源码库是由chromium本身加上一堆第三方开源库组成，极其庞大。为此我写了一个脚本，放到了github上，有兴趣的可以访问[chromium_mirror_script](https://github.com/mogoweb/chromium_mirror_script)项目，其中chromium_git_list.txt为所有的源码库列表，来自[https://chromium.googlesource.com/](https://chromium.googlesource.com/)，随着时间的推移，这个列表也可能发生变化。

有了内部的chromium镜像后，我们可以修改.DEPS.git文件，将其中的git_url值替换为本地git地址，这样通过gclient下载，就是从本地镜像服务器上获取代码了。