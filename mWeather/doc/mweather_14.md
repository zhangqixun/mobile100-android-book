# 增加程序引导页面


    小组成员: 第十五组   最终完成日期：15年12月1日
# 

**一、简介**

*本模块介绍如何为天气预报添加引导界面，并将其设置为只有第一次启动时可见。*

**二、基础知识**

**
   
* 知识点1：

      创建ViewPager布局

* 知识点2：

      为ViewPager增加页面改变的监听事件


* 知识点3：

      为ViewPager增加控件和监听事件
* 知识点4：

      使用SharedPreference记录启动次数


   

**三、主要思路及步骤**

**3.1 主要思路**

*创建一个activity guide并为其设置4个布局文件guide.xml和page1.xml, page2.xml, page3.xml.在gudied.xml中将三个page依次展示。在page3.xml中添加button，用intent跳转到mainActivity；为了实现下方小圆点动态变化，需要监听页面改变事件修改imageView；最后，设置sharedPreference以记录是否是首次使用。*

**3.2 实践步骤**

*（1）添加布局文件：
创建新的活动和布局文件guide，在guide.java中将guide.xml设为可见：
setContentView(R.layout.guide);
创建三个page文件page1.xml,page2.xml,page3.xml;
在guide.java中增加显示三个page文件的方法initView():
private void initViews(){
    LayoutInflater inflater = LayoutInflater.from(this);
    views =new ArrayList<View>();
    views.add(inflater.inflate(R.layout.page1,null));
    views.add(inflater.inflate(R.layout.page2,null));
    views.add(inflater.inflate(R.layout.page3,null));
    vpAdapter = new ViewPagerAdapter(views,this);
    vp = (ViewPager)findViewById(R.id.viewpager_guide);
    vp.setAdapter(vpAdapter);
    vp.setOnPageChangeListener(this);
}
然后在onCreate()方法中调用initView()，这样就可以实现在guide.xml中显示三个page。
最后，在AndroidManifest中将guide.java设置为主activity:
<activity android:name=".Guide">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />

        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
此时，就可以在打开应用时显示pageView:
 
（2）为布局文件添加组件
A、添加切换到主界面的Button
在page3.xml中添加一个button按钮：
<Button
    android:id="@+id/guide_btn"
    android:layout_height="wrap_content"
    android:layout_width="wrap_content"
    android:text="开始体验"/>
为该按钮设置监听事件：
guide_btn = (Button)views.get(2).findViewById(R.id.guide_btn);// 其中，由于该button设置在第//三个视图中，所以实例化该button时需要先指定到对应的视图。
```guide_btn.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        Intent intent = new Intent(Guide.this, MainActivity.class);
        startActivity(intent);
        finish();
    }
});```
B、添加图片和textView等组件以增加美观性：
该部分与在普通activity的布局文件中添加组件的方式相同，只是需要注意指定该组件所在的视图。
（3）动态变化小圆点
首先，实现initDots()方法：
```void initDots(){
    dots = new ImageView[views.size()];
    for(int i=0;i<views.size();i++){
        dots[i]=(ImageView)findViewById(ids[i]);
    }
}```
在onCreate()方法中调用该方法；
重写onPageSelected()方法以实现当选中某个页面时该页面对应的小圆点变为实心：
```@Override
public void onPageSelected(int position) {
    for(int a=0;a<ids.length;a++){
        if(a ==position){
            dots[a].setImageResource(R.drawable.page_indicator_focused);
        }else{
            dots[a].setImageResource(R.drawable.page_indicator_unfocused);
        }
    }
}```
完成后就可以实现小圆点滑动效果：
 
（4）记录首次启动
想要实现只在第一次打开app时才启动guide，需要对应用的启动进行记录，在这里我们选用sharedPreference来进行记录。关于sharedPreference的介绍和使用在前面的章节已经介绍过，在这里不再重复。
在Guide活动的onCreate方法中，实例化一个sharedPreference判断其isFirst是否为true，若为true，则继续执行下面的代码，若为false，则直接用intent跳转到mainActivity:
```super.onCreate(savedInstanceState);
SharedPreferences first = (SharedPreferences)getSharedPreferences("first",MODE_PRIVATE);
Boolean isFirst = first.getBoolean("isFirst", true);
if(isFirst){
    setContentView(R.layout.guide);
    initViews();
    initDots();
    guide_btn = (Button)views.get(2).findViewById(R.id.guide_btn);
    guide_btn.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
	  SharedPreferences.Editor editor = first.edit();
    	  editor.putBolean("isFirst",false);
    	  editor.commit();
            Intent intent = new Intent(Guide.this, MainActivity.class);
            startActivity(intent);
            finish();
        }
    });
}
else{
    Intent intent = new Intent(Guide.this, MainActivity.class);
    startActivity(intent);
    finish();
}```

*

**四、常见问题及注意事项**

*详细描述本部分的常遇到的问题以及开发过程中的注意事项*
