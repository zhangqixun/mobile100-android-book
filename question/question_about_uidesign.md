#移动平台软件开发（Android）面试题汇总参考答案

## Question About Content Provider

###**声明**

本册面试题及答案大部分摘自网络，版权归原创者所有，如有侵权请您及时联系：

邮箱：1596888285@qq.com

参考用书：《第一行代码》（Android）、《疯狂Android讲义》、《Android4编程入门经典》

*本答案仅供参考，如有错误，请您指正*


---
**1、请简述Android开发中经常用到几种布局**

Android 布局是应用界面开发的重要一环，在Android中，共有五种布局方式，分别是：LinearLayout(线性布局)， RelativeLayout(相对布局)，FrameLayout(单帧布局)，TableLayout(表格布局) ，AbsoluteLayout(绝对布局)。

**LinearLayout**：线性布局是一种非常常用的布局。它会将它所包含的控件在吸纳性方向上一次排列。按照排列方向可分为垂直布局（```android:orientation="vertical"```）和水平布局（```android:orientation="horizontal" ```），在LinearLayout里面可以放多个控件，但是一行（列）只能放一个控件。

**RelativeLayout**：相对布局。相对布局也是一种常用的布局方式。其子控件是根据所设置的参照控件来进行布局的，设置的参照控件可以是父控件，也可以使其他的子控件。因此，相对布局显得更加随意一些，它可以通过相对定位的方式让控件出现在布局的任何位置。

**TableLayout**：表格布局，是以行列的形式来管理子控件的，在表格布局中的每一行可以是一个View控件或者是一个TableRow控件。而TableRow控件中还可以添加子控件。

**FrameLayout**：单帧布局，这种布局方法比较简单，没有任何的定位方式。所有控件都放置在屏幕左上角（0,0），可以放多个控件，但是会按控件定义的先后顺序依次覆盖，后一个会直接覆盖在前一个之上显示，如果后放的比之前的大，会把之前的全部盖住（类似于一层层的纸张）。

**AbsoluteLayout**：绝对布局，可以直接指定子控件的绝对位置（例如： android:layout_x="60px" android:layout_y="32px" ），这种布局简单直接，但是由于手机的分辨率大小不统一，绝对布局的适应性比较差。

**2、请简述View，surfaceView，GLSurfaceView之间有什么区别。**

**View：**显示视图，内置画布，提供图形绘制函数、触屏事件、按键事件函数等；必须在UI主线程内更新画面，速度较慢

**SurfaceView：**基于view视图进行拓展的视图类，更适合2D游戏的开发；是view的子类，类似使用双缓机制，在新的线程中更新画面所以刷新界面速度比view快。

**GLSurfaceView：**基于SurfaceView视图再次进行拓展的视图类，专用于3D游戏开发的视图；是SurfaceView的子类，openGL专用。

**3、请简述Adapter有什么作用？常见的Adapter有哪些？**

Adapter是连接后端数据和前端显示的适配器接口，是数据和UI（View）之间一个重要的纽带。在常见的View(ListView,GridView)等地方都需要用到Adapter。常见的Adapter有ArrayAdapter，BaseAdapter，CursorAdapter，HeaderViewListAdapter，ListAdapter，ResourceCursorAdapter，SimpleAdapter，SimpleCursorAdapter，SpinnerAdapter，WrapperListAdapter等。

**4、请简述android中的动画有哪几类，它们的特点和区别是什么？**

android动画有两类：
一种是tweened animation(渐变动画) ；一种是frame by frame(逐帧动画) 

特点：

渐变动画是通过改变view的大小、旋转的角度、透明度、位置来产生动画，可以控制动画的播放快慢以及加速度。

逐帧动画是用一组图片轮流绘制，产生动画的感觉。类似于gif图片。

区别：

渐变动画的所有操作是通过矩阵变换对同一个物体（绘制的一个view或者图片）进行操作产生动画。

而逐帧动画是多个物体（多张图片）进行绘制，可以说这多张图片是不相关的（从播放的角度来看只能说是内容相关）。


**5、请简述Android常用控件的信息**

Android中常用的控件有：TextView、Button、EditText、ImageView、ProgressBar、AlertDialog、ProgressDialog等。

TextView可以用来输入文本信息

Button是程序用于和用户进行交互的一个重要控件，他可以为用户提供一个按钮。

EditText是程序用于和用户交互的另一个重要控件，它允许用户在控件里面编辑文本内容，并可以在程序中对这些内容进行处理。

ImageView是用于在界面上展示图片的控件，通过它可以让我们的程序变得更加丰富多彩。

ProgressBar可以用来在界面上显示一个进度条表示我们正在加载一些数据。

AlertDialog可以在当前的界面弹出一个对话框，这个对话框是置顶于所有界面元素之上的，能够屏蔽掉其他控件的交互能力，因此这个控件一般用来提示一些重要信息
。

ProgressDialog和AlertDialog有些相似，都可以在界面上弹出一个对话框，都能够屏蔽掉其他控件的交互能力。不同的是，ProgressDialog会子啊对话框中显示一个进度条，一般是用于表示当前操作比较耗时的，例如：删除某些文件。

**6、请简述android中的sharedpreferences数据存储方式**

SharedPreferences是一种轻量级的数据存储方式,它可以用键值对的方式把简单数据类型（boolean、int、float、long和String）存储在应用程序的私有目录下(data/data/[包名] /shared_prefs/)自己定义的xml文件中。
SharedPreferences是以键值对来存储应用程序的配置信息的一种方式，它只能存储基本数据类型。一个程序的配置文件仅可以在本应用程序中使用，或者说只能在同一个包内使用，不能在不同的包之间使用。实际上sharedPreferences是采用了XML格式将数据存储到设备中，在DDMS 中的File Explorer中的/data/data//shares_prefs下。

