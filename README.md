# 010_FFMPEG


**① CSDN 博客地址 :** [【Android FFMPEG 开发】Android Studio 工程配置 FFMPEG ( 动态库打包 | 头文件与函数库拷贝 | CMake 脚本配置 )](https://hanshuliang.blog.csdn.net/article/details/104367747)

**②  博客资源下载地址 :**

**③ 示例代码 GitHub 地址 :** [https://github.com/han1202012/010_FFMPEG](https://github.com/han1202012/010_FFMPEG)






<br>
<br>

#### I . FFMPEG 交叉编译后的函数库及头文件

---

<br>

**1 . 编译 FFMPEG 函数库 :** [【Android FFMPEG 开发】FFMPEG 交叉编译配置 ( 下载 | 配置脚本 | 输出路径 | 函数库配置 | 程序配置 | 组件配置 | 编码解码配置 | 交叉编译配置 | 最终脚本 )](https://blog.csdn.net/shulianghan/article/details/104351312)

<font color=purple>编译出的静态库在上面博客的资源文件中下载 ; <font color=blue>下面截图中是在 Ubuntu 中交叉编译出的 6 个静态库 , 共 86 MB ; 

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200219200440219.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbjEyMDIwMTI=,size_16,color_FFFFFF,t_70)

<br>


**2 . 静态库大小说明 :** 

<br>

**① 调试信息 :** <font color=red>编译出的 6 个静态库有 86MB , 这是因为编译时 , 将调试信息也包含在里面 , 方便进行调试 ; 

**② 打包大小 :** <font color=green>最终打包成 APK 之后 , 整个 APK 文件只有不到 10MB 大小 , 

<br>
<br>



<br>
<br>

#### II . FFMPEG 静态库打包动态库 ( 仅做参考 )

---

<br>


**静态库打包到动态库中 ( 仅做参考 ) :** <font color=purple>将上面 6 个静态库打包到 动态库 中 ;  <font color=red>打包后的 libffmpeg.so 在博客资源中可下载 ; 

<br>

**① 设置 交叉编译器 临时环境变量 :** 

<font color=blue>export <font color=red>CC<font color=blue>=<font color=blue>/root/NDK/android-ndk-r17c/toolchains/arm-linux-androideabi-4.9/prebuilt/linux-x86_64/bin/arm-linux-androideabi-gcc

<br>


**② 设置指定 头文件和函数库 的临时环境变量 :** 

<font color=blue>export <font color=red>C_FLAGS<font color=blue>="<font color=green>\-\-sysroot=/root/NDK/android-ndk-r17c/platforms/android-21/arch-arm <font color=purple>-isystem /root/NDK/android-ndk-r17c/sysroot/usr/include <font color=red>-isystem /root/NDK/android-ndk-r17c/sysroot/usr/include/arm-linux-androideabi"



<br>



**③ 打包静态库命令 :** 

<font color=blue>\$CC <font color=purple>$C_FLAGS <font color=green>-shared -o libffmpeg.so  <font color=red>-Wl,\-\-whole-archive <font color=magenta>libavcodec.a libavformat.a libswresample.a libavfilter.a libavutil.a libswscale.a <font color=red>-Wl,\-\-no-whole-archive

<br>

**④ 打包过程 :** 

```shell
root@octopus:~/FFMPEG/ffmpeg-4.0.2/android/armeabi-v7a/lib# export CC=/root/NDK/android-ndk-r17c/toolchains/arm-linux-androideabi-4.9/prebuilt/linux-x86_64/bin/arm-linux-androideabi-gcc
root@octopus:~/FFMPEG/ffmpeg-4.0.2/android/armeabi-v7a/lib# export C_FLAGS="--sysroot=/root/NDK/android-ndk-r17c/platforms/android-21/arch-arm -isystem /root/NDK/android-ndk-r17c/sysroot/usr/include -isystem /root/NDK/android-ndk-r17c/sysroot/usr/include/arm-linux-androideabi"
root@octopus:~/FFMPEG/ffmpeg-4.0.2/android/armeabi-v7a/lib# 
root@octopus:~/FFMPEG/ffmpeg-4.0.2/android/armeabi-v7a/lib# $CC $C_FLAGS -shared -o libffmpeg.so -Wl,--whole-archive libavcodec.a libavformat.a libswresample.a libavfilter.a libavutil.a libswscale.a -Wl,--no-whole-archive
root@octopus:~/FFMPEG/ffmpeg-4.0.2/android/armeabi-v7a/lib# ls
libavcodec.a  libavfilter.a  libavformat.a  libavutil.a  libffmpeg.so  libswresample.a  libswscale.a  pkgconfig
root@octopus:~/FFMPEG/ffmpeg-4.0.2/android/armeabi-v7a/lib# ll
总用量 129336
drwxr-xr-x 3 root root     4096 2月  19 22:30 ./
drwxr-xr-x 5 root root     4096 2月  18 22:19 ../
-rw-r--r-- 1 root root 60104544 2月  18 22:18 libavcodec.a
-rw-r--r-- 1 root root   763728 2月  18 22:14 libavfilter.a
-rw-r--r-- 1 root root 25221538 2月  18 22:15 libavformat.a
-rw-r--r-- 1 root root  1609508 2月  18 22:19 libavutil.a
-rwxr-xr-x 1 root root 41509960 2月  19 22:30 libffmpeg.so*
-rw-r--r-- 1 root root   401316 2月  18 22:18 libswresample.a
-rw-r--r-- 1 root root  2807636 2月  18 22:19 libswscale.a
drwxr-xr-x 2 root root     4096 2月  18 22:19 pkgconfig/

```


<br>
<br>

#### III . 创建 Android Studio 工程

---

<br>

**创建 Android Studio 工程 :** <font color=blue>创建一个 Native C++ 类型的 Android 工程 ; ( 这里就不墨迹基础操作了 )

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200219234837668.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbjEyMDIwMTI=,size_16,color_FFFFFF,t_70)


<br>
<br>

#### IV . FFMPEG 头文件与静态库拷贝

---

<br>

**头文件与静态库文件拷贝 :** <font color=blue>将生成的 include 和 lib 两个目录 , 拷贝到 app/src/main/cpp 目录下 ; 

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200219234001418.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbjEyMDIwMTI=,size_16,color_FFFFFF,t_70)

