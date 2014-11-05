---
layout: post
title: "我的macos编程之旅-应用程序关联"
date: 2014-11-05 08:55:21 +0800
comments: true
categories: macos objective-c
---
对objective-c做了基础的入门之后，我就开始了**365看图**的开发。我个人偏好通过实战来掌握新知识，碰到问题后再来阅读相关的资料并解决问题。从苹果开发者网站找到了一个[IKImageViewDemo](https://developer.apple.com/library/mac/samplecode/IKImageViewDemo/Introduction/Intro.html#//apple_ref/doc/uid/DTS10004049)示例，这个例子足够简单，也满足我要开发的**365看图**的基本功能。以IKImageViewDemo作为模板，**365看图**就有了显示、放大、缩小、旋转图片等功能。接下来需要实现一个功能：关联应用程序到图片类型。简单的说，就是在Finder中鼠标右键点击图片文件，能够选择**365看图**打开图片，或者将图片文件拖拽到**365看图**即打开。

### 注册文档类型

在Windows系统中，有一个叫做注册表的东东来集中管理文档类型和能够打开文档的应用程序列表，在Mac系统中，则是通过应用程序包中的info.plist。在info.plist中，声明应用程序能够处理的文档类型(通过文件名后缀)。在**365看图**项目中有一个ImageViewer-Info.plist的文件，内容如下:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>CFBundleDevelopmentRegion</key>
	<string>zh_CN</string>
    <key>CFBundleDocumentTypes</key>
    <array>
        <dict>
            <key>CFBundleTypeExtensions</key>
            <array>
                <string>jpg</string>
                <string>png</string>
            </array>
            <key>CFBundleTypeIconFile</key>
            <string></string>
            <key>CFBundleTypeRole</key>
            <string>Viewer</string>
        </dict>
    </array>
	<key>CFBundleExecutable</key>
	<string>${EXECUTABLE_NAME}</string>
	<key>CFBundleIconFile</key>
	<string></string>
	<key>CFBundleIdentifier</key>
	<string>com.mogoweb.${PRODUCT_NAME:rfc1034identifier}</string>
	<key>CFBundleInfoDictionaryVersion</key>
	<string>6.0</string>
	<key>CFBundleName</key>
	<string>${PRODUCT_NAME}</string>
	<key>CFBundlePackageType</key>
	<string>APPL</string>
	<key>CFBundleShortVersionString</key>
	<string>1.0</string>
	<key>CFBundleSignature</key>
	<string>????</string>
	<key>CFBundleVersion</key>
	<string>1</string>
	<key>LSApplicationCategoryType</key>
	<string>public.app-category.photography</string>
	<key>LSMinimumSystemVersion</key>
	<string>${MACOSX_DEPLOYMENT_TARGET}</string>
	<key>NSHumanReadableCopyright</key>
	<string>Copyright © 2014年 mogoweb. All rights reserved.</string>
	<key>NSMainNibFile</key>
	<string>MainMenu</string>
	<key>NSPrincipalClass</key>
	<string>NSApplication</string>
</dict>
</plist>
```

其中关键的地方在于:

```xml
<key>CFBundleDocumentTypes</key>
    <array>
        <dict>
            <key>CFBundleTypeExtensions</key>
            <array>
                <string>jpg</string>
                <string>png</string>
            </array>
            <key>CFBundleTypeIconFile</key>
            <string></string>
            <key>CFBundleTypeRole</key>
            <string>Viewer</string>
        </dict>
    </array>
```
指明了应用程序能够处理jpg, png为后缀的文件。

### 获取文件名

通过右键点击开启应用程序，app是如何得到这个文件名呢？请看下面的代码:
```objc
- (BOOL) application : (NSApplication*) sender openFile : (NSString*) pathname
{
    NSLog(@"application is asking to open %@", pathname);
    return YES;
}
```
###调试技巧

通过拖拽打开应用程序如何调试(比如在application:openFile中打断点)呢？方法如下：

1. 在Xcode中点击菜单 **Product > Scheme > Edit Scheme** ，在 **Info** 页修改 **Lauch** 选项为：**Wait for executable to be launched**
2. 在 application:openFile 方法中设置断点。
3. 打开 **Finder**, 将图片文件拖拽到您的应用程序上。［注：您可以在XCode的**Project Navigator**中右键点击Porducts节点下的xxx.app，然后点击**Show in Finder**菜单项来定位你的app可执行文件的位置］