**7、如何实现在EditText控件中显示提示信息？**

在EditText中显示提示信息可以通过“android:hint”来设置。如下面代码：
```
<EditText
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:hint="@string/hint_Demo"  //"hint_Demo"定义在string.xml中/>
        ```
**8、如何限制EditText中的最大行数？**

在EditText控件中，想要限制最大行数，只要通过“android:maxLines”来设定即可。下面代码是将EditText最大行数设置为4.
```
<EditText
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:maxLines="4"/>
        ```
**9、如何让LinearLayout布局中的控件垂直排列？**

LinearLayout又称作线性布局，是一种非常常用的布局。正如它名字所描述的一样，这个布局会将它所包含的控件在线性方向上依次排列。那么线性排列又分为垂直和水平，我们可以通过“```android:orientation```”来控制LinearLayout中的控件排列方向。这个属性有两个参数分别为vertical和horizontal。这两个参数可以将控件的方向设置为垂直和水平。

**10、请解释一下ListView**

ListView可以称得上是Android中最常用的控件之一，几乎所有的应用程序都会用到它。由于手机屏幕空间比较有限，能够一次性在屏幕上显示的内同并不多，当我们的程序中有大量的数据需要展示的时候，就可以借助ListView来实现。ListView允许用户通过手指上下滑动的方式将屏幕外的数据滚动到屏幕内，同时屏幕上原有的数据则会滚动出屏幕。

**11、Android开发在布局文件中指定宽高的固定大小有几种常用的单位，分别介绍这些单位。**

**dip: **device independent pixels(设备独立像素).不同设备有不同的显示效果,这个和设备硬件有关，一般我们为了支持WVGA、HVGA和QVGA cwj推荐使用这个，不依赖像素。

**px: **pixels(像素). 不同设备显示效果相同，一般我们HVGA代表320x480像素，这个用的比较多。

**pt: **point，是一个标准的长度单位，1pt＝1/72英寸，用于印刷业，非常简单易用；

**sp: **scaled pixels(放大像素). 主要用于字体显示best for textsize。由此，根据 google 的建议，TextView 的字号最好使用 sp 做单位，而且查看TextView的源码可知 Android 默认使用 sp 作为字号单位。

**dp**（与密度无关的像素）：一种基于屏幕密度的抽象单位。在每英寸160点的显示器上，1dp = 1px。

**12、Android布局文件中layout_gravity和gravity的区别**

android:gravity：设置的是控件自身上面的内容位置
android:layout_gravity：设置控件本身相对于父控件的显示位置。

**13、给ListView设置适配器的方法是 ？ 答：setAdapter(adapter) ** 

**14、在 Android 中， 在屏幕密度为160时， 1pt 大概等于多少sp**

在Android中，1pt大概等于2.22sp。
Android支持下列所有单位：

1）px （像素）：屏幕上的点；

2）in（英寸）：长度单位；

3）mm（毫米）：长度单位；

4）pt（磅）：1/72英寸

5）dp（与密度无关的像素）：一种基于屏幕密度的抽象单位。在每英寸160点的显示器上，1dp=1px;

6）dip （同dp）

7）sp （与刻度无关的像素）：与dp类似，但是可以根据用户字体大小首选项进行缩放
。
Apk的资源包中：

当屏幕density=240时，使用hdpi标签的资源；

当屏幕density=160时，使用mdpi标签的资源；

当屏幕density=120时，使用ldpi标签的资源；

（1）在屏幕密度为160时，1dp=1px=1dip, 1pt=160/72sp, 1pt=1/72英寸，

（2）当屏幕密度为240时，1dp=1dip=1.5px


**15、如果LinearLayout的排列方向是horizontal，内部的控件是否可以将宽度指定为“match_parent”,为什么？**

不可以，因为当LinearLayout的排列方向是horizontal的时候，内部空间为水平排列，这时候如果宽度指定为match_parent时候，这个控件会覆盖其他控件。


**16、ViewPager类提供了多界面切换的新效果，是谷歌在3.0之后加入的新特性，在使用ViewPager应注意什么？**

ViewPager是android扩展包v4包中的类，这个类可以让用户左右切换当前的view。所以我们在使用ViewPager时候需要导入相应的jar包。

**17、如果一个TextView控件里面的内容是不定的（这些文字可能会很多，甚至多于一个屏幕的大小），但是留给这个控件的高度只有一个固定值Xdp。我们期望即使在文字很多的时候，这些文字也能被显示在这个固定大小的区域，应如何来解决？**

这道题应该有很多答案，我提供一种吧。就是把TextView放到一个ScrollView里面，便可以实现滚动。

**18、请简述GLSurFaceView特性（腾讯2015春招移动客户端开发练习卷）**

GLSurfaceView是一个视图，继承至SurfaceView，它内嵌的surface专门负责OpenGL渲染。GLSurfaceView提供了下列特性：

（1）管理一个surface，这个surface就是一块特殊的内存，能直接排版到android的视图view上。

（2）管理一个EGL display，它能让opengl把内容渲染到上述的surface上。

（3）用户自定义渲染器(render)。

（4）让渲染器在独立的线程里运作，和UI线程分离。

（5）支持按需渲染(on-demand)和连续渲染(continuous)。

（6）一些可选工具，如调试。

**19、表视图的相关类有哪些? （腾讯2015春招移动客户端开发练习卷）**

UITableView、UITableViewController、UITableViewDelegate




