# CMake入门
* CMake是什么？
* 为什么用CMake?
* 怎样使用CMake?

## CMake是什么
CMake是一个构建工程的工具，可以通过简单的配置语句来描述工程的编译安装过程，然后根据平台生成适合该平台的项目工程。
## 为什么用CMake
它跨平台，可以做到可以做到“Write once, run everywhere”。首先允许开发者编写一种平台无关的 CMakeList.txt 文件来定制整个编译流程，然后再根据目标用户的平台进一步生成所需的本地化 Makefile 和工程文件，如 Unix 的 Makefile 或 Windows 的 Visual Studio 工程。

## 怎样使用CMake?
### CMake基本命令

> 指定该程序需要使用的CMake最低版本

CMAKE_MINIMUM_REQUIRED(VERSION 3.2)

> 项目信息

PROJECT(Project名称)

> 生成目标

ADD_EXECUTABLE(生成目标名称 生成目标源文件1 生成目标源文件2)

> 使用AUX_SOURCE_DIRECTORY命令，该命令会查找指定目录下的所有源文件，然后将结果存进指定变量名

AUX_SOURCE_DIRECTORY(目录 变量名)

> 加载子目录下的CMakeLists文件

ADD_SUBDIRECTORY(子目录)

> 指定可执行文件A需要链接的库

TARGET_LINK_LIBRARIES(可执行文件A 库名称B ...)




