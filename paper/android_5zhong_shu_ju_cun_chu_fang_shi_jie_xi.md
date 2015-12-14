# Android 5种数据存储方式解析（1501210903 宫翠峰）



#  **目录**


前言

一、SharedPreferences存储数据

二.文件存储数据

三.SQLite数据库存储数据

四ContentProvider存储数据

五.网络存储数据

六. 小结

前言
---

作为一个完整的应用程序，数据存储操作是必不可少的。Android 提供了5种方式存储数据：使用SharedPreferences存储数据；文件存储数据；SQLite数据库存储数据；使用ContentProvider存储数据；网络存储数据。 无论是什么平台，什么开发环境，什么软件程序，数据都是核心。对于开发平台来讲，如果对数据的存储有良好的支持，那么对应用程序的开发将会有很大的促进作用。因此我们很有必要仔细研究下这5种数据存储方式


### 一、SharedPreferences存储数据



1. 
适用范围：保存少量的数据，且这些数据的格式非常简单：字符串型、基本类型的值。比如应用程序的各种配置信息（如是否打开音效、是否使用震动效果、小游戏的玩家积分等），解锁口 令密码等
* 
核心原理：保存基于XML文件存储的key-value键值对数据，通常用来存储一些简单的配置信息。通过DDMS的File Explorer面板，展开文件浏览树,很明显SharedPreferences数据总是存储在/data/data//shared_prefs目录下。SharedPreferences对象本身只能获取数据而不支持存储和修改,存储修改是通过SharedPreferences.edit()获取的内部接口Editor对象实现。 SharedPreferences本身是一 个接口，程序无法直接创建SharedPreferences实例，只能通过Context提供的getSharedPreferences(String name, int mode)方法来获取SharedPreferences实例，该方法中name表示要操作的xml文件名，第二个参数具体如下：Context.MODE_PRIVATE: 指定该SharedPreferences数据只能被本应用程序读、写；Context.MODE_WORLD_READABLE: 指定该SharedPreferences数据能被其他应用程序读，但不能写；Context.MODE_WORLD_WRITEABLE: 指定该SharedPreferences数据能被其他应用程序读，写。
* 
实现SharedPreferences存储的步骤如下：

  （一）、根据Context获取SharedPreferences对象
　　
　　
（二）、利用edit()方法获取Editor对象。

　　
（三）、通过Editor对象存储key-value键值对数据。

　　
（四）、通过commit()方法提交数据。

 4.下面是用SharedPreferences存储数据的一个案例：
 `
 
 void ReadSharedPreferences()

{
String strName,strPassword;

SharedPreferences user = getSharedPreferences(“user_info”,0);
strName = user.getString(“NAME”,””);

strPassword = user getString(“PASSWORD”,””);

}

void WriteSharedPreferences(String strName,String strPassword)

{
SharedPreferences user = getSharedPreferences(“user_info”,0);
uer.edit();

user.putString(“NAME”, strName);

user.putString(“PASSWORD” ,strPassword);

user.commit();

}``

`
以上面的数据存储结果为例，打开后可以看到一个user_info.xml的文件，打开后可以看到：

<?xml version=”1.0″ encoding=”UTF-8″?>
<map>

<string name=NAME>moandroid</string>


<string name=PASSWORD>SharedPreferences</string>

</map>

* 
使用SharedPreferences是有些限制的：只能在同一个包内使用，不能在不同的包之间使用。

* 
SharedPreferences存储免去了创建数据库，创建表，写SQL语句等诸多操作，相对而言更加方便，简洁。但是SharedPreferences也有其自身缺陷，比如其职能存储boolean，int，float，long和String五种简单的数据类型，比如其无法进行条件查询等。所以不论SharedPreferences的数据存储操作是如何简单，它也只能是存储方式的一种补充，而无法完全替代如SQLite数据库这样的其他数据存储方式。


#**二、文件存储数据**
  
