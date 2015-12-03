# Android小工具开发（1501211016杨晓）





Android小工具开发






作者：杨晓
学院：软件与微电子学院
学号：1501211016











摘  要
安卓小工具（App Widgets）是可以嵌入在其他程序中运行的程序，小工具开发简单，支持的功能也有限，但是小巧精悍，常常用于在桌面显示一些关键信息，桌面上的日历，天气，时间，都是小工具的常见应用。
本文介绍了安卓上小工具开发的方方面面，对于常见的问题给出了解决方案。

关键字：android， widget，小工具
 














正文

开发小工具的一般流程：
1 修改manifest，添加AppWidgetProvider类型的Reciver，在meta-data中填写AppWidgetProviderInfo 配置文件, 

```
<receiver android:name="AppWidget">
    <intent-filter>
        <action android:name="android.appwidget.action.APPWIDGET_UPDATE"></action>
    </intent-filter>
    <meta-data android:name="android.appwidget.provider"
        android:resource="@xml/appwidget01" />
</receiver>
```


2 在res下新建xml目录，创建appwidget01：
```
<appwidget-provider
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:minWidth = "294dp"
    android:minHeight = "72dp"
    android:updatePeriodMillis = "86400000"
    android:initialLayout = "@layout/appwidgetlayout"
    >
</appwidget-provider>
```
这是widget配置信息，updatePeriodMillis表示多久执行一次Widget的OnUpdate（），initialLayout表示layout文件。

3 创建layout文件
Widget支持以下的layout：
```
FrameLayout
LinearLayout
RelativeLayout
GridLayout
```

同时，支持以下的Widget Class
```

AnalogClock
Button
Chronometer
ImageButton
ImageView
ProgressBar
TextView
ViewFlipper
ListView
GridView
StackView
AdapterViewFlipper
```

4 创建类AppWidgetProvider
```
public class AppWidget extends AppWidgetProvider
{

    private final String broadCastString = "com.qlf.appWidgetUpdate";

    /**
     * 删除一个AppWidget时调用
     * */
    @Override
    public void onDeleted(Context context, int[] appWidgetIds)
    {
        super.onDeleted(context, appWidgetIds);
    }

    /**
     * 最后一个appWidget被删除时调用
     * */
    @Override
    public void onDisabled(Context context)
    {
        super.onDisabled(context);
    }

    /**
     * AppWidget的实例第一次被创建时调用
     * */
    @Override
    public void onEnabled(Context context)
    {
        super.onEnabled(context);
    }

    /**
     * 接受广播事件
     * */
    @Override
    public void onReceive(Context context, Intent intent)
    {

        super.onReceive(context, intent);
    }

    /**
     * 到达指定的更新时间或者当用户向桌面添加AppWidget时被调用
     * */
    @Override
    public void onUpdate(Context context, AppWidgetManager appWidgetManager,
                         int[] appWidgetIds)
    {


    }

}
```

5 运行程序，在桌面上长按，就可以看到小工具，拖动到桌面上，done！

6 接下来为天气预报开发一个小工具，显示当前天气，温度。
```
AppWidgetManager  widgetmanager = AppWidgetManager.getInstance(MyService.this);
ComponentName name = new ComponentName(MyService.this,AppWidget.class);// 获取前面参数包下的后参数的Widget
RemoteViews views = new RemoteViews("cn.itcast.mobilesafe",
        R.layout.appwidgetlayout);// 获取Widget的布局
views.setTextViewText(R.id.todayWeather, "北京 雾霾 pm2.5 1000");//
widgetmanager.updateAppWidget(name, views);//更新Widget
```

可以看出对widget的更新是通过RemoteViews实现的。

Widget的生命周期：
Widget有以下的生命周期方法：
1.onEnabled方法：此方法在Widget第一次被创建的时候调用，并且只调用一次，此方法中常放入初始化数据，服务的操作。

2.onReceive方法：通BroadcastReceiver的OnReceive方法，但是这里有所不同的是，当接收到Widget操作时首先调用的是OnReceive方法，然后才是相关的操作方法。这也很好理解，Widget的是运行在桌面运用程序中的小控件，当自己的应用程序需要调用Widget是，就需要发送广播事件去调用。

3.onUpdate：Widget在固定的时间里更新时调用的方法。

4.onDeleted：Widget被删除时调用的方法。

5.onDisabled：所用Widget被删除是调用的方法，同onEnabled方法相对。

