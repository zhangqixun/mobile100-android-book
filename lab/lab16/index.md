# Sqllite

起草人: 黄志达   日期：15年11月29日

修改完善：朱思敏   日期：15年00月00日

# 

**一、实验目的**

*学习Sqllite的使用，了解安卓的数据库*

**二、基础知识**

*了解sqlite的基本概念和使用sqlite*
   
* sqlite的基本概念：

      sqlite是Android内置的轻量级关系型数据库，占用资源少，大概只需几百K内存，
      特别适合在嵌入式系统中使用，支持标准的SQL语法，支持事务机制，
      Android专门提供了SQLiteOpenHelper类，对数据库进行操作

* SqliteOpenHelper类：

      SqliteOpenHelper类是一个用于创建和打开数据库的辅助类
      SqliteOpenHelper类是一个抽象类，包含两个重要的方法：
      --onCreate（SQLiteDatabase db)
      --onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion)
      构造出实例后，再调用getWritableDatabase()或getReadableDatabase方法
      通过该方法创建的数据库存放的目录/data/data/packageName/databases/


* Context类openOrCreateDatabase()方法：

       openOrCreateDatabase(String dbName, int mode, CursorFactory factory); 
       --dbName：数据库名称。 --mode：数据库操作模式。默认值为MODE_PRIVATE 
       -- factory：附加的一个工厂类，当SQLiteDatabase实例的quer y函数被调用时，会使用工厂类返回一个Cursor。可 为null。 
       通过该方法创建的数据库存放的目录 /data/data/packageName/databases/ 

* SQLiteDatabase类的静态方法openOrCreateDatabases()方法：
        openOrCreateDatabase(String path,     CursorFactory factory) 
        openOrCreateDatabase(File file,     CursorFactory factory) 
        openOrCreateDatabase(String path,    CursorFactory factory,    DatabaseErrorHandler errorHandler) 该方法打开或创建的数据库文件可以 与前两个方法的路径不同。 

* Cursor：
        Cursor是每行数据的集合
        使用moveToFirst()定位第一行
        必须知道每一列的名称
        必须知道每一列的数据类型
        Cursor是一个随机的数据源
        所有数据都通过下标取得
        

**三、实验内容及步骤**

**3.1 实验内容**

*简要介绍本次实验的主要内容*

**3.2 实验步骤**

*详细描述本次实验的具体步骤*

**四、常见问题及注意事项**

*详细描述本此实验的可能会遇到的问题以及相关的注意事项*


