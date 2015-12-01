#移动平台软件开发（Android）面试题汇总参考答案

## Question About Thread

###**声明**

本册面试题及答案大部分摘自网络，版权归原创者所有，如有侵权请您及时联系：

邮箱：1596888285@qq.com

参考用书：《第一行代码》（Android）、《疯狂Android讲义》、《Android4编程入门经典》

*本答案仅供参考，如有错误，请您指正*


---
**1、请简述handler机制的原理**

andriod提供了Handler和Looper来满足线程间的通信。Handler先进先出原则。Looper类用来管理特定线程内对象之间的消息交换(MessageExchange)。

1)Looper: 一个线程可以产生一个Looper对象，由它来管理此线程里的MessageQueue(消息队列)。 

2)Handler: 你可以构造Handler对象来与Looper沟通，以便push新消息到MessageQueue里;或者接收Looper从Message Queue取出)所送来的消息。

3) Message Queue(消息队列):用来存放线程放入的消息。 

4)线程：UIthread 通常就是mainthread，而Android启动程序时会替它建立一个MessageQueue。 

（1）Handler创建消息

每一个消息都需要被指定的Handler处理，通过Handler创建消息便可以完成此功能。Android消息机制中引入了消息池。Handler创建消息时首先查询消息池中是否有消息存在，如果有直接从消息池中取得，如果没有则重新初始化一个消息实例。使用消息池的好处是：消息不被使用时，并不作为垃圾回收，而是放入消息池，可供下次Handler创建消息时使用。消息池提高了消息对象的复用，减少系统垃圾回收的次数。

（2）Handler发送消息

UI主线程初始化第一个Handler时会通过ThreadLocal创建一个Looper，该Looper与UI主线程一一对应。使用ThreadLocal的目的是保证每一个线程只创建唯一一个Looper。之后其他Handler初始化的时候直接获取第一个Handler创建的Looper。Looper初始化的时候会创建一个消息队列MessageQueue。至此，主线程、消息循环、消息队列之间的关系是1:1:1。

Hander持有对UI主线程消息队列MessageQueue和消息循环Looper的引用，子线程可以通过Handler将消息发送到UI线程的消息队列MessageQueue中。

（3）Handler处理消息

UI主线程通过Looper循环查询消息队列UI_MQ，当发现有消息存在时会将消息从消息队列中取出。首先分析消息，通过消息的参数判断该消息对应的Handler，然后将消息分发到指定的Handler进行处理。

**2、请简述在单线程模型中Message、Handler、Message Queue、Looper之间的关系**

简单的说，Handler获取当前线程中的looper对象，looper用来从存放Message的MessageQueue中取出Message，再有Handler进行Message的分发和处理。

Message Queue(消息队列)：用来存放通过Handler发布的消息，通常附属于某一个创建它的线程，可以通过Looper.myQueue()得到当前线程的消息队列。

Handler：可以发布或者处理一个消息或者操作一个Runnable，通过Handler发布消息，消息将只会发送到与它关联的消息队列，然也只能处理该消息队列中的消息。

Looper：是Handler和消息队列之间通讯桥梁，程序组件首先通过Handler把消息传递给Looper，Looper把消息放入队列。

Handler：Handler接受到消息后调用handleMessage进行处理

Message：消息的类型，在Handler类中的handleMessage方法中得到单个的消息进行处理

在单线程模型下，为了线程通信问题，Android设计了一个Message Queue(消息队列)， 线程间可以通过该Message Queue并结合Handler和Looper组件进行信息交换。下面将对它们进行分别介绍：

