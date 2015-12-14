# Android framework 浅析 （潘启慧 1501210967）

**Android Framework 启动过程 **

 Android手机系统本质上是一个基于Linux的应用程序，它以Linux系统为内核。系统的启动过程包括Linux内核启动和Android框架启动两个阶段
 
**一**、Linux内核启动
   1、装载引导程序bootloader
      Linux内核启动时首先装载执行bootloader引导程序，装载完成后进入内核程序。
   2、加载Linux内核
      Linux内核加载主要包括初始化kernel核心（内存初始化，打开中断，初始化进程表等）、初始化驱动、启动内核后台（daemons)线程、安装根（root)文件系统等。
      Linux加载的最后阶段启动执行第一个用户级进程init（内核引导参数上一般都会设置“init=/init”，由kernel自动执行，PID为1，是所有进程的父进程）。由此进入Android框架的启动阶段。
      
**二**、Android框架启动
      Android框架的启动始于init进程，这个阶段也是本文要重点讲解的。启动过程可以分为以下几个主要的阶段：
   1、init进程启动
   2、init.rc脚本启动
   3、zygote服务启动
   4、System Server进程启动
   5、Home应用启动
  ![](c092061c-e1ae-3901-985d-0b5f411b6857.jpg)