#移动平台软件开发（Android）面试题汇总参考答案

## Question About Others

###**声明**

本册面试题及答案大部分摘自网络，版权归原创者所有，如有侵权请您及时联系：

邮箱：1596888285@qq.com

参考用书：《第一行代码》（Android）、《疯狂Android讲义》、《Android4编程入门经典》

*本答案仅供参考，如有错误，请您指正*


---
**1、简述Android系统架构**

Android系统架构可以大致分为四层架构：**linux内核层、系统运行库层、应用框架层、应用层**。

**linux内核层**为Android设备的各种硬件提供了底层驱动，如显示驱动、音频驱动、蓝牙驱动等。

**系统运行库层**（C/C++库和Android运行环境RunTime）主要通过一些C/C++库来为Android系统提供了主要的特性支持。如SGL：2D图形引擎库 。SSL：即Secure Socket Layer位于TCP/IP协议与各种应用层协议之间，为数据通信提供安全支持。同在这一层的还有Android运行时库，主要提供一些核心库，能够允许开发者使用Java语言来编写Android应用。

另外Android运行时库还包括Dalvik虚拟机。Android中的所有Java程序都是运行在Dalvik VM上的。Android上的每个程序都有自己的线程，DVM只执行.dex的Dalvik executable 文件。每个Android应用在底层都对应有一个独立的DVM实例并在其解释下执行。

**应用框架层**为应用程序层，这一层是程序员开发自己程序（包括Google发布的应用）的开发者提供API框架，它实际上是一个应用程序的框架。我们在开发应用程序时都是通过框架与Android底层进行交互的。可以简化程序开发的架构设计，但是必须遵守其框架的开发原则。

**应用层**使用 Java 语言编写运行在虚拟机上的程序。这些程序主要是UI方面的，还包含各种资源文件（res目录中），Java程序及其相关资源经过编译后，生成一个.apk包。
Google在Android中内置了一些必要的应用程序，如联系人，主屏幕（Home），日历，地图，浏览器，SMS短消息程序。 应用程序的开发者还可以使用应用程序框架层的API 实现自己的程序。这也是Android 开源的巨大潜力的体现。

**2、Android的四大组件：活动（Activity）、服务（Service）、广播接收器（Broadcast）、内容提供器（Content Provider）**。

**3、Android日志工具Log有几种方法？分别介绍这些不同的方法。**

Android日志工具Log有五种常用的方法。分别是Log.v()、Log.d()、Log.i()、Log.w()、Log.e()。

Log.v()对应级别为verbose。从单词可以看出，这个方法打印的日志文件是很啰嗦冗长的。是android日志里面级别最低的一种。

Log.d()对应级别为debug。这个方法会打印一些调试信息，这些信息对于程序员来讲在调试程序和分析问题方面很有帮助。级别相对verbose较高。

Log.i()对应级别information，这个方法会打印一些相对比较重要的信息或数据，这些信息和数据可以帮助分析用户行为。级别相对debug较高。

Log.w()对应级别warning，这个方法会打印一些我们在程序编写过程中代码可能会出现问题的警告信息，用来提示程序员这样的代码可能会存在风险，如有需要应该修复。级别相对debug较高。

Log.e()对应级别error，这个方法会打印程序的错误信息，错误信息指代码已经出现了严重的问题，当我们看到错误信息时候，应该尽快修复。级别相对warning较高，是Android日志级别里面最高的一种。

**4、简述Androidmanifest.xml在一个android程序中的作用，并且说明Androidmanifest.xml中一般包含哪些信息。**

AndroidManifest.xml 是每个android程序中必须的文件。它是整个Android应用的全局描述文件，位于整个项目的根目录，描述了package中暴露的组件（activities, services, 等等），他们各自的实现类，各种能被处理的数据和启动位置。 除了能声明程序中的Activities, ContentProviders, Services, 和Intent Receivers,还能指定permissions和instrumentation（安全控制和测试）
Androidmanifest.xml清单文件通常可以包含如下信息：

*应用程序的包名，该包名将会作为该应用的唯一标识。

*应用程序所包含的组件，如Activity、Service、BroadcastReceiver和ContentProvider等。

*应用程序兼容的最低版本。

*应用程序使用系统所需的权限声明。

*其它程序访问该程序所需的权限声明。


**5、请简述一下Android中的Activity、Intent、Content Provider、Service等关键词**

**Activity：** 活动，是最基本的android应用程序组件。一个活动就是一个单独的屏幕，每一个活动都被实现为一个独立的类，并且从活动基类继承而来。

