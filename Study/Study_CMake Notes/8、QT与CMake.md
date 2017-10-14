CMake意为cross-platform make，可用于管理c/c++工程。CMake解析配置文件CMakeLists.txt生成Makefile，相比直接用Makefile管理工程，CMake更灵活和简单。

1、例子

```js
//基本结构
cmake_minimum_required(VERSION 2.8.11)//CMake版本号需求
project(testproject)//项目名称



//在程序中判断版本号
set (Tutorial_VERSION_MAJOR 1)//项目版本号需求
set (Tutorial_VERSION_MINOR 0)


//考虑添加一些有些目标平台可能不支持的代码，在顶层的CMakeLists文件中，接下来我们修改TutorialConfig.h.in来定义CMake是否找到这些函数的宏（#cmakedefine HAVE_LOG #cmakedefine HAVE_EXP）对tests和log的测试必须要在配置文件命令前完成。配置文件命令会使用CMake中的配置立马配置文件
include (${CMAKE_ROOT}/Modules/CheckFunctionExists.cmake)
check_function_exists (log HAVE_LOG)
check_function_exists (exp HAVE_EXP)


//选项会显示在CMake的GUI。当用户选择了之后，这个值会被保存在CACHE中，这样就不需要每次CMAKE都进行更改了
option (USE_MYMATH "Use tutorial provided math implementation" ON)


//配置文件
configure_file (
  "${PROJECT_SOURCE_DIR}/TutorialConfig.h.in"//在源目录创建TutorialConfig.h.in文件，并引入项目版本号需求，可以在.cpp中引入.h文件，判断版本号
  "${PROJECT_BINARY_DIR}/TutorialConfig.h"
  )
  
  
//添加头文件搜索目录
include_directories ("${PROJECT_SOURCE_DIR}")


//使得MathFunctions库成为可选的
if (USE_MYMATH)//在源代码中我们同样使用了USE_MYMATH这个宏。它由CMAKE通过配置文件TutorialConfig.h.in来提供给源代码。（#cmakedefine USE_MYMATH）
  include_directories ("${PROJECT_SOURCE_DIR}/MathFunctions")
  add_subdirectory (MathFunctions)
  set (EXTRA_LIBS ${EXTRA_LIBS} MathFunctions)//保持一个大的项目看起来比较简洁
endif (USE_MYMATH)

//添加一个mysqrt.cxx库
add_subdirectory (MathFunctions) //添加子文件夹路径
add_library(MathFunctions mysqrt.cxx)//将库放入一个叫MathFunctions的子文件夹中
target_link_libraries (Tutorial MathFunctions)//链接到库


//添加可执行文件，链接到库
add_executable (Tutorial tutorial.cxx)
target_link_libraries (Tutorial  ${EXTRA_LIBS})


//安装规则
install (TARGETS MathFunctions DESTINATION bin)//对于MathFunctions库我们安装库和头文件
install (FILES MathFunctions.h DESTINATION include)

install (TARGETS Tutorial DESTINATION bin)//对于应用程序，我们只需要在顶层CMakeLists 文件中配置即可以安装可执行程序和配置了的头文件
install (FILES "${PROJECT_BINARY_DIR}/TutorialConfig.h" DESTINATION include)



//测试
add_test (TutorialRuns Tutorial 25)//是否运行，没有段错误或者其它的崩溃问题，并且返回0
add_test (TutorialComp25 Tutorial 25)


//使用了PASS_REGULAR_EXPRESSION测试属性来确认输出的结果中是否含有某个字符串
set_tests_properties (TutorialComp25 
  PROPERTIES PASS_REGULAR_EXPRESSION "25 is 5")//
  
add_test (TutorialNegative Tutorial -25)
set_tests_properties (TutorialNegative
  PROPERTIES PASS_REGULAR_EXPRESSION "-25 is 0")
  
add_test (TutorialSmall Tutorial 0.0001)
set_tests_properties (TutorialSmall
  PROPERTIES PASS_REGULAR_EXPRESSION "0.0001 is 0.01")

add_test (TutorialUsage Tutorial)
set_tests_properties (TutorialUsage
  PROPERTIES 
  PASS_REGULAR_EXPRESSION "Usage:.*number")
  


//需要添加大量的测试来判断不同的输入值
macro (do_test arg result)
  add_test (TutorialComp${arg} Tutorial ${arg})
  set_tests_properties (TutorialComp${arg}
    PROPERTIES PASS_REGULAR_EXPRESSION ${result})
endmacro (do_test)

do_test (25 "25 is 5")//do_test的任意一次调用，就有另一个测试被添加到工程中
do_test (-25 "-25 is 0")



//想要把我们的工程发布给别人从而让他们去使用。我们想要同时给他们不同平台的二进制文件和源代码。这与步骤3中的install略有不同，install是安装我们从源代码中构建的二进制文件。而在此例中，我们将要构建安装包来支持二进制安装以及cygwin，debian，RPMs等的包管理特性。为了达到这个目的，我们会使用CPack来创建平台相关的安装包。具体地说，我们需要在顶层CMakeLists.txt文件中的底部添加数行
include (InstallRequiredSystemLibraries)//这个模块将会包含当前平台所需要的所有运行时库。
set (CPACK_RESOURCE_FILE_LICENSE  
     "${CMAKE_CURRENT_SOURCE_DIR}/License.txt")
set (CPACK_PACKAGE_VERSION_MAJOR "${Tutorial_VERSION_MAJOR}")//设置了一些CPack的变量来保存license以及工程的版本信息。版本信息利用了我们在早前的教程中使用到的变量。
set (CPACK_PACKAGE_VERSION_MINOR "${Tutorial_VERSION_MINOR}")
set (CPACK_PACKAGE_VERSION_PATCH "${Tutorial_VERSION_PATCH}")
include (CPack)//最后我们包含了CPack这个模块来使用这些变量和你所使用的系统的其它特性来设置安装包。

//然后在CPack上运行它。如果要构建一个二进制包你需要运行：
cpack --config CPackConfig.cmake
//如果要创建一个关代码包你需要输入
cpack --config CPackSourceConfig.cmake




//测试结果上传到dashboard上，顶层的CMakeLists文件中包含CTest模块
include (CTest)
//创建了一个CTestConfig.cmake文件来指定这个工程在dashobard上的的名字。CTest会读这个文件并且运行它。如果要创建一个简单的dashboard，你可以在你的工程上运行CMake，改变生成路径的目录，然后运行ctest -D Experimental。你的dashboard的结果会被上传到Kitware的公共dashboard中。
set (CTEST_PROJECT_NAME "Tutorial")


-----------------------------------------MathFunctions的CMakeLists文件-------------------------------------------
//如何将一个生成的源文件加入到应用程序的构建过程中。
add_executable(MakeTable MakeTable.cxx)
add_custom_command (//添加了一个自定义命令来详细描述如何通过运行MakeTable来产生Table.h
  OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/Table.h
  COMMAND MakeTable ${CMAKE_CURRENT_BINARY_DIR}/Table.h
  DEPENDS MakeTable
  )
include_directories( ${CMAKE_CURRENT_BINARY_DIR} )
add_library(MathFunctions mysqrt.cxx ${CMAKE_CURRENT_BINARY_DIR}/Table.h)//让CMake知道mysqrt.cxx依赖于生成的文件Table.h。这是通过往MathFunctions这个库里面添加生成的Table.h来实现的。我们也需要添加当前的生成目录到搜索路径中，从而Table.h可以被mysqrt.cxx找到。
install (TARGETS MathFunctions DESTINATION bin)
install (FILES MathFunctions.h DESTINATION include)

---------------------------------------------TutorialConfig.h----------------------------------------------------
#define Tutorial_VERSION_MAJOR @Tutorial_VERSION_MAJOR@
#define Tutorial_VERSION_MINOR @Tutorial_VERSION_MINOR@
#cmakedefine USE_MYMATH

// does the platform provide exp and log functions?
#cmakedefine HAVE_LOG
#cmakedefine HAVE_EXP

-----------------------------------------------------------------------------------------------------------------






```

