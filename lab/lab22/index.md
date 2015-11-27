# Service

起草人: 郝建勇   日期：15年11月26日

修改完善：OOOO   日期：15年00月00日

# 

**一、实验目的**

*本次实验的主要目的：如何将Service应用到天气预报项目中*

**二、基础知识**

*Service是Android中四大组件之一，在Android开发中起到非常重要的作用，Service（服务）是一个没有用户界面的在后台运行执行耗时操作的应用组件。其他应用组件能够启动Service，并且当用户切换到另外的应用场景，Service将持续在后台运行。另外，一个组件能够绑定到一个service与之交互（IPC机制），例如，一个service可能会处理网络操作，播放音乐，操作文件I/O或者与内容提供者（content provider）交互，所有这些活动都是在后台进行。*
   
   
* Service生命周期：

      两幅图：   
* Service有两种状态，“启动的”和“绑定”：

      通过startService()启动的服务处于“启动的”状态，一旦启动，service就在后台运行，即使启动它的应用组件已经被销毁了。
      通常started状态的service执行单任务并且不返回任何结果给启动者。比如当下载或上传一个文件，当这项操作完成时，service应该停止它本身。
      还有一种“绑定”状态的service，通过调用bindService()来启动，一个绑定的service提供一个允许组件与service交互的接口，可以发送请求、获取返回结果，还可以通过夸进程通信来交互（IPC）。
      绑定的service只有当应用组件绑定后才能运行，多个组件可以绑定一个service，当调用unbind()方法时，这个service就会被销毁了。
      
      另外，在官方的说明文档中还有一个警告：
      service与activity一样都存在与当前进程的主线程中，所以，一些阻塞UI的操作，比如耗时操作不能放在service里进行，比如另外开启一个线程来处理诸如网络请求的耗时操作。
      如果在service里进行一些耗CPU和耗时操作，可能会引发ANR警告，这时应用会弹出是强制关闭还是等待的对话框。所以，对service的理解就是和activity平级的，只不过是看不见的，在后台运行的一个组件，这也是为什么和activity同被说为Android的基本组件。
      
      关于停止Service，如果service是非绑定的，最终当任务完成时，为了节省系统资源，一定要停止service，可以通过stopSelf()来停止，也可以在其他组件中通过stopService()来停止，绑定的service可以通过onUnBind()来停止service。

* Service的一个子类IntentService：
      IntentService使用队列的方式将请求的Intent加入队列，然后开启一个worker thread(线程)来处理队列中的Intent。
      对于异步的startService请求，IntentService会处理完成一个之后再处理第二个，每一个请求都会在一个单独的worker thread中处理，不会阻塞应用程序的主线程。
      这里就给我们提供了一个思路，如果有耗时的操作与其在Service里面开启新线程还不如使用IntentService来处理耗时操作。
      而在一般的继承Service里面如果要进行耗时操作就必须另开线程，但是使用IntentService就可以直接在里面进行耗时操作，因为默认实现了一个worker thread。对于异步的startService请求，IntentService会处理完成一个之后再处理第二个。


   

**三、实验内容及步骤**

**3.1 实验内容**

*本实验主要介绍如何使用Service及如何将Service用到天气预报项目中，实现天气预报的后台更新*

**3.2 实验步骤**

*首先用一个教材上的小例子简单介绍一下Service的用法，及使用的主要步骤*
     
    首先创建一个类继承自Service，实现以下方法
    public class my_service extends Service {
        @Override
        public IBinder onBind(Intent intent) {
            return null;
        }

        @Override
        public int onStartCommand(Intent intent, int flags, int startId) {
            Toast.makeText(this,"Service Start",Toast.LENGTH_SHORT).show();
            return START_STICKY;
        }

        @Override
        public void onDestroy() {
            super.onDestroy();
            Toast.makeText(this,"Service Destroyed",Toast.LENGTH_SHORT).show();
        }
    }
    然后是一个布局文件：
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
    android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".service">

    <Button
        android:id="@+id/btnStartService"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="Start Service"
        android:onClick="startService"
        />
    <Button
        android:id="@+id/btnStopService"
        android:layout_below="@+id/btnStartService"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="Stop Service"
        android:onClick="stopService"/>
    </RelativeLayout>
    创建一个类继承自Activity：
    public class ServicesActivity extends Activity {
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_service);
        }

        public void startService(View view){
            startService(new Intent(getBaseContext(),my_service.class));
        }

        public void stopService(View view) {
            stopService(new Intent(getBaseContext(),my_service.class));
        }
    }
    最后，不要忘记在AndroidManifest.xml文件中声明：
    <activity android:name=".ServicesActivity"/>
    <service android:name=".my_service" />
*下面介绍如何将Service用到天气预报项目中，实现后台更新天气*
     
     .....

**四、常见问题及注意事项**

*详细描述本此实验的可能会遇到的问题以及相关的注意事项*