><font color=purple>动态库拷贝到 jniLibs/armeabi-v7a 目录下 , 静态库可以拷贝到任意位置使用 ; 


<br>
<br>

#### V . CMakeList.txt 配置 FFMPEG 头文件 与 函数库

---

<br>


**1 . 头文件查找路径设置 :**<font color=blue> 使用 include_directories 指定 FFMPEG 头文件查找目录 , CMAKE_SOURCE_DIR 指的是当前的文件地址 ; 

```shell
# IV . 指定 FFMPEG 头文件查找目录
#           CMAKE_SOURCE_DIR 指的是当前的文件地址
include_directories(${CMAKE_SOURCE_DIR}/include)
```

<br>


**2 . 函数库设置 :** <font color=purple>函数库在 cpp/lib 中 , 这里使用 -L 参数指定函数库查找路径 ; 

```shell
# V . 指定 FFMPEG 静态库
# CMAKE_CXX_FLAGS 表示会将 C++ 的参数传给编译器
# CMAKE_C_FLAGS 表示会将 C 参数传给编译器

# 参数设置 : 传递 CMAKE_CXX_FLAGS C+= 参数给编译器时 , 在 该参数后面指定库的路径
#   CMAKE_SOURCE_DIR 指的是当前的文件地址
#   -L 参数指定动态库的查找路径
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -L${CMAKE_SOURCE_DIR}/lib")
```

<br>


**3 . 函数库链接设置 :** <font color=green>注意这里要链接 FFMPEG 的 6 个静态库 , 不要遗漏 ; 

```shell
# V . 链接函数库
#       参数 1 : 本构建脚本要生成的动态库目标
#       参数 2 ~ ... : 后面是之前预编译的动态库或静态库 , 或引入的动态库
target_link_libraries(
        native-lib

        # FFMPEG 静态库
        avcodec
        avfilter
        avformat
        avutil
        swresample
        swscale

        # 日志库
        ${log-lib})
```



