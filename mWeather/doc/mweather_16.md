# widget桌面插件


    小组成员: 刚占慧 邱源 汤炀 谢亮 章伟  最终完成日期：15年11月29日
# 

**一、简介**

*本模块主要是完成天气预报项目的桌面插件功能*

**二、基础知识**

*桌面插件的开发主要使用的Android中的AppWidget框架*
1. AppWidget框架      
    1）AppWidgetProvider ：继承自 BroadcastRecevier ， 在AppWidget 应用 update、enable、disable 和 delete 时接收通知。其中，onUpdate、onReceive 是最常用到的方法，它们接收更新通知。      
    2） AppWidgetProvderInfo：描述 AppWidget 的大小、更新频率和初始界面等信息，以XML 文件形式存在于应用的 res/xml/目录下。   
    3）AppWidgetManger ：负责管理 AppWidget ，向 AppwidgetProvider 发送通知。  
    4）RemoteViews ：一个可以在其他应用进程中运行的类，向 AppWidgetProvider 发送通知。
2. AppWidget 框架的主要类介绍        
    1) AppWidgetManger类     
    * 
    bindAppWidgetId(int appWidgetId, ComponentName provider)  
    *通过给定的ComponentName 绑定appWidgetId  * 
    * 
    getAppWidgetIds(ComponentName provider)  
    *通过给定的ComponentName 获取AppWidgetId    *
    * 
    getAppWidgetInfo(int appWidgetId)  
    *通过AppWidgetId 获取 AppWidget 信息  *
    * 
    getInstalledProviders()  
    *返回一个List<AppWidgetProviderInfo>的信息  *
    * 
    getInstance(Context context)   
    *获取 AppWidgetManger 实例使用的上下文对象   *
    * 
    updateAppWidget(int[] appWidgetIds, RemoteViews views)   
    *通过appWidgetId 对传进来的 RemoteView 进行修改，并重新刷新AppWidget 组件   *
    * 
    updateAppWidget(ComponentName provider, RemoteViews views)  
    *通过 ComponentName 对传进来的 RemoeteView 进行修改，并重新刷新AppWidget 组件   *
    * 
    updateAppWidget(int appWidgetId, RemoteViews views)  
    *通过appWidgetId 对传进来的 RemoteView 进行修改，并重新刷新AppWidget 组件   *
3. 

   

**三、主要思路及步骤**

**3.1 主要思路**

*简要介绍主要思路*

**3.2 实践步骤**

*详细描述开发的具体步骤*

**四、常见问题及注意事项**

*详细描述本部分的常遇到的问题以及开发过程中的注意事项*
