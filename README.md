![](./public/banner.jpg)
[中文](https://github.com/racaljk/yvm/blob/master/README.md) | [English](https://github.com/racaljk/yvm/blob/master/README.EN.md)

YVM是用C++写的一个Java虚拟机，现在支持Java大部分功能，以及一个基于"标记清除算法"的垃圾回收器. 不过还有很多bug等待修复。
感兴趣的朋友pull request/fork/star吧！

# 已支持语言特性

[![Codacy Badge](https://api.codacy.com/project/badge/Grade/ae194d72351641089c72e4bef1fda870)](https://app.codacy.com/app/racaljk/yvm?utm_source=github.com&utm_medium=referral&utm_content=racaljk/yvm&utm_campaign=badger)

:-0 高级特性逐步支持中，可以开Issue提议或者直接PR
+ Java基本算术运算，流程控制语句，面向对象。
+ [RTTI](./javalib_src/ydk/test/InstanceofTest.java)
+ [字符串拼接(+,+=符号重载)](./javalib_src/ydk/test/StringConcatenation.java)
+ [异常处理(可输出stacktrace)](./javalib_src/ydk/test/ThrowExceptionTest.java)
+ [创建异步线程](./javalib_src/ydk/test/CreateAsyncThreadsTest.java)
+ [Synchronized(支持对象锁)](./javalib_src/ydk/test/SynchronizedBlockTest.java)
+ [垃圾回收(标记清除算法)](./javalib_src/ydk/test/GCTest.java)

# 使用方法
0. 支持G++7.0,MSVC 2017,CMakeLists直接编译即可
1. 运行
```bash
# --runtime为在YVM上运行的Java程序所必须的运行时,多个值用";"分隔
# 后面指定运行程序的全修饰名，注意不能使用".".如'java/lang/Object'而不是'java.lang.Object'
./yvm --runtime=C:\Users\Cthulhu\Desktop\yvm\javalib ydk/test/QuickSort
```

# 关于JDK
部分JDK类是JVM运行攸关的,但由于JDK比较复杂不便于初期开发,所以这里用重写过的JDK代替,源码参见[javalib_src](./javalib_src)目录,编译后`*.class`文件位于[javalib](./javalib)
目前重写过的JDK类有:
+ `java.lang.String`
+ `java.lang.StringBuilder`
+ `java.lang.Throwable`
+ `java.lang.Math(::random())`
+ `java.lang.Runnable`
+ `java.lang.Thread`

# 运行效果
+ helloworld
![](./public/hw.png)
![](./public/helloworld.png)
+ 快速排序
![](./public/quicksort_java.png)
![](./public/quicksort_console.png)
+ 异常调用栈轨迹
![](./public/stj.png)
![](./public/stc.png)
+ 原生多线程
![](./public/without_synchronized_java.png)
![](./public/without_synchronized_console.png)
+ Synchronized保护下的多线程
![](./public/synchronized_java.png)
![](./public/synchronized_console.png)

# License
Code licensed under the MIT License.


{↓ For developers who want to explore more...}
---
## {Big picture}
![](./public/arch.png)

## {Components}
+ `javalib` YVM的运行时库，重写了部分JDK类。
+ `public` 文档，图片
+ `src/JavaClass.h` `.class`文件在YVM中的类表示
+ `src/Frame.h` 函数调用栈
+ `src/MethodArea.h` 方法区，管理JavaClass
+ `src/JavaHeap.h` 堆管理类，拥有并管理所有实例和数组数据
+ `src/JavaType.h` 虚拟机机对象表示
+ `src/RuntimeEnv.h` 运行时数据。拥有堆，调用栈，和方法区数据
+ `src/CodeExecution.h` 执行引擎，现在是解释执行

## {Public design, private implementation}
1. YVM中类只有一种权威表示,即"package/foo/Bar",任何其他表示如"java.lang.Object","Thread.class"都拒绝加载
2. 根据JVM SPEC,虚拟机执行引擎会直接操纵JDK一些类,如`java/lang/Class`,,`java/lang/String`.为了实现一个"Runnable"的JVM，这里使用重写的同名类(位于javalib)代替。
3.  YVM不支持(未来也不打算)JavaSE6及其之前版本编译器编译的`.class`
4. **不能使用任何JDK的方法**,不过部分运行攸关的JDK类已经重写过。输出可用`ydk.lang.IO`代替。
5. YVM中`float`相等取6位小数精度，`double`相等取12位小数精度
