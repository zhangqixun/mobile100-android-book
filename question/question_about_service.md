#移动平台软件开发（Android）面试题汇总参考答案

## Question About Service

###**声明**

本册面试题及答案大部分摘自网络，版权归原创者所有，如有侵权请您及时联系：

邮箱：1596888285@qq.com

参考用书：《第一行代码》（Android）、《疯狂Android讲义》、《Android4编程入门经典》

*本答案仅供参考，如有错误，请您指正*


---
**1、android中service的实现（启动）方法有：  startservice  、  bindservice  。**

**2、请简述如何启用Service，如何停用Service**

Android中的服务和windows中的服务是类似的东西，服务一般没有用户操作界面，它运行于系统中不容易被用户发觉，可以使用它开发如监控之类的程序。服务的开发比较简单，如下：

第一步：继承Service类
```
public class SMSService extends Service {
}
```
第二步：在AndroidManifest.xml文 件中的```<application>```节点里对服务进 行配置:
```<service android:name=".SMSService" />```
服务不能自己运行，需要通过调用```Context.startService()```或```Context.bindService()```方法启动服务。这两个方法都 可以启动Service，但是它们的使用场合有所不同。

使用startService()方法启用服务，调用者与服务之间没有关连，即使调用者退出了，服务仍然运行。使用bindService()方法启用服务，调用者与服务绑定在了一起，调用者一旦退出，服务也就终止，大有“不求同时生，必须同时死”的特点。

如果打算采用Context.startService()方法启动服务，在服务未被创建时，系统会先调用服务的onCreate()方 法，接着调用onStart()方法。如果调用startService()方法前服务已经被创建，多次调用startService()方法并不会导致多次创建服务，但会导致多次调 用onStart()方法。采用startService()方法启动的服务，只能调用Context.stopService()方法结束服务，服务结束时会调 用onDestroy()方法。

如果打算采用Context.bindService()方法启动服务，在服务未被创建时，系统会先调用服务的onCreate()方 法，接着调用onBind()方法。这个时候调用者和服务绑定在一起，调用者退出了，系统就会先调用服务的onUnbind()方法，接着调用onDestroy()方法。如果调用bindService()方法前服务已经被绑定，多次调用bindService()方法并不会导致多次创建服务及绑定(也就是说onCreate()和onBind()方 法并不会被多次调用)。如果调用者希望与正在绑定的服务解除绑定，可以调用unbindService()方法，调用该方法 也会导致系统调用服务的onUnbind()-->onDestroy()方 法。

**3、请回答AIDL的全称是什么？如何工作？能处理哪些类型的数据？**

AIDL的英文全称是Android Interface Define Language。
当A进程要去调用B进程中的service时，并实现通信，我们通常都是通过AIDL来操作的

A工程：

首先我们在net.blogjava.mobile.aidlservice包中创建一个RemoteService.aidl文件，在里面我们自定义一个接口，含有方法get。ADT插件会在gen目录下自动生成一个RemoteService.java文件，该类中含有一个名为RemoteService.stub的内部类，该内部类中含有aidl文件接口的get方法。

说明一：aidl文件的位置不固定，可以任意
然后定义自己的MyService类，在MyService类中自定义一个内部类去继承RemoteService.stub这个内部类，实现get方法。在onBind方法中返回这个内部类的对象，系统会自动将这个对象封装成IBinder对象，传递给他的调用者。

其次需要在AndroidManifest.xml文件中配置MyService类，代码如下：
为什么要指定调用AIDL服务的ID,就是要告诉外界MyService这个类能够被别的进程访问，只要别的进程知道这个ID，正是有了这个ID,B工程才能找到A工程实现通信。
说明：AIDL并不需要权限

B工程：

首先我们要将A工程中生成的RemoteService.java文件拷贝到B工程中，在bindService方法中绑定aidl服务
绑定AIDL服务就是将RemoteService的ID作为intent的action参数。

说明：如果我们单独将RemoteService.aidl文件放在一个包里，那个在我们将gen目录下的该包拷贝到B工程中。如果我们将RemoteService.aidl文件和我们的其他类存放在一起，那么我们在B工程中就要建立相应的包，以保证RmoteService.java文件的报名正确，我们不能修改RemoteService.java文件
bindService(newInten("net.blogjava.mobile.aidlservice.RemoteService"),serviceConnection, Context.BIND_AUTO_CREATE);
ServiceConnection的onServiceConnected(ComponentName name,IBinderservice)方法中的service参数就是A工程中MyService类中继承了RemoteService.stub类的内部类的对象。

**4、请简述Context.startService() 、Context.bindService()这两个方法**

这两个方法都可以启动Service。

在同一个应用任何地方调用 startService() 方法就能启动 Service 了，然后系统会回调 Service 类的 onCreate() 以及 onStart() 方法。这样启动的 Service 会一直运行在后台，直到 Context.stopService() 或者 selfStop() 方法被调用。另外如果一个 Service 已经被启动，其他代码再试图调用 startService() 方法，是不会执行 onCreate() 的，但会重新执行一次 onStart() 。

另外一种 bindService() 方法的意思是，把这个 Service 和调用 Service 的客户类绑起来，如果调用这个客户类被销毁，Service 也会被销毁。用这个方法的一个好处是，bindService() 方法执行后 Service 会回调上边提到的 onBind() 方发，你可以从这里返回一个实现了 IBind 接口的类，在客户端操作这个类就能和这个服务通信了，比如得到 Service 运行的状态或其他操作。如果 Service 还没有运行，使用这个方法启动 Service 就会 onCreate() 方法而不会调用 onStart()。