1. Message，理解为线程间交流的信息，处理数据后台线程需要更新UI，则发送Message内含一些数据给UI线程。
2. Handler 
Handler处理者，是Message的主要处理者，负责Message的发送，Message内容的执行处理。后台线程就是通过传进来的 Handler对象引用来sendMessage(Message)。而使用Handler，需要implement 该类的 handleMessage(Message)方法，它是处理这些Message的操作内容，例如Update UI。通常需要子类化Handler来实现handleMessage方法。
3. Message Queue 
Message Queue消息队列，用来存放通过Handler发布的消息，按照先进先出执行。
每个message queue都会有一个对应的Handler。Handler会向message queue通过两种方法发送消息：sendMessage或post。这两种消息都会插在message queue队尾并按先进先出执行。但通过这两种方法发送的消息执行的方式略有不同：通过sendMessage发送的是一个message对象,会被 Handler的handleMessage()函数处理；而通过post方法发送的是一个runnable对象，则会自己执行。
4. Looper 
Looper是每条线程里的Message Queue的管家。Android没有Global的Message Queue，而Android会自动替主线程(UI线程)建立Message Queue，但在子线程里并没有建立Message Queue。所以调用Looper.getMainLooper()得到的主线程的Looper不为NULL，但调用Looper.myLooper() 得到当前线程的Looper就有可能为NULL。对于子线程使用Looper，API Doc提供了正确的使用方法：这个Message机制的大概流程：
1. 在Looper.loop()方法运行开始后，循环地按照接收顺序取出Message Queue里面的非NULL的Message。
2. 一开始Message Queue里面的Message都是NULL的。当Handler.sendMessage(Message)到Message Queue，该函数里面设置了那个Message对象的target属性是当前的Handler对象。随后Looper取出了那个Message，则调用 该Message的target指向的Hander的dispatchMessage函数对Message进行处理。在dispatchMessage方法里，如何处理Message则由用户指定，三个判断，优先级从高到低：
1) Message里面的Callback，一个实现了Runnable接口的对象，其中run函数做处理工作；
2) Handler里面的mCallback指向的一个实现了Callback接口的对象，由其handleMessage进行处理；
3) 处理消息Handler对象对应的类继承并实现了其中handleMessage函数，通过这个实现的handleMessage函数处理消息。
由此可见，我们实现的handleMessage方法是优先级最低的！
3. Handler处理完该Message (update UI) 后，Looper则设置该Message为NULL，以便回收！
在网上有很多文章讲述主线程和其他子线程如何交互，传送信息，最终谁来执行处理信息之类的，个人理解是最简单的方法——判断Handler对象里面的Looper对象是属于哪条线程的，则由该线程来执行！ 
1. 当Handler对象的构造函数的参数为空，则为当前所在线程的Looper； 
2. Looper.getMainLooper()得到的是主线程的Looper对象，Looper.myLooper()得到的是当前线程的Looper对象。


**3、Hanlder是线程与Activity通信的桥梁,如果线程处理不当，你的机器就会变得越慢，那线程销毁的方法是? ** 

onDestroy()  

正确销毁线程的方法应该是handler.removeCallBacks()。


**4、请解释run()、start()、sleep()、suspend()等方法的作用。**

start是启动线程对象，使之从新建状态转入就绪状态；sleep让线程睡眠一段时间，在此期间线程不消耗CPU资源；suspend使线程挂起，暂停执行， 如果想恢复线程，必须由其他线程调用 resume方法。

**5、简述Andriod如何处理UI与耗时操作的通信，有哪些方式及各自的优缺点。**

主要有三种方法，一为Handler，二为AsyncTask，三为自己开子线程执行耗时操作，然后调用Activity的runOnUiThread（）方法更新ui；

handler机制是，在主线程中创建handler对象，
当执行耗时操作时，新建一个线程，在这个线程中执行耗时操作，通过调用handler的sendMessage，post等方法，更新ui界面；

AsyncTask本质上是一个线程池，所有的异步任务都会在这个线程池中的工作线程中执行，当需要操作ui界面时，会和工作线程通过handler传递消息。

自己开子线程执行耗时操作，然后调用Activity的runOnUiThread（）方法更新ui，这种方法需要把context对象强制转换成activity后使用
handler机制的优点是  结构清晰，功能明确，但是代码过多；

asyncTask简单，快捷，但是可能会新开大量线程，消耗系统资源，造成FC

第三种方法最好用，代码也非常简单，只是需要传递context对象
