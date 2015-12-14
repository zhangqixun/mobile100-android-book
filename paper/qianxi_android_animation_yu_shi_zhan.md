# 浅析Android Animation与实战

作者：巫思杏 1501211005


# 1 概述



## 1.1 背景
Animation，如果从单词的词义上解释，我们可以得到其意思是“、活波、动画片”，可以看到这是一个能够让人感觉到富有生气的单词，因而 Android Animation作为Android 过渡动画类的一个总称也是十分贴切的。Android Animation 是Android系统当中，用来做View动画的一个方法，通过Animation和View的结合和设计，我们可以让我们开发的APP变得绚丽多姿。

Android是由Google收购过来的一款产品，事实证明Google的这一举措是十分成功的，然而在Android发展的初期，对比起最大的竞争对手iOS，Android存在着界面简陋、GUI设计差等一系列问题。iOS上每一个操作都有着富有美感的转场动画，每个操作之间过渡的行云流水，极大的提升了使用上的感受，而Android则多是点一下，等待一下，直接跳出下一个界面，十分突兀。随着Google对Android系统自身的规范、对设计的加强以及开发者的更近，近年来Android在包含转场动画在内的各种界面设计规范都有了进一步的提升，基于Android开发的APP也能够拥有丰富的交互动画，拉近了与iOS的差距。
Android 

Animation就是这里面的最大功臣，开发者可以通过Animation，为自己APP的交互切换设计绚丽的交互动画，提升人机的交互。本文的主要目的就是介绍Android Animation的基本信息，包含内容，基本模块，以及部分进阶使用等方法，帮助大家在开发APP时能拥有一个漂亮的动画。

## 1.2 基本情况


Android Animation在较新的安卓版本当中有一定的变革，在3.0以前，Android支持两种动画模式，Tween Animation,Frame Animation，在Android3.0中又引入了一个新的动画系统：Property animation，这三种动画模式在SDK中被称为property animation,view animation,drawable animation。 可通过NineOldAndroids项目在3.0之前的系统中使用Property Animation。

# 2 Animation 基本介绍


## 2.1 基本情况

本章内容主要将会针对Android基本的三种动画模式，Tween Animation,Frame Animation，Property animation做一个介绍，主要的目的是阐述这三种动画模式的运行模式和基本的使用情况。

## 2.2 Tween Animation

Tween Animation又称为View Animation，即补间动画，补间动画的原理在于针对给定的两个关键帧，通过算法计算两个关键帧之间的过渡，最终实现两个关键帧之间的一个过渡。Tween Animation被称作View Animation的一个原因在于，它只能应用于View对象，并且支持的属性也较为有限。

View Animation支持一系列View形状的变换，如大小的缩放，透明度的改变，位置的改变，动画的定义既可以用代码定义也可以用XML定义，因此更加推荐采用XML的方式进行定义。一般需要自定义XML动画的时候，可以将动画元素放在/res/anim/文件当中，XML的根元素可以是alpha,scale,translate,rotate,interpolator元素或set(表示以上几个动画的集合，set可以嵌套)。默认情况下，所有动画是同时进行的，可以通过startOffset属性设置各个动画的开始偏移（开始时间）来达到动画顺序播放的效果。可以通过设置interpolator属性改变动画渐变的方式。

## 2.3 Frame Animation

Frame Animation又称作Drawable Animation，顾名思义，其就是一帧一帧的动画，通过一系列的Drawable来模拟动画的效果，和电影的播放一样，按每一个Frame展现。通常需要在XML中进行定义，XML文件要放在/res/drawable/目录下。在设计Animation XML动画时，必须以nimation-list为根元素，以item表示要轮换显示的图片，duration属性表示各项显示的时间。

## 2.4 Property Animation

Property Animation 属性动画，是在Android 3.0中才带来的动画属性，因此其要求有较高的API Level，而在老旧的API上只能通过Google提供的一些兼容方式实现。为什么会称作属性动画呢？因为Property Animation在绘制效果是真实地属性值，而在之前的View Animation的过度绘制当中，属性值是保持原值的。正因为如此Property Animation可以应用于任何对象，Property Animation本身只是表示一段时间内的改变，当发生改变时需要做什么则由我们自定义。

Property Animation包含着较多的内容，也是当前Android开发最为重要的一部分，因此将在之后花较多的篇幅去介绍Property Animation。

# 3 ViewAnimation 实战

## 3.1 ViewAnimation种类介绍

之前在第二章当中已经介绍了ViewAnimation的一些基本知识，那么这章将开始针对如何应用ViewAnimation做一个深入的实战，首先我们需要了解的是ViewAnimation的种类，具体来说Tween的四个主要实现如下：
* AlphaAnimation：透明渐变动画，一般采用如下的构造方法进行构造

        AlphaAnimation(float fromAlpha, float toAlpha)
    	fromAlpha：动画开始时的透明度（取值范围为0.0到1.0）；
        toAlpha：动画结束时的透明度；
    	或者是在XML当中，采用alpha标签进行描述。
