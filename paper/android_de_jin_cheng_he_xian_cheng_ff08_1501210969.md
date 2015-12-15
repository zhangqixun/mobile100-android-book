# Android 的进程和线程（1501210969 彭正宇）

　　为了方便程序员学习和开发安卓程序，google在设计安卓的时候，刻意淡化了进程和线程的概念，给开发人员提供的是使用组件进行快速开发带UI界面的程序的开发模式。一般开发人员常用的android四大组件，Activity、Service、Broadcast Receiver、Content Provider，便是这种形式。Android开发，强调通过图形界面与用户进行交换。因此，很多开发人员，对于安卓进程和线程，都没有一个全面而具体的掌握。本文的目的就是对安卓进程和线程进行一些基础性的介绍，帮助大家在日后能够更好地进行安卓开发。


### android进程的基本知识

　　对于Android中的进程运行的基本情况，通过几个实验来进行简单的介绍。

　　首先，在Android Studio中，新建了一个只有一个空白activity的测试程序。
![](newblankactivity.png)
点击Run按钮启动该程序，通过Android Device Monitor观察程序启动后的运行状态。可以看到，系统为该测试程序新建了一个主进程。观察该进程拥有的线程，除了我们熟悉的main线程之外，还有一些其他的线程也在启动时被创建了。
![](http://7xp7x0.com1.z0.glb.clouddn.com/GBThread_activityADM.png)
可以看到，运行一个只拥有一个空白activity的程序，在程序被正常启动之后，android系统在后台会新建一系列的线程，通过这些被新建的线程，该应用程序才能完成进程间通信等操作。

这里主要介绍一下main线程。main线程是应用程序进程中的主线程，它的主要责任是负责处理UI事件，而且只有该线程才能处理UI事件。该线程一般也被称为UI线程。Android中的所有组件都是在指定的进程中的主线程中实例化的，对组件的系统调用也是由主线程发出的。每个实例不会建立新的线程。

Android的UI操作被称为UI单线程模式。因为UI线程的操作不是线程安全的（在UI线程进行UI界面刷新的同时，其他线程也在进行UI界面刷新的话，就会导致界面刷新的操作不能同步，这被称为线程不安全）,并且对UI事件，比如用户点击控件，刷新UI界面等操作，都必须通过UI线程进行处理，这就意味着对组件的调用都是通过UI线程进行的，响应的回调方法也是在UI线程中运行的。其他线程是不能去更新UI控件的，否则就会报错：不能在非UI线程中去更新UI控件。

在确保只通过UI线程去访问UI控件的同时，也需要注意，我们编写的代码不能阻塞UI线程。当在UI线程中进行某些耗时的操作时，将会阻塞UI线程，一般阻塞超过5秒就会显示一个ANR （Application Not Responding）对话框。

因此，对于需要长时间处理的操作，android提供了Handler Message机制和AsyncTask等方法帮助实现异步操作。

在启动activity的时候，在onCreate方法上打一个断点，可以观察到它是如何被启动的。

这里通过函数调用的堆栈信息，分析应用程序的进程被启动过程的具体流程。
![](http://7xp7x0.com1.z0.glb.clouddn.com/GBThread_debugframes.png)
![](http://7xp7x0.com1.z0.glb.clouddn.com/GBThread_debugthreads.png)
由上图可以看到，android应用程序进程的主线程是通过ZygoteInit的main函数创建的。进程创建完成之后，Android应用程序就会在这个进程中将ActivityThread类加载进来，然后执行它的main函数，这个main函数就是进程执行消息循环的地方，也就是一般意义上的程序入口点，类似于C的main函数。

在android系统中，应用程序的进程一般都是通过Zygote进程fork出来的子进程，zygote进程的主体就是ZygoteInit。
![](http://7xp7x0.com1.z0.glb.clouddn.com/GBThread_zygoteinit.png)
在android系统启动后，就会创建zygote进程,然后由zygote进程fork出ActivityManagerService系统进程，用于管理Activity和其他的组件的运行状态，在需要启动一个应用程序时，系统会去调用ActivityManagerService去启动该应用程序。
![](http://7xp7x0.com1.z0.glb.clouddn.com/GBThread_activitymanagerservice.png)由ActivityManagerService与zygote进程通信之后，zygote才会fork出一个新的子进程，作为该应用程序的主进程。

观察ZygoteInit的main方法，它主要作了三件事情，

1.调用registerZygoteSocket函数创建了一个socket接口，用来和 ActivityManagerService通讯，
![](http://7xp7x0.com1.z0.glb.clouddn.com/GBThread_zygoteinitmain_1.png)
2.调用startSystemServer函数来启动SystemServer组件，

3.调用 runSelectLoopMode函数进入一个无限循环在前面创建的socket接口上等待ActivityManagerService请求创建新的应用程序进程。
![](http://7xp7x0.com1.z0.glb.clouddn.com/GBThread_zygoteinitmain_2.png)


