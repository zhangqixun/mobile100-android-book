# 更新今日天气数据


    小组成员: 孙晴 宁旭冉 于昌和   最终完成日期：15年00月00日
# 

**一、简介**

*简要介绍本模块所要完成的功能。*

**二、基础知识**

   
* bean包：

      作用：封装数据
      实现： get、set、toString方法


* toString方法：

      toString()源自Object类，重写并不是必须的，需要根据当前程序的
      实际情况而定。在许多场合中,需要打印和比较出对象的内容,所以重写。
      toString()
      譬如一个JavaBean插入数据库时需要打印一段该JavaBean内容的日志
      ，这时在JavaBean里重写toString()就显得很优美了。


* 解析xml数据（PULL）：

      START_DOCUMENT：读取到XML的声明;
      END_DOCUMENT：读取到XML的结束返回；
      START_TAG : 读取到XML的开始标签；
      END_TAG：读渠道XML的结束标签；
      参照XML数据解析。

* 更新UI：

      xxx.setText():
      如果xxx是Text控件，那就是设置Text的控件中的文本内容
      xxx.setImageResource()：
      设置ImageView中的图片，setImageResource的参数是resId，必须是drawable目录下的资源。
      
* 主线程与子线程间的消息传递：

      handler是线程通讯工具类。用于传递消息。它有两个队列：
      1.消息队列
      2.线程队列
      消息队列使用sendMessage和HandleMessage的组合来发送和处理消息。
      handler是Message的主要处理者，负责Message的发送，Message内
      容的执行处理。后台线程就是通过传进来的Handler对象引用来sendMessage(Message)。
      而使用Handler，需要implement 该类的 handleMessage(Message) 
      方法，它是处理这些Message的操作内容，例如Update UI。 
      通常需要子类化Handler来实现handleMessage方法。
   

**三、主要思路及步骤**

**3.1 主要思路**

*简要介绍主要思路*

**3.2 实践步骤**

*详细描述开发的具体步骤*

**四、常见问题及注意事项**

*1. 在处理数据时注意值为空的情况；*
