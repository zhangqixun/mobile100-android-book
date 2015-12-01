#移动平台软件开发（Android）面试题汇总参考答案

## Question About Activity

###**声明**

    本册面试题及答案大部分摘自网络，版权归原创者所有，如有侵权请您及时联系：
    
    邮箱：1596888285@qq.com
    
    参考用书：《第一行代码》（Android）、《疯狂Android讲义》、《Android4编程入门经典》

*本答案仅供参考，如有错误，请您指正*


---


**1、Activity一般会重载哪些方法来维护其生命周期，并简述在什么情况下会调用这些方法。**

    重载的方法有：onCreate(), onStart(), onDestory(), onrestart(), onresume(), onpause(), onstop()。
    
    Activity实例是由系统自动创建，并在不同的状态期间回调相应的方法。一个最简单的完整的Activity生命周期会按照如下顺序回调：onCreate -> onStart -> onResume ->onPause -> onStop -> onDestroy。称之为entire lifetime。
    
    当执行onStart回调方法时，Activity开始被用户所见（也就是说，onCreate时用户是看不到此Activity的，那用户看到的是哪个？当然是此Activity之前的那个Activity），一直到onStop之前，此阶段Activity都是被用户可见，称之为visible lifetime。
    当执行到onResume回调方法时，Activity可以响应用户交互，一直到onPause方法之前，此阶段Activity称之为foreground lifetime。
    
    在实际应用场景中，假设A Activity位于栈顶，此时用户操作，从A Activity跳转到B Activity。那么对AB来说，具体会回调哪些生命周期中的方法呢？回调方法的具体回调顺序又是怎么样的呢？
    
    开始时，A被实例化，执行的回调有A:onCreate -> A:onStart -> A:onResume。
    
    当用户点击A中按钮来到B时，假设B全部遮挡住了A，将依次执行A:onPause -> B:onCreate -> B:onStart -> B:onResume -> A:onStop。
    
    此时如果点击Back键，将依次执行B:onPause -> A:onRestart -> A:onStart -> A:onResume -> B:onStop -> B:onDestroy。
    
    至此，Activity栈中只有A。在Android中，有两个按键在影响Activity生命周期这块需要格外区分下，即Back键和Home键。
    
    我们先直接看下实验结果：
    
    此时如果按下Back键，系统返回到桌面，并依次执行A:onPause -> A:onStop -> A:onDestroy。
    
    此时如果按下Home键（非长按），系统返回到桌面，并依次执行A:onPause -> A:onStop。由此可见，Back键和Home键主要区别在于是否会执行onDestroy。
    
    此时如果长按Home键，不同手机可能弹出不同内容，Activity生命周期未发生变化（此处不同手机可能不同）。

**2、当启动一个Activity并且新的Activity执行完后需要返回到启动它的Activity来执行的回调函数是：**startActivityResult()  。


**3、请简述如果后台Activity由于某原因被系统回收了，如何在被系统回收之前保存当前状态？**

    利用onSaveInstanceState()函数来解决Activity被系统意外回收的数据保存问题。
    当Android程序中某一个Activity A在运行时,主动或被动地运行另一个新的Activity B, 这个时候A会执行onSaveInstanceState()。B完成以后会出栈，需要运行Activity A, 这个时候就有两种情况：一是A被回收,二是A没有被回收，被回收的A就要重新调用onCreate()方法，不同于直接启动的是这回onCreate()里是带上了参数savedInstanceState；而没被收回的就直接执行onResume()，跳过onCreate()了。

**4、请简述Android系统横竖屏切换时候Activity的生命周期**

这个问题我们可以通过实验来解决：

（1）新建一个Activity，并借助Log()方法把各个生命周期打印出来

（2）运行Activity，查看Log日志得到如下信息

```
onCreate--> 
onStart--> 
onResume-->
``` 

（3）切换成横屏后，Log日志信息如下 

```
onSaveInstanceState--> 
onPause--> 
onStop--> 
onDestroy--> 
onCreate--> 
onStart--> 
onRestoreInstanceState--> 
onResume-->
```

（4）再切换成竖屏时，发现打印了两次相同的log 
```
onSaveInstanceState--> 
onPause--> 
onStop--> 
onDestroy--> 
onCreate--> 
onStart--> 
onRestoreInstanceState--> 
onResume--> 
onSaveInstanceState--> 
onPause--> 
onStop--> 
onDestroy--> 
onCreate--> 
onStart--> 
onRestoreInstanceState--> 
onResume--> 
```
（5）修改AndroidManifest.xml，把该Activity添加 
android:configChanges="orientation"，执行步骤3 

```
onSaveInstanceState--> 
onPause--> 
onStop--> 
onDestroy--> 
onCreate--> 
onStart--> 
onRestoreInstanceState--> 
onResume-->
```
（6）再执行步骤4，发现不会再打印相同信息，但多打印了一行onConfigChanged

```
onSaveInstanceState--> 
onPause--> 
onStop--> 
onDestroy--> 
onCreate--> 
onStart--> 
onRestoreInstanceState--> 
onResume--> 
onConfigurationChanged--> 
```
（7）把步骤5的android:configChanges="orientation" 改成 
android:configChanges="orientation|keyboardHidden"，执行步骤3，就只打印 
onConfigChanged

```
onConfigurationChanged--> 
```
（8）执行步骤4 

```
onConfigurationChanged--> 
onConfigurationChanged-->
```

总结： 
1、不设置Activity的android:configChanges时，切屏会重新调用各个生命周期， 
切横屏时会执行一次，切竖屏时会执行两次 

2、设置Activity的android:configChanges="orientation"时，切屏还是会重新调 
用各个生命周期，切横、竖屏时只会执行一次 

