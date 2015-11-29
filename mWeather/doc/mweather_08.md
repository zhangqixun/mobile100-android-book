# 从数据库中读取城市列表


    小组成员: OOOO   最终完成日期：15年00月00日
# 

**一、简介**

*从数据库中读取城市列表*

**二、基础知识**

*简要介绍本模块所需掌握的基础知识*
   
* 知识点1：数据库文件存放位置

      在android中，为某个应用程序创建的数据库，只有它可以访问，其它应用程序是不能访问的，
      数据库位于Android设备/data/data/package_name/databases文件夹中，通过以下方式能够获得其根目录位置：
      
      String path = "/data"
                + Environment.getDataDirectory().getAbsolutePath()
                + File.separator + getPackageName()
                + File.separator + "databases"
                + File.separator
                ;

* 知识点2：获取工程中asset里的资源

      Android除了提供/res目录存放资源文件外，在/assets目录也会提供存放资源文件，在assets目录
      下面不会在R.java里面自动生成ID，所以读取assets目录下面的资源文件需要提供路径，我们可以
      通过AssetManager类来访问这些文件。
      
      AssetManager类提供对应用程序的原始资源文件进行访问的接口；此类提供了一个低级别的API，
      它允许你以简单的字节流的形式打开和读取和应用程序绑定在一起的原始资源文件。我们可以通过
      getAssets()方法获取AssetManager对象。
      
      


* 知识点3：

      知识点介绍


   

**三、主要思路及步骤**

**3.1 主要思路**

*简要介绍主要思路*

**3.2 实践步骤**

*详细描述开发的具体步骤*

**四、常见问题及注意事项**

*详细描述本部分的常遇到的问题以及开发过程中的注意事项*
