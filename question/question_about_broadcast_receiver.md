

移动平台软件开发（Android）
面试题汇总参考答案


声明
本册面试题及答案大部分摘自网络，版权归原创者所有，如有侵权请及时联系

参考用书：《第一行代码》（Android）、《疯狂Android讲义》、《Android4编程入门经典》

（本答案仅供参考，如有错误，请您指正）
邮箱：1596888285@qq.com
 
1、请简述注册广播有几种方式，这些方式有何优缺点？
BroadcastReceiver用于监听被广播的事件必须被注册，注册的方式有两种方法：
1、在应用程序的代码中注册
注册BroadcastReceiver：registerReceiver（receiver，filter）；
取消注册BroadcastReceiver：unregisterReceiver（receiver）；
当BroadcastReceiver更新UI，通常会使用这样的方法注册。启动Activity时候注册BroadcastReceiver，Activity不可见时候，取消注册。
2、在androidmanifest.xml当中注册
<receiver>
    <intent-filter>
     <action android:name = "android.intent.action.PICK"/>
    </intent-filter>
</receiver>
1)第一种不是常驻型广播，也就是说广播跟随程序的生命周期。
2)第二种是常驻型，也就是说当应用程序关闭后，如果有信息广播来，程序也会被系统调用自动运行。使用这样的方法注册弊端：它会始终处于活动状态，毕竟是手机开发，cpu和电源资源比较少，一直处于活动耗费大，不利。

2、请尝试简述Android引入广播的意图
a:从MVC的角度考虑(应用程序内)
其实回答这个问题的时候还可以这样问，android为什么要有那4大组件，现在的移动开发模型基本上也是照搬的web那一套MVC架构，只不过是改了点嫁妆而已。android的四大组件本质上就是为了实现移动或者说嵌入式设备上的MVC架构，它们之间有时候是一种相互依存的关系，有时候又是一种补充关系，引入广播机制可以方便几大组件的信息和数据交互。
b：程序间互通消息(例如在自己的应用程序内监听系统来电)
c：效率上(参考UDP的广播协议在局域网的方便性)
d：设计模式上(反转控制的一种应用，类似监听者模式)

3、android有序广播和无序广播的区别
BroadcastReceiver所对应的广播分两类：普通广播和有序广播。
普通广播：通过Context.sendBroadcast()方法来发送，它是完全异步的。
所有的receivers（接收器）的执行顺序不确定，因此所有的receivers（接收器）接收broadcast的顺序不确定。
这种方式效率更高，但是BroadcastReceiver无法使用setResult系列、getResult系列及abort（中止）系列API
有序广播：是通过Context.sendOrderedBroadcast来发送，所有的receiver依次执行。
BroadcastReceiver可以使用setResult系列函数来结果传给下一个BroadcastReceiver，通过getResult系列函数来取得上个BroadcastReceiver返回的结果，并可以abort系列函数来让系统丢弃该广播，使用该广播不再传送到别的BroadcastReceiver。
可以通过在intent-filter中设置android:priority属性来设置receiver的优先级，优先级相同的receiver其执行顺序不确定。
如果BroadcastReceiver是代码中注册的话，且其intent-filter拥有相同android:priority属性的话，先注册的将先收到广播。
有序广播，即从优先级别最高的广播接收器开始接收，接收完了如果没有丢弃，就下传给下一个次高优先级别的广播接收器进行处理，依次类推，直到最后。如果多个应用程序设置的优先级别相同，则谁先注册的广播，谁就可以优先接收到广播。

4、广播可以通过几种方法来注册，分别是如何实现的。（如果你认为必要，可以写下相应代码）
BroadcastReceiver用于监听被广播的事件必须被注册，注册的方式有两种方法：
1、在应用程序的代码中注册
注册BroadcastReceiver：registerReceiver（receiver，filter）；
取消注册BroadcastReceiver：unregisterReceiver（receiver）；
当BroadcastReceiver更新UI，通常会使用这样的方法注册。启动Activity时候注册BroadcastReceiver，Activity不可见时候，取消注册。
2、在androidmanifest.xml当中注册
<receiver>
    <intent-filter>
     <action android:name = "android.intent.action.PICK"/>
    </intent-filter>
</receiver>
1)第一种不是常驻型广播，也就是说广播跟随程序的生命周期。
2)第二种是常驻型，也就是说当应用程序关闭后，如果有信息广播来，程序也会被系统调用自动运行。使用这样的方法注册弊端：它会始终处于活动状态，毕竟是手机开发，cpu和电源资源比较少，一直处于活动耗费大，不利。


