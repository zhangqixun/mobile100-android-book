# 详解Android中的广播机制（1501210974 屈中山）
##**1. 广播机制简介**
为了方便进行系统级别的消息通知，Android引入了一套广播消息机制。Android中的广播机制非常灵活，每个应用程序都可以对自己感兴趣的广播进行注册，这样该程序就只会接收到自己所关心的广播内容，这些广播可能是来自系统的，也可能是来自其他应用程序的。Android提供了一套完整的API，允许应用程序自由地发送和接受广播。
##**2. 广播的类型**
Android中的广播可以分为两种类型，标准广播和有序广播。
标准广播（Normal broadcasts）是一种完全异步执行的广播，在广播发出之后，所有的广播接收器机会都会在同一时刻接收到这条广播消息，因此它们之间没有任何先后顺序可言。这种广播的效率会比较高，但同时也意味着它是无法被截断的。
有序广播（Ordered broadcasts）则是一种同步执行的广播，在广播发出之后，同一时刻只会有一个广播接收器能够收到这条广播消息，当这个广播接收器中的逻辑执行完毕后，广播才会继续传递。所以此时的广播接收器是有先后顺序的，优先级高的广播接收器就可以先接收到消息，而且前面的广播接收器还可以截断正在传递的广播，这样后面的广播接收器就无法收到广播消息了。
##**3. 接收系统广播**
Android中内置了很多系统级别的广播，开发者可以在应用程序中通过监听这些广播来得到各种系统的状态信息。比如手机开机完成后会发出一条广播，电池的电量发生变化会发出一条广播，时间和时区发生变化也会发出一条广播等等。如果想要接收到这些广播，就需要使用广播接收器。
###**3.1 广播注册方式**
注册广播的方式一般有两种，在代码中注册和在AndroidManifest.xml文件中注册，其中前者也称为动态注册，后者也称为静态注册。
###**3.2 动态注册监听网络变化**
① 新建一个名称为BroadcastTest的项目，在MainActivity中定义一个内部类NetworkChangeReceiver，使这个类继承BroadcastReceiver类，并重写父类的onReceive（）方法。这样当网络状态发生变化时，onReceive（）方法就会得到执行。在onReceive（）方法中，首先通过getSystemService（）方法得到了ConnectivityManager的实例，只是一个系统服务类，专门用于管理网络连接的。然后调用它的getActiveNetworkInfo（）方法可以得到NetworkInfo的实例，接着调用NetworkInfo的isAvailable（）方法，就可以判断出当前是否有网络了，最后通过Toast的方式对用户进行提示。具体代码如下：
    
    class NetworkChangeReceiver extends BroadcastReceiver {
        @Override
        public void onReceive(Context context, Intent intent) {
            ConnectivityManager connectionManager = (ConnectivityManager)getSystemService(Context.CONNECTIVITY_SERVICE);
            NetworkInfo networkInfo = connectionManager.getActiveNetworkInfo();
            if (networkInfo != null && networkInfo.isAvailable()) {
                Toast.makeText(context, "网络可用", Toast.LENGTH_SHORT).show();
            } else {
                Toast.makeText(context, "网络不可用", Toast.LENGTH_SHORT).show();
            }
        }
    } 
    
② 修改MainActivity中的代码，重写onCreate（）方法和onDestroy（）方法。在onCreate（）方法中，首先创建一个IntentFilter的实例，并且为它添加一个值为android.net.conn.CONNECITVITY_CHANGE的action。接下来创建一个NetworkChangeReceiver的实例，然后调用registerReceiver（）方法进行注册，将NetworkChangeReceiver的实例和IntentFilter的实例都传进去，这样NetworkChangeReceiver就会收到所有值为android.net.conn.CONNECTIVITY_CHANGE的广播，也就实现了监听网络变化的工能。注意，动态注册的广播接收器一定要取消注册才行，所以在onDestroy方法中通过调用unregisterReceiver（）方法来实现取消注册。具体代码如下：

public class MainActivity extends Activity {

    private IntentFilter intentFilter;
    private NetworkChangeReceiver networkChangeReceiver;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        intentFilter = new IntentFilter();
        intentFilter.addAction("android.net.conn.CONNECTIVITY_CHANGE");
        networkChangeReceiver = new NetworkChangeReceiver();
        registerReceiver(networkChangeReceiver, intentFilter);
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        unregisterReceiver(networkChangeReceiver);
    }

    class NetworkChangeReceiver extends BroadcastReceiver {

        @Override
        public void onReceive(Context context, Intent intent) {
            ConnectivityManager connectionManager = (ConnectivityManager)getSystemService(Context.CONNECTIVITY_SERVICE);
            NetworkInfo networkInfo = connectionManager.getActiveNetworkInfo();
            if (networkInfo != null && networkInfo.isAvailable()) {
                Toast.makeText(context, "网络可用", Toast.LENGTH_SHORT).show();
            } else {
                Toast.makeText(context, "网络不可用", Toast.LENGTH_SHORT).show();
            }
        }
    }
}

③ 在AndroidManifest.xml文件中加入可以查询网络状态的权限。Android系统为了保证应用程序可安全性做了规定，如果程序要访问一些系统的关键性信息，必须在配置文件中声明权限才可以，否则程序将会直接崩溃。具体代码如下：

<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.broadcasttest" >
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

④ 查看运行结果。运行程序后按下Home键，点击打开/关闭数据网络开关，查看效果。








