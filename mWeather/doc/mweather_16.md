# widget桌面插件


    小组成员: 刚占慧 邱源 汤炀 谢亮 章伟  最终完成日期：15年11月29日
# 

**一、简介**

*本模块主要是完成天气预报项目的桌面插件功能*

**二、基础知识**

*桌面插件的开发主要使用的Android中的AppWidget框架*   
* 
AppWidget框架      
    1、AppWidgetProvider ：继承自 BroadcastRecevier ， 在AppWidget 应用 update、enable、disable 和 delete 时接收通知。其中，onUpdate、onReceive 是最常用到的方法，它们接收更新通知。      
    2、 AppWidgetProvderInfo：描述 AppWidget 的大小、更新频率和初始界面等信息，以XML 文件形式存在于应用的 res/xml/目录下。   
    3、AppWidgetManger ：负责管理 AppWidget ，向 AppwidgetProvider 发送通知。  
    4、RemoteViews ：一个可以在其他应用进程中运行的类，向 AppWidgetProvider 发送通知。  

* 
AppWidget 框架的主要类介绍  
    1. AppWidgetManger类  
    getAppWidgetInfo(int appWidgetId):
    通过AppWidgetId 获取 AppWidget 信息  
    bindAppWidgetId(int appWidgetId, ComponentName provider): 通过给定的ComponentName 绑定appWidgetId  
    getAppWidgetIds(ComponentName provider):
    *通过给定的ComponentName 获取AppWidgetId  
    getAppWidgetInfo(int appWidgetId):
    通过AppWidgetId 获取 AppWidget 信息  
    getInstalledProviders():
    返回一个List< AppWidgetProviderInfo>的信息  
    getInstance(Context context):
    获取 AppWidgetManger 实例使用的上下文对象  
    updateAppWidget(int[] appWidgetIds, RemoteViews views):
    通过appWidgetId 对传进来的 RemoteView 进行修改，并重新刷新AppWidget 组件  
    updateAppWidget(ComponentName provider, RemoteViews views): 
    通过 ComponentName 对传进来的 RemoeteView 进行修改，并重新刷新AppWidget 组件  
    updateAppWidget(int appWidgetId, RemoteViews views) 通过appWidgetId 对传进来的 RemoteView 进行修改，并重新刷新AppWidget 组件

    2. 
继承自 AppWidgetProvider 可实现的方法为如下：  
    onDeleted(Context context, int[] appWidgetIds)  
    onDisabled(Context context)   
    onEnabled(Context context)  
    onReceive(Context context, Intent intent)  
  Tip:因为 AppWidgetProvider 是继承自BroadcastReceiver  所以可以重写onRecevie 方法，当然必须在后台注册Receiver
5、onUpdate(Context context, AppWidgetManager appWidgetManager, int[] appWidgetIds)

   

**三、主要思路及步骤**

**3.1 主要思路**
*一般Widget设计步骤如下*
* 
第一个xml是布局XML文件(如：weather_widget.xml)，是这个widget的布局。
* 
第二个xml是mywidget.xml,主要是用于声明一个appwidget的。
* 
第三个xml是AndroidManifest.xml，注册broadcastReceiver信息。
* 
最后一个Java类用于做一些业务逻辑操作。让其继承类AppWidgetProvider

上述涉及到的文件及其目录情况如下：


**3.2 实践步骤**
* 
main.xml布局，代码如下:  
```
<?xml version="1.0" encoding="utf-8"?>     
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"    
    android:orientation="vertical"    
    android:layout_width="fill_parent"    
    android:layout_height="fill_parent"    
    >     
    <TextView       
        android:id="@+id/wether"    
        android:layout_width="fill_parent"      
        android:layout_height="wrap_content"      
        android:text="@string/hello"    
        android:textSize="12px"    
        android:textColor="#ff0000"    
        />
    </LinearLayout>
```
* 
建立Widget内容提供者文件，我们在res下建立xml文件夹，并且新建一个widget_provider.xml代码入下:  
```
<?xml version="1.0" encoding="utf-8"?>     
<appwidget-provider xmlns:android="http://schemas.android.com/apk/res/android"    
    android:minWidth="50dip"    
    android:minHeight="50dip"    
    android:updatePeriodMillis="10000"    
    
    android:initialLayout="@layout/main"    
/>     
```
* 
修改配置文件AndroidManifest.xml，后台注册Receiver,代码如下:
```
<?xml version="1.0" encoding="utf-8"?>     
<manifest xmlns:android="http://schemas.android.com/apk/res/android"    
      package="com.android.tutor"    
      android:versionCode="1"    
      android:versionName="1.0">     
    <application android:icon="@drawable/icon" android:label="@string/app_name">     
        <receiver android:name=".WidetDemo"    
                  android:label="@string/app_name">     
            <intent-filter>     
                <action android:name="android.appwidget.action.APPWIDGET_UPDATE" />     
            </intent-filter>     
            <meta-data android:name="android.appwidget.provider"    
                       android:resource="@xml/widget_provider"    
            />     
        </receiver>     
    </application>     
    <uses-sdk android:minSdkVersion="7" />     
</manifest>
```
* 
写一个类继承自AppWidgetProvide
```
import java.util.Calendar;     
import java.util.Date;     
import java.util.GregorianCalendar;     
import java.util.Timer;     
import java.util.TimerTask;     
import android.appwidget.AppWidgetManager;     
import android.appwidget.AppWidgetProvider;     
import android.content.ComponentName;     
import android.content.Context;     
import android.widget.RemoteViews;     
public class MyWidet extends AppWidgetProvider {     
    @Override    
    public void onUpdate(Context context, AppWidgetManager appWidgetManager,     
            int[] appWidgetIds) {     
        Timer timer = new Timer();     
        timer.scheduleAtFixedRate(new MyTime(context,appWidgetManager), 1, 60000);     
        super.onUpdate(context, appWidgetManager, appWidgetIds);
    }
    private class MyTime extends TimerTask{     
        RemoteViews remoteViews;     
        AppWidgetManager appWidgetManager;     
        ComponentName thisWidget;     
             
        public MyTime(Context context,AppWidgetManager appWidgetManager){     
            this.appWidgetManager = appWidgetManager;     
            remoteViews = new RemoteViews(context.getPackageName(),R.layout.main);
            thisWidget = new ComponentName(context,MyWidet.class)
        }     
        public void run() {     
            //获取天气预报信息
            String weather="";
            remoteViews.setTextViewText(R.id.wordcup, "今天天气："+weather);     
            appWidgetManager.updateAppWidget(thisWidget, remoteViews);     
        }
    }
}    
```   
**四、常见问题及注意事项**

*详细描述本部分的常遇到的问题以及开发过程中的注意事项*