**Intent：** 意图，描述应用想干什么。最重要的部分是动作和动作对应的数据。

**Service：**服务，具有一段较长生命周期且没有用户界面的程序。例如媒体播放器，用户在导航到其他屏幕的时候，音乐应该还是继续播放的，媒体播放器就会使用一个StartService()来启动一个服务，保证播放

**Content Provider：**内容提供器，android应用程序能够将它们的数据保存到文件、SQLite数据库中，甚至是任何有效的设备中。当你想将你的应用数据和其他应用共享时，内容提供器就可以发挥作用了。

**6、请简述何为“ANR”,我们如何避免ANR问题**

在Android上，如果你的应用程序有一段时间响应不够灵敏，系统会向用户显示一个对话框，这个对话框称作应用程序无响应（ANR：Application Not Responding）对话框。用户可以选择让程序继续运行，但是，他们在使用你的应用程序时，并不希望每次都要处理这个对话框。因此，在程序里对响应性能的设计很重要，这样，系统不会显示ANR给用户。

如何避免它：

考虑上面的ANR定义，让我们来研究一下为什么它会在Android应用程序里发生和如何最佳构建应用程序来避免ANR。

Android应用程序通常是运行在一个单独的线程（例如main）里。这意味着你的应用程序所做的事情如果在主线程里占用了太长的时间的话，就会引发ANR对话框，因为你的应用程序并没有给自己机会来处理输入事件或者Intent广播。

因此，运行在主线程里的任何方法都尽可能少做事情。特别是，Activity应该在它的关键生命周期方法（如onCreate()和onResume()）里尽可能少的去做创建操作。潜在的耗时操作，例如网络或数据库操作，或者高耗时的计算如改变位图尺寸，应该在子线程里（或者以数据库操作为例，通过异步请求的方式）来完成。然而，不是说你的主线程阻塞在那里等待子线程的完成——也不是调用Thread.wait()或是Thread.sleep()。替代的方法是，主线程应该为子线程提供一个Handler，以便完成时能够提交给主线程。以这种方式设计你的应用程序，将能保证你的主线程保持对输入的响应性并能避免由于5秒输入事件的超时引发的ANR对话框。这种做法应该在其它显示UI的线程里效仿，因为它们都受相同的超时影响。

IntentReceiver执行时间的特殊限制意味着它应该做：在后台里做小的、琐碎的工作如保存设定或者注册一个Notification。和在主线程里调用的其它方法一样，应用程序应该避免在BroadcastReceiver里做耗时的操作或计算。但不再是在子线程里做这些任务（因为BroadcastReceiver的生命周期短），替代的是，如果响应Intent广播需要执行一个耗时的动作的话，应用程序应该启动一个Service。顺便提及一句，你也应该避免在IntentReceiver里启动一个Activity，因为它会创建一个新的画面，并从当前用户正在运行的程序上抢夺焦点。如果你的应用程序在响应Intent广播时需要向用户展示什么，你应该使用Notification Manager来实现。

一般来说，在应用程序里，100到200ms是用户能感知阻滞的时间阈值。因此，这里有一些额外的技巧来避免ANR，并有助于让你的应用程序看起来有响应性。

如果你的应用程序为响应用户输入正在后台工作的话，可以显示工作的进度（ProgressBar和ProgressDialog对这种情况来说很有用。特别是游戏，在子线程里做移动的计算。如果你的应用程序有一个耗时的初始化过程的话，考虑可以显示一个SplashScreen或者快速显示主画面并异步来填充这些信息。在这两种情况下，你都应该显示正在进行的进度，以免用户认为应用程序被冻结了。

**7、请简述Android系统中，返回键与Home键的区别**

**Home键**

在Android中，当按下Home键，默认情况下stop前台的actiity，即activity设置成onstop，而不是ondestory。如果再次启动该activity不是调用onCreate，而是调用onSavedInstanceState方法，保持上次Activity的状态则是从onRestart开始->onStart->onResume;

**Back键**

而当按下back键则不同，back键默认finish前台的activity，即activity的状态为onDestory为止，再次启动该activity则从onCreate开始，不会调用onSavedInstanceState方法。

**8、什么情况会导致Foce Close？如何避免？能够捕获导致其的异常？**

抛出运行时异常时就会导致Force Close，比如空指针、数组越界、类型转换异常等等。

捕获：可以通过logcat查看抛出异常的代码出现的位置，然后到程序对应代码中进行修改。

