# JNI、NDK、CMake和Android JNI学习笔记
在Android中使用JNI时，会遇到以下概念。本文将一一介绍这些知识点：
* `JNI`是什么？
* `NDK`是什么？
* `CMake`是什么？
* `Android JNI`如何使用？

## 1 NDK是什么
NDK是一个工具包，详细可看google的[NDK介绍][https://developer.android.google.cn/ndk/guides/]文档。工具包主要包括以下两个模块：
* 编译c、c++的工具
* 提供so库和头文件供编译使用

### 1.1 编译c、c++的工具
> 编译c、c++代码，主要是运行ndk-build脚本

1. 脚本位置。下载NDK后，可以发现在NDK目录下有一个ndk-build脚本。

![ndk-build脚本位置][file:///Users/hh/AndroidStudioProjects/Go2019/Go/Android/jnifile/ndk_build_position.png]

2. 运行脚本。运行脚本时，相当于运行以下命令。[google ndk-build 文档][https://developer.android.google.cn/ndk/guides/ndk-build]
```
$GNUMAKE -f <ndk目录位置>/build/core/build-local.mk
<parameters>
```
其中$GNUMAKE 指向 `GNU Make 3.81` 或`更新版本`。也就是说，使用ndk-build脚本编译c、c++时，最终是使用make工具去编译c和c++代码的。make执行的文件为`build-local.mk`， 该文件包含了编译c、c++的项目、编译器和编译链接的库等配置信息。


### 1.2 提供so库和头文件供编译使用
> 提供了Android平台相关的so库、c、c++标准so库和so库的头文件。

1. 提供c、c++标准src文件、so库和头文件。[google C++库文档][https://developer.android.google.cn/ndk/guides/cpp-support]。这些库的位置为：

    ![ndk-build脚本位置][file:///Users/hh/AndroidStudioProjects/Go2019/Go/Android/jnifile/cpp_library_position.png]

    1. Android 平台提供很小的 C++ 运行时支持库 (libstdc++)。但该库不包括以下支持：
         * 标准 C++ 库支持（几个无关紧要的标头除外）。
         * C++ 异常支持
         * RTTI 支持
    2. 如果你想支持标准 C++ 库支持、C++ 异常支持和RTTI 支持, 可使用以下库，NDK也提供了这些so。要使用这些so库，可在 `Application.mk`文件中配置（只能为应用选择一个运行时，并且只能在 `Application.mk` 中选择）例如：
    ```
    //库名称为下表中的 名称，如gnustl_static
    APP_STL := 库名称
    ```

     名称 |说明|功能
         ---- | ----- | ------
         libstdc++（默认）| 默认最小系统 C++ 运行时库。| 不适用
         gabi++_static|GAbi++ 运行时（静态）。|C++ 异常和 RTTI
         gabi++_shared|GAbi++ 运行时（共享）。|C++ 异常和 RTTI
         stlport_static|STLport 运行时（静态）。|C++ 异常和 RTTI；标准库
         stlport_shared|STLport 运行时（共享）。|C++ 异常和 RTTI；标准库
         gnustl_static|GNU STL（静态）。|C++ 异常和 RTTI；标准库
         gnustl_shared|GNU STL（共享）。|C++ 异常和 RTTI；标准库
         c++_static|LLVM libc++ 运行时（静态）。|C++ 异常和 RTTI；标准库
         c++_shared|LLVM libc++ 运行时（共享）。|C++ 异常和 RTTI；标准库



2. Android原生库。[google NDK原生API文档][https://developer.android.google.cn/ndk/guides/cpp-support]。Android NDK 提供一组随新的 Android API 版本后续发布而逐渐增加的原生标头和共享库文件。这些库的位置为：

    ![ndk-build脚本位置][file:///Users/hh/AndroidStudioProjects/Go2019/Go/Android/jnifile/android_api_position.png]

    1. Android原生库包含以下库：
       * C 库
       * C++ 库
       * Android Log库
       * ZLib 压缩库
       * 动态链接器库
       * OpenGL ES 1.x Library
       * OpenGL ES 2.0 库
       * jnigraphics库
       * EGL
       * OpenSL ES
       * OpenMAX AL
       * OpenSL ES
       * OpenGL ES 3.0
       * OpenGL ES 3.1

    2. 使用Android原生库分两个步骤:
        * 步骤一：在C或C++代码中添加与您想使用的库关联的头文件。这些库的头文件路径大部分位于`<ndk-bundle目录>/sysroot/usr/include/android`这个目录中。
        * 步骤二：通知构建系统您的原生模块需要在加载时链接库。例如，如需链接 链接到 `/system/lib/liblog.so`，则向 `Android.mk` 文件添加以下行：
        ```
        LOCAL_LDLIBS := -llog
        ```