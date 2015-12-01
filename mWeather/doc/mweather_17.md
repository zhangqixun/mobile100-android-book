# 测试与优化


    小组成员: 丁泽武，盛啸然，谢峰，高参，李智博   最终完成日期：15年00月00日
# 

**一、简介**

&#160; &#160; &#160; &#160;软件测试：在规定的条件下对程序进行操作，以发现程序错误，衡量软件质量，并对其是否能满足设计要求进行评估的过程。同样，Android的测试也是一种实际输出与预期输出间的审核或者比较过程。  
&#160; &#160; &#160; &#160;Android的测试大致分为三大块：

* 1.代码层测试；  
* 2.用户操作模拟，功能测试；
* 3.稳定性测试。

&#160; &#160; &#160; &#160;本文将介绍以上三种测试，其中着重介绍如何使用Robotium来模拟用户的操作完成测试。


**二、基础知识**

   
* 代码层测试：

&#160; &#160; &#160; &#160;存在Application、Activity、Service等特殊组件，而这些组件都涉及到生命周期管理的问题。为了对这些组件进行测试，Google提供了一套针对性的测试框架，AndroidTestFramework。

&#160; &#160; &#160; &#160;官方教程链接 http://developer.android.com/training/testing.html

&#160; &#160; &#160; &#160;官方api http://developer.android.com/reference/android/test/package-summary.html

&#160; &#160; &#160; &#160;其中最为常用的就是针对Activity的测试，即ActivityInstrumentationTestCase2类。继承该类后可通过getActivity（）方法获取Activity的一个mock对象，从而实现各种界面元素的测试。代码如下：
```
public class LoginActivityTest extends ActivityInstrumentationTestCase2<LoginActivity> {
        LoginActivityTest mActivity;
        Button btnLogin;

        public LoginActivityTest(){
      super(LoginActivity.class);//必须实现super(testclass)
        }
    
    @Override
    protected void setUp() throws Exception {
        super.setUp();
        mActivity = getActivity();
                btnLogin = mActivity.findViewById(R.id.btnLogin);
    }

    public void testGetActivity(){
       assertNotNull("can't get LoginActivity" , mActivity);
       assertNotNull("can't get loginButton" , btnLogin);
    }
}
```

&#160; &#160; &#160; &#160;需要注意的是，测试代码运行的线程并不是UI线程。因此如果需要对UI元素进行setText或是click之类的操作，需要通过getActivity().runOnUiThread(action)方法执行。代码如下:


```
getActivity().runOnUiThread(new Runnable() {
            @Override
            public void run() {
                 btnLogin.click();
            }
        });
```


* 用户操作模拟测试：

&#160; &#160; &#160; &#160;虽然AndroidTestFramework可以帮助我们完成各种界面的测试。但是，这些代码的编写非常繁琐。而且在大部分情况下，我们需要的是一个连贯性的，在多个Activity之间存在跳转的业务流程测试。Robotium是一款国外的Android自动化测试的框架，主要针对Android平台的应用进行黑盒自动化测试，它提供了模拟各种手势操作（点击、长按、滑动等）、查找和断言机制的API，能够对各种控件进行操作。它在有源码是可以测试源码，进行白盒测试；没有源码时，只有一个应用程序的apk也可以对其进行黑盒测试。Robotium结合Android官方提供的测试框架达到对应用程序进行自动化的测试。

&#160; &#160; &#160; &#160;源码及相关资料地址：https://code.google.com/p/robotium/

&#160; &#160; &#160; &#160;Robotium具有清晰的调用方法、良好的兼容性、完善的文档和大量的实际应用案例，并且支持截屏。最为符合我们目前的实际需求。


* 稳定性测试：

&#160; &#160; &#160; &#160;安卓系统最为让人诟病的问题就是碎片化，这点在中国比较明显。大致上，2.3.X 和 4.x 的系统各占半壁江山，此外还存在大量的山寨定制系统。因此在这推荐两款云端测试工具，Testin和百度云测试。两个框架测试都很简单，在官网注册账号后上传apk即可，网站会用大量的真机进行安装部署和monkey测试。测试完成后会发送一份测试给注册邮箱。Testin支持Robotium框架的代码测试，同时上传项目apk和测试apk（相同签名）即可。

&#160; &#160; &#160; &#160;Testin地址： http://www.testin.cn/

&#160; &#160; &#160; &#160;百度云测试： http://mtc.baidu.com/


**三、主要思路及步骤**

**3.1 主要思路**

&#160; &#160; &#160; &#160;下面主要介绍如何使用Robotium进行自动化测试，测试的思路为：设置一个按钮Button和文本框TextView，然后使用Robotium模拟点击Button，之后调用Button的onClick事件修改TextView的text属性值。在测试代码中监测TextView的text属性值是否与预期的属性值相等。同时还测试了Robotium的其他功能，比如使用Button的text属性查找Button等。

**3.2 实践步骤**



&#160; &#160; &#160; &#160;下面将会介绍如何在Android Studio中创建Robotium测试的步骤：

* 1.创建一个工程，命名为TestRobotium；
* 2.在app下的build.gradle文件中加入dependencies，如下所示：

```
androidTestCompile 'com.jayway.android.robotium:robotium-solo:5.1'
```

