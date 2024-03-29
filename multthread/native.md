# native

## 1，关于native关键字 <a id="a1"></a>

想必读者已经了解过native关键字了。这里笔者就大致囊括一下，被native关键字修饰的方法叫做本地方法，本地方法和其它方法不一样，本地方法意味着和平台有关，因此使用了native的程序可移植性都不太高。另外native方法在JVM中运行时数据区也和其它方法不一样，它有专门的本地方法栈。native方法主要用于加载文件和动态链接库，由于Java语言无法访问操作系统底层信息（比如：底层硬件设备等），这时候就需要借助C语言来完成了。被native修饰的方法可以被C语言重写。

## 2，使用native关键字 <a id="a2"></a>

### 2.1，使用步骤 <a id="a21"></a>

* Java程序中声明native修饰的方法，类似于abstract修饰的方法，只有方法签名，没有方法实现。编译该java文件，会产生一个.class文件。
* 使用javah编译上一步产生的class文件，会产生一个.h文件。
* 写一个.cpp文件实现上一步中.h文件中的方法。
* 将上一步的.cpp文件编译成动态链接库文件.dll。
* 最后就可以使用System或是Runtime中的loadLibrary\(\)方法加载上一步的产生的动态连接库文件了。

### 2.2，案例 <a id="a22"></a>

为了更好理解，该案例的所有都在文件在 D:\JNI\ 目录下。

#### 2.2.1 编写.java文件 <a id="a221"></a>

![](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) HelloWorld.java

#### 2.2.2 编译.java文件 <a id="a222"></a>

在AMD中编译该程序 javac HelloWorld.java ，就会产生一个HelloWorld.class文件。

#### 2.2.3 获得.h文件 <a id="a223"></a>

将第二步中产生的字节码文件，通过 javah -jni HelloWorld 就会产生一个HelloWorld.h文件。

我们用记事本打开HelloWorld.h文件[![&#x590D;&#x5236;&#x4EE3;&#x7801;](http://common.cnblogs.com/images/copycode.gif)](javascript:void%280%29;)

```text
/* DO NOT EDIT THIS FILE - it is machine generated */
#include <jni.h>
/* Header for class HelloWorld */

#ifndef _Included_HelloWorld
#define _Included_HelloWorld
#ifdef __cplusplus
extern "C" {
#endif
/*
 * Class:     HelloWorld
 * Method:    h
 * Signature: ()V
 */
JNIEXPORT void JNICALL Java_HelloWorld_h
  (JNIEnv *, jobject);

#ifdef __cplusplus
}
#endif
#endif
```

[![&#x590D;&#x5236;&#x4EE3;&#x7801;](http://common.cnblogs.com/images/copycode.gif)](javascript:void%280%29;)

可以看出，在HelloWorld.java文件中的h\(\)方法已经变成了 JNIEXPORT void JNICALL Java\_HelloWorld\_h \(JNIEnv \*, jobject\); ，方法名是原来的 包名\_类名\_方法名 。在该文件中还引用了 jni.h 文件。

#### 2.2.4 编写hello.cpp文件 <a id="a224"></a>

编写hello.cpp文件的方式有许多，可以利用Visual Studio软件，因为最后需要生成dll文件，因此在下载Visual Studio之前应该查一查版本是否能够生成自己电脑需要的dll版本（32位dll或64为dll）。这里读者下载的是vs2013，该版本既可以生成32的dll，由可以生成64位的dll。关于vs2013生成dll可以参考[Visual Studio 2013生成64位dll](https://jingyan.baidu.com/article/380abd0a4640171d90192c9e.html)

因为我们在第一步中调用的文件名称为hello，所以这里的.cpp文件必须为 hello.cpp 文件。这里笔者的文件如下：[![&#x590D;&#x5236;&#x4EE3;&#x7801;](http://common.cnblogs.com/images/copycode.gif)](javascript:void%280%29;)

```text
// hello.cpp : 定义 DLL 应用程序的导出函数。
//

#include "stdafx.h"
#include "HelloWorld.h"

JNIEXPORT void JNICALL Java_HelloWorld_h(JNIEnv *, jobject) {
    printf("Hello! ");//打印信息
}
```

[![&#x590D;&#x5236;&#x4EE3;&#x7801;](http://common.cnblogs.com/images/copycode.gif)](javascript:void%280%29;)

可以看出引入了 HelloWorld.h 文件，所以hello.cpp 文件应该和 HelloWorld.h文件在同一个目录下面。如果读者现在编译hello.cpp文件会报错 “jni.h”: No such file or directory 。在 HelloWorld.h 文件中我们引入了 jni.h文件，所以也应该把 jni.h 文件放到同一级目录下面，关于这个文件和相关的文件读者可以到JDK的安装目录下面的include下面查找，更多信息可以[查看JDk、JRE、JVM的关系](http://www.cnblogs.com/HDK2016/p/6715035.html)。还应该把 HelloWorld.h 文件中的 \#include &lt;jni.h&gt; 改为 \#include "jni.h" 。最后生成 hello.dll 文件就可以了

#### 2.2.5 部署hello.dll文件 <a id="a225"></a>

我们使用了 System.loadLibary\("hello"\); 加载动态链接库，这个加载路径是按照java.libary.path进行查询的，读者可以根据System.getProperty\("java.libary.path"\)验证，该路径就是环境变量中的path路径。网上有好多说直接把hello.dll仍在 C:\Windows\System32 路径下。不过笔者建议，先应该查看自己环境变量path的值，那么把hello.dll放到path中配置的第一个路径下。

#### 2.2.6 运行HelloWorld.class文件 <a id="a226"></a>

我们回到  D:/JNI  路径下，使用  java HelloWorld  就成功调用动态连接库了。

![](https://images2015.cnblogs.com/blog/1070689/201707/1070689-20170724015523649-1717973585.png)

笔者的控制台上成功打印了hello!。笔者对这里加载的理解，就是利用反射机制，在运行的时候找到hello.dll文件并且解析，根据动态链接库中的文件名称创建出对象和方法，然后我们就可以利用对象调用方法了。上面的HelloWorld.java文件，创建动态链接库和调用方法都在同一个类中，这样的话一个只需要使用这个类的对象调用方法就可以通过编译和运行了。如果我们引入的是被人的.cpp文件，那么根据.cpp文件中的方法名，在需要的地方做适当调整就可以调用了。