使用PendingIntent
对Widget的事件的处理要通过pendingIntent进行。
pendingIntent是一种特殊的Intent。主要的区别在于Intent的执行立刻的，而pendingIntent的执行不是立刻的。pendingIntent执行的操作实质上是参数传进来的Intent的操作，但是使用pendingIntent的目的在于它所包含的Intent的操作的执行是需要满足某些条件的。
```

             // Create an Intent to launch ExampleActivity
            Intent intent = new Intent(context, ExampleActivity.class);
            PendingIntent pendingIntent = PendingIntent.getActivity(context, 0, intent, 0);

            // Get the layout for the App Widget and attach an on-click listener
            // to the button
            RemoteViews views = new RemoteViews(context.getPackageName(), R.layout.appwidget_provider_layout);
            views.setOnClickPendingIntent(R.id.button, pendingIntent);

            // Tell the AppWidgetManager to perform an update on the current app widget
            appWidgetManager.updateAppWidget(appWidgetId, views);
            ```
设置预览图
在选择小工具界面会显示预览图，如果没有设置预览图，将会显示app的图标。
预览图在XML中配置：
```
<appwidget-provider xmlns:android="http://schemas.android.com/apk/res/android"
  ...
  android:previewImage="@drawable/preview">
</appwidget-provider>
```

在锁屏界面上显示小工具
最好让我们的天气预报小工具能在锁屏界面直接显示，这么我们就不用麻烦的解锁了。
只要进行稍许配置，就能实现在锁屏界面上显示小工具：
```

<appwidget-provider xmlns:android="http://schemas.android.com/apk/res/android"
   ...
   android:widgetCategory="keyguard|home_screen">
</appwidget-provider>
```
现在我们的小工具既可能出现在桌面上，也可能在解锁界面上，我们可能需要对这两种情况配置不同的界面
首先我们需要获取当前处在哪个界面上：
```
AppWidgetManager appWidgetManager;
int widgetId;
Bundle myOptions = appWidgetManager.getAppWidgetOptions (widgetId);

// Get the value of OPTION_APPWIDGET_HOST_CATEGORY
int category = myOptions.getInt(AppWidgetManager.OPTION_APPWIDGET_HOST_CATEGORY, -1);

// If the value is WIDGET_CATEGORY_KEYGUARD, it's a lockscreen widget
boolean isKeyguard = category == AppWidgetProviderInfo.WIDGET_CATEGORY_KEYGUARD;

然后再根据不同的情况选择不同的layout：

int baseLayout = isKeyguard ? R.layout.keyguard_widget_layout : R.layout.widget_layout;
```

使用集合的widget
我们可以使用RemoteViewsService提供的数据，将一系列数据显示在widget里面，我们需要用到以下的”collection view”:
1 List View，显示一列纵向滚动的数据，比如Gmail小工具
2 Grid View，显示2维数据，比如Bookmarks小工具
3StackView，以卡片盒子的形式显示数据

4AdapterViewFlipper，显示多个view，可以在多个view之间切换。每次只显示一个view

如上所述，这些collection views将显示由远程数据所组成的数据集合.这就意味着我们需要一个Adapter来把数据绑定到视图。
这个Adapter要负责把数据集的单个数据项和单个View对象进行绑定.因为collection views是由adapters在后台提供的, 所以Android framework必须使用另外一种框架来支持在app widgets中使用它们. 在app widget应用中, 我们使用RemoteViewsFactory来代替Adapter , RemoteViewsFactory提供了和Adapter类似的接口.当请求数据集的一个数据项的时候，RemoteViewsFactory将以一个RemoteViews的形式返回数据集的一个数据项.为了在app widget中能使用collection view, 你必须实现一个RemoteViewsService和RemoteViewsFactory
RemoteViewsService是一个service，远程的adapter可以通过它请求并获得RemoteViews对象. 

实现一个collection view有如下步骤：

1 配置widget的RemoteViewService，为了让Widget正确的从RemoteViewsService获取数据，我们为RemoteViewService配置BIND_REMOTEVIEWS，这种策略确保我们的Widget不会被别的app篡改。RemoteViewService大概是这样：
<service android:name="MyWidgetService"
...
android:permission="android.permission.BIND_REMOTEVIEWS" />
MyWidgetService是我们的RemoteViewService的类名

2 在layout文件中使用collection view：
```
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <StackView xmlns:android="http://schemas.android.com/apk/res/android"
        android:id="@+id/stack_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:gravity="center"
        android:loopViews="true" />
    <TextView xmlns:android="http://schemas.android.com/apk/res/android"
        android:id="@+id/empty_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:gravity="center"
        android:background="@drawable/widget_item_background"
        android:textColor="#ffffff"
        android:textStyle="bold"
        android:text="@string/empty_view_text"
        android:textSize="20sp" />
</FrameLayout>
```
除了这个布局文件，你改要编写另外的布局文件，以用于数据项的View布局。比如，在StackView Widget示例程序中它只有一个用于数据项显示的View布局的文件, widget_item.xml。而在WeatherList Widget示例程序中，它只有两个用于数据项显示的View布局的文件: dark_widget_item.xml和light_widget_item.xml

