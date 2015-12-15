# Android 界面布局分析（1501210662 盛意林）

作者：盛意林

学号：1501210662

学院：北京大学软件与微电子学院

  相信大家对andriod界面都不陌生呢。一款漂亮的软件必然是需
  
  要界面优雅、交互友好的UI的呢。今天主要跟大家讲一讲两个方
  
  面的内容，一是UI界面布局的几种方式，二是浅谈一下Android布
  
  局优化的信息，希望对您有所帮助。
  



# 1.andriod的几大布局

##  ①.线性布局(LinearLayout)
##  ②.框架布局(FrameLayout)
##  ③.表格布局(TableLayout)
##  ④.相对布局(RelativeLayout)
##  ⑤.绝对布局(AbsoluteLayout)
##  ⑥.和网格布局(GridLayout)
##  ⑦.自定义布局 (custom-layout) 

# 2.Android布局优化


---

##一、线性布局(LinearLayout)
先看图：

![](../07202223-88e65fa588fb4b1e8aad7fb027608cd6.png)

LinearLayout是线性布局控件：要么横向排布，要么竖向排布

线性布局的整体布局方式包括了垂直和水平，这个也是必须指定的呢。

android:orientation：vertical (垂直方向) ：
 ![](../20115783413658.png)
   
 horizontal(水平方向)：
 ![](../201212301731329293735.png)


接下来请看一个例子：

```   
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    >
   <EditText
       android:id="@+id/edtInput"
       android:layout_width="fill_parent"
       android:layout_height="wrap_content"
       android:text="@+string/hello_world"
       />

    <Button
        android:id="@+id/myButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        />

    <TextView
		android:id="@+id/firstText"
		android:text="TextView第一行"
		android:gravity="center_vertical"
		android:textSize="15pt"
		android:background="#aa0000"
		android:layout_width="fill_parent"
		android:layout_height="wrap_content"
		android:layout_weight="10000"
        android:singleLine="true"/>
	<TextView
		android:id="@+id/secondText"
		android:text="TextView第二行"
		android:gravity="center_vertical"
		android:textSize="15pt"
		android:background="#0000aa"
		android:layout_width="fill_parent"
		android:layout_height="wrap_content"
		android:layout_weight="1"/>
    
</LinearLayout>

```

运行效果：

![](../13633282439959.png)




    



   ②
   
    。
    自定义布局 (custom-layout)








[sdfsdfdsf](www.daidu.com)
# sdfdsf 


## sdfsdfsd

sdfsdfsdfsd
### sdfdsfsdf* 
* sdfsdfsdf
1. sdfsdfdsf
2. sdfsdfds
1. 
dsfsdfds

---
1. 
sdfsadfasdf

1. sdfsdfdsf

2. sdfsdfds
3. sdfdsf

sdfsdfsdfsdf

| sdfsdf:afdsadfadsf | aaaaa |
| --| - -       |
| sdfdsf | 1:2 |