2、命令收集

```js

****************************************************************************//自定义变量（可在命令行下向CMake传递）
cmake -DMY_VAR=hello .//-D为命令
message(${MY_VAR})

****************************************************************************//构建类型
//CMAKE_BUILD_TYPE	对应的c编译选项变量	         对应的c++编译选项变量
//None	            CMAKE_C_FLAGS                CMAKE_CXX_FLAGS
//Debug           	CMAKE_C_FLAGS_DEBUG	         CMAKE_CXX_FLAGS_DEBUG
//Release         	CMAKE_C_FLAGS_RELEASE	       CMAKE_CXX_FLAGS_RELEASE
//RelWithDebInfo  	CMAKE_C_FLAGS_RELWITHDEBINFO CMAKE_CXX_FLAGS_RELWITHDEBINFO
//MinSizeRel	      CMAKE_C_FLAGS_MINSIZEREL     CMAKE_CXX_FLAGS_MINSIZEREL
set(CMAKE_CXX_FLAGS_RELEASE "-O2")
cmake -DCMAKE_BUILD_TYPE=Release//运行cmake的时候，传入相应的构建类型即可


****************************************************************************//检查CMake版本
cmake_minimum_required(VERSION 2.8)

****************************************************************************//定义工程名称
project(helloworld)

****************************************************************************//查找当前目录下所有的源文件并保存到SRC_LIST变量里
aux_source_directory(. SRC_LIST)

****************************************************************************//查找src目录下所有以cmake开头的文件并保存到CMAKE_FILES变量里
file(GLOB CMAKE_FILES "src/cmake*")

****************************************************************************//file命令同时支持目录递归查找,不建议使用file的GLOB指令来收集工程的源文件
file(GLOB_RECURSE CMAKE_FILES "src/cmake*")

****************************************************************************//set命令
//经常配合set命令使用的CMake变量，使用set(variable value)进行设置。
set(CMAKE_VERBOSE_MAKEFILE on )//输出详细的编译和链接信息
set(CMAKE_CXX_COMPILER "g++") //c++编译器
set(CMAKE_CXX_FLAGS "-Wall") //c++编译器参数
set(CMAKE_CXX_FLAGS_DEBUG) //除CMAKE_CXX_FLAGS外，debug版本的额外编译器参数
set(CMAKE_CXX_FLAGS_RELEASE) //除CMAKE_CXX_FLAGS外，release版本的额外编译器参数
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin) //可执行文件的输出目录
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib )//链接库的输出目录
set(MY_GREETINGS "hello world")//set命令还可以设置自定义变量
set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_AUTOMOC ON)//编译时自动打开MOC
SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -m32")//指定编译64bit程序
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -m32")//指定编译32bit
****************************************************************************//包含目录和链接目录
//将./include和./abc加入包含目录列表
include_directories(
    ./include
    ./abc
)
//将./lib加入编译器链接阶段的搜索目录列表
link_directories(
    ./lib
)

****************************************************************************//添加生成目标
//使用SRC_LIST源文件列表里的文件生成一个可执行文件hello
add_executable(hello ${SRC_LIST})
//使用SRC_LIST源文件列表里的文件生成一个静态链接库libhello.a
add_library(hello STATIC ${SRC_LIST})
//使用SRC_LIST源文件列表里的文件生成一个动态链接库libhello.so
add_library(hello SHARED ${SRC_LIST})
//将若干库文件链接到生成的目标hello(libhello.a或libhello.so)
target_link_libraries(hello A B.a C.so)
//target_link_libraries里库文件的顺序符合gcc链接顺序的规则，即被依赖的库放在依赖它的库的后面，比如上面的命令里，libA.so可能依赖于libB.a和libC.so，如果顺序有错，链接时会报错。还有一点，B.a会告诉CMake优先使用静态链接库libB.a，C.so会告诉CMake优先使用动态链接库libC.so，也可直接使用库文件的相对路径或绝对路径。使用绝对路径的好处在于，当依赖的库被更新时，make的时候也会重新链接。

****************************************************************************//自定义链接选项
//有时候需要自定义链接选项，比如需要单独对B.a使用--whole-archive选项，可以
target_link_libraryies(hello A -Wl,--whole-archive B.a -Wl,--no-whole-archive C.so)

//自定义Makefile目标
//运行下面的whatever目标make whatever，会先创建一个目录./hello，然后将当前目录的a.txt拷贝到新建的./hello目录里。
add_custom_command(
    OUTPUT ./hello/a.txt
    COMMAND mkdir -p ./hello 
            cp a.txt ./hello
    DEPENDS a.txt
)
add_custom_target(whatever DEPENDS ./hello/a.txt)

//自定义目标还可以使用add_dependencies命令加入到其他目标的依赖列表里，当执行make demo时，whatever目标会被自动调用。
add_executable(demo ${SRC_LIST})
add_dependencies(demo whatever)

****************************************************************************//其他常用命令
//包含其他目录的CMakeLists.txt
include(/path/to/another/CMakeLists.txt)

//if命令
if(${CMAKE_BUILD_TYPE} MATCHES "debug")
    ...
else()
    ...
endif()

//list命令
list(APPEND SRC_LIST 
    a.cpp
    b.cpp
)

list(REMOVE_ITEM SRC_LIST
    a.cpp
)

//寻找包
find_package(Qt5Widgets)

//变量的引用方式是使用“${}”，在IF中，不需要使用这种方式，直接使用变量名即可
//自定义变量使用SET(OBJ_NAME xxxx)，使用时${OBJ_NAME}
//cmake的常用变量：
CMAKE_BINARY_DIR,PROJECT_BINARY_DIR,_BINARY_DIR//这三个变量内容一致，如果是内部编译，就指的是工程的顶级目录，如果是外部编译，指的就是工程编译发生的目录。
CMAKE_SOURCE_DIR,PROJECT_SOURCE_DIR,_SOURCE_DIR//这三个变量内容一致，都指的是工程的顶级目录
CMAKE_CURRENT_BINARY_DIR//外部编译时，指的是target目录，内部编译时，指的是顶级目录
CMAKE_CURRENT_SOURCE_DIR//CMakeList.txt所在的目录
CMAKE_CURRENT_LIST_DIR//CMakeList.txt的完整路径
CMAKE_CURRENT_LIST_LINE//当前所在的行
CMAKE_MODULE_PATH//如果工程复杂，可能需要编写一些cmake模块，这里通过SET指定这个变量
LIBRARY_OUTPUT_DIR,BINARY_OUTPUT_DIR//库和可执行的最终存放目录
PROJECT_NAME//程序名

```