3 AppWidgetProvider
app widgets with collections像一般的app widget一样，你主要代码应该在AppWidgetProvider的onUpdate()方法中。
主要区别在于，在 app widgets with collections中，你必须对RemoteViews调用setRemoteAdapter方法。这个函数能告诉collection view能在哪里取得它的数据.。RemoteViewsService必须能返回一个RemoteViewsFactory对象。当你调用setRemoteAdapter()方法的时候, 你必须传递一个用于启动你的RemoteViewsService的intent和一个App Widget ID用于说明对哪个app widget进行更新.

4 RemoteViewsService

如上面描述那样, 你的RemoteViewsService应该编写一个RemoteViewsFactory，以提供数据项的remote collection view.
具体来说，您需要执行这些步骤：
第一、新建一个RemoteViewsService的子类. RemoteViewsService是个service，通过它remote adapter可以请求并获得RemoteViews.
第二，在你RemoteViewsService的子类中, 添加一个实现了RemoteViewsFactory接口的方法. RemoteViewsFactory为collection view (such as ListView, GridView, and so on)和collection view的数据提供了适配接口。在RemoteViewsFactory中，你要负责为数据集的每个数据项提供一个RemoteViews对象. RemoteViewsFactory相等于Adapter的一个精简版本。
编写RemoteViewsService的主要工作集中在RemoteViewsFactory,关于次将在下文进行介绍

5、RemoteViewsFactory interface
你的RemoteViewsFactory需要为app widget提供数据集的每个数据项其对应的RemoteViews对象
为此, 你需要用app widget数据项的View布局文件和数据集的数据项来合成RemoteViews对象。数据源可以为任何形式，可以是一个简单的数组，也来自于数据库。在StackView Widget示例程序中, 数据源是个WidgetItems数组. RemoteViewsFactory的主要功能就是把数据变成 RemoteViews.
在RemoteViewsFactory中，你主要需要实现onCreate() 和getViewAt()这两个方法 。
当RemoteViewsFactory首次被创建时，onCreate()方法将被调用. 
在该函数中，你应该做些初始工作，比如建立针对你的数据源的连接或光标。比如, 在StackView Widget示例程序中的onCreate()方法中，
就初始了一个WidgetItem对象数组. 当你app widget活动的时候, 系统可以通过数组索引号来访问这些WidgetItem对象。

6，设置数据项View的行为
在普通的App Widgets中，我们可以通过setOnClickPendingIntent()来设置View控件Click时，发送的Intent.通过次方法，我们就可以实现当用户点击一个按钮时，就启动一个Activity.但是这种方法对app widget collection的数据集的数据项View并不适用（你可以像Gmail app widget中一样通过setOnClickPendingIntent()来设置global button的click行为来启动一个应用程序, 但是对数据集的数据项View不能使用这种方法）。替代的是首先使用setOnClickFillInIntent()方法为数据集的数据项View统一设置其Pending的Intent模板,然后在RemoteViewsFactory中为数据集的数据项View的Pending的Intent模板的设置填充Intent。这样当数据项View在Click时，发送的Intent将是其Pending的Intent模板和其Pending的Intent模板填充Intent合成的Intent。

7，更新collection view
下图说明了App Widget with Collections如何进行数据更新。它也给我们展示了App Widget代码如何和RemoteViewsFactory进行交互, 和我们App Widget with Collections应该如何请求进行数据更新。
 App Widgets with collections的一个重要属性就是可以让用户进行手动更新数据.比如Android 3.0中的Gmail app widget, 提供邮件快照的inbox. 为了显示最新的数据, 你需要让RemoteViewsFactory提取数据并更新collection view的显示. 
你可以通过调用AppWidgetManager的notifyAppWidgetViewDataChanged (int appWidgetId, int viewId)方法来请求更新. 
调用了该方法之后，系统会对RemoteViewsFactory的onDataSetChanged()方法进行回调, 在这里你可以进行最新数据的提取. 
注意:你可以在 onDataSetChanged()回调函数中做一些耗时工作（同步的）的处理。系统将保证这些操作在从RemoteViewsFactory返回新数据的信息或View之前完成。
另外, 你可以在getViewAt()返回中进行耗时操作的处理. 如果getViewAt()超过一定的时限，系统就先用RemoteViewsFactory的getLoadingView()返回的View进行显示，直到getViewAt()完成。






















参考文献：

[1]Android中pendingIntent的深入理解,http://blog.csdn.net/yuzhiboyi/article/details/8484771

[2]官方文档，http://wear.techbrood.com/guide/topics/appwidgets/index.html#collections

[3]Android widgets应用学习笔记,http://mobile.51cto.com/widget-290434.htm

