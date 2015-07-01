---
layout: post
title: "如何打印gyp构建系统中的变量值"
date: 2015-06-30 14:16:51 +0800
comments: true
categories: 
  chromium
---

chromium项目使用了gyp构建系统，gyp的全称是"Generate Your Projects"。gyp构建系统使用的并不广泛，目前已知的只有chromium开源项目采用了这一构建系统。官方虽然也有一些文档，但都是一些比较基础的指南，总体来说缺少详细的文档来指导开发人员在项目中使用。
因为研究chromium的缘故，所以对gyp构建系统也有一些了解，在使用中也碰到一些问题，本文所要探讨的是如何输出gyp的变量值。这个问题的场景是这样的：在gyp中定义了变量，但是经过多重gypi包含，在别的gyp文件中可能对变量值进行了赋值。处于调试目的，我们希望输出gyp变量的值。

方法看起来很简单，但也摸索了很久。方法如下，在gyp中合适的位置加入如下代码：

~~~
'variables': {
  'mytest': '<!(echo <(variable) 1>&2)',
}
~~~

其中'mytest'是我随便定义的一个变量值，variable是希望打印的gyp变量。比如以下语句：

~~~
'variables': {
  'mytest': '<!(echo <(OS) 1>&2)',
}
~~~

输出如下结果：

~~~
Updating projects from gyp files...
android
gyp: Call to 'echo android 1>&2' returned exit status 0.
~~~

从上面的输出可以看出，OS的变量值为android。