3、文件编译搜集

```js
set(CMAKE_AUTOMOC ON)//编译时自动打开MOC编译
set(CMAKE_AUTORCC ON)//自动编译qrc文件
set(CMAKE_AUTOUIC ON)//qt中ui文件自动编译
```

```js
//比使用target_link_libraries要好
find_package(Qt5Core)
get_target_property(QtCore_location Qt5::Core LOCATION)//为了不重复加入产生冲突，判断目标是否已经存在

//如
get_target_property(OUTPUT_VALUE all STATUS)  
if(${OUTPUT_VALUE} STREQUAL OUTPUT_VALUE-NOTFOUND)  
    #Target all 不存在  
else()  
    #Target all 存在  
endif() 

```

```js
find_package(Qt5Core)

set(CMAKE_CXX_FLAGS_COVERAGE "${CMAKE_CXX_FLAGS_RELEASE} -fprofile-arcs -ftest-coverage")//指定编译模式

# set up a mapping so that the Release configuration for the Qt imported target is
# used in the COVERAGE CMake configuration.
set_target_properties(Qt5::Core PROPERTIES MAP_IMPORTED_CONFIG_COVERAGE "RELEASE")


```

```js

foreach(plugin ${Qt5Network_PLUGINS})
  get_target_property(_loc ${plugin} LOCATION)
  message("Plugin ${plugin} is at location ${_loc}")
endforeach()
```