避免：编写程序时，要思维缜密，在可能出现异常的地方都作相应的处理，增强程序的健壮性。

**9、请简述MVC模式的原理和它在android中的运用。**

mvc是model,view,controller的缩写，mvc包含三个部分：

**模型（model）对象**：是应用程序的主体部分，所有的业务逻辑都应该写在该层。

**视图（view）对象**：是应用程序中负责生成用户界面的部分。也是在整个mvc架构中用户唯一可以看到的一层，接收用户的输入，显示处理结果。

**控制器（control）对象**：是根据用户的输入，控制用户界面数据显示及更新model对象状态的部分，控制器更重要的一种导航功能，想用用户出发的相关事件，交给m哦得了处理。

android鼓励弱耦合和组件的重用，在android中mvc的具体体现如下：

**1)视图层（view）**：一般采用xml文件进行界面的描述，使用的时候可以非常方便的引入，当然，如何你对android了解的比较的多了话，就一定可以想到在android中也可以使用javascript+html等的方式作为view层，当然这里需要进行java和javascript之间的通信，幸运的是，android提供了它们之间非常方便的通信实现。

**2)控制层（controller）**：android的控制层的重任通常落在了众多的acitvity的肩上，这句话也就暗含了不要在acitivity中写代码，要通过activity交割model业务逻辑层处理，这样做的另外一个原因是android中的acitivity的响应时间是5s，如果耗时的操作放在这里，程序就很容易被回收掉。

**3)模型层（model）**：对数据库的操作、对网络等的操作都应该在model里面处理，当然对业务计算等操作也是必须放在的该层的。

**10、请简述DDMS和TraceView的区别**

DDMS是一个程序执行查看器，在里面可以看见线程和堆栈等信息，Trace是程序性能分析器

**11、将解释下Android程序运行时权限与文件系统权限的区别**

运行时权限Dalvik（android授权）

文件系统Linux内核授权

**12、请简述一下Activity、Service、Broadcase、BroadcaseReceiver、intent、intent filter等相关概念**

activity呈现了一个用户可以操作的可视化用户界面

service不包含可见的用户界面，而是在后台无限地运行。可以连接到一个正在运行的服务中，连接后，可以通过服务中暴露出来的借口与其进行通信

broadcast receiver是一个接收广播消息并作出回应的component，broadcast receiver没有界面

intent:content provider在接收到ContentResolver的请求时被激活。

activity, service和broadcast receiver是被称为intents的异步消息激活的。

intent是一个Intent对象，它保存了消息的内容。对于activity和service来说，它指定了请求的操作名称和待操作数据的URI。Intent对象可以显式的指定一个目标component。如果这样的话，android会找到这个component(基于manifest文件中的声明)并激活它。但如果它不是一个显示指定的目标，android必须找到响应intent的最佳component。它通过将Intent对象和目标的intent filter相比较来完成这一工作。component的intentfilter告诉android该component能处理的intent。intent-filter也是在manifest文件中声明的。

**13、请回答android dvm的进程和Linux的进程，应用程序的进程是否为同一个概念？**

DVM指dalivk的虚拟机。每一个Android应用程序都在它自己的进程中运行，都拥有一个独立的Dalvik虚拟机实例。而每一个DVM都是在Linux 中的一个进程，所以说可以认为是同一个概念。

**14、请简述Android的国际化与本地化两个概念**

nternationalization （国际化）简称i18n,因为在i和n之间还有18个字符，localization（本地化 ），简称L10n。一般用 语言地区的形式表示一种语言，如 zh_CN, zh_TW.

在Android工程的res目录下，通过定义特殊的文件夹名称就可以实现多语言支持。比如我们的程序兼容简体中文、日文、英文、法文和德文，在values文件夹中建立默认strings.xml，再建立 values-zh-rCN（zh表示中文rCN表示简体，类似还有美式英语，奥式英语）、values-ja、values、values-fr和 values-de文件夹。在每个文件夹里放置一个strings.xml，strings.xml里是各种语言字符串。如果涉及到参数配置类xml文件夹也要改成xml-zh、xml-ja、xml、xml-fr和xml-de。这样在android的系统中进行语言切换，所开发的程 序也会跟着切换语言。

在代码中切换语言：

```
Resources resources = getResources();//获得res资源对象
Configuration config = resources.getConfiguration();//获得设置对象
DisplayMetrics dm = resources .getDisplayMetrics();//获得屏幕参数：主要是分辨率，像素等。
config.locale = Locale.SIMPLIFIED_CHINESE; //简体中文
resources.updateConfiguration(config, dm);
```

