#移动平台软件开发（Android）面试题汇总参考答案

## Question About Stroage

###**声明**

本册面试题及答案大部分摘自网络，版权归原创者所有，如有侵权请您及时联系：

邮箱：1596888285@qq.com

参考用书：《第一行代码》（Android）、《疯狂Android讲义》、《Android4编程入门经典》

*本答案仅供参考，如有错误，请您指正*


---
**1、请介绍几种Android常用的数据存储方式**

Android中数据存储的方式总共有5种：文件存储方式、使用SharedPreferences进行数据存储、SQLite数据库存储数据、ContentProvider存储数据、网络存储。

（1）文件数据存储：在Android中读取、写入文件的方法，与Java中实现I/O的程序是完全一样的，提供了openFileInput()、openFileOutput()来读取与写入设备中的文件。

（2）使用SharedPreferences进行数据存储：Android用来存储一些简单配置信息的一种机制，采用XML格式将数据存储到设备中，通过getSharedPreferences()来获得SharedPreferences接口，再通过编辑器进行提交保存，通过getString()来读取文件中的内容；采用了Map数据结构来存储数据，已键值的方式进行存储。

（3）SQLite数据库存储数据：SQLite是Android自带的一种标准数据库，支持大部分标准SQL语句，是一种嵌入式的关系型数据库。通过SQLiteOpenHelper来获得数据库操作实例，从而进行增删改查以及分页来读取存储查询数据。

（4）ContentProvider存储数据：主要是用于对外共享数据，而且使其他应用能够访问ContentProvider中的数据，利用继承ContentProvider类来共享数据，使用ContentResolver操作ContentProvider中的数据。使用ContentProvider共享数据的好处是统一了数据访问方式。

（5）网络数据存储：通过网络上给我们提供的存储空间来上传（存储）、下载（获取）我们存储在网络空间中的数据信息。

**2、请简述如何将打开res aw目录中的数据库文件**

在Android中不能直接打开res aw目录中的数据库文件，而需要在程序第一次启动时将该文件复制到手机内存或SD卡的某个目录中，然后再打开该数据库文件。复制的基本方法是使用getResources().openRawResource方法获得res aw目录中资源的 InputStream对象，然后将该InputStream对象中的数据写入其他的目录中相应文件中。在Android SDK中可以使用SQLiteDatabase.openOrCreateDatabase方法来打开任 意目录中的SQLite数据库文件。

**3、请简述Android开发过程中，SQLiteOpenHelper类的使用方法**

SQLiteOpenHelper是一个辅助类来管理数据库的创建和版本。可以通过继承这个类，实现它的一些方法来对数据库进行一些操作。所有继承了这个类的类都必须实现下面这样的一个构造方法： 

public DatabaseHelper(Context context, String name, CursorFactory factory, int version){
    ……
} 
第一个参数：Context类型，上下文对象。 
第二个参数：String类型，数据库的名称 
第三个参数：CursorFactory类型 
第四个参数：int类型，数据库版本 
下面是这个类的几个方法：
方法名	返回值类型	描述	备注
getReadableDatabase()	synchronized SQLiteDatabase	创建或打开一个数据库	可以通过这两个方法返回的SQLiteDatabase对象对数据库进行一系列的操作，如新建一个表，插入一条数据等
getWritableDatabase()	synchronized SQLiteDatabase	创建或打开一个可以读写的数据库	
onCreate(SQLiteDatabase db)	abstract void	第一次创建的时候调用	
onOpen(SQLiteDatabase db)	void	打开数据库	
onUpgrade(SQLiteDatabase db,int oldVersion,int newVersion)	abstract void	升级数据库	
close()	synchronized void	关闭所有打开的数据库对象	

**4、assest文件夹里放文件，对于文件的大小有没有限制**

assets目录更像一个附录类型的目录，Android不会为这个目录中的文件生成ID并保存在R类当中，因此它与Android中的一些类和方法兼容度更低。同时，由于你需要一个字符串路径来获取这个目录下的文件描述符，访问的速度会更慢。但是把一些文件放在这个目录下会使一些操作更加方便，比方说拷贝一个数据库文件到系统内存中。要注意的是，你无法在Android XML文件中引用到assets目录下的文件，只能通过AssetManager来访问这些文件。数据库文件和游戏数据等放在这个目录下是比较合适的。另外，网上关于assets和raw的资料都千篇一律了，因此关于这两者中单个文件大小不能超过1M的**错误**描述也在传播，即如果读取超过1M的文件会报"Data exceeds UNCOMPRESS_DATA_MAX (1314625 vs 1048576)"的IOException，还引申出种种解决方案。个人认为不应该有这样的限制，为了验证这个说法写了个Demo，发现将近5M的压缩包在assets和raw中都能正常访问，因此在这里纠正一下，理论上只要打包不超过Android APK 50M大小的限制都是没有问题的。当然了，不排除是Android很早期的时候因为设备硬件原因aapt在编译的时候对这两个文件夹大小做出了限制，如果是这样，较新版的ADT应该不会出现这。

