# Android framework 浅析 （潘启慧 1501210967）

**Android Framework 启动过程 **

 Android手机系统本质上是一个基于Linux的应用程序，它以Linux系统为内核。系统的启动过程包括Linux内核启动和Android框架启动两个阶段
 
**一、Linux内核启动**
   1、装载引导程序bootloader
      Linux内核启动时首先装载执行bootloader引导程序，装载完成后进入内核程序。
   2、加载Linux内核
      Linux内核加载主要包括初始化kernel核心（内存初始化，打开中断，初始化进程表等）、初始化驱动、启动内核后台（daemons)线程、安装根（root)文件系统等。
      Linux加载的最后阶段启动执行第一个用户级进程init（内核引导参数上一般都会设置“init=/init”，由kernel自动执行，PID为1，是所有进程的父进程）。由此进入Android框架的启动阶段。
      
**二、Android框架启动**
      Android框架的启动始于init进程。启动过程可以分为以下几个主要的阶段：
   1、init进程启动
   2、init.rc脚本启动
   3、zygote服务启动
   4、System Server进程启动
   5、Home应用启动
   
 其总体框架运行流程如图所示：
  ![](c092061c-e1ae-3901-985d-0b5f411b6857.jpg)
  
  1、init进程启动

     init进程是Android系统的入口，它会以后台进程（daemon）的形式一直存在。该进程的程序源码在目录system/core/init中，入口函数是init.c:main()。主要有如下功能：
    　　1）创建/安装设备文件/进程文件/系统文件节点；
    　　2）解析init.rc和init.hardware.rc；
    　　3）启动脚本中指定的服务，执行指定的命令或动作；
    　　4）作为守护进程循环检查是否有action需要执行、是否需要重启某服务等。
         详细代码可以参考init.c中入口函数main的实现。
  2、init.rc脚本启动

     init.rc文件是Android系统的重要配置文件，位于/system/core/rootdir/目录中。主要功能是定义了系统启动时需要执行的一系列action及service：执行特定动作、设置环境变量和属性和执行特定的service。该脚本在init进程的init.c:main函数中解析并启动。
     需要重点说明的是init.rc脚本文件配置了一些重要的服务，init进程通过创建子进程启动这些服务，这里创建的service都属于native服务，运行在Linux空间，通过socket向上层提供特定的服务，并以守护进程的方式运行在后台。
     通过init.rc脚本系统启动了以下几个重要的服务：
      1）servicemanager:启动binder IPC，管理所有的Android系统服务
      2）mountd:设备安装Daemon，负责设备安装及状态通知
      3）debuggerd:启动debug system，处理调试进程的请求
      4）rild:启动radio interface layer daemon服务，处理电话相关的事件和请求
      5）mediaserver:启动AudioFlinger,MediaPlayerService and CameraService，负责多媒体播放相关的功能，包括音视频解码、显示输出
      6）zygote:进程孵化器，启动Android Java VMRuntime和启动systemserver，负责Android应用进程的孵化工作