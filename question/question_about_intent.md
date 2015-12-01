#移动平台软件开发（Android）面试题汇总参考答案

## Question About Intent

###**声明**

本册面试题及答案大部分摘自网络，版权归原创者所有，如有侵权请您及时联系：

邮箱：1596888285@qq.com

参考用书：《第一行代码》（Android）、《疯狂Android讲义》、《Android4编程入门经典》

*本答案仅供参考，如有错误，请您指正*


---
**1、请简述Android中的Intent作用**
用来解决Android应用的的各项组件之间的通讯。并且作为传递数据和事件的桥梁。传递数据时的代码有两种：

第一种是：
```
Intent intent = new  Intent(CurrentActivity.this , OtherActivity.class); 
intent.putExtra(“data” , somedata);
```
第二种是新建一个Bundle，再把该Bundle加入intent,如：
```
Bundle bundle = new Bundle() ; 
bundle.putString(“data” , somedata) ; 
intent.putExtras(bundle)。
```

**2、若一个Android系统里安装了多种浏览器，能否指定某浏览器访问指定页面？请说明理由**

通过直接发送Uri把参数带过去，或者通过manifest离得intentfilter里的data属性。代码如下：

```
Intent intent = new Intent();
Intent.setAction(“android.intent.action.View”);
Uri uriBrowsers = Uri.parse(“http://www.ss.pku.edu.cn/”);
Intent.setData(uriBrowsers);
//包名、要打开的activity
intent.setClassName(“com.android.browser”,”com.android.browser.BrowserActivity”);
startActivity(intent);
```
**3、IntentService有何优点**


IntentService使用队列的方式将请求的Intent加入队列，然后开启一个workerthread(线程)来处理队列中的Intent，对于异步的startService请求，IntentService会处理完成一个之后再处理第二个，每一个请求都会在一个单独的workerthread中处理，不会阻塞应用程序的主线程，这里就给我们提供了一个思路，如果有耗时的操作与其在Service里面开启新线程还不如使用IntentService来处理耗时操作。

4、Activity间通过Intent传递数据大小有没有限制
Intent在传递数据时是有大小限制的，这里官方并未详细说明，不过通过实验的方法可以测出数据应该被限制在1MB之内（1024KB），可以采用传递Bitmap的方法，你会发现当图片大小超过1024kB（准确地说是1020KB左右）的时候，程序就会出现闪退、停止运行等异常(不同的手机反应不同)，因此可以判断Intent的传输容量在1MB之内。


5、现有两个Activity，分别是FstActivity和SecActivity，FstActivity有一个Button为Button1，当点击Button1的时候，FstActivity会通过Intent方式跳转到SecActivity，那么请简述如何从SecActivity返回一些数据给FstActivity？（如果你认为必要，可以写下相应代码）
使用startActivityForResult()方法。startActivityForResult()方法也是用来启动活动的，但是这个方法期望在活动销毁的时候能够返回一个结果给上一个活动的点击事件。
startActivityForResult()方法接收两个参数，第一个参数是Intent，第二个参数是请求码，用于在之后的回调中判断数据的来源。
部分代码如下：
/*
**假如我们已经定义了两个Activity，分别是FirstActivity和SecondActivity。FirstActivity中**定义了一个button1；SecondActivity中定义了一个button2
*/
button1.setOnClickListener(new OnClickListener() { 
@Override 
public void onClick(View v) { 
Intent intent = new Intent(FirstActivity.this, SecondActivity.class); 
startActivityForResult(intent, 1); 
} 
});
这里我们使用了startActivityForResult()方法来启动SecondActivity，请求码只要是一个唯一值就可以了，这里传入了1。接下来我们在SecondActivity中给按钮注册点击事件，并在点击事件中添加返回数据的逻辑，代码如下所示： 
public class SecondActivity extends Activity { 
@Override 
protected void onCreate(Bundle savedInstanceState) { 
super.onCreate(savedInstanceState); 
requestWindowFeature(Window.FEATURE_NO_TITLE); 
setContentView(R.layout.second_layout); 
Button button2 = (Button) findViewById(R.id.button_2); 
button2.setOnClickListener(new OnClickListener() { 
@Override 
public void onClick(View v) { 
Intent intent = new Intent(); 
intent.putExtra("data_return", "Hello FirstActivity"); 
setResult(RESULT_OK, intent); 
finish(); 
} 
}); 
} 
} 
可以看到，我们还是构建了一个Intent，只不过这个Intent仅仅是用于传递数据而已，它没有指定任何的“意图”。紧接着把要传递的数据存放在Intent中，然后调用了setResult()方法。这个方法非常重要，是专门用于向上一个活动返回数据的。setResult()方法接收两个参数，第一个参数用于向上一个活动返回处理结果，一般只使用RESULT_OK或RESULT_CANCELED这两个值，第二个参数则是把带有数据的Intent传递回去，然后调用了finish()方法来销毁当前活动。 
由于我们是使用startActivityForResult()方法来启动SecondActivity的，在SecondActivity被销毁之后会回调上一个活动的onActivityResult()方法，因此我们需要在FirstActivity中重写这个方法来得到返回的数据，如下所示： 
@Override 
protected void onActivityResult(int requestCode, int resultCode, Intent data) { 
switch (requestCode) { 
case 1: 
if (resultCode == RESULT_OK) { 
String returnedData = data.getStringExtra("data_return"); 
Log.d("FirstActivity", returnedData); 
} 
break; 
default: 
} 
} 
onActivityResult()方法带有三个参数，第一个参数requestCode，即我们在启动活动时传入的请求码。第二个参数resultCode，即我们在返回数据时传入的处理结果。第三个参数data，即携带着返回数据的Intent。由于在一个活动中有可能调用startActivityForResult()方法去启动很多不同的活动，每一个活动返回的数据都会回调到onActivityResult()这个方法中，因此我们首先要做的就是通过检查requestCode的值来判断数据来源。确定数据是从SecondActivity返回的之后，我们再通过resultCode的值来判断处理结果是否成功。最后从data中取值并打印出来，这样就完成了向上一个活动返回数据的工作。

6、请简述Intent中putExtras(Bundle data)、Bundle getExtras()、putExtra(String name,***value)、get***Extra(String name)各有什么作用。
putExtras(Bundle data):向Intent中放入需要的数据包
Bundle getExtras():取出Intent所携带的数据
putExtras(String name, Xxx value):向Intent中按照key-value形式传值
getXxxExtra(String name):从Intent中按key取值


7、使用Intent来传递对象有几种方法？分别介绍这些方法（如果你认为必要，可以写下相应代码）
有两种方法：显式Intent和隐式Intent
显式Intent使用以下代码实现：
Intent intent = new Intent(FirstActivity.this, SecondActivity.class);
startActivity(intent);
隐式Intent则含蓄很多，它并不明确指出我们想要启动哪一个活动，而是指定了一系列更为抽象的action和category等信息，然后交由系统去分析这个Intent，并帮我们找出合适的活动去启动。


8、Intent传递数据时，哪些数据类型可以被传递？（腾讯2015春招移动客户端开发练习卷）
可以传递以下内容：简单或基本数据类型、传递一个Bundle、传递Serializable对象、Parcelable对象、Intent