* 
核心原理: Context提供了两个方法来打开数据文件里的文件IO流 FileInputStream openFileInput(String name); FileOutputStream(String name , int mode),这两个方法第一个参数用于指定文件名，第二个参数指定打开文件的模式。具体有以下值可选：
MODE_PRIVATE：为默认操作模式，代表该文件是私有数据，只能被应用本身访问，在该模式下，写入的内容会覆盖原文件的内容，如果想把新写入的内容追加到原文件中。可 以使用Context.MODE_APPEND
MODE_APPEND：模式会检查文件是否存在，存在就往文件追加内容，否则就创建新文件。
MODE_WORLD_READABLE：表示当前文件可以被其他应用读取；
MODE_WORLD_WRITEABLE：表示当前文件可以被其他应用写入。
除此之外，Context还提供了如下几个重要的方法：
etDir(String name , int mode):在应用程序的数据文件夹下获取或者创建name对应的子目录
File getFilesDir():获取该应用程序的数据文件夹得绝对路径
String[] fileList():返回该应用数据文件夹的全部文件

代码示例：
public void save()
{

       try { 
           FileOutputStream outStream=this.openFileOutput("a.txt",Context.MODE_WORLD_READABLE); 
           outStream.write(text.getText().toString().getBytes()); 
           outStream.close(); 
           Toast.makeText(MyActivity.this,"Saved",Toast.LENGTH_LONG).show(); 
       } catch (FileNotFoundException e) { 
           return; 
       } 
       catch (IOException e){ 
           return ; 
       } 





---

openFileOutput()方法的第一参数用于指定文件名称，不能包含路径分隔符“/” ，如果文件不存在，Android 会自动创建它。
创建的文件保存在/data/data/<package name>/files目录，如： /data/data/cn.itcast.action/files/itcast.txt ，通过点击Eclipse菜单“Window”-“Show View”-“Other”，在对话窗口中展开android文件夹，选择下面的File Explorer视图，然后在File Explorer视图中展开/data/data/<package name>/files目录就可以看到该文件。
openFileOutput()方法的第二参数用于指定操作模式，有四种模式，分别为：
Context.MODE_PRIVATE = 0
Context.MODE_APPEND = 32768
Context.MODE_WORLD_READABLE = 1
Context.MODE_WORLD_WRITEABLE = 2
Context.MODE_PRIVATE：为默认操作模式，代表该文件是私有数据，只能被应用本身访问，在该模式下，写入的内容会覆盖原文件的内容，如果想把新写入的内容追加到原文件中。可以使用Context.MODE_APPEND
Context.MODE_APPEND：模式会检查文件是否存在，存在就往文件追加内容，否则就创建新文件。
Context.MODE_WORLD_READABLE和Context.MODE_WORLD_WRITEABLE用来控制其他应用是否有权限读写该文件。
MODE_WORLD_READABLE：表示当前文件可以被其他应用读取；
MODE_WORLD_WRITEABLE：表示当前文件可以被其他应用写入。
   如果希望文件被其他应用读和写，可以传入： openFileOutput(“itcast.txt”, Context.MODE_WORLD_READABLE + Context.MODE_WORLD_WRITEABLE); android有一套自己的安全模型，当应用程序(.apk)在安装时系统就会分配给他一个userid，当该应用要去访问其他资源比如文件的时候，就需要userid匹配。默认情况下，任何应用创建的文件，sharedpreferences，数据库都应该是私有的（位于/data/data/<package name>/files），其他程序无法访问。
   除非在创建时指定了Context.MODE_WORLD_READABLE或者Context.MODE_WORLD_WRITEABLE ，只有这样其他程序才能正确访问。
读取文件示例:

public void load() 

{ 

    try { 
        FileInputStream inStream=this.openFileInput("a.txt"); 
        ByteArrayOutputStream stream=new ByteArrayOutputStream(); 
        byte[] buffer=new byte[1024]; 
        int length=-1; 

    while((length=inStream.read(buffer))!=-1)   { 
            stream.write(buffer,0,length); 
        } 

        stream.close(); 
        inStream.close(); 
        text.setText(stream.toString()); 
        Toast.makeText(MyActivity.this,"Loaded",Toast.LENGTH_LONG).show(); 
    } catch (FileNotFoundException e) { 
        e.printStackTrace(); 
    } 
    catch (IOException e){ 
        return ; 
    } 

}

对于私有文件只能被创建该文件的应用访问，如果希望文件能被其他应用读和写，可以在创建文件时，指定Context.MODE_WORLD_READABLE和Context.MODE_WORLD_WRITEABLE权限。
    Activity还提供了getCacheDir()和getFilesDir()方法： getCacheDir()方法用于获取/data/data/<package name>/cache目录 getFilesDir()方法用于获取/data/data/<package name>/files目录。
把文件存入SDCard：
   使用Activity的openFileOutput()方法保存文件，文件是存放在手机空间上，一般手机的存储空间不是很大，存放些小文件还行，如果要存放像视频这样的大文件，是不可行的。对于像视频这样的大文件，我们可以把它存放在SDCard。
SDCard是干什么的？你可以把它看作是移动硬盘或U盘。 在模拟器中使用SDCard，你需要先创建一张SDCard卡（当然不是真的SDCard，只是镜像文件）。
创建SDCard可以在Eclipse创建模拟器时随同创建，也可以使用DOS命令进行创建，如下： 在Dos窗口中进入android SDK安装路径的tools目录，输入以下命令创建一张容量为2G的SDCard，文件后缀可以随便取，建议使用.img： mksdcard 2048M D:\AndroidTool\sdcard.img 在程序中访问SDCard，你需要申请访问SDCard的权限。
在AndroidManifest.xml中加入访问SDCard的权限如下:

<!-- 在SDCard中创建与删除文件权限 --> 
    <uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"/> 

    <!-- 往SDCard写入数据权限 --> 
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
   要往SDCard存放文件，程序必须先判断手机是否装有SDCard，并且可以进行读写。
注意：访问SDCard必须在AndroidManifest.xml中加入访问SDCard的权限。
 
if(Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED))
{  
    
    File sdCardDir = Environment.getExternalStorageDirectory();
    
    
    //获取SDCard目录          

    File saveFile = new File(sdCardDir, “a.txt”); 
        FileOutputStream outStream = new FileOutputStream(saveFile); 
        outStream.write("test".getBytes()); 
        outStream.close(); 

}

   Environment.getExternalStorageState()方法用于获取SDCard的状态，如果手机装有SDCard，并且可以进行读写，那么方法返回的状态等于Environment.MEDIA_MOUNTED。
   Environment.getExternalStorageDirectory()方法用于获取SDCard的目录，当然要获取SDCard的目录，你也可以这样写：
