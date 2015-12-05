# Android Binder机制(1501210451 张志康)

    学号：1501210451  姓名：张志康  专业：集成电路工程

# 
本文主要分析native层和Java层的Android binder通信机制。

binder是Android最为常见的进程通信机制之一，其驱动和通信库是binder的核心，分别由C和C++编写，应用程序通过JNI同底层库进行关联，也就是native层驱动和通信库通过Java层包装后被Java层调用。

源代码网址：http://androidxref.com/4.2_r1/

参考博客：http://blog.csdn.net/coding_glacier/article/details/7520199



**一、native层整体通信流程**

* 通信流程概要 
      在探究binder通信流程之前，首先我们需要了解Binder机制的四个组件：Client、Server、Service Manager和Binder驱动程序。关系如图：
    
    

    

**二、基础知识**

*简要介绍本模块所需掌握的基础知识*
   


      知识点介绍

* 知识点2：

      知识点介绍


* 知识点3：

      知识点介绍


   

**三、主要思路及步骤**

**3.1 主要思路**

*简要介绍主要思路*

**3.2 实践步骤**

*详细描述开发的具体步骤*

**四、常见问题及注意事项**

*详细描述本部分的常遇到的问题以及开发过程中的注意事项*



