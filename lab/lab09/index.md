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

      比如说发出一条网络请求时，考虑到网速等其他原因，服务器未必会立刻相应我们的请求，如果不将这类操作放在子
      线程里去运行，就会导致主线程被阻塞住，从而影响用户对软件的正常使用。

* 知识点2：主线程

      主要负责处理与UI相关的事件，如：用户的按键事件，用户接触屏幕的事件以及屏幕绘图事件，并把相关的事件分发
      到对应的组件进行处理。所以主线程通常又被叫做UI线程。


* 知识点3：子线程

      负责除UI之外较费时的操作，如从网络上 下载数据，或者访问数据库


* 知识点3：线程的三种基本用法

      **第一种**
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
        
        **第二种**
        直接实现Runnable接口来定义一个线程
        class MyThread implements Runnable{
        @Override
        public void run(){
        //处理具体的逻辑
        }
        启动该线程的方法：
        MyThread myThread=new MyThread();
        new Thread(myThread).start();
        
        **第三种**
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
        
* AsyncTask工具类
    

        Android的AsyncTask比Handler更轻量级一些，适用于简单的异步处理。首先明确Android之所以有Handler和AsyncT
        ask，都是为了不阻塞主线程（UI线程），且UI的更新只能在主线程中完成，因此异步处理是不可避免的。Android
        为了降低这个开发难度，提供了AsyncTask。AsyncTask就是一个封装过的后台任务类，顾名思义就是异步任务。As
        yncTask直接继承于Object类，位置为android.os.AsyncTask。要使用AsyncTask工作我们要提供三个泛型参数，并
        重载几个方法(至少重载一个)。
        
        AsyncTask定义了三种泛型类型 Params，Progress和Result。
        1）Params 启动任务执行的输入参数，比如HTTP请求的URL。
        2）Progress 后台任务执行的百分比。
        3）Result 后台执行任务最终返回的结果，比如String。
        
        使用过AsyncTask 的同学都知道一个异步加载数据最少要重写以下这两个方法：
        
        1）doInBackground(Params…)  后台执行，比较耗时的操作都可以放在这里。注意这里不能直接操作UI。此方法在后台线程执行，完成任务的主要
        工作，通常需要较长的时间。在执行过程中可以调用publicProgress(Progress…)来更新任务的进度。
        
        2）onPostExecute(Result)  相当于Handler 处理UI的方式，在这里面可以使用在doInBackground
        得到的结果处理操作UI。 此方法在主线程执行，任务执行的结果作为此方法的参数返回。
        
        有必要的话你还得重写以下这三个方法，但不是必须的：
        
        1）onProgressUpdate(Progress…)   可以使用进度条增加用户体验度。 
        此方法在主线程执行，用于显示任务执行的进度。
        
        2）onPreExecute()  这里是最终用户调用Excute时的接口，当任务执行之前开始调用此方法，可以在这里显示进度对话框。
        
        3）onCancelled()   用户调用取消时，要做的操作
        
        使用AsyncTask类，以下是几条必须遵守的准则：
        1）Task的实例必须在UI thread中创建；
        2）execute方法必须在UI thread中调用；
        不要手动的调用onPreExecute(), 
        onPostExecute(Result)，doInBackground(Params...),onProgressUpdate(Progress...)这几个方法；
        该task只能被执行一次，否则多次调用时将会出现异常；



    



   

**三、实验内容及步骤**

**3.1 实验内容**

*简要介绍本次实验的主要内容*

**3.2 实验步骤**

*详细描述本次实验的具体步骤*

**四、常见问题及注意事项**

*详细描述本此实验的可能会遇到的问题以及相关的注意事项*



