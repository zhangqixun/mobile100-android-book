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
第一个xml是布局XML文件(如：main.xml)，是这个widget的布局。一般来说如果用这个部件显示时间，那就只在这个布局XML中声明一个textview
* 
第二个xml是widget_provider.xml,主要是用于声明一个appwidget的。其中，Layout就是指定上面那个main.xml
* 
第三个xml是AndroidManifest.xml，注册broadcastReceiver信息。
* 
最后一个Java类用于做一些业务逻辑操作。让其继承类AppWidgetProvider

**3.2 实践步骤**
* 
main.xml  
```<?xml version="1.0" encoding="utf-8"?> 
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android" android:orientation="vertical" android:layout_width="fill_parent" 
    android:layout_height="fill_parent"> 
　　<TextView android:id="@+id/tvCurrTime" android:layout_width="wrap_content" android:layout_height="wrap_content" android:text="@string/hello" 
    　　android:textColor="@color/black"/> 
</LinearLayout> ```

**四、常见问题及注意事项**

*详细描述本部分的常遇到的问题以及开发过程中的注意事项*
