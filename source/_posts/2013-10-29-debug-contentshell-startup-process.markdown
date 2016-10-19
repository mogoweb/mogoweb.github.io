---
layout: post
title: "调试ContentShell启动过程的方法"
date: 2013-10-29 08:42:23 +0800
comments: true
categories: 
  android, chromium
---

本文探讨的如何调试ContentShell for Android的启动过程，主要是C++部分代码的流程，这对于理解ContentShell的启动流程、render进程是如何创建并启动的有很大的帮助。

理解Contenthell启动流程的最好方法是使用调试器逐步跟踪，不过chromium在很多地方采用了异步方式，单步跟踪相当困难，比较好的方法是在关键代码处打上断点，查看调用栈。由于ContentShell程序一启动，就会执行启动流程，启动主进程和render进程，并加载网页，这时再用gdb attach到进程，往往我们希望跟踪的代码已经执行过去了。一种比较土的方法就是在程序入口处加上sleep语句，在程序暂停时，执行gdb attach到进程，然后在关键代码处设上断点。这种方法有点不好的地方就是sleep的时间不好定，定短了，gdb还没有attach上去，程序已经执行过了。设长了，每次等的时间就长了。

其实chromium开发者已经考虑到这种需求，所以加入了一个命令行参数：wait-for-java-debugger。查看代码，可以发现在ContentShellActivity.java中就有一个方法waitForDebuggerIfNeeded()，其作用就是等待java调试器attach之后才继续往下执行。所以本文介绍的方法就是jdb/gdb组合来调试ContentShell启动过程。

1.  修改content/shell/android/shell_apk/AndroidManifest.xml，在application节点加入android:debuggable="true"，然后编译ContentShell，安装到连接的android设备
2.  从[这里](https://pan.baidu.com/s/1i5seB8h)下载jdb_content_shell脚本，放到build/android目录下。
3.  执行如下命令增加命令行参数： 
    <pre>adb_content_shell_command_line --wait-for-java-debugger</pre>

4.  启动android设备上的ContentShell。应该可以看到界面只是一个空白界面，程序没有接着往下执行。
5.  执行adb_gdb_content_shell命令启动gdb调试，设置断点。
6.  执行jdb_content_shell命令，可以观察到程序在继续往后执行。
