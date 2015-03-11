---
layout: post
title: "Ubuntu 14.04 VirtualBox启动问题之解决"
date: 2015-03-11 08:54:49 +0800
comments: true
categories:Linux 
---

开发机上的Ubuntu从12.10升级到14.04后，发现VirtualBox无法启动，有如下提示
```
Setting up virtualbox (4.3.10-dfsg-1ubuntu3) ...
 * Stopping VirtualBox kernel modules                   [ OK ] 
 * Starting VirtualBox kernel modules
     * No suitable module for running kernel found
                                                        [fail]
invoke-rc.d: initscript virtualbox, action "restart" failed.
```
解决方法如下:
```
sudo apt-get purge virtualbox
sudo apt-get install linux-generic linux-image-generic linux-headers-generic linux-signed-generic
sudo apt-get install virtualbox
```
安装irtualbox的信息如下:
```
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following extra packages will be installed:
  virtualbox-dkms virtualbox-qt
Suggested packages:
  virtualbox-guest-additions-iso vde2
The following NEW packages will be installed:
  virtualbox virtualbox-dkms virtualbox-qt
0 upgraded, 3 newly installed, 0 to remove and 29 not upgraded.
Need to get 0 B/20.5 MB of archives.
After this operation, 84.1 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
WARNING: The following packages cannot be authenticated!
  virtualbox virtualbox-dkms virtualbox-qt
Install these packages without verification? [y/N] y
Selecting previously unselected package virtualbox.
(Reading database ... 276049 files and directories currently installed.)
Preparing to unpack .../virtualbox_4.3.10-dfsg-1ubuntu3_amd64.deb ...
Unpacking virtualbox (4.3.10-dfsg-1ubuntu3) ...
Selecting previously unselected package virtualbox-dkms.
Preparing to unpack .../virtualbox-dkms_4.3.10-dfsg-1ubuntu3_all.deb ...
Unpacking virtualbox-dkms (4.3.10-dfsg-1ubuntu3) ...
Selecting previously unselected package virtualbox-qt.
Preparing to unpack .../virtualbox-qt_4.3.10-dfsg-1ubuntu3_amd64.deb ...
Unpacking virtualbox-qt (4.3.10-dfsg-1ubuntu3) ...
Processing triggers for man-db (2.6.7.1-1ubuntu1) ...
Processing triggers for ureadahead (0.100.0-16) ...
Processing triggers for hicolor-icon-theme (0.13-1) ...
Processing triggers for shared-mime-info (1.2-0ubuntu3) ...
Processing triggers for bamfdaemon (0.5.1+14.04.20140409-0ubuntu1) ...
Rebuilding /usr/share/applications/bamf-2.index...
Processing triggers for desktop-file-utils (0.22-1ubuntu1) ...
Processing triggers for mime-support (3.54ubuntu1.1) ...
Processing triggers for gnome-menus (3.10.1-0ubuntu2) ...
Setting up virtualbox (4.3.10-dfsg-1ubuntu3) ...
 * Stopping VirtualBox kernel modules                                                                                                                                                                 [ OK ] 
 * Starting VirtualBox kernel modules                                                                                                                                                                         * No suitable module for running kernel found
                                                                                                                                                                                                      [fail]
invoke-rc.d: initscript virtualbox, action "restart" failed.
Processing triggers for ureadahead (0.100.0-16) ...
Setting up virtualbox-dkms (4.3.10-dfsg-1ubuntu3) ...
Loading new virtualbox-4.3.10 DKMS files...
First Installation: checking all kernels...
Building only for 3.13.0-46-generic
Building initial module for 3.13.0-46-generic
Done.

vboxdrv:
Running module version sanity check.
 - Original module
   - No original module exists within this kernel
 - Installation
   - Installing to /lib/modules/3.13.0-46-generic/updates/dkms/

vboxnetadp.ko:
Running module version sanity check.
 - Original module
   - No original module exists within this kernel
 - Installation
   - Installing to /lib/modules/3.13.0-46-generic/updates/dkms/

vboxnetflt.ko:
Running module version sanity check.
 - Original module
   - No original module exists within this kernel
 - Installation
   - Installing to /lib/modules/3.13.0-46-generic/updates/dkms/

vboxpci.ko:
Running module version sanity check.
 - Original module
   - No original module exists within this kernel
 - Installation
   - Installing to /lib/modules/3.13.0-46-generic/updates/dkms/

depmod....

DKMS: install completed.
 * Stopping VirtualBox kernel modules         [ OK ]
 * Starting VirtualBox kernel modules         [ OK ]
Setting up virtualbox-qt (4.3.10-dfsg-1ubuntu3) ...
```
这个时候virtualbox可以启动了。但是建立了虚拟机后，启动虚拟系统时提示：
```
VT-x is disabled in the BIOS 
```
解决方法:
```
重启系统
长按 DEL 进入 BIOS
方向键切到 BIOS Features
选中如下选项
Intel Virtualization Technology

选择 Enable
按下 F10 保存并重启
```
因为各个机器的bios设置并不相同，请根据自己电脑的设置查找“Intel Virtualization Technology”, 大部分电脑默认是Disable的。