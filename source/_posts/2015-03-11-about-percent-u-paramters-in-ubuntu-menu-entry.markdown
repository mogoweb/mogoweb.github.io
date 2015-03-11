---
layout: post
title: "关于Ubuntu菜单项中的%U参数"
date: 2015-03-11 16:05:57 +0800
comments: true
categories: Ubuntu chromium
---
今天在研究chromium deb打包的时候，发现Google Chrome deb包中的/usr/share/applications/google-chrome.desktop文件定义入口为：
```
Exec=/usr/bin/google-chrome-stable %U
```
这个%U参数是做什么用的呢？查找了一下资料，有如下描述：
```
%u A single URL. Local files may either be passed as file: URLs or as file path.

%U A list of URLs. Each URL is passed as a separate argument to the executable program. Local files may either be passed as file: URLs or as file path.
```
也就是说，加入了%U参数，可以传递多个URL给Chrome浏览器，浏览器会依次打开各URL。比如您在文件管理器中选择了多个html文件，然后在鼠标右键菜单中点击"Open with Google Chrome"，Chrome浏览器就会依次打开所选的网页。

以下是Exec支持的参数说明，来自[http://standards.freedesktop.org/desktop-entry-spec/latest/ar01s06.html](http://standards.freedesktop.org/desktop-entry-spec/latest/ar01s06.html):
```
Code	Description
%f	A single file name, even if multiple files are selected. The system reading the desktop entry should recognize that the program in question cannot handle multiple file arguments, and it should should probably spawn and execute multiple copies of a program for each selected file if the program is not able to handle additional file arguments. If files are not on the local file system (i.e. are on HTTP or FTP locations), the files will be copied to the local file system and %f will be expanded to point at the temporary file. Used for programs that do not understand the URL syntax.
%F	A list of files. Use for apps that can open several local files at once. Each file is passed as a separate argument to the executable program.
%u	A single URL. Local files may either be passed as file: URLs or as file path.
%U	A list of URLs. Each URL is passed as a separate argument to the executable program. Local files may either be passed as file: URLs or as file path.
%d	Deprecated.
%D	Deprecated.
%n	Deprecated.
%N	Deprecated.
%i	The Icon key of the desktop entry expanded as two arguments, first --icon and then the value of the Icon key. Should not expand to any arguments if the Icon key is empty or missing.
%c	The translated name of the application as listed in the appropriate Name key in the desktop entry.
%k	The location of the desktop file as either a URI (if for example gotten from the vfolder system) or a local filename or empty if no location is known.
%v	Deprecated.
%m	Deprecated.
```