File sdCardDir = new File("/sdcard"); //获取SDCard目录  

    File saveFile = new File(sdCardDir, "itcast.txt");  

    //上面两句代码可以合成一句：  

    File saveFile = new File("/sdcard/a.txt");  

    FileOutputStream outStream = new FileOutputStream(saveFile);  

    outStream.write("test".getBytes());  

    outStream.close();

# **三、SQLite数据库存储数据 **

* 
SQLite Database数据库。Android对数据库的支持很好，它本身集成了SQLite数据库，每个应用都可以方便的使用它，或者更确切的说，Android完全依赖于SQLite数据库，它所有的系统数据和用到的结构化数据都存储在数据库中。 它具有以下优点： a. 效率出众，这是无可否认的 b. 十分适合存储结构化数据 c. 方便在不同的Activity，甚至不同的应用之间传递数据。

   *下面就向大家介绍一下SQLite常用的操作方法，为了方便，我将代码写在了Activity的onCreate中：

.protected void onCreate(Bundle savedInstanceState) {  
3.        super.onCreate(savedInstanceState);  
4.          
5.        //打开或创建test.db数据库  
6.        SQLiteDatabase db = openOrCreateDatabase("test.db", Context.MODE_PRIVATE, null);  
7.        db.execSQL("DROP TABLE IF EXISTS person");  
8.        //创建person表  
9.        db.execSQL("CREATE TABLE person (_id INTEGER PRIMARY KEY AUTOINCREMENT, name VARCHAR, age SMALLINT)");  
10.        Person person = new Person();  
11.        person.name = "john";  
12.        person.age = 30;  
13.        //插入数据  
14.        db.execSQL("INSERT INTO person VALUES (NULL, ?, ?)", new Object[]{person.name, person.age});  
15.          
16.        person.name = "david";  
17.        person.age = 33;  
18.        //ContentValues以键值对的形式存放数据  
19.        ContentValues cv = new ContentValues();  
20.        cv.put("name", person.name);  
21.        cv.put("age", person.age);  
22.        //插入ContentValues中的数据  
23.        db.insert("person", null, cv);  
24.          
25.        cv = new ContentValues();  
26.        cv.put("age", 35);  
27.        //更新数据  
28.        db.update("person", cv, "name = ?", new String[]{"john"});  
29.          
30.        Cursor c = db.rawQuery("SELECT * FROM person WHERE age >= ?", new String[]{"33"});  
31.        while (c.moveToNext()) {  
32.            int _id = c.getInt(c.getColumnIndex("_id"));  
33.            String name = c.getString(c.getColumnIndex("name"));  
34.            int age = c.getInt(c.getColumnIndex("age"));  
35.            Log.i("db", "_id=>" + _id + ", name=>" + name + ", age=>" + age);  
36.        }  
37.        c.close();  
38.          
39.        //删除数据  
40.        db.delete("person", "age < ?", new String[]{"35"});  
41.          
42.        //关闭当前数据库  
43.        db.close();  
44.          
45.        //删除test.db数据库  
46.//      deleteDatabase("test.db");  
47.    }  
48.    

