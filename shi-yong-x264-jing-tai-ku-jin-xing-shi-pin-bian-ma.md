## 使用x264静态库进行视频编码

编译准备：

1.linux环境，由于windos环境下，直接虚拟机种跑起来（VM）.

2.x264源码，搜索一下，直接官网下载

3.android-ndk，官网下载即可

详细编译步骤：

将ndk放在home路径下，进入x264目录，创建编译脚本，如下：

```
#nkd路径
NDK=/home/android-ndk-r14b
#平台类型 arm arm64 详见ndk目录
PLATFORM=$NDK/platforms/android-21/arch-arm/
#toolchain目录目录，可以在ndk目录下找到

TOOLCHAIN=$NDK/toolchains/arm-linux-androideabi-4.9/prebuilt/linux-x86_64
PREFIX=./lib/armeabi
function build_one
{
  ./configure \
  --prefix=$PREFIX \
  --enable-static \
  --enable-pic \
  --host=arm-linux \
  --cross-prefix=$TOOLCHAIN/bin/arm-linux-androideabi- \
  --sysroot=$PLATFORM

  make clean
  make
  make install
}
build_one
echo Android ARM builds finished
```

更改脚本为可执行权限 chmod 744 xxx.sh  （4 可读 2可写 1可执行 三位对应 root用户 普通用户 访客）

执行脚本./xxx.sh

执行完成后在 生成文件位于 /x264/lib/armeabi ，其中包含-bin-include-lib- bin内包含x264执行文件，include内为头文件，lib内为.a静态库

-----添加静态库到android项目中------

创建app/libs/armeabi ，若还编译了其他abi类型 依次创建如（arm64-v8a,mips）。

将lib目录下.a文件复制到以上目录

gradle 文件android节点内下添加

```
externalNativeBuild {
    cmake {
      path 'CMakeLists.txt'
       }
}
sourceSets {
    main {
      jniLibs.srcDirs = ['libs']
      }
   }
}
```

在src/main/cpp目录下创建include文件夹，用于存放，静态库头文件-&gt;libx264 将include内头文件复制到该文件夹内。

添加cmakelist 配置

```
#添加头文件目录，可多个
include_directories(src/main/cpp/include/libx264)
#添加静态库 
add_library(libx264
            STATIC
            IMPORTED)
#连接动态库和头文件
set_target_properties(libx264
                      PROPERTIES IMPORTED_LOCATION
                      ${CMAKE_SOURCE_DIR}/libs/${ANDROID_ABI}/libx264.a)
#关联库
target_link_libraries( # Specifies the target library.
#可以多个，依次添加
libx264
# Links the log library to the target library.
${log-lib})
```

详细可见orz项目下