**15、sim卡的EF 文件有何作用**

sim卡的文件系统有自己的规范，主要是为了和手机通讯，sim本身可以有自己的操作系统，EF就是做存储并和手机通讯用的。

**16、嵌入式操作系统内存管理有哪几种， 各有何特性**

嵌入式操作系统的内存管理有页式、段式、段页。用到了MMU，虚拟空间等技术。

**17、一条最长的短信息约占多少byte?**

中文70 byte（包括标点），引文160 byte

**18、Android开发过程中，去除顶部标题栏除设置主题方法外，还有什么方法？需要注意什么？**

隐藏的方法非常简单，打开FirstActivity，在onCreate()方法中添加如下代码：

```
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    requestWindowFeature(Window.FEATURE_NO_TITLE); //去除窗口标题栏
    setContentView(R.layout.first_layout);
}
```
其中requestWindowFeature(Window.FEATURE_NO_TITLE)的意思就是不在活动中显示标题栏，注意这句代码一定要在setContentView()之前执行，不然会报错。

**19、如果在android应用程序中需要发送短信,那么需要在AndroidManifest.xml文件中增加什么样的权限**

增加“android.permission.SEND_SMS”权限




**20、以下代码中的“this”是指？**  （指：ActivityTest  ） 
```
Public class ActivityTest extends Activity{
    Private Button button1；
    /**Called when the activity is first created、*/
    @Override
    Public void onCreate(Bundle savedlnstanceState){
        Super.onCreate(savedlntanceState);
        setContentView(R.layout.main);
        button1=(Button)findViewByld(R.id.button1);
        button1.setOnClickListener(new OnClickListener(){
             @Override
             Public void onClick(View v){
                 Intent intent =new Intent();
                 Intent.setClass(ActivityTest.this,
nextActivity.class);
                 startActivity(intent);             
             }
         }
} }); ……
```


**21、Android中MVC模式 C层指的是？ **

Activity  

Android控制层的任务一般就是Activity来完成。视图层对应了Android中的View，也可以理解为布局或者控件。


**22、android中如何处理耗时操作， asyncTask有什么不足之处**

android系统推荐是用asyncTask来处理异步，它包括了准备运行：onPreExecute()，正在后台运行：doInBackground(Params...)，进度更新：onProgressUpdate(Progress...)，完成后台任务：onPostExecute(Result)取消任务：onCancelled ()，AsyncTask的构造函数有三个模板参数： 

1.Params，传递给后台任务的参数类型。 

2.Progress，后台计算执行过程中，进步单位（progress　units）的类型。（就是后台程序已经执行了百分之几了。） 

3.Result， 后台执行返回的结果的类型。 

AsyncTask并不总是需要使用上面的全部3种类型。标识不使用的类型很简单，只需要使用Void类型即可。 

不足之处： 1，线程池有限，太多内容同时异步会超时，甚至出现ARN效果。 
           2，AsyncTask可能存在新开大量线程消耗系统资源和导致应用FC的风险

**23、『卡顿』（特指不流畅的动画）是App开发中最为常见的性能问题表现。请结合你所熟悉的移动操作系统（Android、iOS 或 Windows Phone），解释一下App中卡顿的发生机理，并分别针对『界面切换卡顿』和『屏幕滑动卡顿』提出典型的优化思路。（面试题）**

大概可以归置两方面问题：1、程序本身：需要网络的情况下加载一些东西，网络不畅通可以导致暂时或长久的卡顿。算法需优化，内存消耗过于庞大。线程的阻塞，一些耗时的要放在子线程进行操作。主线程尽量只放些UI控件，网络请求放到子线程完成等。  2、硬件原因 。

**24、请简述Android中style.xml和Theme.xml有什么区别。**

一、使用的地方不同

1.theme.xml：对整个应用或某个Activity存在全局性影响。

AndroidManifest.xml中：
```
<application android:theme="@android:style/theme">
<activity android:theme="@android:style/theme">
```
application 和  activity  java中：setTheme(R.style.theme);  

2 style.xml：用在单独的View。

```
<EditText 
android:layout_height="wrap_content"
android:text="EditText"
android:style="@style/Title"
android:layout_width="fill_parent"
android:id="@+id/editText1"></EditText>
```

二、 在R.attr定义中以window开头的一些属性只对theme有效。

三、如果一个应用使用了theme，同时应用下的view也使用了style，那么当theme与样式style发生冲突时，style的优先级高于主题。

