# Android中ListView的使用与优化(1501210925 姜昊)

作者：姜昊

学号：1501210925

学院：北京大学软件与微电子学院


## 摘要
ListView是Android控件中比较常用，比较难用的控件之一。ListView是一个用来纵向显示条目的视图,这些条目内容来自于与该ListView相关联的Adapter。手机通讯录、好友列表等面板的显示，都是用ListView控件实现的。  

关键字：ListView      Adapter       优化


## 正文
**1：引言**     
列表的显示需要三个元素：        
1．ListVeiw： 用来展示列表的View；      
2．Adapter：用来把数据映射到ListView上的中介；    
3．Data：具体的将被映射的字符串，图片，或者基本组件。   
**1.1：ListVeiw**     
一个ListView通常有两个职责。    
（1）将数据填充到布局；     
（2）处理用户的选择点击等操作。     
在布局文件中添加ListView控件：
```     
<ListView
    android:id="@+id/listview"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content">
</ListView>```  
ListView的布局是由一条一条的Item组成的，每一个Item又是一个View。通过Adapter适配器将View添加到ListView中。
**1.2：Adapter**      
Adapter是连接后端数据和前端显示的适配器接口，是数据和UI（View）之间一个重要的纽带。在常见的View(List View,Grid View)等地方都需要用到Adapter。如下图直观的表达了Data、Adapter、View三者的关系：    
![](img001.png)     
一个Adapter是View视图与数据之间的桥梁，Adapter提供对数据的访问，也负责为每一项数据产生一个对应的View。  
常用的Adapter有以下几种：   
* BaseAdapter是一个抽象类，继承它需要实现较多的方法，所以也就具有较高的灵活性；   
* ArrayAdapter支持泛型操作，最为简单，只能展示一行字；    
* SimpleAdapter有最好的扩充性，可以自定义出各种效果；     
* SimpleCursorAdapter可以适用于简单的纯文字型ListView，它需要Cursor的字段和UI的id对应起来。如需要实现更复杂的UI也可以重写其他方法。可以认为是SimpleAdapter对数据库的简单结合，可以方便地把数据库的内容以列表的形式展示出来。  

**2：ListView的实现**   
**2.1 ListView控件与ArrayAdapter适配器的实现**  
ArrayAdapter是android中自己定义好的一种适配器，将数据添加到自己定义的View中，View中只有一个TextView。具体步骤如下：   
1：定义要添加的数据，数据以数组形式存储，举例我们定义为String数组，用来存储人物姓名。   
```
String[] datelist ={"梅长苏","蔺晨","飞流","蒙挚","霓凰郡主"};```
2：创建ArrayAdapter对象，将数据添加到View中。   
```
ArrayAdapter arrayAdapter = new ArrayAdapter(MainActivity.this,
                        android.R.layout.simple_expandable_list_item_1,datelist);```    

在创建ArrayAdapter对象过程中就已经将数据添加到了View中。ArrayAdapter构造器如下：
```
public ArrayAdapter(Context context, @LayoutRes int resource, @NonNull T[] objects) {
    this(context, resource, 0, Arrays.asList(objects));
}```
在此构造器中，最终调用了ArrayAdapter的另一个构造器：
```
public ArrayAdapter(Context context, @LayoutRes int resource, @IdRes int textViewResourceId,
        @NonNull List<T> objects) {
    mContext = context;
    mInflater = LayoutInflater.from(context);
    mResource = mDropDownResource = resource;
    mObjects = objects;
    mFieldId = textViewResourceId;
}```
在此构造器中，将数据和View一一对应。    
第一个参数Context context是指创建ArrayAdapter的Activity，因此在此设为MainActivity.this。    
第二个参数int resource是指Item对应的布局文件，指要将数据以什么样的View显示在ListView中，此处使用android中自定义的一个View，当然我们可以自己定义我们想要的View，然后赋予第二个参数。   
其具体代码如下：
```
<?xml version="1.0" encoding="utf-8"?>
<TextView
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@android:id/text1"
    android:layout_width="match_parent"
    android:layout_height="?android:attr/listPreferredItemHeight"
android:paddingStart="?android:attr/expandableListPreferredItemPaddingLeft"
    android:textAppearance="?android:attr/textAppearanceListItem"
    android:gravity="center_vertical" />```

第三个参数T[] objects是指显示的数据。   
我们也可以用另一个构造器，第三个参数传入一个List：
```
public ArrayAdapter(Context context, @LayoutRes int resource, @NonNull List<T> objects)```
ArrayAdapter的使用就是基于这三步，其实总体来说，所有的Adapter适配器都是基于这三步。     
最终实现结果为：    