来自：http://my.eoe.cn/futurexiong/archive/5350.html


**5、对于一个已经存在的SharedPreferences对象setting,想向其中存入一个字符串"person",setting应该先调用什么方法.**

调用edit()方法
//实例化SharedPreferences对象（第一步） 
SharedPreferences mySharedPreferences= getSharedPreferences("test", 
Activity.MODE_PRIVATE); 
//实例化SharedPreferences.Editor对象（第二步）(android.content.SharedPreferences.Editor)
SharedPreferences.Editor editor = mySharedPreferences.edit(); 
//用putString的方法保存数据 
editor.putString("name", "Karl"); 
editor.putString("habit", "sleep"); 
//提交当前数据 
editor.commit();

6、SharedPreferences保存文件的路径和扩展名是  /data/data/package name/shared_prefs/ *.xml    

**7、Android用什么充当数据库来存取数据？**
SQLite

**8、要想使用SharePreferences来存储数据，首先需要获取到SharePreferences对象。Android中主要提供三种方法用于得到SharePreferences对象，请简述这三种方法。**

要想使用SharedPreferences来存储数据，首先需要获取到SharedPreferences对象。Android中主要提供了三种方法用于得到SharedPreferences对象。
1. Context类中的getSharedPreferences()方法
此方法接收两个参数，第一个参数用于指定SharedPreferences文件的名称，如果指定的文件不存在则会创建一个，SharedPreferences文件都是存放在/data/data/<package name>/shared_prefs/目录下的。第二个参数用于指定操作模式，主要有两种模式可以选择，MODE_PRIVATE和MODE_MULTI_PROCESS。MODE_PRIVATE仍然是默认的操作模式，和直接传入0效果是相同的，表示只有当前的应用程序才可以对这个SharedPreferences文件进行读写。MODE_MULTI_PROCESS则一般是用于会有多个进程中对同一个SharedPreferences文件进行读写的情况。类似地，MODE_WORLD_READABLE和MODE_WORLD_WRITEABLE这两种模式已在Android 4.2版本中被废弃。
2. Activity类中的getPreferences()方法
这个方法和Context中的getSharedPreferences()方法很相似，不过它只接收一个操作模式参数，因为使用这个方法时会自动将当前活动的类名作为SharedPreferences的文件名。
3. PreferenceManager类中的getDefaultSharedPreferences()方法
这是一个静态方法，它接收一个Context参数，并自动使用当前应用程序的包名作为前缀来命名SharedPreferences文件。
得到了SharedPreferences对象之后，就可以开始向SharedPreferences文件中存储数据了，主要可以分为三步实现。
1. 调用SharedPreferences对象的edit()方法来获取一个SharedPreferences.Editor对象。
2. 向SharedPreferences.Editor对象中添加数据，比如添加一个布尔型数据就使用putBoolean方法，添加一个字符串则使用putString()方法，以此类推。
3. 调用commit()方法将添加的数据提交，从而完成数据存储操作。

9、SQLiteDataase中提供了一个query()方法用于对数据进行查询。这个方法最少需要几个参数，分别说明这几个参数的含义。
这个方法的参数非常复杂，最短的一个方法重载也需要传入七个参数。
第一个参数指表名，表示我们希望从哪张表中查询数据。第二个参数用于指定去查询哪几列，如果不指定则默认查询所有列。第三、第四个参数用于去约束查询某一行或某几行的数据，不指定则默认是查询所有行的数据。第五个参数用于指定需要去group by的列，不指定则表示不对查询结果进行group by操作。第六个参数用于对group by之后的数据进行进一步的过滤，不指定则表示不进行过滤。第七个参数用于指定查询结果的排序方式，不指定则表示使用默认的排序方式。详情请查看下表：
query()方法参数 	对应SQL部分 	描述 
table 	from table_name 	指定查询的表名 
columns 	select column1, column2 	指定查询的列名 
selection 	where column = value 	指定where的约束条件 
selectionArgs 	- 	为where中的占位符提供具体的值 
groupBy 	group by column 	指定需要group by的列 
having 	having column = value 	对group by后的结果进一步约束 
orderBy 	order by column1, column2 	指定查询结果的排序方式 

10、android中使用SQLiteOpenHelper这个辅助类时，可以生成一个数据库，并可以对数据库版本进行管理的方法可以是什么？（腾讯2015春招移动客户端开发练习卷）
方法为：getWriteableDatabase()、getReadableDatabase()