**25、请简述ViewFlipper 和 ViewPager 的区别**

ViewFlipper继承ViewAnimator，切换view的时候是有动画效果的，适合做ppt，多界面的程序欢迎引导界面，算是个轻量级的组件，适合展示静态数据，少量数据。

ViewPager继承ViewGroup。看官网描述，它和Fragment是好搭档，Fragment有自己的生命周期。也就是说ViewPager更适用复杂的视图切换，而且Viewpager有自己的adapter，这也让其适应复杂对象，实现数据的动态加载。使用时候需要加入相应的jar包。

**26、PopupWindow可以创建类似对话框风格的窗口，请简述PoupupWindow的使用步骤。**

1、为PopupWindow的view布局，通过LayoutInflator获取布局的view.如:
```
LayoutInflater inflater =(LayoutInflater) 
this.anchor.getContext().getSystemService(Context.LAYOUT_INFLATER_SERVICE);
View textEntryView =  inflater.inflate(R.layout.paopao_alert_dialog, null);
```
2、显示位置，可以有很多方式设置显示方式
```
pop.showAtLocation(findViewById(R.id.ll2), Gravity.LEFT, 0, -90);
```
或者
```
pop.showAsDropDown(View anchor, int xoff, int yoff)
```
3、进出场动画
```
pop.setAnimationStyle(R.style.PopupAnimation);
```
4、点击PopupWindow区域外部,PopupWindow消失
```
this.window = new PopupWindow(anchor.getContext());
this.window.setTouchInterceptor(new OnTouchListener() {
    @Override
    public boolean onTouch(View v, MotionEvent event) {
        if(event.getAction() ==MotionEvent.ACTION_OUTSIDE) {              
            BetterPopupWindow.this.window.dismiss();
            return true;
        }
        return false;
    }
});
```

**27、我们常常会用到“findViewByid()”来加载一个布局文件，或是一个控件。例如：findViewByid(R.id.xxx);那么，findViewByid()方法需要传进什么类型的数值，他的结果又是什么类型呢？**

输入为int类型，输出为View类型。

**28、Java国际化主要通过哪几个类进行，请简述这个类有什么作用。**

Java程序的国际化的思路是将程序中的标签、提示等信息放在资源文件中，程序需要支持哪些国家、语言环境，就对应提供相应的资源文件。资源文件是key-value对，每个资源文件中的key是不变的，但value则随不同国家、语言改变。

Java程序的国际化主要通过如下三个类完成：

 java.util.ResourceBundle：用于加载一个国家、语言资源包。
 
 java.util.Locale：用于封装一个特定的国家/区域、语言环境。
 
 java.text.MessageFormat：用于格式化带占位符的字符串。


**29、如果在android应用程序中需要发送短信,那么需要如何修改AndroidManifest.xml文件来获得权限。**

增加“android.permission.SEND_SMS”权限

**30、广播可以通过几种方法来注册，分别是如何实现的。（如果你认为必要，可以写下相应代码）**
BroadcastReceiver用于监听被广播的事件必须被注册，注册的方式有两种方法：

1、在应用程序的代码中注册

注册BroadcastReceiver：registerReceiver（receiver，filter）；

取消注册BroadcastReceiver：unregisterReceiver（receiver）；

当BroadcastReceiver更新UI，通常会使用这样的方法注册。启动Activity时候注册BroadcastReceiver，Activity不可见时候，取消注册。

2、在androidmanifest.xml当中注册
```
<receiver>
    <intent-filter>
     <action android:name = "android.intent.action.PICK"/>
    </intent-filter>
</receiver>
```
1)第一种不是常驻型广播，也就是说广播跟随程序的生命周期。

2)第二种是常驻型，也就是说当应用程序关闭后，如果有信息广播来，程序也会被系统调用自动运行。使用这样的方法注册弊端：它会始终处于活动状态，毕竟是手机开发，cpu和电源资源比较少，一直处于活动耗费大，不利。

31、在android中使用Menu时可能需要重写的方法有? （腾讯2015春招移动客户端开发练习卷）
需要重写的方法为：onCreateOptionsMenu()、onOptionsItemSelected()
32、创建联系人使用的函数有哪些（腾讯2015春招移动客户端开发练习卷）
需要使用ABPersonCreate()、ABRecordSetValue()、ABMultiValueCreateMutable()

33、修改联系人使用的函数有哪些？（腾讯2015春招移动客户端开发练习卷）
ABPersonCreate()、ABRecordSetValue()、ABAddressBookGetPersonWithRecordID()

