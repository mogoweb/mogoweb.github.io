---
layout: post
title: "WebKit的JavaScript对象扩展"
date: 2012-01-12 08:59:29 +0800
comments: true
categories: 
    android, webkit
---
本文的内容主要参考网上收集的资料，不过在Android 4.0 webkit上做扩展时，碰到一些问题，觉得有必要记录下来。

所谓扩展JavaScript对象，就是增加一个JS对象，但它并没有定义在标准的JS对象集合中。如果网页中包含了扩展的JS对象，使用普通的浏览器就会报JS错误。

下面以添加HelloObject对象为例说明具体步骤，该对象具有description属性：

+ 添加HelloObject.h, HelloObject.cpp, HelloObject.idl文件，简单起见，将这三个文件放到Source/WebCore/page目录下。

```cpp
#ifndef HelloObject_h   
#define HelloObject_h 
 
#include <wtf/PassRefPtr.h>  
#include <wtf/RefCounted.h>
 
#include "PlatformString.h"
 
namespace WebCore { 
 
class HelloObject : public RefCounted<HelloObject> {   
public:   
    static PassRefPtr<HelloObject> create() { return adoptRef(new HelloObject()); }

    String description() const;
private:
    HelloObject();
};

} // namespace WebCore

#endif // HelloObject_h
```

<font>HelloObject.h</font>

```cpp
#include "config.h"
#include "HelloObject.h"

namespace WebCore { 

HelloObject::HelloObject()   
{   
}

String HelloObject::description() const
{   
    return "Hello Object";   
}

} // namespace WebCore
```

<font>HelloObject.cpp</font>

```idl
module window { 

    interface [OmitConstructor] HelloObject {   
        readonly attribute DOMString description;   
    };
}
```

<font>HelloObject.idl</font>

+ 修改Source/WebCore/page/下的DOMWindow.h文件，添加如下声明：

```cpp
class HelloObject;

public:
    HelloObject* helloObject() const;
    HelloObject* optionalHelloObject() const { return m_helloObject.get(); }
private:
    mutable RefPtr<HelloObject> m_helloObject;
```

+ 修改Source/WebCore/page/下的DOMWindow.cpp文件，添加接口实现:

```cpp
HelloObject* DOMWindow::helloObject() const
{   
    if (!m_helloObject)   
        m_helloObject = HelloObject::create();   
    return m_helloObject.get();   
}
```

<font>在DOMWindow::clear()函数中添加一行语句：</font>

```cpp
m_helloObject = 0;
```

+ 修改DOMWindow.idl文件，添加:

```idl
attribute [Replaceable] HelloObject helloObject;
```

+ 接下来需要修改编译系统，让android编译系统编译新增的文件：

首先修改Source/WebCore/Android.mk，增加page/HelloObject.cpp到**LOCAL\_SRC\_FILES**变量，其次需要修改Source/WebCore/Android.derived.v8bindings.mk，增加
$(intermediates)/bindings/V8HelloObject.h到**GEN**变量。（注：这个是必须的，否则就不会根据HelloObject.idl生成V8HelloObject.h文件，在编译时会出错，这也
是折腾了半天得出的成果）

至此，工作基本上完成，待webkit重新编译后，可以用如下的网页进行验证：

```html
<html>
<body>
<script type="text/javascript">
document.write("<pThis is from HelloObject: ");
document.write(helloObject.description + "</p>");
</script>
</body>
</html>
```

[参考资料]

1. [webkit的js对象扩展（一）——binding方式创建自定义对象（单实例）][1]

2. [android 上 webkit js 本地扩展之全局本地对象实现步骤][2]

 [1]: http://blog.csdn.net/perfectpdl/article/details/7051129
 [2]: http://blog.csdn.net/perfectpdl/article/details/7058084