```js
//CMake3.1.0之后
set(CMAKE_CXX_STANDARD 14)
```

```js
//CMake3.1.0之前，增加编译选项
-std=gnu++11
```

```js
//CMake2.8.11以前配置qt5_use_modules宏
find_package(Qt5Widgets)
add_executable(helloworld WIN32 main.cpp)
qt5_use_modules(helloworld Widgets)
```

```js

//CMake2.8.9以前qt5_use_modules宏不存在，使用其他命令
# Find includes in corresponding build directories
set(CMAKE_INCLUDE_CURRENT_DIR ON)

# Find the QtWidgets library
find_package(Qt5Widgets)

# Add the include directories for the Qt 5 Widgets module to
# the compile lines.
include_directories(${Qt5Widgets_INCLUDE_DIRS})

# Use the compile definitions defined in the Qt 5 Widgets module
add_definitions(${Qt5Widgets_DEFINITIONS})

# Add compiler flags for building executables (-fPIE)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${Qt5Widgets_EXECUTABLE_COMPILE_FLAGS}")

qt5_generate_moc(main.cpp main.moc)

# Tell CMake to create the helloworld executable
add_executable(helloworld main.cpp main.moc)

#Link the helloworld executable to the Qt 5 widgets library.
target_link_libraries(helloworld Qt5::Widgets)
```





<http://doc.qt.io/qt-5/cmake-manual.html>

