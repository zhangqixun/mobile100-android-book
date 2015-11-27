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
        

    



   

**三、实验内容及步骤**

**3.1 实验内容**

*简要介绍本次实验的主要内容*

**3.2 实验步骤**

*详细描述本次实验的具体步骤*

**四、常见问题及注意事项**

*详细描述本此实验的可能会遇到的问题以及相关的注意事项*



