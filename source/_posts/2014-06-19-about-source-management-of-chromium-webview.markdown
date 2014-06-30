---
layout: post
title: "关于Chromium WebView项目的源代码管理"
date: 2014-06-19 10:53:25 +0800
comments: true
categories: chromium, android
---

chromium项目可谓开源项目集大成者，里面使用的第三方开源库数目高达60+。为了应对庞大的代码库管理问题，chromium项目引入了[gclient](http://code.google.com/p/gclient/)，一套开源的代码管理方案。gclient是一套python脚本，用来简化多个git/svn源码库的管理。本文不探讨gclient的使用、配置，而是谈谈chromium webview项目是如何组织源码库的。

由于历史原因，chromium项目本身及许多第三方开源库采用的是SVN源码管理系统。SVN采用的是集中式管理，不如git那样能否方便的clone代码库。虽然google后来使用了git-svn等手段，为chromium和所有第三方开源库都做了git镜像库，但是如果需要获取某个release分支的代码，还是需要走svn（google也在不断改进，期望不久的将来，可以通过git获取某个release版本的源码）。
<!--more-->
基于chromium的开源项目有三种组织源码方案：

1. [CEF](http://code.google.com/p/chromiumembedded)开源项目本身没有放chromium源码，只是提供了一套脚本，从google的服务器上去获取对应的源码，对chromium所做的修改以patch提交，同样提供了脚本应用这些patch。有了自动化脚本，开发人员其实也不关心代码托管在何处，项目组也可以省掉同步chromium源码的工作。但这种方案对项目组人员要求较高，必须要有扎实的脚本语言开发功底，每次对chromium的修改都必须做patch，也很考研开发人员的细致与耐心程度。
2. [Chromium Browser for Snapdragon Project](https://www.codeaurora.org/xwiki/bin/Chromium+for+Snapdragon/WebHome)开源项目为所有chromium及第三方开源库都做了git镜像，对chromium的修改直接提交到这些镜像库上。这种方案的优点是可控，非常适合企业内部开发（非开源），在企业内部部署，开发人员就无需每人都从外部下载，涉及到技术保密的提交也无需让外界知晓。但这种方案的缺点也是非常明显的，为60+个git库做镜像，有一定的工作量，还要定期同步，需要处理内部提交与外部提交合并问题。
3. [crosswalk](https://crosswalk-project.org/)开源项目只镜像了chromium/blink/v8等少量几个会修改的开源库，其它的库则从google的网站上取。这种方案在维护与便捷之间做了最佳平衡，因为chromium项目使用了大量的第三方开源库，但我们极少有需要去修改这些第三方开源库，处于定制和优化的目的，我们通常也只在chromium/blink库上修改代码。

Chromium WebView项目采用了第三种方案，由于chromium和blink这两个影响库太庞大，屡次上传到github都失败，于是改成从crosswalk项目clone相关的代码库。所以在我的名下多了chromium-crosswalk, blink-crosswalk, v8-crosswalk这几个库，其实它们都是为chromium webview项目服务的。