* 3.在androidTest目录下创建MainActivityTest类，如下图所示：
* ![](http://7xoi5h.com1.z0.glb.clouddn.com/截图20151130234734.png)
* 4.在主界面上面放置四个按钮，用以使用Robotium模拟对按钮的点击事件。修改activity_main.xml文件，代码如下：

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
    android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">

    <Button
        android:id="@+id/btn_test"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="Click me"
        android:onClick="onClikcMe"/>
    <TextView
        android:id="@+id/show_test"
        android:text="@string/hello_world"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@id/btn_test"/>
    <EditText
        android:id="@+id/insertText"
        android:layout_width="fill_parent"
        android:layout_height="50dp"
        android:layout_below="@+id/show_test"
        />
    <Button
        android:id="@+id/btn_test1"
        android:layout_below="@+id/insertText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="绿色"
        android:onClick="onClikcMe1"/>
    <Button
        android:id="@+id/btn_test2"
        android:layout_below="@id/btn_test1"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="黄色"
        android:onClick="onClikcMe2"/>
    <Button
        android:id="@+id/btn_test3"
        android:layout_below="@id/btn_test2"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="蓝色"
        android:onClick="onClikcMe3"/>

</RelativeLayout>
```

* 5.在MainActivity.java文件中添加按钮的点击事件，代码如下：

```
public void onClikcMe(View view){
        TextView textView = (TextView)findViewById(R.id.show_test);
        textView.setText("hi");
    }

    public void onClikcMe1(View view){
        TextView textView = (TextView)findViewById(R.id.show_test);
        textView.setText("hi1");
    }

    public void onClikcMe2(View view){
        TextView textView = (TextView)findViewById(R.id.show_test);
        textView.setText("hi2");
    }

    public void onClikcMe3(View view){
        TextView textView = (TextView)findViewById(R.id.show_test);
        textView.setText("hi3");
    }
```

* 6.接下来我们就可以模拟点击按钮的事件了。在MainActivityTest.java类当中写点击按钮的测试，代码如下：

```

package com.pku.lesshst.testrobotium;
import android.test.ActivityInstrumentationTestCase2;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;

import com.robotium.solo.Solo;
/**
 * Created by lesshst on 2015/11/30.
 */
public class MainActivityTest extends ActivityInstrumentationTestCase2{
    private Solo solo;

    public MainActivityTest() {
        super(MainActivity.class);
    }

    @Override
    public void setUp() throws Exception {
        super.setUp();
        solo = new Solo(getInstrumentation(), getActivity());
    }

    @Override
    protected void tearDown() throws Exception {
        solo.finishOpenedActivities();
        super.tearDown();
    }

    public void test_MainActivityChangeTextView_hi() throws Exception {
        //等待  Activity　"MainActivity"　启动
        assertTrue("无法启动启动类", solo.waitForActivity("MainActivity", 30000));
        solo.sleep(5000);
        //choose environment
        Button btn_test = (Button)solo.getView(R.id.btn_test);
        solo.clickOnView(btn_test);
        solo.waitForText("hi", 1, 5000);
        TextView tv = (TextView)solo.getView(R.id.show_test);
        String result = tv.getText().toString();
        assertEquals(result, "hi");
        solo.sleep(2000);
        //输入文字："131243"
        solo.enterText((EditText)solo.getView("insertText"), "这是一个测试！");
        solo.sleep(2000);

        //清空输入框的内容
        solo.clearEditText((EditText) solo.getView("insertText"));

        //按下 按钮 "绿色"
        solo.clickOnButton("^绿色$");
        solo.waitForText("hi1", 1, 5000);
        result = tv.getText().toString();
        assertEquals(result, "hi1");
        solo.sleep(2000);

        //按下 按钮 "黄色"
        solo.clickOnButton("^黄色$");
        solo.waitForText("hi2", 1, 5000);
        result = tv.getText().toString();
        assertEquals(result, "hi2");
        solo.sleep(2000);

        //按下 按钮 "蓝色"
        solo.clickOnButton("^蓝色$");
        solo.waitForText("hi3", 1, 5000);
        result = tv.getText().toString();
        assertEquals(result, "hi1");
        solo.sleep(2000);
    }
}

```
* 7.测试配置：点击菜单Run->Edit Configurations...，在Run/Debug Configurations窗口当中添加一个Android Tests，1、名字可以随意取；2、Module选择app；3、Test选择All in Module；4、Target Device选择Show chooser dialog。如下图所示：
![](http://7xoi5h.com1.z0.glb.clouddn.com/截图20151130231710.png)
* 8.如下图所示选择刚刚创建的Android Test
![](http://7xoi5h.com1.z0.glb.clouddn.com/截图20151201000944.png)
* 9.点击运行按钮即可完成自动化测试。
* 10.运行结果分析：
![](http://7xoi5h.com1.z0.glb.clouddn.com/截图20151201001251.png)
由上图可以看到，我们的test测试出了问题。这句代码（见下面）assertEquals语句出错，也就是当点击显示字符为“蓝色”的按钮后TextView的text属性值发生变化，但是其属性值与我们设想的值“hi1”不同，所以在此项测试当中发现这种不一致，达到测试的目的。

```
    solo.clickOnButton("^蓝色$");
    solo.waitForText("hi3", 1, 5000);
    result = tv.getText().toString();
    assertEquals(result, "hi1");
    solo.sleep(2000);
```

**四、常见问题及注意事项**

* 1.如何获取、区分具有相同id的多个控件？

&#160; &#160; &#160; &#160;基本思路：当控件都是一样的情况下去寻找能够区别它们的东西，有下面的集中方法。

&#160; &#160; &#160; &#160;方法1、ParentView（要找的控件对应的父布局）和index（控件位置）；

&#160; &#160; &#160; &#160;方法2、利用同级的具有唯一性的其他控件指定它们的ParentView，再通过父布局查找该控件；

&#160; &#160; &#160; &#160;方法3、若控件有具有特殊性的文本信息，可直接根据文本信息获取控件。

