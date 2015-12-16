# Android源码编译流程及（1501210942 李智博）

##**1.简介**##
####1.1 背景  
&ensp;&ensp;&ensp;&ensp;在智能手机时代，每一个手机厂商都有自己的特点。正是依靠这种与众不同的个性来吸引用户，营造品牌的凝聚力和用户忠诚度。智能手机的厂商分为两大阵营，Android平台因为其完全开放的特点吸引了世界众多的手机厂商，他们分别根据自己对市场的把握度以及用户的需求修改并完善了Android系统，国内比较有代表性的就是小米的MI UI和中兴的Nubia UI，国外三星的Android系统也深受用户欢迎。这些手机厂商通过修改Google提供的Android源码，重新编译，生成了定制化功能的Android系统。如果自己通过这样的方法定制自己喜欢的Android系统，这定是一件很酷的事。
####1.2 本文介绍
&ensp;&ensp;&ensp;&ensp;本文主要分为两个部分来介绍，首先是Android Build系统。Android Build 系统是用来编译 Android 系统，Android SDK 以及相关文档的一套框架。众所周知，Android 是一个开源的操作系统。Android 的源码中包含了许许多多的模块。 不同产商的不同设备对于 Android 系统的定制都是不一样的。如何将这些模块统一管理起来，如何能够在不同的操作系统上进行编译，如何在编译时能够支持面向不同的硬件设备，不同的编译类型，且还要提供面向各个产商的定制扩展，是非常有难度的。 但 Android Build 系统很好的解决了这些问题，这里面有很多值得我们开发人员学习的地方。对于 Android 平台开发人员来说，这些知识可以帮助你熟悉你每天接触到的构建环境。对于其他开发人员来说，也可以作为一个 GNU Make 的使用案例，学习这些成功案例，可以提升我们的开发经验。    
&ensp;&ensp;&ensp;&ensp;第二，我将通过实际操作来简单介绍编译源码的整个流程。这主要包括四个步骤：编译环境的构建，源码的下载，编译以及运行系统。Android源码的编译运行是其他很多底层Android开发者必须要经历的过程。对于硬件驱动工程师来说，需要修改kernel/common/drivers目录下的源码并重新编译来实现对硬件的操控；为了向Android的Application Frameworks层提供硬件服务，需要在frameworks/base/services/jni目录下编写C或者C++程序，然后通过JNI接口向上层提供服务。这些对Android的定制操作都离不了对源码的编译过程。


##**2.Android Build系统介绍**##

&ensp;&ensp;&ensp;&ensp;由前文可知，Android Build系统是用来编译Android系统、Android SDK、Android的开发的各种辅助工具和相关文档的一套框架。Build系统中最主要的处理逻辑都在Make文件当中，而其他的脚本文件知识起到一些辅助作用。接下来先介绍Linux下关于Make文件的知识。    



####**2.1 Make编译指令简介**####

&ensp;&ensp;&ensp;&ensp;对于Linux或者Unix下的用户来说，make是一个非常重要的编译命令。不管是自己进行项目开发还是安装应用软件，我们都经常要用到make或make install。利用make工具，我们可以将大型的开发项目分解成为多个更易于管理的模块，对于一个包括几百个源文件的应用程序，使用make和makefile工具就可以简洁明快地理顺各个源文件之间纷繁复杂的相互关系。而且如此多的源文件，如果每次都要键入gcc命令进行编译的话，那对程序员来说简直就是一场灾难。而make工具则可自动完成编译工作，并且可以只对程序员在上次编译后修改过的部分进行编译。因此，有效的利用make和makefile工具可以大大提高项目开发的效率。同时掌握make和makefile之后，再也不会面对着Linux下的应用软件手足无措了。  
&ensp;&ensp;&ensp;&ensp;Make工具最主要也是最基本的功能就是通过makefile文件来描述源程序之间的相互关系并自动维护编译工作。而makefile 文件需要按照某种语法进行编写，文件中需要说明如何编译各个源文件并连接生成可执行文件，并要求定义源文件之间的依赖关系。  
&ensp;&ensp;&ensp;&ensp;在Android的Build系统当中，存在着大量的makefile文件，其中有部分文件定义着不同模块的依赖关系，而模块内部又有自己的makefile文件，这里又定义了详细的编译细节。正是这些makefile文件控制着整个源码的编译流程。

  
####**2.2 Android Build系统概述**####