<br>




**4 . 完整构建脚本 :** 

```shell
# I . 指定 CMake 最低版本
cmake_minimum_required(VERSION 3.4.1)

# II . 设置函数库编译
add_library( # 参数 1 : 设置生成的动态库名称
        native-lib

        # 参数 2 : 设置生成的函数库类型 : ① 静态库 STATIC ② 动态库 SHARED
        SHARED

        # 参数 3 : 配置要编译的源文件
        native-lib.cpp)

# III . 到预设的目录查找 log 库 , 将找到的路径赋值给 log-lib
#       这个路径是 NDK 的 ndk-bundle\platforms\android-29\arch-arm\usr\lib\liblog.so
#       不同的 Android 版本号 和 CPU 架构 需要到对应的目录中查找 , 此处是 29 版本 32 位 ARM 架构的日志库
#
# 可以不配置 :
#       可以不进行该配置, 直接在后面的 target_link_libraries 中链接 log 也不会出错
find_library(
        log-lib

        log)


# IV . 指定 FFMPEG 头文件查找目录
#           CMAKE_SOURCE_DIR 指的是当前的文件地址
include_directories(${CMAKE_SOURCE_DIR}/include)

# V . 指定 FFMPEG 静态库
# CMAKE_CXX_FLAGS 表示会将 C++ 的参数传给编译器
# CMAKE_C_FLAGS 表示会将 C 参数传给编译器

# 参数设置 : 传递 CMAKE_CXX_FLAGS C+= 参数给编译器时 , 在 该参数后面指定库的路径
#   CMAKE_SOURCE_DIR 指的是当前的文件地址
#   -L 参数指定动态库的查找路径
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -L${CMAKE_SOURCE_DIR}/lib")


# V . 链接函数库
#       参数 1 : 本构建脚本要生成的动态库目标
#       参数 2 ~ ... : 后面是之前预编译的动态库或静态库 , 或引入的动态库
target_link_libraries(
        native-lib

        # FFMPEG 静态库
        avcodec
        avfilter
        avformat
        avutil
        swresample
        swscale

        # 日志库
        ${log-lib})
```

<br>
<br>

#### VI . C++ 代码

---

<br>

**1 . 引入头文件 :** <font color=red>引入 libavcodec/avcodec.h 头文件 , FFMPEG 是 C 开发的 , 在 C++ 中使用需要使用 extern "C" 进行兼容 ;

```c
// 注意 FFMPEG 是 C 开发的 , 在 C++ 中使用需要使用 extern "C" 进行兼容
extern "C"{
    #include <libavcodec/avcodec.h>
}
```

<br>

**2 . 获取版本号方法 :** <font color=blue>调用 av_version_info() 可以获取到 FFMPEG 版本号 , 即 "4.0.2" , 将这个字符串传输到主界面显示 ; 

<br>

**3 . 完整代码 :** 

```cpp
#include <jni.h>
#include <string>

// 注意 FFMPEG 是 C 开发的 , 在 C++ 中使用需要使用 extern "C" 进行兼容
extern "C"{
    #include <libavcodec/avcodec.h>
}


extern "C" JNIEXPORT jstring JNICALL
Java_kim_hsl_ffmpeg_MainActivity_stringFromJNI(
        JNIEnv *env,
        jobject /* this */) {
    std::string hello = "Hello from C++";

    return env->NewStringUTF(av_version_info());
}

```

**4 . 执行结果 :** 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200220000233997.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbjEyMDIwMTI=,size_16,color_FFFFFF,t_70)

<br>
<br>

#### VII . 博客资源

---

<br>

**博客相关资源 :**

<br>

**① CSDN 博客地址 :** [【Android FFMPEG 开发】Android Studio 工程配置 FFMPEG ( 动态库打包 | 头文件与函数库拷贝 | CMake 脚本配置 )](https://hanshuliang.blog.csdn.net/article/details/104367747)

**②  博客资源下载地址 :**

**③ 示例代码 GitHub 地址 :** [https://github.com/han1202012/010_FFMPEG](https://github.com/han1202012/010_FFMPEG)




