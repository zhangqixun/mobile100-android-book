# Android中service的用法和地位


### 1501210986  孙晴

## 1.服务是什么
    
* 服务(Service)是Android中实现程序后台运行的解决方案,它非常适合用于去执行那 些**不需要和用户交互**而且还要求**长期运行**的任务。服务的运行不依赖于任何用户界面,即使 当程序被切换到后台,或者用户打开了另外一个应用程序,服务仍然能够保持正常运行。不过需要注意的是,服务并不是运行在一个独立的进程当中的,**而是依赖于创建服务时所在的应用程序进程**。当某个应用程序进程被杀掉时,所有依赖于该进程的服务也会停 止运行。另外,服务并不会自动开启线程,所有的代码 都是默认运行在主线程当中的。也就是说,我们需要在服务的内部手动创建子线程,并在这 里执行具体的任务,否则就有可能出现主线程被阻塞住的情况。我们就先来了解一下关于 Android 多线程编程的知识。


## 2.Android 多线程编程

* 多线程编程的重要性：

    当我们需要执行一些耗时操作,比如说发起一条网络请求时,考虑到网速等其他原因,服务器未必会立刻响应我们的请求,如果 不将这类操作放在子线程里去运行,就会导致主线程被阻塞住,从而影响用户对软件的正常使用。
   

### 2.1 线程的基本用法

定义一个线程只需要新建一个类继承自 Thread,然后重写父类的 run()方法,并在里面 编写耗时逻辑即可,如下所示:


        class MyThread extends Thread {
        @Override
            public void run() {
            // 处理具体的逻辑 
            }
        }
        
那么该如何启动这个线程呢?其实也很简单,只需要 new 出 MyThread 的实例,然后调 用它的 start()方法,这样 run()方法中的代码就会在子线程当中运行了,如下所示:


        new MyThread().start();
当然,使用继承的方式耦合性有点高,更多的时候我们都会选择使用实现 Runnable 接 口的方式来定义一个线程,如下所示:

        class MyThread implements Runnable {
        @Override
            public void run() {
            // 处理具体的逻辑 
            }
        }
如果使用了这种写法,启动线程的方法也需要进行相应的改变,如下所示:

        MyThread myThread = new MyThread();
        new Thread(myThread).start();
        
可以看到,Thread 的构造函数接收一个 Runnable 参数,而我们 new 出的 MyThread 正是 一个实现了 Runnable 接口的对象,所以可以直接将它传入到 Thread 的构造函数里。接着调用 Thread 的 start()方法,run()方法中的代码就会在子线程当中运行了。 当然,如果你不想专门再定义一个类去实现 Runnable 接口,也可以使用匿名类的方式,
这种写法更为常见,如下所示:

        new Thread(new Runnable() {
            @Override
            public void run() {
            // 处理具体的逻辑 
            }
        }).start();

### 2.2 在子线程中更新UI
和许多其他的 GUI 库一样,Android 的 UI 也是线程不安全的。也就是说,如果想要更新应用程序里的UI 元素,则必须在主线程中进行,否则就会出现异常。
眼见为实,让我们通过一个具体的例子来验证一下吧。新建一个 AndroidThreadTest项目,然后修改 activity_test_update_ui.xml 中的代码：

    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android" android:layout_width="match_parent" android:layout_height="match_parent" >
            <Button
                android:id="@+id/change_text"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:text="Change Text" />
            <TextView
                android:id="@+id/text"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_centerInParent="true"
                android:text="Hello world"
                android:textSize="20sp" />
    </RelativeLayout>
布局文件中定义了两个控件,TextView 用于在屏幕的正中央显示一个 Hello world 字符串,Button 用于改变 TextView 中显示的内容,我们希望在点击 Button 后可以把 TextView 中 显示的字符串改成 Nice to meet you。
接下来修改 activity_test_update_ui 中的代码,如下所示:

    public class TestUpdateUi extends Activity implements OnClickListener {
            private TextView text;
            private Button changeText;
            @Override
            protected void onCreate(Bundle savedInstanceState) {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.activity_main);
                text = (TextView) findViewById(R.id.text);
                changeText = (Button) findViewById(R.id.change_text);
                changeText.setOnClickListener(this);
            }
            @Override
            public void onClick(View v) {
                switch (v.getId()) {
                case R.id.change_text:
                    new Thread(new Runnable() {
                        @Override
                        public void run() {
                            text.setText("Nice to meet you");
                        }
                    }).start();
                    break;
                default:
                    break; 
                }
            } 
    }


可以看到,我们在 Change Text 按钮的点击事件里面开启了一个子线程,然后在子线程 中调用 TextView 的 setText()方法将显示的字符串改成 Nice to meet you。代码的逻辑非常简 单,只不过我们是在子线程中更新 UI 的。现在运行一下程序,并点击 Change Text 按钮,你 会发现程序果然崩溃了,如图 





 