&ensp;&ensp;&ensp;&ensp;有了上面关于Make指令的知识，就可以学习Android源码当中各个模块是如何通过Make文件来组织编译了。  
&ensp;&ensp;&ensp;&ensp;整个 Build 系统中的 Make 文件可以分为三类：  
&ensp;&ensp;&ensp;&ensp;第一类是 Build 系统核心文件，此类文件定义了整个 Build 系统的框架，而其他所有 Make 文件都是在这个框架的基础上编写出来的。  
&ensp;&ensp;&ensp;&ensp;图 1 是 Android 源码树的目录结构，Build 系统核心文件全部位于 /build/core（本文所提到的所有路径都是以 Android 源码树作为背景的，“/”指的是源码树的根目录，与文件系统无关）目录下。  
![](http://7xoi5h.com1.z0.glb.clouddn.com/picture1.png)   
图 1. Android 源码树的目录结构   
&ensp;&ensp;&ensp;&ensp;第二类是针对某个产品（一个产品可能是某个型号的手机或者平板电脑）的 Make 文件，这些文件通常位于 device 目录下，该目录下又以公司名以及产品名分为两级目录，图 2 是 device 目录下子目录的结构。对于一个产品的定义通常需要一组文件，这些文件共同构成了对于这个产品的定义。例如，/device/htc/passion 目录下的文件共同构成了对于 HTC-Passion 型号手机的定义。  
![](http://7xoi5h.com1.z0.glb.clouddn.com/picture2.png)
&ensp;&ensp;&ensp;&ensp;第三类是针对某个模块（关于模块后文会详细讨论）的 Make 文件。整个系统中，包含了大量的模块，每个模块都有一个专门的 Make 文件，这类文件的名称统一为“Android.mk”，该文件中定义了如何编译当前模块。Build 系统会在整个源码树中扫描名称为“Android.mk”的文件并根据其中的内容执行模块的编译。   

####**2.3 Make文件说明**####

&ensp;&ensp;&ensp;&ensp;整个 Build 系统的入口文件是源码树根目录下名称为“Makefile”的文件，当在源代码根目录上调用 make 命令时，make 命令首先将读取该文件。
Makefile 文件的内容只有一行：“include build/core/main.mk”。该行代码的作用很明显：包含 build/core/main.mk 文件。在 main.mk 文件中又会包含其他的文件，其他文件中又会包含更多的文件，这样就引入了整个 Build 系统。  
&ensp;&ensp;&ensp;&ensp;这些 Make 文件间的包含关系是相当复杂的，图 3 描述了这种关系，该图中黄色标记的文件（且除了 $开头的文件）都位于 build/core/ 目录下。  
&ensp;&ensp;&ensp;&ensp;图 3. 主要的 Make 文件及其包含关系
![](http://7xoi5h.com1.z0.glb.clouddn.com/image004.png)

&ensp;&ensp;&ensp;&ensp;其中最重要的是main.mk文件，该文件中首先将对编译环境进行检查，同时引入其他的 Make 文件。另外，该文件中还定义了几个最主要的 Make 目标，例如 droid，sdk，等（参见后文“Make 目标说明”）。   
&ensp;&ensp;&ensp;&ensp;另外一个文件就是config.mk，不同类型的模块的编译步骤和方法是不一样，为了能够一致且方便的执行各种类型模块的编译，在此文件中定义了许多的常量，这其中的每个常量描述了一种类型模块的编译方式，这些常量有BUILD_STATIC_LIBRARY，BUILD_HOST_STATIC_LIBRARY，BUILD_PACKAGE等。这些常量分别对应一个Make文件，在这些文件当中定义了详细的编译方式。常量名与文件名有简单的对应方式，常量的名称是 Make 文件的文件名除去后缀全部改为大写然后加上“BUILD_”作为前缀。例如常量 BUILD_HOST_PREBUILT 的值对应的文件就是 host_prebuilt.mk。其中BUILD_STATIC_LIBRARY文件对应的static_library.mk文件定义了如何编译设备上的静态库，BUILD_PACKAGE变量对应的package.mk定义了如何编译apk文件，而BUILD_HOST_PREBUILT对应的host_prebuilt.mk文件定义了处理一个或多个主机上使用的已编译文件，该文件的实现依赖 multi_prebuilt.mk。可是这里面的主机和设备到底是什么意思呢？原因如下：  
&ensp;&ensp;&ensp;&ensp;Android 源码中包含了许多的模块，模块的类型有很多种，例如：Java 库，C/C++ 库，APK 应用，以及可执行文件等 。并且，Java 或者 C/C++ 库还可以分为静态的或者动态的，库或可执行文件既可能是针对设备（本文的“设备”指的是 Android 系统将被安装的设备，例如某个型号的手机或平板）的也可能是针对主机（本文的“主机”指的是开发 Android 系统的机器，例如装有 Ubuntu 操作系统的 PC 机或装有 MacOS 的 iMac 或 Macbook）的。  
&ensp;&ensp;&ensp;&ensp;所以说这个Android的源码不只只是能够编译出Android手机操作系统，还能够编译出各种针对Android系统开发的工具，比如说在主机上开发Android App时要使用到的sdk，以及模拟手机运行要使用到的模拟器等等。  

####**2.4 Make目标说明**####

&ensp;&ensp;&ensp;&ensp;如果在源码树的根目录直接调用“make”命令而不指定任何目标，则会选择默认目标：“droid”（在 main.mk 中定义）。因此，这和执行“make droid”效果是一样的。
droid 目标将编译出整个系统的镜像。从源代码到编译出系统镜像，整个编译过程非常复杂。这个过程并不是在 droid 一个目标中定义的，而是 droid 目标会依赖许多其他的目标，这些目标的互相配合导致了整个系统的编译。比如说它会依赖一些已经编译好的文件等等。其依赖关系为图4所示：
![](http://7xoi5h.com1.z0.glb.clouddn.com/image006.png)
&ensp;&ensp;&ensp;&ensp;简要说明其中的几个Make目标。apps_only将会编译出当前配置下不包含user、userdebug、eng标签的应用程序；systemimg用于生成system.img系统文件。

##**3.源码编译流程介绍**##

&ensp;&ensp;&ensp;&ensp;在对Android Build系统有了一定的了解之后，我们将接下来将通过实例来介绍Android源码的编译流程。本文使用的Android版本为2.3。

####**3.1 编译环境准备**####
&ensp;&ensp;&ensp;&ensp;1、首先，Android是基于Linux的操作系统，所以它的编译需要在Unix/Linux系列的电脑上面编译。Google官网推荐使用Ubuntu和Mac OS系统来完成编译流程。本文将要在Ubuntu系统上完成整个流程。对于电脑不是Linux或者Mac OS系统的朋友，可以尝试在虚拟机上面安装上述中的系统。  
&ensp;&ensp;&ensp;&ensp;2、安装Git工具。Android源代码采用Git工具来管理，与SVN相比，这是一种分布式的源代码管理工具，而SVN是集中式的源代码管理工具。要安装Git工具，在Ubuntu上执行以下命令即可：  

    $ sudo apt-get install git-core gnupg

&ensp;&ensp;&ensp;&ensp;3、Android源码当中包含大量的Java源码，所以需要编译这些文件。安装Java SDK，在Ubuntu上执行以下命令：  

    $ sudo add-apt-repository ppa:ferramroberto/java
    $ sudo apt-get update
    $ sudo apt-get install sun-java6-jre sun-java6-plugin
    $ sudo apt-get install sun-java6-jdk 

4、在编译过程中会依赖大量其他的库，在Ubuntu上执行下面的命令：

    $ sudo apt-get install git-core gnupg flex bison gperf build-essential zip curl libc6-dev libncurses5-dev:i386 x11proto-core-dev libx11-dev:i386 libreadline6-dev:i386 libgl1-mesa-glx:i386 libgl1-mesa-dev g++-multilib mingw32 tofrodos python-markdown libxml2-utils xsltproc zlib1g-dev:i386
5、安装调试工具，命令如下：

    $ sudo apt-get install valgrind


####**3.2 下载源码**####
&ensp;&ensp;&ensp;&ensp;接下来就是从Google提供的官方源获取Android源码，过程如下。   

1、下载repo工具。在Ubuntu上执行以下命令：

    $ wget https://dl-ssl.google.com/dl/googlesource/git-repo/repo
    $ chmod 777 repo
    $ cp repo /bin/
    	
2、下载Android最新版本源代码。在Ubuntu上执行以下命令：

    $ mkdir Android
    $ cd Android
    $ repo init -u https://dl-ssl.google.com/dl/googlesource/git-repo/repo
    $ repo sync

&ensp;&ensp;&ensp;&ensp;但是因为镜像存在于国外的服务器，下载时间可能比较长，甚至有时候会出现下载出错的提示。此时，还有一种比较不错的解决方案，这是清华大学TUNA提供的镜像源，具体操作方法可以参考官方文档：https://mirrors.tuna.tsinghua.edu.cn/help/#AOSP。



####**3.3 执行编译**####

&ensp;&ensp;&ensp;&ensp;在完成编译环境的准备工作以及获取到完整的Android源码之后，想要编译出整个Android系统非常的容易。  
&ensp;&ensp;&ensp;&ensp;打开控制台之后转到Android源码的根目录，然后执行如下的三条命令即可。  

    $ source build/envsetup.sh 
    $ lunch full-eng 
    $ make -j8

&ensp;&ensp;&ensp;&ensp;其中第三个指令的参数“-j”制定了同时编译的Job数量，这是个整数，该值通常是编译主机 CPU 支持的并发线程总数的 1 倍或 2 倍（例如：在一个 4 核，每个核支持两个线程的 CPU 上，可以使用 make -j8 或 make -j16）。在调用 make 命令时，如果没有指定任何目标，则将使用默认的名称为“droid”目标，该目标会编译出完整的 Android 系统镜像。完整的编译时间依赖于编译主机的配置，因为项目的庞大，一般需要的时间比较长。  
&ensp;&ensp;&ensp;&ensp;当编译完成之后，Build结果的目录结构如下：  
&ensp;&ensp;&ensp;&ensp;所有的编译产物都将位于 /out 目录下，该目录下主要有以下几个子目录：  
&ensp;&ensp;&ensp;&ensp;/out/host/：该目录下包含了针对主机的 Android 开发工具的产物。即 SDK 中的各种工具，例如：emulator，adb，aapt 等。  
&ensp;&ensp;&ensp;&ensp;/out/target/common/：该目录下包含了针对设备的共通的编译产物，主要是 Java 应用代码和 Java 库。  
&ensp;&ensp;&ensp;&ensp;/out/target/product/[product_name]/：包含了针对特定设备的编译结果以及平台相关的 C/C++ 库和二进制文件。其中，[product_name]是具体目标设备的名称。  
&ensp;&ensp;&ensp;&ensp;/out/dist/：包含了为多种分发而准备的包，通过“make disttarget”将文件拷贝到该目录，默认的编译目标不会产生该目录。  
&ensp;&ensp;&ensp;&ensp;**Build 生成的镜像文件**  
&ensp;&ensp;&ensp;&ensp;Build的产物中最重要的是三个镜像文件，它们都位于 /out/target/product/[product_name]/ 目录下。  
&ensp;&ensp;&ensp;&ensp;这三个文件是：  
&ensp;&ensp;&ensp;&ensp;system.img：包含了 Android OS 的系统文件，库，可执行文件以及预置的应用程序，将被挂载为根分区。  
&ensp;&ensp;&ensp;&ensp;ramdisk.img：在启动时将被 Linux 内核挂载为只读分区，它包含了 /init 文件和一些配置文件。它用来挂载其他系统镜像并启动 init 进程。  
&ensp;&ensp;&ensp;&ensp;userdata.img：将被挂载为 /data，包含了应用程序相关的数据以及和用户相关的数据。  

####**3.4 系统运行**####

&ensp;&ensp;&ensp;&ensp;接下来按照下面的步骤把编译好的Android系统放在模拟器上面运行。  
1、设置环境变量：  

    $ export PATH=$PATH:~/Android/out/host/linux-x86/bin  
    $ export ANDROID_PRODUCT_OUT=~/Android/out/target/product/generic
&ensp;&ensp;&ensp;&ensp;其中，~/Android/out/host/linux-x86/bin有我们要执行的emulator命令，而~/Android/out/target/product/generic是Android镜像存放目录，下面执行emulator命令时会用到。  
2、运行模拟器：  
&ensp;&ensp;&ensp;&ensp;模拟器运行需要四个文件，分别是Linux Kernel镜像zImage和Android镜像文件system.img、userdata.img和ramdisk.img。执行emulator命令时，如果不带任何参数，则Linux Kernel镜像默认使用~/Android/prebuilt/android-arm/kernel目录下的kernel-qemu文件，而Android镜像文件则默认使用ANDROID_PRODUCT_OUT目录下的system.img、userdata.img和ramdisk.img，也就是我们刚刚编译出来的镜像文件。  
&ensp;&ensp;&ensp;&ensp;当然，我们也可以以指定的镜像文件来运行模拟器，即运行emulator时，即：

    $ emulator -kernel ./prebuilt/android-arm/kernel/kernel-qemu -sysdir ./out/target/product/generic -system system.img -data userdata.img -ramdisk ramdisk.img
    
&ensp;&ensp;&ensp;&ensp;到这里，我们就可以在模拟器上运行我们自己编译的Android镜像文件了，是不是很酷呢？
![](http://7xoi5h.com1.z0.glb.clouddn.com/emulator.jpg)
##**4.总结**##
&ensp;&ensp;&ensp;&ensp;本文主要介绍了Android Build系统和Android源码的编译流程。Android Build系统的主要作用是使用Make文件组织庞大的Android源码文件之间的编译依赖关系，使用此系统不仅可以编译Android系统文件，还可以编译Android SDK，Android模拟器等文件。接下来又通过介绍了Android源码的编译流程，在我们未来为Android系统修改或者添加源文件时可以方便的利用现在所学习的知识将我们的创意编译到Android系统当中，从而生成我们定制后的Android系统。 