在执行完上面的代码后，系统就会在/data/data/[PACKAGE_NAME]/databases目录下生成一个“test.db”的数据库文件　。
   
上面的代码中基本上囊括了大部分的数据库操作；对于添加、更新和删除来说，我们都可以使用。

1.db.executeSQL(String sql);  
2.db.executeSQL(String sql, Object[] bindArgs);//sql语句中使用占位符，然后第二个参数是实际的参数集  
除了统一的形式之外，他们还有各自的操作方法：

1.db.insert(String table, String nullColumnHack, ContentValues values);  
2.db.update(String table, Contentvalues values, String whereClause, String whereArgs);  

* 
以上三个方法的第一个参数都是表示要操作的表名；insert中的第二个参数表示如果插入的数据每一列都为空的话，需要指定此行中某一列的名称，系统将此列设置为NULL，不至于出现错误；insert中的第三个参数是ContentValues类型的变量，是键值对组成的Map，key代表列名，value代表该列要插入的值；update的第二个参数也很类似，只不过它是更新该字段key为最新的value值，第三个参数whereClause表示WHERE表达式，比如“age > ? and age < ?”等，最后的whereArgs参数是占位符的实际参数值；delete方法的参数也是一样。

下面来说说查询操作。查询操作相对于上面的几种操作要复杂些，因为我们经常要面对着各种各样的查询条件，所以系统也考虑到这种复杂性，为我们提供了较为丰富的查询形式：

1.db.rawQuery(String sql, String[] selectionArgs);  
2.db.query(String table, String[] columns, String selection, String[] selectionArgs, String groupBy, String having, String orderBy);  
3.db.query(String table, String[] columns, String selection, String[] selectionArgs, String groupBy, String having, String orderBy, String limit);  
4.db.query(String distinct, String table, String[] columns, String selection, String[] selectionArgs, String groupBy, String having, String orderBy, String limit);  
* 
上面几种都是常用的查询方法，第一种最为简单，将所有的SQL语句都组织到一个字符串中，使用占位符代替实际参数，selectionArgs就是占位符实际参数集；下面的几种参数都很类似，columns表示要查询的列所有名称集，selection表示WHERE之后的条件语句，可以使用占位符，groupBy指定分组的列名，having指定分组条件，配合groupBy使用，orderBy指定排序的列名，limit指定分页参数，distinct可以指定“true”或“false”表示要不要过滤重复值。需要注意的是，selection、groupBy、having、orderBy、limit这几个参数中不包括“WHERE”、“GROUP BY”、“HAVING”、“ORDER BY”、“LIMIT”等SQL关键字。
* 
最后，他们同时返回一个Cursor对象，代表数据集的游标，有点类似于JavaSE中的ResultSet。

下面是Cursor对象的常用方法：