3、设置Activity的android:configChanges="orientation|keyboardHidden"时， 
切屏不会重新调用各个生命周期，只会执行onConfigurationChanged方法

总结一下整个Activity的生命周期 ：

补充一点，当前Activity产生事件弹出Toast和AlertDialog的时候Activity的生命 
周期不会有改变 

Activity运行时按下HOME键(跟被完全覆盖是一样的)：
onSaveInstanceState --> 
onPause --> onStop onRestart -->onStart--->onResume 

Activity未被完全覆盖只是失去焦点：onPause--->onResume


**5、请简述如何将一个Activity设置能窗口的样式**

（1）在res/value文件夹下的style.xml文件中加入如下代码：
```
<style name="Theme.FloatActivity" parent="android:style/Theme.Dialog">
    <!-- float_box为我们定义的窗口背景 ，这个不是必须的-->
    <item name="android:windowBackground">@drawable/float_box</item>
</style>
```
（2）在res/drawable文件夹下新建float_box.xml文件，代码如下：

```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <solid android:color="#ffffff" />
    <stroke android:width="3dp" android:color="#ff000000" />
    <corners android:radius="3dp" />
    <padding android:left="10dp" android:top="10dp" android:right="10dp" android:bottom="10dp" />
</shape>
```

（3）在AndroidMainifest.xml中Activity的声明中加入：


```
android:theme="@style/Theme.FloatActivity"
```


**6、请简述如何退出一个单一的Activity，如何安全退出已调用多个Activity的Application？**

对于单一Activity的应用来说，退出很简单，直接finish()即可。当然，也可以用killProcess()和System.exit()这样的方法。

但是对于多个Activity的Application来说，很显然这些方法不会奏效。但是有以下方法可以实现：

（1）抛异常强制退出：

该方法通过抛异常，使程序Force Close。验证可以，但是，需要解决的问题是，如何使程序结束掉，而不弹出Force Close的窗口。

（2）记录打开的Activity：每打开一个Activity，就记录下来。在需要退出时，关闭每一个Activity即可。

（3）发送特定广播：在需要结束应用时，发送一个特定的广播，每个Activity收到广播后，关闭即可。

（4）递归退出
在打开新的Activity时使用startActivityForResult，然后自己加标志，在onActivityResult中处理，递归关闭。除了第一个，都是想办法把每一个Activity都结束掉，间接达到目的。

但是这样做同样不完美。你会发现，如果自己的应用程序对每一个Activity都设置了nosensor，在两个Activity结束的间隙，sensor可能有效了。但至少，我们的目的达到了，而且没有影响用户使用。为了编程方便，最好定义一个Activity基类，处理这些共通问题。

**7、启动Activity有几种方式？尝试解释这几种方式各有什么不同？**

在实际项目中我们要根据特定的需求为每个活动指定恰当的启动模式。启动模式一共有四种，分别是：*standard、singleTop、singleTask*和*singleInstance*。我们可以在AndroidManifest.xml文件中通过给```<activity>```标签指定“```android:launchMode```”属性来选择启动模式。下面我们来一一分析他们的区别：

*standard*：

standard是Activity的默认启动模式，在不进行显示指定的情况下，所有活动都会自动使用这种启动模式。因此，如果之前你没有特意改过Activity的启动模式，那么你所有的活动都是使用的standard模式。由于Android是使用返回栈来管理活动的，在standard模式（即默认情况）下，每当启动一个新的活动，它就会在返回栈中入栈，并处于栈顶的位置。对于使用standard模式的活动，系统不会在乎这个活动是否已经在返回栈中存在，每次启动都会创建该活动的一个新的实例。

*singleTop*：

在有些情况下，我们会觉得standard模式不太合理。活动明明已经在栈顶了，为什么再次启动的时候还要创建一个新的活动实例呢？你要知道，这只是系统默认的一种启动模式而已，你完全可以根据自己的需要进行修改，比如说使用singleTop模式。当活动的启动模式指定为singleTop，在启动活动时如果发现返回栈的栈顶已经是该活动，则认为可以直接使用它，不会再创建新的活动实例。

*singleTask*：

使用singleTop模式可以很好地解决重复创建栈顶活动的问题，但是singleTop模式只会检查返回栈的栈顶是不是我们要启动的活动，如果该活动并没有处于栈顶的位置，还是可能会创建多个活动实例的。那么有没有什么办法可以让某个活动在整个应用程序的上下文中只存在一个实例呢？这就要借助singleTask模式来实现了。当活动的启动模式指定为singleTask，每次启动该活动时系统首先会在返回栈中检查是否存在该活动的实例，如果发现已经存在则直接使用该实例，并把在这个活动之上的所有活动统统出栈，如果没有发现就会创建一个新的活动实例。

*singleInstance*：

singleInstance模式应该算是四种启动模式中最特殊也最复杂的一个了。它不同于以上三种启动模式，指定为singleInstance模式的活动会启用一个新的返回栈来管理这个活动（其实如果singleTask模式指定了不同的taskAffinity，也会启动一个新的返回栈）。那么这样做有什么意义呢？想象以下场景，假设我们的程序中有一个活动是允许其他程序调用的，如果我们想实现其他程序和我们的程序可以共享这个活动的实例，应该如何实现呢？使用前面三种启动模式肯定是做不到的，因为每个应用程序都会有自己的返回栈，同一个活动在不同的返回栈中入栈时必然是创建了新的实例。而使用singleInstance模式就可以解决这个问题，在这种模式下会有一个单独的返回栈来管理这个活动，不管是哪个应用程序来访问这个活动，都共用的同一个返回栈，也就解决了共享活动实例的问题。