* ScaleAnimation：缩放渐变动画，一般采用如下的构造方法：


        ScaleAnimation(float fromX, float toX, float fromY, float toY, int pivotXType, float pivotXValue, int pivotYType, float pivotYValue)来构造；
        fromX：动画开始X坐标上的伸缩尺度；
        toX：动画结束X坐标上的伸缩尺度；
        fromY：动画开始Y坐标上的伸缩尺度；
        toY：动画结束Y坐标上的伸缩尺度；
        pivotXType：X坐标上的伸缩模式，取值有：Animation.ABSOLUTE, Animation.RELATIVE_TO_SELF, Animation.RELATIVE_TO_PARENT；
        pivotXValue：X坐标上的伸缩值；
        pivotYType：Y坐标上的伸缩模式，取值有：Animation.ABSOLUTE, Animation.RELATIVE_TO_SELF, Animation.RELATIVE_TO_PARENT；
        pivotYValue：Y坐标上的伸缩值；


* TranslateAnimation：实现位置变换的实现类，常使用如下的方法构造
 
        TranslateAnimation(float fromXDelta, float toXDelta, float fromYDelta, float toYDelta)
        fromXDelta：动画开始的X坐标；
        toXDelta：动画结束的X坐标；
        fromYDelta：动画开始的Y坐标；
        toYDelta：动画结束的Y坐标；
* RotateAnimation：主要控制旋转的动画实现类，常使用如下构造方法

        RotateAnimation(float fromDegrees, float toDegrees, int pivotXType, float pivotXValue, int pivotYType, float pivotYValue)来构造；
        fromDegrees：旋转开始角度；
        toDegrees：旋转结束角度；
        pivotXType, pivotXValue, pivotYType, pivotYValue与尺度变化动画ScaleAnimation类似；
        fromXDelta：动画开始的X坐标；
        toXDelta：动画结束的X坐标；
        fromYDelta：动画开始的Y坐标；
        toYDelta：动画结束的Y坐标；

## 3.2 ViewAnimation实战

在这一节当中，将介绍如何在Anroid项目中，使用ViewAnimation效果，步骤如下：

* 创建一个空白的应用程序，新建一个Activity，找到res里的anima文件夹，如果是Android Studio当中没有的话可以通过AndroidStudio自带的工具建立一个文件夹即可。


* 分别为四种不同的动画建立对应的xml文件，具体可以参考如下代码合并显示了：

```
例如新建一个Alpha动画的XML（mebiuw_anima_alpha.xml)文件，就如下的形式，以Set标签做维ROOT，按照我们给定的参数填写参数
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <!--从0.1的透明度过渡到1.0的透明度，同AlphaAnimation(float fromAlpha, float toAlpha)一致,这里多加了一个持续时间-->
    <alpha
        android:fromAlpha="1.0"
        android:toAlpha="0.1"
        android:duration="5000">
    </alpha>

</set>

同理对不同的动画，具有相同的基础结构，这里仅贴出<Set>以内有改变的地方，如果需要查看所有代码，可以到Github上查看源代码
Rotate：
<!--旋转配置，同上文介绍的构造方法相对应，旋转1周，持续1000ms们同事定义伸缩值-->
<rotate android:fromDegrees="0"
        android:toDegrees="360"
        android:pivotX="50%"
        android:pivotY="50%"
        android:duration="1000" />

Scale:
<!--伸缩配置，具体的动画信息，同上文介绍的参数介绍一致-->
    <scale android:fromXScale="0.0"
        android:toXScale="1.0"
        android:fromYScale="0.0"
        android:toYScale="1.0"
        android:pivotX="50%"
        android:pivotY="50%"
        android:duration="5000" />

Translate:
<!--转换配置，具体的动画信息，同上文介绍的参数介绍一致-->
<translate android:fromXDelta="10"
        android:toXDelta="100"
        android:fromYDelta="10"
        android:toYDelta="100" />     

```
* 在Activity当中，新建一个图片（ImageView）和四个按钮（Button），后文的事例将通过让每个按钮分别触发一种功能，使得图片产生相应地变化，布局结束后如下图所示。![](3-1.png)
* 在Activity当中，新建一个图片（ImageView）和四个按钮（Button），后文的事例将通过让每个按钮分别触发一种功能，使得图片产生相应地变化，布局结束后如下图所示。

* 此后，在Activity当中用代码让ImageView和Button与控件绑定，注意实现如下的两个核心方法，其他方法比较简单，就不在这里赘述，需要的可以自行查看源代码

