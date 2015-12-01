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


