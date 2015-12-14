# android UI布局设计(丁泽武 1501210893)




一、简介
1. 背景
    如今的手机端APP是以惊人的速度在增加，随着APP数量的无限放大，
    相同功能的APP也是越来越多。如音乐的APP有豆瓣电台、网易云音乐、酷我、
    多米等等。而这么多的应用，在初次映入眼帘的时候，是什么吸引了用户呢？是什么是用户继续使用下去呢？毫无疑问，这将是UI设计。一个好的UI设计不能决定一个APP是否成功，因为决定APP成功的最终元素肯定是业务和性能。但是一个好的UI给了你的软件一个强大的竞争力。Android虽然在智能手机市场份额上远高于iOS，但是近几年谷歌PlayStore的应用数量却低于AppStore。随着安卓系统的不断扩张，谷歌Play Store的应用数量也飞涨。根据应用跟踪平台appFigures今天发布的报告，2014年Google Play应用商店增加的新应用数量首次超过苹果App Store，开发者社区增长连续第三年超过苹果，开发者数量达到38.8万。面对如此大的市场，好的UI设计有着战略性作用。

1. 主要内容
    * 
    布局类
        1. FrameLayout
            
            FrameLayout是最简单的布局管理器，他所做的是将新的子视图放在前一个parent视图的左上角(默认)，可以通过gravity属性来改变他的位置，并且有可能覆盖上一个父视图。

        1. LinearLayout
        
            LinearLayout是按照垂直或者水平的方向来排列每一个子视图。一个水平方向的布局是一个视图行，垂直方向的为一个视图列。
        
        1. RelativeLaout
            
            RelativeLayout是根据相对位置来布局，笔者认为是用处最广最灵活的。可以根据自己的布局需要灵活的使用。
            
        1. GridLayout
        
            GridLayout是在Android 4.0之后引入的，用法极其灵活，可以简化布局。
            
        1. AbsoluteLayout
        
            AbsoluteLayout是按照绝对坐标来布局组件。
            
    * 
    XML外部资源定义
    
        1. 可扩展标记语言，标准通用标记语言的子集，是一种用于标记电子文件使其具有结构性的标记语言。是Internet环境中跨平台的、依赖于内容的技术，也是当今处理分布式结构信息的有效工具。XML文件在网络数据传输中跟JSON格式一样，是属于轻量级的数据存储和传输的载体。而在android等开发中，XML格式更多的是一种配置文件，在UI设计中，layout_xml文件可以有效的将布局从表示层从视图和代码中分离出来，可以达到解耦的作用，使得程序可读性更强。而且当数据和需求更改时，通过XML文件更容易适应新的更改。
        
            
            

二、布局类详解及应用
    
        *  FrameLayout
            1. 详解
                1.1定义
                FrameLayout是帧布局，整个界面对于FrameLayout来说是一块备用的空白区域，区域的大小由最大的子视图决定，换句话说，如果各个视图的大小一样，则只显示最后一个加载的子视图，前面的全部被覆盖。
                1.2属性
                FrameLayout是比较简单的布局，所以属性比较少。如下
                    * layout_width
                        组件的宽度：可以是fill-parent或者是warp-content
                    * layout_height
                        组件的高度：可以是fill-parent或者是warp-content
                    * layout_marginLeft
                        组件跟左边缘的距离
                    * layout_marginTop
                        组件跟顶部的距离
                    * layout_marginRight
                        组件跟右边缘的距离
                    * layout_marginBottom
                        组件跟底部的距离
                    * layout_gravity
                        组件内部的位置
            
            2. 应用
                下面的实例通过
                
               ```
               <FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
                xmlns:tools="http://schemas.android.com/tools"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent">
                <TextView
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:textSize="100sp"
                android:textColor="#000ff0"
                android:text="最底下的视图"/>
                <TextView
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:textSize="70sp"
                android:textColor="#f77700"
                android:text="第二层视图"/>
                <TextView
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:textSize="30sp"
                android:textColor="#f6666f"
                android:text="第三层视图"/>
                <TextView
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:textSize="50sp"
                android:textColor="#000000"
                android:text="第四层视图"/>
                </FrameLayout>
               ```
            3.截图
            ![](1298207998@chatroom_1450078205212_75.png)
            
    
        *  RelativeLaout
            
            1. 详解
            
            
            
            2. 应用 


        
        *  LinearLayout
        
            1.详解
            
            2.应用
         
        *  GridLayout
            
            1.详解
            
            2.应用
        
        *  AbsoluteLayout
        
            1.详解
            
            2.应用
            
            
三、XML布局文件详解及应用

    
        * 详解
        
        * 应用
        
        
        
        
        
        
        
四、UI高级设计


五、UI优化

六、总结
















    






