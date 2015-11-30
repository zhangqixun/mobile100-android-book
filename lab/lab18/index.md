# 控件样式定制

起草人: 白鹤松   日期：15年11月30日

修改完善：OOOO   日期：15年00月00日

# 

**一、实验目的**

以一个自定义样式按钮的实例带大家了解控件样式定制。

**二、基础知识**

   
* 知识点1：

    自己在学习和做例子的过程中，常常会需要按钮，由于系统自带按钮样式不太好看，所以需要我们自己来定制项目的按钮，我们常常采用定制drawable的xml文件，这样做的好处不用图片做背景，节省空间。
 

* 知识点2：

   控件常见状态：在XML文件中用到了selector节点，selector可以理解为状态切换器，不同的状态下切换不同的样式，各种状态用Item节点表示，以下为一些常见的状态：

        1、android:state_pressed
        boolean。“true”表示按下状态使用（例如按钮按下）；“false”表示非按下状态使用。

        2、android:state_focused
        boolean。“true”表示聚焦状态使用（例如使用滚动球/d-pad聚焦button）；“false”表示非聚焦状态使用。

        3、android:state_selected
        boolean。“true”表示选中状态使用（例如tab打开）；“false”表示非选中状态使用。

        4、android:state_checkable
        boolean。“true”表示可勾选状态时使用；“false”表示非可勾选状态使用。（只对能切换可勾选—非可勾选的构件有用。）

        5、android:state_checked
        boolean。“true”表示勾选状态使用；“false”表示非勾选状态使用。

        6、android:state_enabled
        boolean。“true”表示可用状态使用（能接收触摸/点击事件）；“false”表示不可用状态使用。

        7、android:window_focused
        boolean。“true”表示应用程序窗口有焦点时使用（应用程序在前台）；“false”表示无焦点时使用（例如notification栏拉下或对话框显示）。


* 知识点3：

     shape的属性：每个状态（item）都对应着一个效果，shape是用来定义形状的，以下为shape的一些常见属性：
     
     1.solid：实心，就是填充的意思
       android:color指定填充的颜色
     2.gradient：渐变
       android:startColor和android:endColor分别为起始和结束颜色，android:angle是渐变角度，必须为45的整数倍。当angle=0时，渐变色是从左向
       右。 然后逆时针方向转，当angle=90时为从下往上。另外渐变默认的模式为android:type="linear"，即线性渐变，可以指定渐变为径向渐变，
       android:type="radial"，径向渐变需要指定半径android:gradientRadius="50"，也可一指定二者的综合，扫描渐变 android: type="sweep"

     3.stroke：描边
       android:width="2dp" 描边的宽度，android:color 描边的颜色。
       我们还可以把描边弄成虚线的形式，设置方式为：
       android:dashWidth="5dp"
       android:dashGap="3dp"
       其中android:dashWidth表示'-'这样一个横线的宽度，android:dashGap表示之间隔开的距离。

     4.corners：圆角
       android:radius为角的弧度，值越大角越圆。
       我们还可以把四个角设定成不同的角度，方法为： 
                android:topRightRadius="20dp" 右上角
                android:bottomLeftRadius="20dp" 右下角
                android:topLeftRadius="1dp" 左上角
                android:bottomRightRadius="0dp" 左下角
       这里有个地方需要注意，bottomLeftRadius是右下角，而不是左下角
       
     5.panding：内边矩


   

**三、实验内容及步骤**

**3.1 实验内容**
 

完成一个简单的自定义按钮。

**3.2 实验步骤**

1.在drawable目录下新建buttonstyle.xml：

    <?xml version="1.0" encoding="utf-8"?>
    <selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_enabled="true" android:state_pressed="true">
    <shape android:shape="rectangle">
        <corners android:radius="8dp"/>
        <solid android:color="#007c9b"></solid>
        <size android:height="32dp" android:width="72dp"></size>
        <padding android:bottom="5dp" android:left="5dp"
            android:right="5dp" android:top="5dp">
        </padding>
    </shape>
    </item>
    <item android:state_enabled="true" android:state_pressed="false">
    <shape android:shape="rectangle">
        <corners android:radius="8dp"/>
        <size android:height="32dp" android:width="72dp"></size>
        <padding android:bottom="5dp" android:left="5dp"
            android:right="5dp" android:top="5dp"></padding>
        <solid android:color="#00c9fc"></solid>
    </shape>
    </item>
    </selector>

2.给按钮添加定制的样式：

    <Button
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="自定义按钮测试"
        android:background="@drawable/buttonstyle"/>

**四、常见问题及注意事项**

*详细描述本此实验的可能会遇到的问题以及相关的注意事项*