1.c.move(int offset); //以当前位置为参考,移动到指定行  
2.c.moveToFirst();    //移动到第一行  
3.c.moveToLast();     //移动到最后一行  
4.c.moveToPosition(int position); //移动到指定行  
5.c.moveToPrevious(); //移动到前一行  
6.c.moveToNext();     //移动到下一行  
7.c.isFirst();        //是否指向第一条  
8.c.isLast();     //是否指向最后一条  
9.c.isBeforeFirst();  //是否指向第一条之前  
10.c.isAfterLast();    //是否指向最后一条之后  
11.c.isNull(int columnIndex);  //指定列是否为空(列基数为0)  
12.c.isClosed();       //游标是否已关闭  
13.c.getCount();       //总数据项数  
14.c.getPosition();    //返回当前游标所指向的行数  
15.c.getColumnIndex(String columnName);//返回某列名对应的列索引值  
16.c.getString(int columnIndex);   //返回当前行指定列的值  

* 
在上面的代码示例中，已经用到了这几个常用方法中的一些，关于更多的信息，大家可以参考官方文档中的说明。

 最后当我们完成了对数据库的操作后，记得调用SQLiteDatabase的close()方法释放数据库连接，否则容易出现SQLiteException。
 
   上面就是SQLite的基本应用，但在实际开发中，为了能够更好的管理和维护数据库，我们会封装一个继承自SQLiteOpenHelper类的数据库操作类，然后以这个类为基础，再封装我们的业务逻辑方法。
   
下面，我们就以一个实例来讲解具体的用法，我们新建一个项目，结构如下：


其中DBHelper继承了SQLiteOpenHelper，作为维护和管理数据库的基类，DBManager是建立在DBHelper之上，封装了常用的业务方法，Person是我们的person表对应的JavaBean，MainActivity就是我们显示的界面。

下面我们先来看一下DBHelper：

1.import android.content.Context;  
2.import android.database.sqlite.SQLiteDatabase;  
3.import android.database.sqlite.SQLiteOpenHelper;  
4.  
5.public class DBHelper extends SQLiteOpenHelper {  
6.  
7.    private static final String DATABASE_NAME = "test.db";  
8.    private static final int DATABASE_VERSION = 1;  
9.      
10.    public DBHelper(Context context) {  
11.        //CursorFactory设置为null,使用默认值  
12.        super(context, DATABASE_NAME, null, DATABASE_VERSION);  
13.    }  
14.  
15.    //数据库第一次被创建时onCreate会被调用  
16.    @Override  
17.    public void onCreate(SQLiteDatabase db) {  
18.        db.execSQL("CREATE TABLE IF NOT EXISTS person" +  
19.                "(_id INTEGER PRIMARY KEY AUTOINCREMENT, name VARCHAR, age INTEGER, info TEXT)");  
20.    }  
21.  
22.    //如果DATABASE_VERSION值被改为2,系统发现现有数据库版本不同,即会调用onUpgrade  
23.    @Override  
24.    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {  
25.        db.execSQL("ALTER TABLE person ADD COLUMN other STRING");  
26.    }  
27.}  

正如上面所述，数据库第一次创建时onCreate方法会被调用，我们可以执行创建表的语句，当系统发现版本变化之后，会调用onUpgrade方法，我们可以执行修改表结构等语句。

   
  为了方便我们面向对象的使用数据，我们建一个Person类，对应person表中的字段，如下：
  
  1.public class Person {  
2.    public int _id;  
3.    public String name;  
4.    public int age;  
5.    public String info;  
6.      
7.    public Person() {  
8.    }  
9.      
10.    public Person(String name, int age, String info) {  
11.        this.name = name;  
12.        this.age = age;  
13.        this.info = info;  
14.    }  
15.}  
1.public class Person {  
2.    public int _id;  
3.    public String name;  
4.    public int age;  
5.    public String info;  
6.      
7.    public Person() {  
8.    }  
9.      
10.    public Person(String name, int age, String info) {  
11.        this.name = name;  
12.        this.age = age;  
13.        this.info = info;  
14.    }  
15.}  
1.public class Person {  
2.    public int _id;  
3.    public String name;  
4.    public int age;  
5.    public String info;  
6.      
7.    public Person() {  
8.    }  
9.      
10.    public Person(String name, int age, String info) {  
11.        this.name = name;  
12.        this.age = age;  
13.        this.info = info;  
14.    }  
15.}  
然后，我们需要一个DBManager，来封装我们所有的业务方法，代码如下：
