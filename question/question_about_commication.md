#移动平台软件开发（Android）面试题汇总参考答案

## Question About Commnication

###**声明**

本册面试题及答案大部分摘自网络，版权归原创者所有，如有侵权请您及时联系：

邮箱：1596888285@qq.com

参考用书：《第一行代码》（Android）、《疯狂Android讲义》、《Android4编程入门经典》

*本答案仅供参考，如有错误，请您指正*


---
**1、请尝试简述Android的IPC（进程间通信）机制**

IPC是内部进程通信的简称，是共享“命名管道”的资源。Android中的IPC机制是为了让Activity和Service之间可以随时的进行交互，故在Android中该机制只适用于Activity和Service之间的通信，类似于远程方法调用，类似于C/S模式的访问。通过定义AIDL接口文件来定义IPC接口。Servicer端实现IPC接口，Client端调用IPC接口本地代理。

**2、Android中Looper的实现原理，为什么调用Looper.prepare()就在当前线程关联了一个Looper对象，它是如何实现的。(腾讯2015春招移动客户端开发练习卷)**

1）线程间通信机制：首先，Looper，Handler，MessageQueue三者共同实现了Android系统里线程间通信机制。如在A，B两个子线程之间需要传递消息，首先给每个子线程绑定一套Handler,Looper,MessageQueue机制，然后这三个对象都与各自的线程对应。然后A线程通过调用B线程的Handler对象，发送消息。这个消息会被Handler发送到B线程的MessageQueue中， 而属于B线程的Looper对象一直在循环无限遍历MessageQueue，一旦发现该消息队列里收到新的消息，就会去对消息进行处理，处理过程中会回调自身Handler，HandleMessage方法。从而实现了不同线程间通信。

2）Looper实现原理：Looper类里面包含一个消息队列对象和一个线程对象。当创建Looper时，会自动创建一个消息队列，同时将内部线程对象指向创建Looper的线程。当开启Looper后（Looper.loop()），会自动进入无限for循环中，不断去遍历消息队列，如果没有消息则阻塞，有消息则回调Handler的handleMessage()方法进行处理。

3）Looper.prepare()：首先，要使用Looper机制一般会在当前线程中创建Handler对象，里面会自动创建一个Looper对象和消息队列，这里面的消息队列属于当前线程空间。但此时的Looper还不会去遍历，也没有绑定到当前线程。其中，Looper对象内部也包含一个空消息队列对象和空线程。通过Looper.prepare()方法，先让该消息队列指向当前线程的消息队列，让空线程也指向当前线程，从而实现了绑定。

