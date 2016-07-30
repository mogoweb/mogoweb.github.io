---
layout: post
title: "using chromium mirror in china"
date: 2016-07-27 08:44:27 +0800
comments: true
categories:
  chromium android 
---

在中国，由于伟大的GFW的存在，使得我们开发人员备受折磨。如果仅仅是使用google搜索，使用普通的梯子，差不多可以应付。但是对于Android和chromium开发人员而言，动辄几十G的代码，没有一个很稳定的梯子实在不行。现代的SCM系统通常采用git，好处不必说了，但有一个重大的问题，不支持断点续传。也就是说，如果有10几个G的源码，下载到90%，突然断了，就前功尽弃了。偏偏GFW又格外努力，好好的梯子，说不定哪天就突然被封，通常越是出名，封锁的越厉害。

因为这几年一直在做浏览器开发相关的工作，对此有深刻体会。我们在公司内部为chromium的git库做了镜像，开发人员可以减少一些痛苦。然而chromium更新的相当频繁，我们做镜像也跟得相当痛苦。

不过现在好了，CodeAurora下的Chromium browser for snapdragon也做了chromium的镜像，而且非常贴心的在中国部署了镜像服务器。这个项目由高通资助，致力于浏览器在高通snapdragon芯片上的优化。如果你是在开发ARM上的浏览器产品，非常具有借鉴意义。

下面说明一下如何通过CodeAurora的镜像服务器下载chromium源码。在此需要说明的是，通过镜像服务器，下载源码是不需要翻墙的，但是在后面，有一些工具链什么的，还是需要从google的服务器上下载，仍然需要翻墙。不过此时的下载量较小，普通的VPN，比如GreenVPN可以应付的过来。

因为chromium browser for snapdragon项目fork自chromium，所以下载、build、安装等方法，和chromium项目基本相同，在此不多说，如果需要，请访问相关文档：

[Environment Setup](https://wiki.codeaurora.org/xwiki/bin/Chromium+for+Snapdragon/Setup/)

[Android Build Instructions](https://chromium.googlesource.com/chromium/src/+/master/docs/android_build_instructions.md)

## 配置CAF镜像
```
git config --global url.https://source.codeaurora.org.insteadOf git://codeaurora.org
git config --global url.https://beijing.source.codeaurora.org.insteadOf git://codeaurora.org
git config --global url.https://beijing.source.codeaurora.org.insteadOf https://source.codeaurora.org
```
注：还有几个可选镜像，可根据实际情况选择上海或香港的镜像服务器，将上面的beijing替换为shanghai或hongkong即可。

## 选择活动的分支

截止写这篇文章为止，有三个主要的分支：

- m52 beta分支
- m46 正式分支
- m42 维护分支，不建议在新项目中采用

考虑到chromium v52已经出来一段时间，趋于稳定，而且经过一段beta期后，很快就会成为正式版本，所以推荐使用m52分支。

## m52下载与编译指南

- 编辑.gclient文件，内容如下：

```
solutions = [
  { "name"        : "src",
    "url"         : "git://codeaurora.org/quic/chrome4sdp/chromium/src.git@refs/remotes/origin/m52",
    "deps_file"   : "DEPS",
    "managed"     : False,
    "safesync_url": "",
  },
]
target_os = ["android"]
```
- 同步代码

```
export GYP_CHROMIUM_NO_ACTION=1 # don't process gyp
gclient sync
cd src
./build/install-build-deps-android.sh
. ./build/android/envsetup.sh
gn gen out/Default --args='target_os="android" is_debug=false'
```
- 生成SWE浏览器

```
ninja -C out/Default swe_browser_apk
```