```
/**
     * 加载并启动动画，执行动画的关键
     * @param view 动画执行的对象，在这里全部都是应用到img，注意看onClick部分
     * @param type  动画的种类
     */
    private void  doAnimation(View view,int type){
        Animation animation = AnimationUtils.loadAnimation(this, type);
        view.startAnimation(animation);
    }

    /**
     * 根据不同的按钮，选择执行不同的方法作为一个参数，交由doAnimation执行
     * @param v
     */
    @Override
    public void onClick(View v) {
        if(v.getId()==R.id.button_alpha){
            this.doAnimation(img,R.anim.mebiuw_anima_alpha);
        }
        else if(v.getId()==R.id.button_scale){
            this.doAnimation(img,R.anim.mebiuw_anima_scale);
        }
        else if(v.getId()==R.id.button_rotate){
            this.doAnimation(img,R.anim.mebiuw_anima_rotate);
        }
        else if(v.getId()==R.id.button_translate){
            this.doAnimation(img,R.anim.mebiuw_anima_translate);
        }
    }
```

* 由此，完成了ViewAnimation的实战，ViewAnimation简单易懂，操作方便，大家不妨在自己的项目中多多使用。


# 4 FrameAnimation 实战

## 4.1 FrameAnimation 基础
Frame动画是按照一系列的帧顺序播放展示出来的动画效果，而这些帧都是一系列的图片。为此我们可以将Frame动画的展示过程定义在XML文件当中，如果是采用代码编写，则是使用到AnimationDrawable对象。Drawable Animation可以加载Drawable资源实现帧动画，而AnimationDrawable是实现Drawable Animations的基本类，但是因为Frame Animation的特殊性，通常的做法都是采用XML的方式实现，不推荐使用代码的方式实现这个动画，所以在这里也就不做介绍了。


当我们需要使用FrameAnimation的时候可以采用如下的方式定义XML文件：
```
<?xml version="1.0" encoding="utf-8"?>  
<animation-list xmlns:android="http://schemas.android.com/apk/res/android"  
    android:oneshot=["true" | "false"] >  
    <item  
        android:drawable="@[package:]drawable/drawable_resource_name"  
        android:duration="integer" />  
</animation-list>  
<animation-list>元素是必须的，并且必须要作为根元素，可以包含一或多个<item元素；android:onshot如果定义为true的话，此动画只会执行一次，如果为false则一直循环。
<item>元素代表一帧动画，android:drawable指定此帧动画所对应的图片资源，android:druation代表此帧持续的时间，整数，单位为毫秒。
```

## 4.2 FrameAnimation 实战

在这一部分，我们将继续3中的那一个工程，在原有的基础上增加代码，直接展示FrameAnimation的效果

* 首先在准备好你的过度动画，放在drawable文件夹下，接着在drawable(请注意这次的动画是放在drawable中，而不是anima中)文件当中新建一个XML文件，内容包含你需要过渡的帧，在这里我准备了5张从正常亮度渐变到黑色的图像，XML（我的这里为mebiuw_drawable_frame）大致形式如下


```

<?xml version="1.0" encoding="utf-8"?>
<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot="true">
    <!-- 一下图片请按照顺序自行替换，这里是我对原图做了后期，依次变暗的图片 -->
    <item android:drawable="@drawable/psb" android:duration="200" />
    <item android:drawable="@drawable/psb1" android:duration="200" />
    <item android:drawable="@drawable/psb2" android:duration="200" />
    <item android:drawable="@drawable/psb3" android:duration="200" />
    <item android:drawable="@drawable/psb4" android:duration="200" />
    <item android:drawable="@drawable/psb5" android:duration="200" />
</animation-list>

```
* 完成上述的代码后，就代表我们已经写完了一个动画的XML文件，随后我们需要对其调用。根据材料，他有两种调用方式，因为FrameAnimation本身就是Drawable资源，所以可以直接设置为某个View的背景。当然，更为常见的是在Java中编写代码，调用这个XML文件所描述的动画。这里将演示在Java下的方式，所以我们在原有的Activity上新增加一个Button，命名为Frame，编写该按钮的事件，用作Frame动画的处罚，关键代码如下：

```
 //首先设置背景为我们设置的XML
                this.img=(ImageView) this.findViewById(R.id.testImg);
                this.img.setBackgroundResource(R.drawable.mebiuw_drawable_frame);
                //获得AnimationDrawable对象，并且开始触发
                AnimationDrawable animationDrawable = (AnimationDrawable) this.img.getBackground();
                animationDrawable.start();
```
* 由此，完成了FrameAnimation的实战，FrameAnimation可以理解为一个定制的GIF动画，一般使用频度不高，同时需要注意的是FrameAnimation在不同的API Level时运行结果可能不一致。