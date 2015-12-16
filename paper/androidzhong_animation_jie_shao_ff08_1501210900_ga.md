# Android中Animation介绍（1501210900 高鸿宾）



## 一、animation简介

&#160;&#160;&#160;&#160;&#160;&#160;&#160;Animation的意思是“动画”，在Android手机上做出漂亮的动态画面是让人非常兴奋的一件事情，也会为自己的APP增加不少亮点，Android中的Animation主要分为三种：Drawable Animation、View Animation、Property Animation下面将分别依次介绍三种Animation。


## 二、Drawable Animation

&#160;&#160;&#160;&#160;&#160;&#160;&#160;首先介绍Drawable Animatio，因为Drawable Animatio是三种里面最简单的，Drawable Animatio就是逐帧动画，也就是画面的每个帧按序显示，组成动态画面。

&#160;&#160;&#160;&#160;&#160;&#160;&#160;下面举一个简单的例子来体验一下Drawable Animation：

*主界面：activity_main.xml*

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:orientation="vertical">

    <ImageView
        android:id="@+id/loading"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:padding="5px" />

    <Button android:id="@+id/buttonA"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:padding="5px"
        android:text="开始动画"
        android:gravity="center"
        />

    <Button android:id="@+id/buttonB"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:padding="5px"
        android:text="停止动画"
        />

</LinearLayout>
```
&#160;&#160;&#160;&#160;&#160;&#160;&#160;对应ImageView中的loading，下面是loading.xml的代码，
其中loading.xml的位置在res/drawable/loading.xml。也就是把loading.xml当做普通的图片一样处理。

*动画：loading.xml*

```
<?xml version="1.0" encoding="UTF-8"?>
<animation-list xmlns:android="http://schemas.android.com/apk/res/android" android:oneshot="false"
    android:layout_height="wrap_content"  android:layout_width="wrap_content">
    <item android:drawable="@drawable/login_loading_00" android:duration="150"/>
    <item android:drawable="@drawable/login_loading_01" android:duration="150"/>
    <item android:drawable="@drawable/login_loading_02" android:duration="150"/>
</animation-list>
```

&#160;&#160;&#160;&#160;&#160;&#160;&#160;loading.xml代码解析：整个动画包含三张静态图片，分别为login_loading_00.png、login_loading_01.png、login_loading_02.png，这三张图片也是存放在res/drawable文件夹下，三张图片必需放在<animation-list>下的<item>中，其中animation-list中的android:oneshot取值false或者true，当取值true表示动态图片只显示一次，当取值false表示动态图片会一直循环显示。item中的android：duration表示每张图片在动画中显示的时间，单位为毫秒。当加载这个动画是，画面会按照item的顺序一次将每张图片按规定时间显示出来，最终表现出动画的效果。

&#160;&#160;&#160;&#160;&#160;&#160;&#160;有时在<item>结点下还会包含<layer-list>,这样在图片显示时，被<layer-list>包含的多张图片会被重叠当做一帧同事显示。


*主活动：MainActivity.java*

```
import android.app.Activity;
import android.graphics.drawable.AnimationDrawable;
import android.os.Bundle;
import android.view.MotionEvent;
import android.view.View;
import android.view.Window;
import android.widget.Button;
import android.widget.ImageView;

public class MainActivity extends Activity implements View.OnClickListener {

    private AnimationDrawable loadingAnimation;
    private Button mShow, mStop;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        requestWindowFeature(Window.FEATURE_NO_TITLE);
        setContentView(R.layout.activity_main);

        mShow = (Button) findViewById(R.id.buttonA);
        mStop = (Button) findViewById(R.id.buttonB);

        mShow.setOnClickListener( this);
        mStop.setOnClickListener( this);

        ImageView loadingImg = (ImageView) findViewById(R.id.loading);
        loadingImg.setBackgroundResource(R.drawable.loading);
        loadingAnimation = (AnimationDrawable) loadingImg.getBackground();
    }

    public void onClick(View v) {
        if (v == mShow){
            loadingAnimation.start();

        } else if (v == mStop){
            loadingAnimation.stop();
        }
    }
}

```

&#160;&#160;&#160;&#160;&#160;&#160;&#160;整个程序的运行结果为：

![](animation1.png)




## 三、View Animation

&#160;&#160;&#160;&#160;&#160;&#160;&#160;
Drawable Animation是定义好每一帧图片，规定每一张图片显示的时间，这只是最初级的动画，而View Animation却可以定义更多的丰富的动态效果，例如：移动、旋转、放大缩小、背景亮度变化。

&#160;&#160;&#160;&#160;&#160;&#160;&#160;
在View Animation中：
alpha	表示渐变透明度动画效果，
scale	表示渐变尺寸伸缩动画效果，
translate	表示画面转换位置移动动画效果，
rotate	表示画面转移旋转动画效果。

&#160;&#160;&#160;&#160;&#160;&#160;&#160;
下面依然是举个简单的例子来帮助理解。

例子中使用animation1.xml来规定动画效果，animation1.xml的存放位置为res/anim/animation1.xml。

动画效果animation1.xml：

```
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:shareInterpolator="false"
    android:layout_height="wrap_content"
    android:layout_width="wrap_content">
    <scale
        android:interpolator="@android:anim/accelerate_decelerate_interpolator"
        android:fromXScale="1.0"
        android:toXScale="1.4"
        android:fromYScale="1.0"
        android:toYScale="0.6"
        android:pivotX="50%"
        android:pivotY="50%"
        android:fillAfter="false"
        android:duration="1000" />
    <set
        android:interpolator="@android:anim/accelerate_interpolator"
        android:startOffset="1000">
        <scale
            android:fromXScale="1.4"
            android:toXScale="0.0"
            android:fromYScale="0.6"
            android:toYScale="0.0"
            android:pivotX="50%"
            android:pivotY="50%"
            android:duration="400" />
        <rotate
            android:fromDegrees="0"
            android:toDegrees="60"
            android:toYScale="0.0"
            android:pivotX="50%"
            android:pivotY="50%"
            android:duration="400" />
    </set>

</set>
```
对一个图片的执行动画过程，先执行最外面的set标签里的内容，再依次执行内部嵌套set的内容，当一个set标签内有多个scale，rotate，alpha，rotate时，这几个动画效果是同时对图片起作用，而不是一次执行这四种效果。

主界面：activity_main.xml

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context=".AnimaXmlActivity" >

    <ImageView
        android:id="@+id/Image"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"
        android:src="@drawable/android" />

    <Button
        android:id="@+id/xml_btn"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:text="运动" />

</RelativeLayout>
```

主活动 main_activity.java

```
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.view.Window;
import android.view.animation.Animation;
import android.view.animation.AnimationUtils;
import android.widget.Button;
import android.widget.ImageView;

public class MainActivity extends Activity {

    private Button btn;
    private ImageView img;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        requestWindowFeature(Window.FEATURE_NO_TITLE);
        setContentView(R.layout.activity_main);
        btn = (Button)findViewById(R.id.xml_btn);
        img = (ImageView)findViewById(R.id.Image);

        btn.setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View v) {
                Animation anim = AnimationUtils.loadAnimation(MainActivity.this, R.anim.animation1);
                img.startAnimation(anim); //对img执行动画效果
            }
        });
    }
}
```



