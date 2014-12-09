---
title: "SublimeText配置"
layout: post
date: 2013-08-10 21:27:02 +0800
categories: 开发工具
---
在我的一台开发机上在ubuntu系统下使用eclipse，经常会出现死机，不得不寻求其它的代码编辑器。windows下大名鼎鼎的source insight放到linux下就状况不断。后看到chromium项目推荐了sublime text这款编辑器，用起来感觉还不错。下面记录一下使用过程中的一些设置，网上各种资料比较凌乱，所以就稍微整理一下，供自己今后翻阅。

### 1. 过滤不相关的文件

可以针对某个特定的工程，进入菜单**project | Edit Project**，编辑当前工程的sublime-project文件，加入两个过滤器，*folder\_exclude\_patterns*和*file\_exclude\_patterns*，如下所示:

<pre>"folders":
[
    {
        "path":"/home/alex/android/source/android4.2",
        "file_exclude_patterns":
        [
            "*.o"
        ],
        "folder_exclude_pattern":
        [
            ".repo"
        ]
    }
]</pre>

### 2. 模拟eclipse的快捷键

在eclipse中，有几个快捷键用的非常多，分别为：

<pre>CTRL+TAB 头文件和c/c++文件之间切换
CTRL+SHIFT+R 对话框中输入文件名（有联想功能），快速打开文件
CTRL+o 输入方法（函数）名，可快速定位到方法（函数）
CTRL+l 输入行号，快速定位到指定行
CTRL+h 打开文件搜索对话框</pre>

可以修改sublime text的Key Bindings达到模拟eclipse快捷键的效果，有两个配置项可以修改，一个是Key Bindings-Default，一个是Key Bindings-User，最好修改后一个配置文件。进入菜单Preferences | Key Bindings &#8211; User，加入如下行：

<pre>[
{ "keys": ["ctrl+shift+r"], "command": "show_overlay", "args": {"overlay": "goto", "show_files": true} },
{ "keys": ["ctrl+o"], "command": "show_overlay", "args": {"overlay": "goto", "text": "@"} },
{ "keys": ["ctrl+l"], "command": "show_overlay", "args": {"overlay": "goto", "text": ":"} },
{ "keys": ["ctrl+t"], "command": "show_overlay", "args": {"overlay": "goto", "text": "#"} },
{ "keys": ["ctrl+tab"], "command": "switch_file", "args": {"extensions": ["cpp", "cxx", "cc", "c", "hpp", "hxx", "h", "ipp", "inl", "m", "mm"]} }
]</pre>

### 3. 针对项目的代码风格设置

chromium的项目的代码缩进为两个空格，而其它项目的代码缩进一般为4个空格，所以我就希望针对特定的项目进行代码风格设置，方法是编辑当前工程的sublime-project文件，加入如下行（和folders项平级）：

<pre>"settings":
{
"tab_size": 2,
"translate_tabs_to_spaces": true,
"trim_trailing_white_space_on_save": true,
"draw_white_space": "all"
}</pre>

###  4. 插件集合

sublime_text的插件扩展机制非常强大，已经有很多人出于不同的需求，开发出各种插件。首先说一下插件的安装。

要安装插件，最好是先安装插件包管理器，最简单的方法是在sublime_text控制台(可以通过**ctrl+`**快捷键或View > Show Console菜单打开)，在控制台中粘贴如下python代码：

```python
import urllib2,os,hashlib; h = '7183a2d3e96f11eeadd761d777e62404' + 'e330c659d4bb41d3bdf022e94cab3cd0'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); os.makedirs( ipp ) if not os.path.exists(ipp) else None; urllib2.install_opener( urllib2.build_opener( urllib2.ProxyHandler()) ); by = urllib2.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); open( os.path.join( ipp, pf), 'wb' ).write(by) if dh == h else None; print('Error validating download (got %s instead of %s), please try manual install' % (dh, h) if dh != h else 'Please restart Sublime Text to finish installation')
```

按ctrl+shit+p显示命令列表，在其中输入install，选择Package Control: Install package，接着就会出现插件列表，选择你所需要的插件即可。