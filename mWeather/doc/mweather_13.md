# 制作未来6天天气界面


    小组成员: 盛意林、宫翠峰、李仕聪、胡玉、刘艺霞   最终完成日期：15年00月00日
# 

**一、简介**

    本模块是制作天气预报项目中的未来6天天气界面。

**二、基础知识**

*简要介绍本模块所需掌握的基础知识*
   
* 知识点1：ViewPager控件

      ViewPager控件可以用来制作程序的引导页面，也可以在界面中制作多个视图切换的效果。其使用流程为：
        1. 在布局文件中增加ViewPager控件：Android提供了兼容低版本终端的的android.support.v4.view.ViewPager
        2. 自定义一个PagerAdapter
        3. 初始化要展示的页面列表
        4. 在Activity里实例化ViewPager控件，并设置它的Adapter

* 知识点2：PagerAdapter适配器

      实现一个PagerAdapter， 至少需要重写以下几个方法:
        1、instantiateItem(ViewGroup, int)
            //将当前视图添加到container中，返回当前View
        2、destroyItem(ViewGroup, int, Object)
            //从当前container中删除指定位置（position）的View
        3、getCount()
            //返回要滑动的View的个数
        4、isViewFromObject(View, Object)
            //该函数用于判断instantiateItem(ViewGroup, int)返回的对象是否与当前View代表的是同一个对象


* 知识点3：

      知识点介绍


   

**三、主要思路及步骤**

**3.1 主要思路**

    本模块我们主要完成了两个工作：
    1、在主布局中添加了ViewPager控件，并创建两个布局文件，用于对未来六天的天气信息进行布局，并把它们加载到ViewPager中的视图；
    2、把XML中对未来天气解析出来的数据动态显示到布局文件中

**3.2 实践步骤**

*详细描述开发的具体步骤*

**四、常见问题及注意事项**

    1、添加ViewPager控件的时候，是使用android.support.v4.view.ViewPager。
    2、如何在主Activity中获取ViewPager中增加的布局页面的控件，并修改其值？
        由于在ViewPager中加载的布局文件是未来六天的天气信息，所以需要在MainActivity.java中对其控件进行监听，但是直接通过findViewById()方法是不能绑定该控件的，因为在MainActivity.java中只通过setContentView(R.layout.weather_info)绑定了主布局，所以其他.xml中的控件是不能直接操作的。
        解决办法：
        LayoutInflater inflater = LayoutInflater.from(this);
        View one_page = inflater.inflate(R.layout.page1, null);
        View two_page = inflater.inflate(R.layout.page2, null);
        
        weekD1 = (TextView)one_page.findViewById(R.id.weekDay1);
        
        //inflater.inflate(R.layout.xxx, null) 返回的是当前指定的xml的生成的View对象，可以把inflater理解为xml视图解析器。通过这种方式就可以监听了
    3、在做对不同天气类型更新相应的图片时，一定要做信息为空的时候的异常处理
