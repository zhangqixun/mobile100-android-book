# 国际化

起草人: 黄汉煜   日期：15年00月00日

修改完善：杨晓   日期：15年00月00日

# 

**一、实验目的**

了解android开发中国际化的概念，及实现app国际化的基本方法。

**二、基础知识**

总所周知，Android系统有可能运行在不同的语言环境，如：中文、英文，因此，如何使自己开发的应用程序在不同的语言环境中表现一致，即为Android的国际化问题。当程序员开发Android应用时，得益于 Android 中的资源管理方式。程序员的代码可以不直接和资源发生关系。Android 中，我们通常通过 R 文件提供的索引来间接的引用某一个资源。因此，在开发中，程序员可以通过准备多套资源文件从而实现app的国际化。
   
* 知识点1：

      字符串国际化，只需创建对应的values文件夹即可，例如：中文为values-zh、美国英文为values-en-rUS。

* 知识点2：

      图片国际化，只需创建对应的drawable文件夹即可，例如：中文为values-zh-hdpi、美国英文为values-en-rUS-hdpi（hdpi为高分辨率）。


* 知识点3：

      知识点介绍


   

**三、实验内容及步骤**

**3.1 实验内容**
安卓字符串国际化，实现对美国的国际化

**3.2 实验步骤**

1 在res目录下新建一个values-en-rUS，en-rUS是美国的代号
2 将values里面的文件拷进values-en-rUS，然后修改xml文件，将value修改为英文：
    

---

    <string name="cityList">citys</string>
    <string name="today">today</string>
    <string name="monday">Monday</string>
    <string name="tuesday">Tuesday</string>
    <string name="wednesday">Wednesday</string>
    <string name="thursday">Thursday</string>
    <string name="friday">Friday</string>
    <string name="saturday">Saturday</string>
    <string name="sunday">Sunday</string>
3 运行程序，在设置中选择地区为美国，查看效果

**四、常见问题及注意事项**

每次运行程序检验对应语言环境下的效果时，切记先对android系统的语言环境进行切换。

具体国家简写查询：

各国语言缩写  http://www.loc.gov/standards/iso639-2/php/code_list.php

国家和地区简写  
http://www.iso.org/iso/en/prods-services/iso3166ma/02iso-3166-code-lists/list-en1.html

国家_地区语言速查表：http://www.cnblogs.com/Mien/archive/2008/08/22/1273950.html


