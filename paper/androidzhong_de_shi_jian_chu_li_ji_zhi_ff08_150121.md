# Android中的事件处理机制（1501210707 王可颖）
不管是桌面应用还是手机应用程序，面对最多的就是用户，经常需要处理的就是用户动作——就是需要为用户动作提供响应，这种为用户动作提供响应的机制就是事件处理。

Android提供了强大的事件处理机制，包括两套事件处理机制：
 
* 基于监听的事件处理。
* 基于回调的事件处理。

对于基于监听的事件处理模型来说，主要做法就是为Android界面组件绑定特定的事件监听器。其中，事件源和事件监听器是分离的，当事件源上发生特定事件之后，该事件交给事件监听器来处理；

对于基于回调的事件处理模型来说，主要做法就是重写Android界面组件特定的回调方法，或者重写Activity的回调方法。事件源和事件监听器是统一的，当事件源发生特定事件后，该事件还是事件源本身负责处理。

##  1 基于监听的事件处理

在事件监听的处理模型中，主要涉及以下三类对象：
* Event Source（事件源）：事件发生的场所，通常就是各个组件，例如：按钮、窗口等。
* Event（事件）：事件封装了界面组件上发生的特定事情，通常就是一次用户操作。如果程序需要获得界面组件上所发生事件的相关信息，一般通过Event对象取得。
* Event Listener（事件监听器）：负责监听事件源发生的事件，并对各种事件做出相应的响应。

在基于事件监听的处理模型中，事件监听器必须实现事件监听器接口，Android为不同的界面组件提供了不同的监听器接口，这些接口通常以内部类的形式存在。以View类为例，它包含了如下几个内部类接口：
* View.onClickListener：单击事件的事件监听器必须实现的接口。
* View.onCreateContextMenuListener：创建上下文菜单事件的事件监听器必须实现的接口。
* View.onFocusChangedListener：焦点改变事件的事件监听器必须实现的接口。
* View.onKeyListener：按键事件的时间监听器必须实现的接口。
* View.onLangClickListener：长单击事件的事件监听器必须实现的接口。
* View.onTouchListener：触摸屏事件的事件监听器必须实现的接口。

所谓事件监听器，其实就是实现了特定接口的Java类的实例。在程序中实现事件监听器通常有以下几种形式：
* 内部类形式：将事件监听器类定义为当前类的内部类。
* 外部类形式：将事件监听器定义为一个外部类。
* Activity本身作为事件监听器类：让Activity本身实现监听器接口，并实现事件处理方法。
* 匿名内部类形式：使用匿名内部类创建事件监听器对象。



### 1.1 内部类作为事件监听器类

使用内部类可以在当前类中复用该监听器类；因为监听器类是外部类的内部类，所以可以自由访问外部类的所有界面组件。这也是内部类的两个优势。

```
public class MainActivity extends Activity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button button = (Button) findViewById(R.id.button);
        button.setOnClickListener(new MyClickListner());
    }
    //单击事件的监听器（内部类）
    class MyClickListner implements View.OnClickListener{
        //事件处理器
        @Override
        public void onClick(View v) {
            Toast.makeText(MainActivity.this,"单击了按钮",Toast.LENGTH_SHORT).show();
        }
    }
}
```


## 1.2 外部类作为事件监听器类

使用顶级类定义事件监听器类的形式比较少见，主要因为一下两个原因：

* 事件监听器属于特定的GUI界面，定义成外部类不利于提高程序的内聚性。
* 外部类形式的事件监听器不能自由访问创建GUI界面的类中的组件，编程不够简洁。

但如果某个事件监听器确实需要被多个GUI界面所共享，而且主要是完成某种业务逻辑的实现，则可以考虑使用外部类形式。下面的程序定义了一个外部事件监听器类，实现了发送短信的功能。
```
public class MySendListener implements View.OnLongClickListener {
    private Activity act;
    private EditText address;
    private EditText content;
    public MySendListener(Activity act, EditText address, EditText content) {
        this.act = act;
        this.address = address;
        this.content = content;
    }
    @Override
    public boolean onLongClick(View v) {
        String addressStr = address.getText().toString();
        String contentStr = content.getText().toString();
        // 获取短信管理器
        SmsManager smsManager = SmsManager.getDefault();
        // 创建发送短信的PendingIntent
        PendingIntent sentIntent = PendingIntent.getBroadcast(act, 0, new Intent(), 0);
        // 发送文本短信
        smsManager.sendTextMessage(addressStr, null, contentStr, sentIntent, null);
        Toast.makeText(act, "短信发送完成", Toast.LENGTH_LONG).show();
        return false;
    }
}
```
然后，在MainActivity中，为指定按钮的长单击事件绑定监听器，当用户长单击按钮时，程序触发监听器，其中包含的事件处理方法向指定手机发送短信。
```
public class MainActivity extends Activity {
    private Activity act;
    private EditText address;
    private EditText content;
    public MainActivity(Activity act, EditText address, EditText content) {
        this.act = act;
        this.address = address;
        this.content = content;
    }
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        // 获取页面中收件人地址、短信内容
        address = (EditText)findViewById(R.id.address);
        content = (EditText)findViewById(R.id.content);
        Button bn = (Button)findViewById(R.id.send);
        // 使用外部类的实例作为事件监听器
        bn.setOnLongClickListener(new MySendListener(this, address, content));
    }
}
```

## 1.3 Activity本身作为事件监听器
这种形式非常简洁，直接在Activity类中定义事件处理器的方法。但这种做法有两个缺点：

* 可能造成程序结构混乱，Activity主要职责应该是完成界面初始化工作，但此时还需包含事件处理器的方法，从而引起混乱。
* 如果Activity界面类需要实现监听器接口，让人感觉比较怪异。

```
public class MainActivity extends Activity implements View.OnClickListener{
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button button = (Button) findViewById(R.id.button);
        button.setOnClickListener(this);
    }
    @Override
    public void onClick(View v) {
        Toast.makeText(this,"单击了按钮",Toast.LENGTH_SHORT).show();
    }
}
```
程序中，Activity类实现了OnClickListner事件监听器接口。当为某个组件注册事件监听器对象时，直接使用this作为事件监听器对象即可。


## 1.4 匿名内部类作为事件监听器类

大部分时候，事件处理器没有什么复用价值（可复用代码通常被抽象成业务逻辑方法），因此大部分只临时使用一次，所以使用匿名内部类形式的事件监听器更合适。这种形式也是目前使用最广泛的事件监听器形式。

```
public class MainActivity extends Activity{
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button button = (Button) findViewById(R.id.button);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Toast.makeText(MainActivity.this,"单击了按钮",Toast.LENGTH_SHORT).show();
            }
        });
    }
}
```



---


“new监听器接口”或“new事件适配器”的形式就是用于创建匿名内部类形式的事件监听器。


## 1.5 直接绑定到标签

Android中还可以直接在界面布局文件中为指定标签绑定事件处理方法。

对于很多Android界面组件标签而言，它们都支持onClick等属性，属性值就是形如xxx(View source)的方法的方法名。

```
 <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/button"
        android:text="单击我"
        android:onClick="click"/>
```


---

```
public class MainActivity extends Activity{
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }
        //source参数代表事件源
     public void click(View source){
        Toast.makeText(MainActivity.this,"单击了按钮",Toast.LENGTH_SHORT).show();
    }
}
```