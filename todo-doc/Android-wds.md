
- 解压代码之后，编译错误  

make -j4 

```
============================================
PLATFORM_VERSION_CODENAME=REL
PLATFORM_VERSION=5.0.2
TARGET_PRODUCT=full_tiny4412
TARGET_BUILD_VARIANT=eng
TARGET_BUILD_TYPE=release
TARGET_BUILD_APPS=
TARGET_ARCH=arm
TARGET_ARCH_VARIANT=armv7-a-neon
TARGET_CPU_VARIANT=cortex-a9
TARGET_2ND_ARCH=
TARGET_2ND_ARCH_VARIANT=
TARGET_2ND_CPU_VARIANT=
HOST_ARCH=x86_64
HOST_OS=linux
HOST_OS_EXTRA=Linux-4.4.0-116-generic-x86_64-with-Ubuntu-16.04-xenial
HOST_BUILD_TYPE=release
BUILD_ID=LRX22G
OUT_DIR=out
============================================
Checking build tools versions...
/bin/bash: javac: command not found
************************************************************
You are attempting to build with the incorrect version
of java.
 
Your version is: openjdk version "1.8.0_151" OpenJDK Runtime Environment (build 1.8.0_151-8u151-b12-0ubuntu0.16.04.2-b12) OpenJDK 64-Bit Server VM (build 25.151-b12, mixed mode).
The required version is: "1.7.x"
 
Please follow the machine setup instructions at
    https://source.android.com/source/initializing.html
************************************************************
build/core/main.mk:167: *** stop.  Stop.

#### make failed to build some targets (16 seconds) ####





---------------------

1. 开发环境参考的博客[Ubuntu搭建Android开发环境
](https://www.jianshu.com/p/4187f419a386)


vmware 专业版永久激活码：5A02H-AU243-TZJ49-GTC7K-3C61N