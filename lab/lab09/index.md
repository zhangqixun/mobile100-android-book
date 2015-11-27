# 多线程

起草人: OOOO   日期：15年00月00日

修改完善：OOOO   日期：15年00月00日
# 


    小组成员: 赖涛，舒心   最终完成日期：15年00月00日
# 

**一、实验目的**

学习并掌握android中的多线程机制

**二、基础知识**

*简要介绍本次实验所需掌握的基础知识*
   
* 知识点1：为什么要使用多线程

      知识点介绍

* 知识点2：主线程

      知识点介绍


* 知识点3：子线程

      知识点介绍


* 知识点3：线程的三种基本用法

       1、定义一个线程只需要新建一个类继承自Thread，然后重写父类的run()方法，并在里面编写耗时逻辑即可。
       如下所示
        class MyThread extends Thread{
            @Override
            public void run(){
            //处理具体的逻辑
        }
        
        2、启动这个线程时，只需要new出一个MyThread的实例，然后调用它的start()方法，样run()方法中的代码就
        会在子线程当中运行了。如下所示：
        new MyThread().start()；
        或者是：
        MyThread myThread=new MyThread();
        myThread.start();
        第二种
        直接实现Runnable接口来定义一个线程
        class MyThread implements Runnable{
        @Override
        public void run(){
        //处理具体的逻辑
        }
        启动该线程的方法：
        MyThread myThread=new MyThread();
        new Thread(myThread).start();
        
        第三种
        匿名内部类的方式
        new Thread (new Runnable(){
        @Override
        public void run(){
        //处理具体的逻辑
        }
        }).start();
        
* 知识点4：异步消息处理机制

    
        和许多其他的GUI库一样，Android的UI也是线程不安全的，也就是说，如果想要更新应用程序里的UI元素，则必须
        在主线程中运行，否则就会出现异常，这就要提到android异步消息处理机制
    
        Android中的异步消息处理主要由四个部分组成，Message、Handler、MessageQueue和Looper。
        
        1）Message
        Message是在线程之间传递的消息，它可以在内部携带少量信息，用于在不同的线程之间交换数据。比如Message的w
        hat字段、arg1和arg2字段（携带一些整形数据）、使用obj字段携带一个Object对象。
        
        2）Handler
        主要用于发送和处理消息，发送消息一般是使用Handler的sendMessage()方法，而发出的消息经过一系列的处理后
        ，最终会传到Handler的handlerMessage()方法中。
        
        3）MessageQueue
        MessageQueue是消息队列的意思，它主要用于存放所有通过Handler发送的消息。这部分消息会一直存在于消息队列
        中，等待被处理，每个线程中只会有一个MessageQueue对象。
        
        4）Looper
        Looper是每个线程中的MessageQueue的管家，调用Looper的loop()方法后，就会进入到一个无线循环当中，然后每
        当发现MessageQueue中存在一条消息，就会将a它提出来，并传递到Handler的handlerMessage()方法中。每个线程
        中也只会有一个Looper对象。
        


    



   

**三、实验内容及步骤**

**3.1 实验内容**

*简要介绍本次实验的主要内容*

**3.2 实验步骤**

*详细描述本次实验的具体步骤*

**四、常见问题及注意事项**

*详细描述本此实验的可能会遇到的问题以及相关的注意事项*



