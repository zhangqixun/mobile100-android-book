#移动平台软件开发（Android）面试题汇总参考答案

## Question About Content Provider

###**声明**

本册面试题及答案大部分摘自网络，版权归原创者所有，如有侵权请您及时联系：

邮箱：1596888285@qq.com

参考用书：《第一行代码》（Android）、《疯狂Android讲义》、《Android4编程入门经典》

*本答案仅供参考，如有错误，请您指正*


---
**1、请介绍下ContentProvider是如何实现数据共享的**

一个程序可以通过实现一个Content provider的抽象接口将自己的数据完全暴露出去，而且Content providers是以类似数据库中表的方式将数据暴露。Content providers存储和检索数据，通过它可以让所有的应用程序访问到，这也是应用程序之间唯一共享数据的方法。
要想使应用程序的数据公开化，可通过2种方法：创建一个属于你自己的Content provider或者将你的数据添加到一个已经存在的Content provider中，前提是有相同数据类型并且有写入Content provider的权限。
如何通过一套标准及统一的接口获取其他应用程序暴露的数据？
Android提供了ContentResolver，外界的程序可以通过ContentResolver接口访问ContentProvider提供的数据。

---


**2、Context类中提供了一个openFileOutput()方法，请简述这个方法以及它的用法。**

openFileOutput()方法的第一参数用于指定文件名称，不能包含路径分隔符“/” ，如果文件不存在，Android 会自动创建它。创建的文件保存在/data/data/<package name>/files目录，如：/data/data/edu.pku.ss.zhangxitao.action/files/zhangxitao.txt。

openFileOutput()方法的第二参数用于指定操作模式，有四种模式，分别为： 

Context.MODE_PRIVATE=0

Context.MODE_APPEND    =  32768

Context.MODE_WORLD_READABLE =  1

Context.MODE_WORLD_WRITEABLE =  2

Context.MODE_PRIVATE：为默认操作模式，代表该文件是私有数据，只能被应用本身访问，在该模式下，写入的内容会覆盖原文件的内容，如果想把新写入的内容追加到原文件中。可以使用Context.MODE_APPEND

Context.MODE_APPEND：模式会检查文件是否存在，存在就往文件追加内容，否则就创建新文件。

Context.MODE_WORLD_READABLE和Context.MODE_WORLD_WRITEABLE用来控制其他应用是否有权限读写该文件。

MODE_WORLD_READABLE：表示当前文件可以被其他应用读取；MODE_WORLD_WRITEABLE：表示当前文件可以被其他应用写入。

如果希望文件被其他应用读和写，可以传入：
openFileOutput(“zhangxitao.txt”, Context.MODE_WORLD_READABLE + Context.MODE_WORLD_WRITEABLE);

---


**3、Android中如何实现跨程序共享数据的功能。**

内容提供器（Content Provider）主要用于在不同的应用程序之间实现数据共享的功能，它提供了一套完整的机制，允许一个程序访问另一个程序中的数据，同时还能保证被访数据的安全性。目前，使用内容提供器是Android实现跨程序共享数据的标准方式。

---

**4、ContentProvider类中几个抽象方法，简单介绍这几种方法。**

ContentProvider类中有六个抽象方法，分别是：onCreate()、query()、insert()、update()、delete()、getType

我们在使用子类继承它的时候，需要将这六个方法全部重写。

（1） onCreate()

初始化内容提供器的时候调用。通常会在这里完成对数据库的创建和升级等操作，返回true表示内容提供器初始化成功，返回false则表示失败。注意，只有当存在ContentResolver尝试访问我们程序中的数据时，内容提供器才会被初始化。

（2）query()

从内容提供器中查询数据。使用uri参数来确定查询哪张表，projection参数用于确定查询哪些列，selection和selectionArgs参数用于约束查询哪些行，sortOrder参数用于对结果进行排序，查询的结果存放在Cursor对象中返回。

（3） insert()

向内容提供器中添加一条数据。使用uri参数来确定要添加到的表，待添加的数据保存在values参数中。添加完成后，返回一个用于表示这条新记录的URI。

（4）update()

更新内容提供器中已有的数据。使用uri参数来确定更新哪一张表中的数据，新数据保存在values参数中，selection和selectionArgs参数用于约束更新哪些行，受影响的行数将作为返回值返回。

（5） delete()

从内容提供器中删除数据。使用uri参数来确定删除哪一张表中的数据，selection
第 7 章 跨程序共享数据，探究内容提供器和selectionArgs参数用于约束删除哪些行，被删除的行数将作为返回值返回。

（6）getType()

根据传入的内容URI来返回相应的MIME类型。


