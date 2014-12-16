---
layout: post
title: "Ubuntu上从源码编译chromium浏览器"
date: 2014-12-15 15:09:43 +0800
comments: true
categories: chromium ubuntu
---

Ubuntu不仅是一个免费且开源的操作系统，而且其平台也是开放且透明的。每个组件的源码都可以获取到，也可以从源码自己编译软件包。下面就探讨一下在ubuntu下如何获取chromium源码，并编译Chromium Web Browser。

要从ubuntu的仓库下载源码并编译软件包，最方便的就是使用apt-build实用工具。在ubuntu的发布版本中，该实用工具并没有安装，您可以用如下命令安装:

```
sudo apt-get install apt-build
```

接下来就可以使用如下命令下载并build chromium browser了:

```
sudo apt-build install chromium-browser
```

因为我的ubuntu是64位系统(ubuntu 11.04 LTS 64bit)，所以出现了如下错误:

```
E: There are problems and -y was used without --force-yes
E: Failed to process build dependencies
-----> Updating package lists <-----
Ign file: apt-build Release.gpg
Get:1 file: apt-build Release [107 B]                                                                                                                
Ign file: apt-build/main TranslationIndex                                                                                                            
Err file: apt-build/main i386 Packages                                                                                                               
  File not found
Ign file: apt-build/main Translation-en_US                                                                                                           
Ign file: apt-build/main Translation-en                                                                                                              
Get:2 http://mirrors.163.com precise-updates Release.gpg [198 B]                                                                                     
Get:3 http://mirrors.163.com precise Release.gpg [198 B]                                                                                             
Hit http://mirrors.163.com precise-updates Release                                                                                                   
Hit http://mirrors.163.com precise Release                                                                                                           
Hit http://mirrors.163.com precise-updates/restricted Sources                                                                                        
Get:4 http://dl.google.com stable Release.gpg [198 B]                                                                                                
Get:5 http://archive.ubuntu.com precise Release.gpg [198 B]                                                                                          
Hit http://mirrors.163.com precise-updates/main Sources                                                                                              
Hit http://mirrors.163.com precise-updates/universe Sources                                                                                          
Hit http://mirrors.163.com precise-updates/main amd64 Packages                                                                                       
Hit http://mirrors.163.com precise-updates/restricted amd64 Packages                                                                                 
Hit http://mirrors.163.com precise-updates/universe amd64 Packages                                                                                   
Get:6 http://ppa.launchpad.net precise Release.gpg [316 B]                                                                                           
Get:7 http://extras.ubuntu.com precise Release.gpg [72 B]                                                                                            
Hit http://mirrors.163.com precise-updates/main i386 Packages                                                                                        
Hit http://mirrors.163.com precise-updates/restricted i386 Packages                                                                                  
Hit http://mirrors.163.com precise-updates/universe i386 Packages                                                                                    
Hit http://mirrors.163.com precise-updates/main TranslationIndex                                                                                     
Hit http://mirrors.163.com precise-updates/restricted TranslationIndex                                                                               
Hit http://mirrors.163.com precise-updates/universe TranslationIndex                                                                                 
Hit http://mirrors.163.com precise/universe amd64 Packages                                                                                           
Hit http://mirrors.163.com precise/universe i386 Packages                                                                                            
Hit http://dl.google.com stable Release                                                                                                              
Hit http://mirrors.163.com precise/universe TranslationIndex                                                                                         
Hit http://mirrors.163.com precise-updates/main Translation-en                                                                                       
Hit http://mirrors.163.com precise-updates/restricted Translation-en                                                                                 
Hit http://mirrors.163.com precise-updates/universe Translation-en                                                                                   
Hit http://mirrors.163.com precise/universe Translation-en                                                                                           
Hit http://dl.google.com stable/main amd64 Packages                                                                                                  
Hit http://archive.ubuntu.com precise Release                                                                                                        
Hit http://dl.google.com stable/main i386 Packages                                                                                                   
Hit http://ppa.launchpad.net precise Release                                                                                                         
Ign http://dl.google.com stable/main TranslationIndex                                                                                                
Hit http://extras.ubuntu.com precise Release                                                                                                         
Get:8 http://security.ubuntu.com precise-security Release.gpg [198 B]                                                                                
Hit http://archive.ubuntu.com precise/main Sources                                                                                                   
Get:9 http://cn.archive.ubuntu.com precise Release.gpg [198 B]                                                                                       
Hit http://ppa.launchpad.net precise/main Sources                                                                                                    
Hit http://extras.ubuntu.com precise/main Sources                                                                                                    
Hit http://security.ubuntu.com precise-security Release                                                                                              
Hit http://archive.ubuntu.com precise/restricted Sources                                                                                             
Hit http://ppa.launchpad.net precise/main amd64 Packages                                                                                    
Hit http://ppa.launchpad.net precise/main i386 Packages                                                                              
Ign http://ppa.launchpad.net precise/main TranslationIndex                                                                           
Hit http://extras.ubuntu.com precise/main amd64 Packages                                                                             
Hit http://extras.ubuntu.com precise/main i386 Packages                                                                              
Ign http://extras.ubuntu.com precise/main TranslationIndex                                                                           
Hit http://security.ubuntu.com precise-security/restricted Sources                                                
Hit http://security.ubuntu.com precise-security/main Sources                                                      
Hit http://security.ubuntu.com precise-security/universe Sources                                                                   
Hit http://cn.archive.ubuntu.com precise Release                                                                                   
Hit http://security.ubuntu.com precise-security/main amd64 Packages                                                
Hit http://security.ubuntu.com precise-security/restricted amd64 Packages                                                                            
Hit http://security.ubuntu.com precise-security/universe amd64 Packages                                                                              
Hit http://security.ubuntu.com precise-security/main i386 Packages                                                                                   
Hit http://security.ubuntu.com precise-security/restricted i386 Packages                                                                             
Hit http://security.ubuntu.com precise-security/universe i386 Packages                                                                               
Hit http://security.ubuntu.com precise-security/main TranslationIndex                                                                                
Hit http://security.ubuntu.com precise-security/restricted TranslationIndex                                                                          
Hit http://security.ubuntu.com precise-security/universe TranslationIndex                                                                            
Ign http://dl.google.com stable/main Translation-en_US                                                                                               
Hit http://security.ubuntu.com precise-security/main Translation-en                                                                                  
Ign http://dl.google.com stable/main Translation-en                                                                                                  
Hit http://cn.archive.ubuntu.com precise/main Sources                                                                                                
Hit http://security.ubuntu.com precise-security/restricted Translation-en                                                                            
Hit http://cn.archive.ubuntu.com precise/restricted Sources                                                                                          
Hit http://cn.archive.ubuntu.com precise/main amd64 Packages                                                                                         
Hit http://security.ubuntu.com precise-security/universe Translation-en                                                                              
Hit http://cn.archive.ubuntu.com precise/restricted amd64 Packages                                                                                   
Hit http://cn.archive.ubuntu.com precise/main i386 Packages                                                                                          
Hit http://cn.archive.ubuntu.com precise/restricted i386 Packages                                                                                    
Hit http://cn.archive.ubuntu.com precise/main TranslationIndex                                                                                       
Hit http://cn.archive.ubuntu.com precise/restricted TranslationIndex                                                                                 
Ign http://ppa.launchpad.net precise/main Translation-en_US                                                                                          
Hit http://cn.archive.ubuntu.com precise/main Translation-en                                                                                         
Ign http://extras.ubuntu.com precise/main Translation-en_US                                                                                          
Ign http://extras.ubuntu.com precise/main Translation-en                                                                                             
Hit http://cn.archive.ubuntu.com precise/restricted Translation-en                                                                                   
Ign http://ppa.launchpad.net precise/main Translation-en                                                                                             
Fetched 1,576 B in 10s (147 B/s)                                                                                                                     
W: Failed to fetch file:/var/cache/apt-build/repository/dists/apt-build/main/binary-i386/Packages  File not found
```

从网上找到了答案，解决方法就是指定配置文件中的架构：修改*/etc/apt/sources.list.d/apt-build.list*文件，将其中的

```
deb file:/var/cache/apt-build/repository apt-build main
```

替换为:

```
deb [arch=amd64] file:/var/cache/apt-build/repository apt-build main
```

接下来就是下载源码，再经过漫长的build过程，生成并安装Chromium Web Browser。版本信息如下图:

![chrome://version](http://7d9je4.com1.z0.glb.clouddn.com/mogoweb_2014_chromium_for_linux_37.0.2062.120_info.png)

下载的源码位于*/var/cache/apt-build/build/*下：

![](http://7d9je4.com1.z0.glb.clouddn.com/mogoweb_2014_chromium_ubuntu_source_info.png)

从上图中可以看到源码包的大小只有200多M，而我们从chromium开源项目下的源码通常有7、8个G，主要原因是这里下的源码是不带git和svn信息的，是纯粹的源代码。