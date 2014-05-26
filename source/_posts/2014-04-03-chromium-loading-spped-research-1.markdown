---
layout: post
title: "Chromium网络加载速度研究(2)"
date: 2014-04-03 18:10:06 +0800
comments: true
categories: chromium, android
---
本文主要内容翻译自chromium的文档[NetLog: Chrome’s network logging system][1]，如果您觉得文章写的不明白，请参看原文。

通过<<[Chromium网络加载速度研究(1)][2]>>这篇文章的分析，可以得出结论，云端加速是高富帅的玩法。对于个人开发者和小公司而言，是没有那个实力去部署强大的数据中心的。所以接下来还得继续研究，看看有没有其它的途径提高网页加载速度。在着手优化之前，我们需要一个profiling手段找出瓶颈，有目的的优化。另一方面，也需要考虑如何评估优化的效果。如果没有一个客观的评估，优化方案是否有效心中就没有底。谈到Profiling，大多数人估计都会想到log大法，就是在程序的一些关键位置打上log。log通常包括时间、事件、状态等。在chromium中，开发人员早就预料了此类需求，设计了一套强大的NetLog。下面就分析一下chromium中NetLog的设计、实现及其用法。
<!--more-->
##概述
NetLog是一套为Chrome网络栈而设计的事件日志机制，帮助调试问题和分析性能。它遵照"抓取->转储->分析"的工作流程，这点和某些工具，如tcpdump有些相像。

典型的使用场景为：

  1. 用户启用网络日志
  2. 用户重现问题
  3. 用户将日志上传到BUG报告系统
  4. 开发人员分析日志，定位问题

在chrome网络栈内部，在一些关键位置都加入了日志机制，产生事件。观察者监控事件流，对数据进行处理。Chrome中已有的观察者有：

* (net_log_logger.cc) 将事件流序列化到文件
* (net_internals_ui.cc) 将事件传递到Javascript应用程序chrome://net-internals,改应用程序可以将数据可视化展现，也可以导出到文件

##NetLog的设计理念：

* NetLog缺省关闭
* NetLog关闭时不会影响性能
* Chrome官方发行版本支持NetLog
* 事件容易序列化/反序列化到磁盘
* NetLog仅仅用作记录日志

也就是说，日志如何序列化，如何分析并非NetLog需要关心的事情。此外，不要通过NetLog获取内部网络信息，比如为NetLog增加一个观察者得到内部网络状态信息。正确的做法是为NetworkDelegate增加接口，进行充分的测试，然后增加文档进行说明。

##NetLog事件的结构定义

C++中NetLog事件用net::NetLog::Entry定义，这是在内存中的表示，非常容易序列化到JSON。

不到必要的时候，NetLog事件并不会序列化到JSON。在需要序列化事件时，JSON格式如下：

 字段 | 类型 | 描述 
 :--:|:--:|:--
 time | string |事件发生的时间（毫秒)，这是时间滴答数而不是unix时间戳. 尽管这是一个数字域，使用字符串表示是避免精度损失
 type | number |事件类型的ID，枚举值定义在[net_log_event_type_list.h](http://src.chromium.org/viewvc/chrome/trunk/src/net/base/net_log_event_type_list.h?view=markup)
 source | object |产生事件的实例，比如可以标识出某个特定的URLRequest
 phase | number |BEGIN, END, NONE三个枚举值之一
 params | object |可选字段 




[1]: http://www.chromium.org/developers/design-documents/network-stack/netlog
[2]: http://mogoweb.github.io/blog/2014/03/18/chromium-loading-speed-research-0/