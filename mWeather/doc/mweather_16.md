# widget桌面插件


    小组成员: 刚占慧 邱源 汤炀 谢亮 章伟  最终完成日期：15年11月29日
# 

**一、简介**

*本模块主要是完成天气预报项目的桌面插件功能*

**二、基础知识**

*桌面插件的实现是基于Broadcast的形式实现的；每一个桌面插件都对应于一个BroadcastReceiver类。Android系统提供了一个AppWidgetProvider类（它就是BroadcastReceiver的子类），这个类很关键，你在写桌面小控件时只需继承这个类就行。继承了AppWidgetProvider类之后，你可以根据自己的需要覆盖它的不同的生命周期的方法，来达到自己的目的。*
   
* AppWidgetProvider类的生命周期：

      AppWidgetProvider类的主要提供如下不同生命周期的方法：
      void onUpdate(Context context, AppWidgetManager appWidgetManager,int[] appWidgetIds) { }
      //这个方法字面意思是负责更新桌面小控件，但貌似只有在小控件被用户放到桌面上时被调用了一次，读者可以自己通过输出Log来测试  
      // 实现桌面控件是通常都会考虑重写该方法
      void onDeleted(Context context, int[] appWidgetIds)  
      // 在小控件被删除时调用该方法 

* 知识点2：

      知识点介绍


* 知识点3：

      知识点介绍


   

**三、主要思路及步骤**

**3.1 主要思路**

*简要介绍主要思路*

**3.2 实践步骤**

*详细描述开发的具体步骤*

**四、常见问题及注意事项**

*详细描述本部分的常遇到的问题以及开发过程中的注意事项*
