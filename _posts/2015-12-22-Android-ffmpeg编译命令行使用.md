---
layout: post
category : lessons
tagline: "涉及到ndk的问题"
tags : [android]
date: 2015-12-22 15:01:45
title:   android-ffmpeg的编译命令行使用
---


### a:背景   
工作上需要，编写一个视频转换的工具，然后我选择了ffmpeg这个重量级的工具。下面讲述的是如何编译ffmpeg，如何在android上面使用ffmpeg。教程或许简单，但我编译后可用。可行性应该属于挺高的。   
配置环境：   
1：android-ndk-r10e    
2：macbook os X Yosemite     

编译的目标的ffmpeg的版本是ffmpeg-2.8.3    [ffmpeg-2.8 的下载地址](https://github.com/FFmpeg/FFmpeg/tree/release/2.8)    
ffmpeg-2.8.3的下载地址
提供下载地址的原因，部分同学会找不到当前的版本去下载 而说一些无法编译的情况。
边看边编译。模仿是最好的学习途径。   

---   
 
#### 1：配置好编译之前的环境   
a:下载和配置和ndk的环境。配置ndk的时候在mac下面比较麻烦，因为mac有个机制是当前窗口适用。所以得写在配置文件上面。   
b:下载ffmpeg，建议ffmpeg这个文件夹的源码放在ndk目录的sources下面。放在这个下面可以很好的管理一些ndk的相互依赖性。  
c:对config文件进行修改。如果不修改的话，那么到时候编译出来的so文件的格式会变成libxx.so.67之类的存在。这个格式android上面不能好好的识别。   
修改方法。在configure下面找到下面的几行代码。
```xml
SLIBNAME_WITH_MAJOR='$(SLIBNAME).$(LIBMAJOR)'  
LIB_INSTALL_EXTRA_CMD='$$(RANLIB)"$(LIBDIR)/$(LIBNAME)"'  
SLIB_INSTALL_NAME='$(SLIBNAME_WITH_VERSION)'  
SLIB_INSTALL_LINKS='$(SLIBNAME_WITH_MAJOR)$(SLIBNAME)' 
```
然后把这几行代码替换成下面这样就可以了  
```xml
SLIBNAME_WITH_MAJOR='$(SLIBPREF)$(FULLNAME)-$(LIBMAJOR)$(SLIBSUF)'  
LIB_INSTALL_EXTRA_CMD='$$(RANLIB)"$(LIBDIR)/$(LIBNAME)"'  
SLIB_INSTALL_NAME='$(SLIBNAME_WITH_MAJOR)'  
SLIB_INSTALL_LINKS='$(SLIBNAME)'  
```   
    
d:生成类库，那么如何去生成类库呢？这个时候我们需要编写一个build_android.sh 的文件，编写完毕后可以很轻松的sh build_android.sh 去编译我们的ffmpeg，生成我们所需要的文件。    
build_android.sh 文件如下。   
```xml
#!/bin/bash
NDK=/appdev/ndk/android-ndk-r10e
SYSROOT=$NDK/platforms/android-9/arch-arm/
TOOLCHAIN=$NDK/toolchains/arm-linux-androideabi-4.8/prebuilt/darwin-x86_64

CC=/appdev/ndk/android-ndk-r10e/toolchains/arm-linux-androideabi-4.8/prebuilt/darwin-x86_64/bin
X264_ROOT=/appdev/ndk/android-ndk-r10e/sources/x264-snapshot-20151220-2245/android/include

function build_one
{
./configure \
 --extra-libs=-lgcc \
  --target-os=linux \
  --prefix=$PREFIX \
    --enable-cross-compile \
    --enable-runtime-cpudetect \
    --disable-asm \
    --arch=$CPU \
    --sysroot=$SYSROOT \
    --cc=$CC/arm-linux-androideabi-gcc \
    --cross-prefix=$TOOLCHAIN/bin/arm-linux-androideabi- \
    --disable-stripping \
    --nm=$TOOLCHAIN/bin/arm-linux-androideabi-nm \
    --enable-gpl \
    --enable-libx264 \
    --enable-shared \
    --disable-static \
    --enable-small \
    --disable-ffprobe \
    --disable-ffplay \
    --disable-ffmpeg --disable-ffserver --disable-debug \
    --extra-cflags="-fPIC -DANDROID -D__thumb__ -mthumb -Wfatal-errors -Wno-deprecated -mfloat-abi=softfp -marm -march=armv7-a -I$X264_ROOT" \
    --extra-ldflags=-L/appdev/ndk/android-ndk-r10e/sources/x264-snapshot-20151220-2245/android/lib 
make clean
make 
make install
}
CPU=arm
PREFIX=$(pwd)/android/$CPU
ADDI_CFLAGS="-marm"
build_one
```
在上面的配置文件需要注意的参数有 NDK SYSROOT TOOLCHAIN X264_ROOT 这几个参数。   
NDK 是ndk所在的目录下面。   X264_ROOT 是因为在我需要的功能中，需要求h264的编码格式文件进行编码。通常ffmpeg是解码的工具，但里面不包含一些编码工具，这个时候需要使用到第三方的lib库。   
在配置文件中，详情可以使用 ./configure --help 查看对应的命令。然后根据自己想要的去配置。我的项目仅仅是提供参数。    
    
在运行sh build_android.sh 之前，我们是不是看到了X264_ROOT，那么这个编译会需要依赖x264编译好的so文件或者是.a的链接文件。   
那么在第二大步，就是关于如何编译libx264。   

---

#### 2：编译libx264   
a:编译libx264之前，需要到官网去下载源码 http://www.videolan.org/developers/x264.html ，我编译的x264是什么版本我也不知道。所以，我也提供了我自己的下载的source给你们下载。   (x264下载)[]    
b:编译libx264的时候，你会发现有一个类似ffmpeg的configure的文件，那么我们也需要写一个sh脚本去编译它。一切为了编译方便和为了以后的维护。   
脚本文件 build.sh：
```java
export NDK=/appdev/ndk/android-ndk-r10e
export VERSION=4.8
export PREBUILT=$NDK/toolchains/arm-linux-androideabi-$VERSION/prebuilt
export PLATFORM=$NDK/platforms/android-8/arch-arm
./configure --enable-static \
	--enable-shared \
	--enable-pic \
	--disable-asm \
	--disable-cli \
	--host=arm-linux \
	--cross-prefix=$PREBUILT/darwin-x86_64/bin/arm-linux-androideabi- \
	--sysroot=$PLATFORM \
	--prefix=./android
make && make install
``` 
运行 sh build.sh 之后，你会在./android文件夹下面看到生成的文件夹inclue 和 lib ，然后仔细看看lib 下面的so文件，你会发现里面的名字和上面提醒的一样。那么这个时候需要修改configure文件，如何去修改呢？    
```java
 elif [ "$SYS" = "MACOSX" ]; then
        echo "SOSUFFIX=dylib" >> config.mak
        echo "SONAME=libx264.dylib.$API" >> config.mak
        echo "SOFLAGS=-shared -dynamiclib -Wl,-single_module -Wl,-read_only_relocs,suppress -install_name \$(DESTDIR)\$(libdir)/\$(SONAME) $SOFLAGS" >> config.mak
    elif [ "$SYS" = "SunOS" ]; then
        echo "SOSUFFIX=so" >> config.mak
        echo "SONAME=libx264.so.$API" >> config.mak
        echo "SOFLAGS=-shared -Wl,-h,\$(SONAME) $SOFLAGS" >> config.mak
    else
        echo "SOSUFFIX=so" >> config.mak
        echo "SONAME=libx264.so.$API" >> config.mak
        echo "SOFLAGS=-shared -Wl,-soname,\$(SONAME) $SOFLAGS" >> config.mak
```
这个时候你会看到 .$API 是在so文件的后面。那么把.$API 这个名称移到so的前面就好了。   
c:删了之前生成的android的文件，重新运行sh build.sh , 那么就可以看到编译好的需要的文件。
   
---   

#### 3：编译ffmpeg
这个时候基本上准备的工作都准备好了。在ffmpeg的目录下运行 sh android_build.sh    
那么等待等待，这个时候会等待一段时间，然后出现一些配置文件之类的东西。如何没有显示配置文件，那么就是 android_build.sh 里面的配置属性出了问题。请重新配置。要多点了解如何去配置，编译我们需要的模块。     

#### 4：编译完成后，新建一个android的工程。然后提取我们需要的文件，放到项目的jni的目录下面去，这个时候，我们优先使用的是编译好后的include文件夹里面的头文件。   
文件夹结构：   
<img src="" />

#### 5：配置Android.md文件    
因为把所需要的so文件以及一些include头文件放到jni的目录下面。    
所以在配置上面，只需要配置好当前目录下面所需要的。    
---   
   
```java
```

   
---   
 

