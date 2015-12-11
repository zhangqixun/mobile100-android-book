# Android动效与个性化交互（1501210479 白鹤松）

作者：白鹤松

学号：1501210479


在天气预报项目中，当我们点击更新天气按钮后，如果网络速度较慢，在等待的过程中会有按钮旋转的动画效果，减少用户等待的焦急感。

单一的静态页面容易使人感觉枯燥乏味，因此动效越来越受到产品和设计师同学的重视，如此一来，也就增大了对开发同学的考验，虽说简单的动效：如移动，旋转，缩放，渐变或普通的界面跳转相对简单，但在目前日益激烈的竞争条件下，出彩复杂的动效也越来越多

## 
在Android中开发动效有两套框架可以使用，分别为 Animation 和 Property Animation；两者之间的主要区别在于：

1.需要的Anroid API level不一样。Property Animation需要Android API level 11的支持,当然可以使用nineoldandroids.jar进行兼容，而View Animation则是最原始的版本。

2.适用范围不一样Property Animation适用于所有的Object对象，而View Animation则只能应用于View对象。

3.实际改变不一样。Animation改变的是view的绘制位置，而非实际属性；Property Animation改变的是view的实际属性；如：用两套框架分别对一个button做位移动画，用animation处理之后的点击响应会在原处，而用Property Animation处理后的点击响应会在最终位置处。

总的来说可以分为：Tween Animation（补间动画） 和Frame Animation（帧动画）

###2.1帧动画（Frame Animation）：
帧动画其实就是按照一定的时间间隔进行快速的图片切换，达到视觉上的动画效果，这种动画相对来说缺点较多，
比如：

1.根据动画的复杂度需要切多张图，这样就不可避免的增大了包大小

2.由于是多张图，运行时需要加载到内存，那么又会增大运行时的内存大小

所以如果在动效上有别的方案选择，个人不太建议使用帧动画的实现方式，当然有时候不得不用，这时我们可以尽可能的在效果连贯的基础上减少图片张数；
帧动画的实现也相对简单，就举一个例子，但是例子之中有几条帧动画的使用规则，是我以前在使用的过程中遇到问题后总结出来的；

1.在drawable目录下xml中定义如下文件，节点为animation-list，oneshot代表是否执行一次，duration代表每张图对应展示时间：

    <animation-list xmlns:android="http://schemas.android.com/apk/res/android"  
    android:oneshot="true">  
    <item android:drawable="@drawable/test1" android:duration="200" />  
    <item android:drawable="@drawable/test2" android:duration="200" />  
    <item android:drawable="@drawable/test3" android:duration="200" />  
    </animation-list>  
2.然后将该drawable设置给对应的imageview

3.在activity中
animDrawable = (AnimationDrawable) imageView.getBackground();

4.在onWindowFocusChanged()中调用：

animDrawable.stop();

animDrawable.start();

如果不这么做，那么在性能比较差的机器上很可能就会出现没有播放的情况，因为只显示出了第一帧，问题在于动画没有和view完成关联，所以不要在onCreate中去调用启动，而需要在onWindowFocusChanged中进行调用；

在极特殊的情况下如果还无法播放，则可以mHandler.postDelay 200 毫秒解决。
###2.2补间动画（Tween Animation）
Animation下有五个子类：AlphaAnimation(渐变),RotateAnimation(旋转),ScaleAnimation(缩放),TranslateAnimation(位移)
在实现原理上除了AlphaAnimation是动态的去改变view的alpha值，其他三个都是去改变里面的Matrix；

在Animation框架里，主要的类主要有Animation和Transformation、Interpolator(插值器，后面也会专门讲)
Transformation里面主要对alpha和matrix进行了封装，而改变view的透明度就是改变alpha，移动、旋转、缩放甚至错切则都是改变matrix
Animation里有一个重要的方法applyTransformation，实现自定义Animation也主要是实现这个方法。

以AlphaAnimation为例：
    
    /**  
    * Changes the alpha property of the supplied {@link Transformation}  
    */  
    @Override  
        protected void applyTransformation(float interpolatedTime, Transformation t) {  
        final float alpha = mFromAlpha;  
        t.setAlpha(alpha + ((mToAlpha - alpha) * interpolatedTime));  
    }  