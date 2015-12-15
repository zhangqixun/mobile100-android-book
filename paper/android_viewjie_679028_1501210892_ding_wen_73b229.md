# Android View解析(1501210892 丁文玲)

本篇从LayoutInflater原理解析以及Android如何自定义View两个方面来讲解Android View解析，首先我们讲一下LayoutInflater的原理。

## 一：LayoutInflater原理解析

LayoutInflater主要是用来进行加载布局的，它的基本用法非常简单，首先我们要做的就是获取到LayoutInflater的实例，有以下两种方式可以：第一种代码如下：

```LayoutInflater layoutInflater = LayoutInflater.from(context);```

除此之外，还有另外一种方法
```LayoutInflater layoutInflater = (LayoutInflater) context
		.getSystemService(Context.LAYOUT_INFLATER_SERVICE);```

其实两种方法的本质相同，只不过第一种是第二种的简单写法，在我们得到实例之后，就可以调用它里面的inflate（）方法来加载布局，代码如下：

```layoutInflater.inflate(resourceId, root);```

这里面有两个参数，第一个参数的含义是我们要加载的那个布局的id，第二个参数的含义是为该布局嵌套的父布局，如果第二个参数不需要的就传null，通过以上方式我们就建立了一个布局的实例，之后我们再指定位置把它添加进去就可以显示出来了。
下面我们通过一个例子来说明：

建立一个布局文件activity_main.xml，代码如下：

```<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/main_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >
</LinearLayout>```

由代码看到，这个布局文件的内容是非常简单的，除了一个空的LinearLayout，其余什么都没有，所以界面上不会显示东西。
然后我们定义一个button_layout.xml，代码如下：

```<Button xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Button" >
</Button>```

我们看到这个布局文件也是非常简单，就仅仅有一个Button的按钮，现在我们要做的是想办法，如何将button_layout添加到LinearLayout里面，根据以上我们所介绍的用法，修改MainActivity里面的代码，修改后的代码如下：

```public class MainActivity extends Activity {
	private LinearLayout mainLayout;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		mainLayout = (LinearLayout) findViewById(R.id.main_layout);
		LayoutInflater layoutInflater = LayoutInflater.from(this);
		View buttonLayout = layoutInflater.inflate(R.layout.button_layout, null);
		mainLayout.addView(buttonLayout);
	}
}```

由以上代码，我们可以看到，首先是获取到了LayoutInflater的实例，然后我们调用它的Inflate（）方法来加载button_layout，最后调用LinearLayout的addView（）来把它加到LinearLayout里面，运行结果如下图表示：