# Android 5种数据存储方式解析（1501210903 宫翠峰）



#  **目录**


前言

一.SharedPreferences存储数据

二.文件存储数据

三.SQLite数据库存储数据

四ContentProvider存储数据

五.网络存储数据

六. 小结

前言
---

作为一个完整的应用程序，数据存储操作是必不可少的。Android 提供了5种方式存储数据：使用SharedPreferences存储数据；文件存储数据；SQLite数据库存储数据；使用ContentProvider存储数据；网络存储数据。无论是什么平台，什么开发环境，什么软件程序，数据都是核心。对于开发平台来讲，如果对数据的存储有良好的支持，那么对应用程序的开发将会有很大的促进作用。因此我们很有必要仔细研究下这5种数据存储方式


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

 . 下面是用SharedPreferences存储数据的一个案例：
 ```
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
}
 ```
 
`
以上面的数据存储结果为例，打开后可以看到一个user_info.xml的文件，打开后可以看到：

```
<?xml version=”1.0″ encoding=”UTF-8″?>
<map>
<string name=”NAME”>moandroid</string>
<string name=” PASSWORD”>SharedPreferences</string>
</map>
```
* 
使用SharedPreferences是有些限制的：只能在同一个包内使用，不能在不同的包之间使用。

* 
SharedPreferences存储免去了创建数据库，创建表，写SQL语句等诸多操作，相对而言更加方便，简洁。但是SharedPreferences也有其自身缺陷，比如其职能存储boolean，int，float，long和String五种简单的数据类型，比如其无法进行条件查询等。所以不论SharedPreferences的数据存储操作是如何简单，它也只能是存储方式的一种补充，而无法完全替代如SQLite数据库这样的其他数据存储方式。


 
###二、文件存储数据


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
```

public void save()
{
try
{ 
FileOutputStream outStream=this.openFileOutput("a.txt",context.MODE_WORLD_READABLE);
outStream.write(text.getText().toString().getBytes());
outStream.close();
Toast.makeText(MyActivity.this,"Saved",Toast.LENGTH_LONG).show();
}catch(FileNotFoundException e)
{return;
}catch(IOException e)
{return;
}
}



```
* 
openFileOutput()方法的第一参数用于指定文件名称，不能包含路径分隔符“/” ，如果文件不存在，Android 会自动创建它。
创建的文件保存在/data/data/<package name>/files目录，如： /data/data/cn.itcast.action/files/itcast.txt ，通过点击Eclipse菜单“Window”-“Show View”-“Other”，在对话窗口中展开android文件夹，选择下面的File Explorer视图，然后在File Explorer视图中展开/data/data/<package name>/files目录就可以看到该文件。
* 
openFileOutput()方法的第二参数用于指定操作模式，有四种模式，分别为：
Context.MODE_PRIVATE = 0
Context.MODE_APPEND = 32768
Context.MODE_WORLD_READABLE = 1
Context.MODE_WORLD_WRITEABLE = 2
* 
Context.MODE_PRIVATE：为默认操作模式，代表该文件是私有数据，只能被应用本身访问，在该模式下，写入的内容会覆盖原文件的内容，如果想把新写入的内容追加到原文件中。可以使用Context.MODE_APPEND
* 
Context.MODE_APPEND：模式会检查文件是否存在，存在就往文件追加内容，否则就创建新文件。
* 
Context.MODE_WORLD_READABLE和Context.MODE_WORLD_WRITEABLE用来控制其他应用是否有权限读写该文件。
* 
MODE_WORLD_READABLE：表示当前文件可以被其他应用读取；
* 
MODE_WORLD_WRITEABLE：表示当前文件可以被其他应用写入。
   如果希望文件被其他应用读和写，可以传入： openFileOutput(“itcast.txt”, Context.MODE_WORLD_READABLE + Context.MODE_WORLD_WRITEABLE); * 
* 
android有一套自己的安全模型，当应用程序(.apk)在安装时系统就会分配给他一个userid，当该应用要去访问其他资源比如文件的时候，就需要userid匹配。默认情况下，任何应用创建的文件，sharedpreferences，数据库都应该是私有的（位于/data/data/<package name>/files），其他程序无法访问。
   除非在创建时指定了Context.MODE_WORLD_READABLE或者Context.MODE_WORLD_WRITEABLE ，只有这样其他程序才能正确访问.
* 
读取文件示例:


```
public void load()
{
try{
FileInputStream inStream=this.openFileInput("a.txt");
ByteArrayOutputStream stream=new ByteArrayOutputStream();
byte[] buffer=new byte[1024];
int length=-1;
while((length=inStream.read(buffer))!=-1){
stream.write(buffer,0,length);
}
stream.close();
inStream.close();
text.setText(stream.toString());
Toast.makeText(MyActivity.this,"Loaded",Toast.LENGTH_LONG).show();
}catch(FileNotFoundException e){
e.printStackTrace();
}catch(IOException e){
return;
}
}
```

* 
对于私有文件只能被创建该文件的应用访问，如果希望文件能被其他应用读和写，可以在创建文件时，指定Context.MODE_WORLD_READABLE和Context.MODE_WORLD_WRITEABLE权限。
* 
Activity还提供了getCacheDir()和getFilesDir()方法： getCacheDir()方法用于获取/data/data/<package name>/cache目录 getFilesDir()方法用于获取/data/data/<package name>/files目录。
* 
把文件存入SDCard：
   使用Activity的openFileOutput()方法保存文件，文件是存放在手机空间上，一般手机的存储空间不是很大，存放些小文件还行，如果要存放像视频这样的大文件，是不可行的。对于像视频这样的大文件，我们可以把它存放在SDCard。
* 
SDCard是干什么的？你可以把它看作是移动硬盘或U盘。 在模拟器中使用SDCard，你需要先创建一张SDCard卡（当然不是真的SDCard，只是镜像文件）。
* 
创建SDCard可以在Eclipse创建模拟器时随同创建，也可以使用DOS命令进行创建，如下： 在Dos窗口中进入android SDK安装路径的tools目录，输入以下命令创建一张容量为2G的SDCard，文件后缀可以随便取，建议使用.img： mksdcard 2048M D:\AndroidTool\sdcard.img * 
* 
在程序中访问SDCard，你需要申请访问SDCard的权限。
在AndroidManifest.xml中加入访问SDCard的权限如下:

```
<!--在SDCard中创建与删除文件权限-->
<uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"/>
<!--往SDCard写入数据权限-->
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```
 要往SDCard存放文件，程序必须先判断手机是否装有SDCard，并且可以进行读写。
注意：访问SDCard必须在AndroidManifest.xml中加入访问SDCard的权限。
```
if(Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED)){
File sdCardDir = Environment.getExternalStorageDirectory();//获取SDCard目录
File saveFile = new File(sdCardDir,"a.txt");
FileOutputStream outStream = new FileOutputStream(saveFile);
outStream.write("test".getBytes());
outStream.close();
}
```

* 
Environment.getExternalStorageState()方法用于获取SDCard的状态，如果手机装有SDCard，并且可以进行读写，那么方法返回的状态等于Environment.MEDIA_MOUNTED。
* 
Environment.getExternalStorageDirectory()方法用于获取SDCard的目录，当然要获取SDCard的目录，你也可以这样写：
```
File sdCardDir = new File("/sdcard");//获取SDCard目录
File saveFile = new File(sdCardDir,"itcast.txt");
//上面两句代码可以合成一句：
File saveFile = new File("/sdcard/a.txt");
FileOutputStream outStream = new FileOutputStream(saveFile);
outStream.write("test".getBytes());
outStream.close();
```

# **三、SQLite数据库存储数据 **

* 
SQLite Database数据库。Android对数据库的支持很好，它本身集成了SQLite数据库，每个应用都可以方便的使用它，或者更确切的说，Android完全依赖于SQLite数据库，它所有的系统数据和用到的结构化数据都存储在数据库中。 它具有以下优点： a. 效率出众，这是无可否认的 b. 十分适合存储结构化数据 c. 方便在不同的Activity，甚至不同的应用之间传递数据。

* 
下面就向大家介绍一下SQLite常用的操作方法，为了方便，我将代码写在了Activity的onCreate中：

```
protected void onCreate(Bundle savedInstanceState){
super.onCreate(savedInstanceState);
//打开或创建test.ddb数据库
SQLiteDatabase db = openOrCreateDatabase(“test.db”,Context.MODE_PRIVATE,null);
db.execSQL(“DROP TABLE IF EXISTS person”);
//创建person表
db.execSQL(“CREATE TABLE person(_id INTEGER PRIMARY KEY AUTOINCREMENT,name VARCHAR,age SMALLINT)”);
Person person = new Person();
person.name = “john”;
person.age = 30;
//插入数据
db.execSQL(“INSERT INTO person VALUES(NULL,?,?)”,new Object[]{
person.name,person.age});
person.name = “david”;
person.age = 33;
//ContentValues以键值对的形式存放数据
ContentValues cv = new ContentValues();
cv.put(“name”,person.name);
cv.put(“age”,person.age);
//插入ContentValues中的数据
db.insert(“person”,null,cv);
cv = new ContentValues();
cv.put(“age”,35);
//更新数据
db.update(“person”,cv,”name=?”,new String[]{“john”});
Cursor c = db.rawQuery(“SELECT * FROM person WHERE age >=?”,new String[]{“33”});
while(c.moveToNext()){
int _id = c.getInt(c.getColumnIndex(“_id”));
String name = c.getString(c.getColumnIndex(“name”));
int age = c.getInt(c.getColumnIndex(“age”));
Log.i(“db”,”_id=>”+_id+”,name=>”+name+”,age=>”+age);
}
c.close();
//删除数据
db.delete(“person”,”age<?”,new String[]{“35”});
//关闭当前数据库
db.close();
//删除test.db数据库
//deleteDatabase(“test.db”);
}
```

在执行完上面的代码后，系统就会在/data/data/[PACKAGE_NAME]/databases目录下生成一个“test.db”的数据库文件　。
   
上面的代码中基本上囊括了大部分的数据库操作；对于添加、更新和删除来说，我们都可以使用。

```
db.executeSQL(String sql);
db.executeSQL(String sql,Object[] bindArgs);//sql语句中使用占位符，然后第二个参数是实际的参数集
```
除了统一的形式之外，他们还有各自的操作方法：

```
db.insert(String table,String nullColumnHack,ContentValues values);
db.update(String table,Contentvalues values,String whereClause,String whereArgs);
```
* 
以上三个方法的第一个参数都是表示要操作的表名；insert中的第二个参数表示如果插入的数据每一列都为空的话，需要指定此行中某一列的名称，系统将此列设置为NULL，不至于出现错误；insert中的第三个参数是ContentValues类型的变量，是键值对组成的Map，key代表列名，value代表该列要插入的值；update的第二个参数也很类似，只不过它是更新该字段key为最新的value值，第三个参数whereClause表示WHERE表达式，比如“age > ? and age < ?”等，最后的whereArgs参数是占位符的实际参数值；delete方法的参数也是一样。

下面来说说查询操作。查询操作相对于上面的几种操作要复杂些，因为我们经常要面对着各种各样的查询条件，所以系统也考虑到这种复杂性，为我们提供了较为丰富的查询形式：

```
db.rawQuery(String sql,String[] selectionArgs);
db.query(String table,String[] columns,String selection,String[] selectionArgs,String groupBy,String having,String orderBy);
db.query(String table,String[] columns,String selection,String[] selectionArgs,String groupBy,String having,String orderBy,String limit);
db.query(String distinct,String table,String[] columns,String selection,String[] selectionArgs,String groupBy,String having,String orderBy,String limit);

```
* 
上面几种都是常用的查询方法，第一种最为简单，将所有的SQL语句都组织到一个字符串中，使用占位符代替实际参数，selectionArgs就是占位符实际参数集；下面的几种参数都很类似，columns表示要查询的列所有名称集，selection表示WHERE之后的条件语句，可以使用占位符，groupBy指定分组的列名，having指定分组条件，配合groupBy使用，orderBy指定排序的列名，limit指定分页参数，distinct可以指定“true”或“false”表示要不要过滤重复值。
* 
需要注意的是，selection、groupBy、having、orderBy、limit这几个参数中不包括“WHERE”、“GROUP BY”、“HAVING”、“ORDER BY”、“LIMIT”等SQL关键字。
* 
最后，他们同时返回一个Cursor对象，代表数据集的游标，有点类似于JavaSE中的ResultSet。

下面是Cursor对象的常用方法：

```
c.move(int offset);//以当前位置为参考，移动到指定行
c.moveToFirst();//移动到第一行
c.moveToLast();//移动到最后一行
c.moveToPosition(int position);//移动到指定行
c.moveToPrevious();//移动到前一行
c.moveToNext();//移动到下一行
c.isFirst();//是否指向第一条
c.isLast();//是否指向最后一条
c.isBeforeFirst();//是否指向第一条之前
c.isAfterLast();//是否指向最后一条之后
c.isNull(int columnIndex);//指定列是否为空（列基数为0）
c.isClosed();//游标是否已关闭
c.getCount();//总数据项数
c.getPosition();//返回当前游标所指向的行数
c.getColumnIndex(String columnName);//返回某列名对应的列索引值
c.getString(int columnIndex);//返回当前行指定列的值

```
* 
在上面的代码示例中，已经用到了这几个常用方法中的一些，关于更多的信息，大家可以参考官方文档中的说明。

 最后当我们完成了对数据库的操作后，记得调用SQLiteDatabase的close()方法释放数据库连接，否则容易出现SQLiteException。
 
   上面就是SQLite的基本应用，但在实际开发中，为了能够更好的管理和维护数据库，我们会封装一个继承自SQLiteOpenHelper类的数据库操作类，然后以这个类为基础，再封装我们的业务逻辑方法。
   
下面，我们就以一个实例来讲解具体的用法，我们新建一个项目，


其中DBHelper继承了SQLiteOpenHelper，作为维护和管理数据库的基类，DBManager是建立在DBHelper之上，封装了常用的业务方法，Person是我们的person表对应的JavaBean，MainActivity就是我们显示的界面。

下面我们先来看一下DBHelper：

```
import android.content.Context;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;
public class DBHelper extends SQLiteOpenHelper{
private static final String DATABASE_NAME = "test.db";
private static final int DATABASE_VERSION=1;
public DBHelper(Context context){
//CursorFactory设置为null，使用默认值
super(context,DATABASE_NAME,null,DATABASE_VERSION);
}
//数据库第一次被创建时onCreate会被调用
public void onCreate(SQLiteDatabase db){
db.execSQL("CREATE TABLE IF NOT EXISTS person"+"(_id INTEGER PRIMARY KEY AUTOINCREMENT,name VARCHAR,age INTEGER, info TEXT)");
}
//如果DATABASE_VERSION值被改为2，系统发现现有数据库版本不同，即会调用 onUpgrade
public void onUpgrade(SQLiteDatabase db,int oldVersion,int newVersion)
{
db.execSQL("ALTER TABLE person ADD COLUMN other STRING");
}
}
```

正如上面所述，数据库第一次创建时onCreate方法会被调用，我们可以执行创建表的语句，当系统发现版本变化之后，会调用onUpgrade方法，我们可以执行修改表结构等语句。

   
  为了方便我们面向对象的使用数据，我们建一个Person类，对应person表中的字段，如下：
  
```
public class Person{
public int _id;
public String name;
public int age;
public String info;
public Person(){
}
public Person(String name,int age,String info){
this.name=name;
this.age=age;
this.info=info;
}
}
```
然后，我们需要一个DBManager，来封装我们所有的业务方法，代码如下：
```
import java.util.ArrayList;
import java.util.List;
import android.content.ContentValues;
import android.content.Context;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
public class DBManager{
private DBHelper helper;
private SQLiteDatabase db;
public DBManager(Context context){
helper=new DBHelper(context);//因为getWritableDatabase内部调用了mContext.openOrCreateDatabase(mName,0,mFactory);//所以要确保context已初始化，我们可以把实例化DBManager的步骤放在Activity的onCreate里
db=helper.getWritableDatabase();
}
public void add(List<Person>person){
db.beginTransaction();//开始事务
try{
for(Person person:person){
db.execSQL("INSERT INTO person VALUES(null,?,?,?)",new Object[]{person.name,person.age,person.info});
}
db.setTransactionSuccessful();//设置事务成功完成
}finally{
db.endTransaction();//结束事务
}
}
public void updateAge(Person person){
ContentValues cv=new ContentValues();
cv.put("age",person.age);
db.update("person",cv,"name=?",new String[]{person.name});
}
public void deleteOldPerson(Person person){
db.delete("person","age>=?",new String[]{String.valueOf(person.age)});
}
public List<Person>query(){
ArrayList<Person> persons=new ArrayList<Person>();
Cursor c = queryTheCursor();
while(c.moveToNext()){
Person person = new Person();
Person._id=c.getInt(c.getColumnIndex("_id"));
person.name=c.getString(c.getColumnIndex("name"));
person.age=c.getInt(c.getColumnIndex("age"));
person.info=c.getString(c.getColumnIndex("info"));
person.add(person);
}
c.close();
return person;
}
public Cursor queryTheCursor(){
Cursor c=db.rawQuery("SELECT * FROM person",null);
return c;
}
public void closeDB(){
db.close();
}
}
```
* 
我们在DBManager构造方法中实例化DBHelper并获取一个SQLiteDatabase对象，作为整个应用的数据库实例；在添加多个Person信息时，我们采用了事务处理，确保数据完整性；最后我们提供了一个closeDB方法，释放数据库资源，这一个步骤在我们整个应用关闭时执行，这个环节容易被忘记，所以朋友们要注意。

* 
我们获取数据库实例时使用了getWritableDatabase()方法，也许朋友们会有疑问，在getWritableDatabase()和getReadableDatabase()中，你为什么选择前者作为整个应用的数据库实例呢？在这里我想和大家着重分析一下这一点。

* 
我们来看一下SQLiteOpenHelper中的getReadableDatabase()方法：

```
public synchronized SQLiteDatabase getReadableDatabase(){
if(mDatabase!=null&&mDatabase.isOpen()){
//如果发现mDatabase不为空并且已经打开则直接返回
return mDatabase;
}
if(mIsInitializing){
//如果正在初始化则抛出异常
throw new IllegalStateException("getReadableDatabase called recursively");
}
//开始实例化数据库mDatabase
try{
//注意这里是调用了getWritableDatabase()方法
return getWritableDatabase();
}catch(SQLiteException e){
if(mName==null)
throw e;//Can't open a temp database read-only!
Log.e(TAG,"Couldn't open"+mName+"for writing(will try read-only):",e);
}
//如果无法以可读写模式打开数据库，则以只读方式打开
SQLiteDatabase db=null;
try{
mIsInitializing=true;
String path=mContext.getDatabasePath(mName).getPath();/*获取数据库路径以只读方式打开数据库*/
db=SQLiteDatabase.openDatabase(path,mFactory,SQLiteDatabase.OPEN_READONLY);
if(db.getVersion()!=mNewVersion){
throw new SQLiteException("Can't upgrade read-only database from version"+db.getVersion()+"to"+mNewVersion+":"+path);
}
onOpen(db);
Log.w(TAG,"Opened"+mName+"in read-only mode");
mDatabase=db;//为mDatabase指定新打开的数据库
return mDatabase;//返回打开的数据库
}finally{
mIsInitializing=false;
if(db!=null&&db!=mDatabase)
db.close();
}
}
```
 在getReadableDatabase()方法中，首先判断是否已存在数据库实例并且是打开状态，如果是，则直接返回该实例，否则试图获取一个可读写模式的数据库实例，如果遇到磁盘空间已满等情况获取失败的话，再以只读模式打开数据库，获取数据库实例并返回，然后为mDatabase赋值为最新打开的数据库实例。既然有可能调用到getWritableDatabase()方法，我们就要看一下了：

```
public synchronized SQLiteDatabase getWritableDatabase(){
if(mDatabase!=null&&mDatabase.isOpen()&&!mDatabase.isReadOnly()){
//如果mDatabase不为空已打开并且不是只读模式，则返回该实例
return mDatabase;
}
if(mIsInitializing){
throw new IllegalStateException("getWritableDatabase called recursively");
}
boolean success = false;
SQLiteDatabase db=null;
//如果mDatabase不为空则加锁阻止其他的操作
if(mDatabase!=null)
mDatabase.lock();
try{
mIsInitializing = true;
if(mName==null){
db=SQLiteDatabase.create(null);
}else{
//打开或创建数据库
db=mContext.openOrCreateDatabase(mName,0,mFactory);
}
//获取数据库版本（如果刚创建的数据库版本为0）
int version = db.getVersion();
//比较版本（我们代码中的版本mNewVersion 为1）
if(version!=mNewVersion){
db.beginTransaction();//开始事务
try{
if(version==0){
//执行我们的onCreate方法
onCreate(db);
}else{
//如果我们应用升级了mNewVersion为2，而原版本为1则执行onUpgrade方法
onUpgrade(db,version,mNewVersion);
}
db.setVersion(mNewVersion);//设置最新版本
db.setTransactionSuccessful();//设置事务成功
}finally{
db.endTransaction();//结束事务
}
}
onOpen(db);
success=true;
return db;//返回可读写模式的数据库实例
}finally{
mIsInitializing = false;
if(success){
//打开成功
if(mDatabase!=null){
//如果mDatabase有值则先关闭
try{
mDatabase.close();
}catch(Exception e){
}
mDatabase.unlock();//解锁
}
mDatabase=db;//赋值给mDatabase
}else{
//打开失败的情况：解锁、关闭
if(mDatabase!=null)
mDatabase.unlock();
if(db!=null)
db.close();
}
}

```
* 
大家可以看到，几个关键步骤是，首先判断mDatabase如果不为空已打开并不是只读模式则直接返回，否则如果mDatabase不为空则加锁，然后开始打开或创建数据库，比较版本，根据版本号来调用相应的方法，为数据库设置新版本号，最后释放旧的不为空的mDatabase并解锁，把新打开的数据库实例赋予mDatabase，并返回最新实例。
 
* 
看完上面的过程之后，大家或许就清楚了许多，如果不是在遇到磁盘空间已满等情况，getReadableDatabase()一般都会返回和getWritableDatabase()一样的数据库实例，所以我们在DBManager构造方法中使用getWritableDatabase()获取整个应用所使用的数据库实例是可行的。当然如果你真的担心这种情况会发生，那么你可以先用getWritableDatabase()获取数据实例，如果遇到异常，再试图用getReadableDatabase()获取实例，当然这个时候你获取的实例只能读不能写了。

* 
最后，让我们看一下如何使用这些数据操作方法来显示数据，下面是MainActivity.java的布局文件和代码：

```
<?xml version="1.0" encoding="utf=8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:orientation="vertical"
android:layout_width="fill_parent"
android:layout_height="fill_parent">
<Button
android:layout_width="fill_parent"
android:layout_height="wrap_content"
android:text="add"
android:onClick="add"/>
<Button
android:layout_width="fill_parent"
android:layout_heigth="wrap_content"
android:text="update"
android:onClick="update"/>
<Button
android:layout_width="fill_parent"
android:layout_height="wrap_content"
android:text="delete"
android:onClick="delete"/>
<Button
android:layout_width="fill_parent"
android:layout_height="wrap_content"
android:text="query"
android:onClick="query"/>
<Button
android:layout_width="fill_parent"
android:layout_height="wrap_content"
android:text="queryTheCursor"
android:onClick="queryTheCursor"/>
<ListView
android:id="@+id/listView"
android:layout_width="fill_parent"
android:layout_height="wrap_content"/>
</LinearLayout>



```

```
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import android.app.Activity;
import android.database.Cursor;
import android.database.CursorWrapper;
import android.os.Bundle;
import android.view.View;
import android.widget.ListView;
import android.widget.SimpleAdapter;
import android.widget.SimpleCursorAdapter;
public class MainActivity extends Activity{
private DBManager mgr;
private ListView listView;
public void onCreate(Bundle saveInstanceState){
super.onCreate(savedInstanceState);
setContentView(R.layout.main);
listView=(ListView)findViewById(R.id.listView);
//初始化DBManager
mgr=new DBManager(this);
}
protected void onDestroy(){
super.onDestroy();
//应用的最后一个Activity关闭时应释放DB
mgr.closeDB();
}
public void add(View view){
ArrayList<Person> persons = new ArrayList<Person>();
Person person1=new Person("Ella",22,"lively girl");
Person person2=new Person("Jenny",22,"beautiful girl");

Person person3=new Person("Jessica",23,"sexy girl");

Person person4=new Person("Kelly",23,"hot baby");

Person person5=new Person("Jane",25,"a pretty woman");
persons.add(person1);
persons.add(person2);
persons.add(person3);
persons.add(person4);
persons.add(person5);
mgr.add(persons);
}
public void update(View view){
Person person=new Person();
person.name="Jane";
person.age=30;
mgr.updateAge(person);
}
public void delete(View view){
Person person = new Person();
person.age=30;
mgr.deleteOldPerson(person);
}
public void query(View view){
List<Person> persons=mgr.query();
ArrayList<Map<String,String>> list=new ArrayList<Map<String,String>>();
for(Person person:persons){
HashMap<String,String> map=new HashMap<String,String>();
map.put("name",person.name);
map.put("info",person.age+"years old,"+person.info);
list.add(map);
}
SmipleAdapter adapter = new SimpleAdapter(this,list,android.R.layout.simple_list_item_2,new String[]{"name","info"},new int[]{android.R.id.text1,android.R.id.text2});
listView.setAdapter(adapter);
}
public void queryTheCursor(View view){
Cursor c=mgr.queryTheCursor();
startManagingCursor(c);//托付给activity根据自己的生命周期去管理Cursor的生命周期
CursorWrapper cursorWrapper=new CursorWrapper(c){
public String getString(int columnIndex){
if(getColumnName(columnIndex).equals("info")){
int age=getInt(getColumnIndex("age"));
return age+"years old,"+super.getString(columnIndex);
}
return super.getString(columnIndex);
}
};
//确保查询结果中有“_id”列
SimpleCursorAdapter adapter=new SimpleCursorAdapter(this,android.R.layout.simple_list_item_2,cursorWrapper,new String[]{"name","info"},new int[]{andorid.R.id.text1,android.R.id.text2});
ListView listView=(ListView)findViewById(R.id.listView);
listView.setAdapter(adapter);
}
}



```


* 
这里需要注意的是SimpleCursorAdapter的应用，当我们使用这个适配器时，我们必须先得到一个Cursor对象，这里面有几个问题：如何管理Cursor的生命周期，如果包装Cursor，Cursor结果集都需要注意什么。

* 
如果手动去管理Cursor的话会非常的麻烦，还有一定的风险，处理不当的话运行期间就会出现异常，幸好Activity为我们提供了startManagingCursor(Cursor cursor)方法，它会根据Activity的生命周期去管理当前的Cursor对象，下面是该方法的说明：

```
/**
  *This method allows the activity to take care of managing the given
  *{@link Cursor}'s lifecycle for you based on the activity's lifecycle.
  *That is,when the activity is stopped it will automatically call
  *{@link Cursor#deactivate} on the given Cursor, and when it is later restarted
  *it will call {@link Cursor#requery} for you.When the activity is
  *destroyed,all managed Cursors will be closed automatically.
  *@param c The Cursor to be managed.
  *@see #managedQuery(android.net.Uri,String[],String,String[],String)
  *@see #stopManagingCursor
  */
```

* 
文中提到，startManagingCursor方法会根据Activity的生命周期去管理当前的Cursor对象的生命周期，就是说当Activity停止时他会自动调用Cursor的deactivate方法，禁用游标，当Activity重新回到屏幕时它会调用Cursor的requery方法再次查询，当Activity摧毁时，被管理的Cursor都会自动关闭释放。

* 
如何包装Cursor：我们会使用到CursorWrapper对象去包装我们的Cursor对象，实现我们需要的数据转换工作，这个CursorWrapper实际上是实现了Cursor接口。我们查询获取到的Cursor其实是Cursor的引用，而系统实际返回给我们的必然是Cursor接口的一个实现类的对象实例，我们用CursorWrapper包装这个实例，然后再使用SimpleCursorAdapter将结果显示到列表上。

* 
Cursor结果集需要注意些什么：一个最需要注意的是，在我们的结果集中必须要包含一个“_id”的列，否则SimpleCursorAdapter就会翻脸不认人，为什么一定要这样呢？因为这源于SQLite的规范，主键以“_id”为标准。解决办法有三：第一，建表时根据规范去做；第二，查询时用别名，例如：SELECT id AS _id FROM person；第三，在CursorWrapper里做文章：

```
CursorWrapper cursorWrapper = new CursorWrapper(c){
public int getColumnIndexOrThrow(String columnName) throws IllegalArgumentException{
if(columnName.equals("_id")){
return super.getColumnIndex("id");
}
return super.getColumnIndexOrThrow(columnName);
}
};
```

如果试图从CursorWrapper里获取“_id”对应的列索引，我们就返回查询结果里“id”对应的列索引即可。

**
# 四.使用ContentProvider存储数据

ContentProvider（内容提供者）是Android中的四大组件之一。主要用于对外共享数据，也就是通过ContentProvider把应用中的数据共享给其他应用访问，其他应用可以通过ContentProvider对指定应用中的数据进行操作。ContentProvider分为系统的和自定义的，系统的也就是例如联系人，图片等数据。

  总结：
  
1、ContentProvider为存储和读取数据提供了统一的接口

2、使用ContentProvider，应用程序可以实现数据共享

3、android内置的许多数据都是使用ContentProvider形式，供开发者调用的(如视频，音频，图片，通讯录等)

  Uri介绍：
  
1、每一个ContentProvider都拥有一个公共的Uri，这个Uri用于表示这个ContentProvider提供的数据

2、Android所提供的ContentProvider都存放在andriod.provider这个包里面
  Android的ContentProvider URI有固定的形式：content://contract/people
  
前缀：固定为content : //

认证：contract 资源的唯一标识符

路径：people 具体的资源类型

   在Android中广泛应用URI，而不是URL。URL标识资源的物理位置，相当于文件的路径；而URI则是标识资源的逻辑位置，并不提供资源的具体位置。比如说电话薄中的数据，如果用URL来标识的话，可能会是一个很复杂的文件结构，而且一旦文件的存储路径改变，URL也必须得改动。但是若是URI，则可以用诸如content://contract/people这样容易记录的逻辑地址来标识，而且并不需要关心文件的具体位置，即使文件位置改动也不需要做变化，当然这都是对于用户来说，后台程序中URI到具体位置的映射还是需要程序员来改动的。
   
   ContentProvider提供的函数：
   
1、query() 查询

2、insert() 插入

3、update() 更新

4、delete()　删除

5、getType() 得到数据类型

6、onCreate() 创建时的回调函数

实现ContentProvider的过程：

1、定义一个COTENT_URI常量

2、定义一个类，继承ContentProvider

3、实现query(),delete(),update(),insert(),onCreate(),getType()方法

4、在AndroidMainfest.xml中申明

Content Provider增删改查实例（通过listview显示操作过的数据）


### 1，创建person库，person表



```
import android.content.Context;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;
public class DBOpenHelper extends SQLiteOpenHelper{
private static final String DATABASE_NAME="person.db";//数据库名称
private static final int DATABASE_VERSION=1;//数据库版本
public DBOpenHelper(Context context){
super(context,DATABASE_NAME,null,DATABSE_VERSION);
}
public void onCreate(SQLiteDatabase db){
db.execSQL("CREATE TABLE person(_id integer primary key autoincrement,name varchar(20),age varchar(10))");
}
public void onUpgrage(SQLiteDatabse db,int oldVersion,int newVersion)
{
db.execSQL("DROP TABLE IF EXISTS person");
onCreate(db);
}
}
```

### 2，自定义Content Provider类，增删改查的方法


```

```

### 3，activity


[java] view plaincopy
  

2```.import android.app.Activity;  
3.import android.content.ContentResolver;  
4.import android.content.ContentValues;  
5.import android.database.Cursor;  
6.import android.net.Uri;  
7.import android.os.Bundle;  
8.import android.view.View;  
9.import android.view.View.OnClickListener;  
10.import android.widget.AdapterView;  
11.import android.widget.AdapterView.OnItemClickListener;  
12.import android.widget.Button;  
13.import android.widget.EditText;  
14.import android.widget.ListView;  
15.import android.widget.SimpleCursorAdapter;  
16.import android.widget.Toast;  
17.  
.public class ResolverDemoActivity extends Activity {    
19.    /** Called when the activity is first created. */    
20.    private SimpleCursorAdapter adapter;    
21.    private ListView listView;   
22.    private Button button_insert;  
23.    private Button button_query;  
24.    private Button button_query_one;  
25.    private EditText edittext_query;  
26.    private Button button_update_one;  
27.    private EditText edittext_update;  
28.    private Button button_update;  
29.    private Button button_delete;  
30.    private Button button_delete_one;  
31.    private EditText edittext_delete;  
32.      
33.      
34.      
35.    @SuppressWarnings("deprecation")  
36.    @Override    
37.    public void onCreate(Bundle savedInstanceState) {    
38.        super.onCreate(savedInstanceState);    
39.        setContentView(R.layout.main);    
40.            
41.        listView=(ListView) this.findViewById(R.id.listView);    
42.        ContentResolver contentResolver = getContentResolver();    
43.        Uri selectUri = Uri.parse("content://cn.com.karl.personProvider/person");    
44.        Cursor cursor=contentResolver.query(selectUri, null, null, null, null);    
45.        adapter = new SimpleCursorAdapter(this, R.layout.item, cursor,    
46.                    new String[]{"_id", "name", "age"}, new int[]{R.id.id, R.id.name, R.id.age});    
47.            listView.setAdapter(adapter);    
48.            listView.setOnItemClickListener(new OnItemClickListener() {    
49.                @Override    
50.                public void onItemClick(AdapterView<?> parent, View view, int position, long id) {    
51.                    ListView lView = (ListView)parent;    
52.                    Cursor data = (Cursor)lView.getItemAtPosition(position);    
53.                    int _id = data.getInt(data.getColumnIndex("_id"));    
54.                    Toast.makeText(ResolverDemoActivity.this, _id+"", 1).show();    
55.                }    
56.            });    
57.                
58.            button_insert = (Button) this.findViewById(R.id.insertbutton);    
59.            button_insert.setOnClickListener(new OnClickListener() {              
60.                @Override    
61.                public void onClick(View v) {    
62.                    ContentResolver contentResolver = getContentResolver();    
63.                    Uri insertUri = Uri.parse("content://cn.com.karl.personProvider/person");    
64.                    ContentValues values = new ContentValues();    
65.                    values.put("name", "wangkuifeng");    
66.                    values.put("age", 23);    
67.                    Uri uri = contentResolver.insert(insertUri, values);    
68.                    Toast.makeText(ResolverDemoActivity.this, "添加完成", 1).show();    
69.                }    
70.            });   
71.              
72.            button_query = (Button)this.findViewById(R.id.querybutton);  
73.            button_query.setOnClickListener(new OnClickListener() {  
74.                  
75.                @Override  
76.                public void onClick(View v) {  
77.                    // TODO Auto-generated method stub  
78.                        ContentResolver contentResolver = getContentResolver();    
79.                        Uri selectUri = Uri.parse("content://cn.com.karl.personProvider/person");    
80.                          
81.                        Cursor cursor=contentResolver.query(selectUri, null, null, null, null);  
82.                        SimpleCursorAdapter adapter= new SimpleCursorAdapter(ResolverDemoActivity.this, R.layout.item, cursor,    
83.                                new String[]{"_id", "name", "age"}, new int[]{R.id.id, R.id.name, R.id.age});    
84.                        listView.setAdapter(adapter);  
85.                          
86.                        Toast.makeText(ResolverDemoActivity.this, "查询完成", 1).show();   
87.                }  
88.            });  
89.            button_query_one = (Button)this.findViewById(R.id.queryonebutton);  
90.            edittext_query = (EditText)this.findViewById(R.id.queryone_et);  
91.            button_query_one.setOnClickListener(new OnClickListener() {  
92.                  
93.                @Override  
94.                public void onClick(View v) {  
95.                    // TODO Auto-generated method stub  
96.                    String num = edittext_query.getText().toString();  
97.                    ContentResolver contentResolver = getContentResolver();    
98.                    Uri selectUri = Uri.parse("content://cn.com.karl.personProvider/person"+"/"+num);  
99.                    Cursor cursor=contentResolver.query(selectUri, null, null, null, null);  
100.                    SimpleCursorAdapter adapter = new SimpleCursorAdapter(ResolverDemoActivity.this, R.layout.item, cursor,    
101.                            new String[]{"_id", "name", "age"}, new int[]{R.id.id, R.id.name, R.id.age});    
102.                    listView.setAdapter(adapter);  
103.                      
104.                    Toast.makeText(ResolverDemoActivity.this, "查询完成", 1).show();   
105.                }  
106.            });  
107.            edittext_update = (EditText)this.findViewById(R.id.update_et);  
108.            button_update_one = (Button)this.findViewById(R.id.updateonebutton);  
109.            button_update_one.setOnClickListener(new OnClickListener() {  
110.                  
111.                @Override  
112.                public void onClick(View v) {  
113.                    // TODO Auto-generated method stub  
114.                    String num = edittext_update.getText().toString();  
115.                    ContentResolver contentResolver = getContentResolver();    
116.                    Uri updateUri = Uri.parse("content://cn.com.karl.personProvider/person"+"/"+num);  
117.                    ContentValues values = new ContentValues();    
118.                    values.put("name", "superjunjin");    
119.                    values.put("age", 26);    
120.                    int count = contentResolver.update(updateUri, values, null, null);  
121.                     
122.                      
123.                    Toast.makeText(ResolverDemoActivity.this, "更新完成", 1).show();   
124.                }  
125.            });  
126.              
127.            button_update = (Button)this.findViewById(R.id.updatebutton);  
128.            button_update.setOnClickListener(new OnClickListener() {  
129.                  
130.                @Override  
131.                public void onClick(View v) {  
132.                    // TODO Auto-generated method stub  
133.                    ContentResolver contentResolver = getContentResolver();   
134.                    Uri updateUri = Uri.parse("content://cn.com.karl.personProvider/person");  
135.                    ContentValues values = new ContentValues();    
136.                    values.put("name", "superjunjin");    
137.                    values.put("age", 26);    
138.                    int count = contentResolver.update(updateUri, values, null, null);  
139.                     
140.                    Toast.makeText(ResolverDemoActivity.this, "更新完成", 1).show();   
141.                }  
142.            });  
143.              
144.            button_delete = (Button)this.findViewById(R.id.deletebutton);  
145.            button_delete.setOnClickListener(new OnClickListener() {  
146.                  
147.                @Override  
148.                public void onClick(View v) {  
149.                    // TODO Auto-generated method stub  
150.                    ContentResolver contentResolver = getContentResolver();   
151.                    Uri deleteUri = Uri.parse("content://cn.com.karl.personProvider/person");  
152.                    contentResolver.delete(deleteUri, null, null);  
153.                      
154.                    Toast.makeText(ResolverDemoActivity.this, "删除完成", 1).show();   
155.                }  
156.            });  
157.              
158.            edittext_delete = (EditText)this.findViewById(R.id.delete_et);  
159.            button_delete_one = (Button)this.findViewById(R.id.deleteonebutton);  
160.            button_delete_one.setOnClickListener(new OnClickListener() {  
161.                  
162.                @Override  
163.                public void onClick(View v) {  
164.                    // TODO Auto-generated method stub  
165.                    String num = edittext_delete.getText().toString();  
166.                    ContentResolver contentResolver = getContentResolver();    
167.                    Uri deleteUri = Uri.parse("content://cn.com.karl.personProvider/person"+"/"+num);  
168.                     
169.                    contentResolver.delete(deleteUri, null, null);  
170.                     
171.                      
172.                    Toast.makeText(ResolverDemoActivity.this, "删除完成", 1).show();  
173.                }  
174.            });  
175.              
176.    }    ```
.}    

# **五.网络存储数据**

前面介绍的几种存储都是将数据存储在本地设备上，除此之外，还有一种存储（获取）数据的方式，通过网络来实现数据的存储和获取。

   我们可以调用WebService返回的数据或是解析HTTP协议实现网络数据交互。
具体需要熟悉java.net.*，Android.net.*这两个包的内容，在这就不赘述了，请大家参阅相关文档。

   下面是一个通过地区名称查询该地区的天气预报，以POST发送的方式发送请求到webservicex.net站点，访问WebService.webservicex.net站点上提供查询天气预报的服务。
 
代码如下：

```import java.util.ArrayList;

import java.util.List;

import org.apache.http.HttpResponse;

import org.apache.http.NameValuePair;

import org.apache.http.client.entity.UrlEncodedFormEntity;

import org.apache.http.client.methods.HttpPost;

import org.apache.http.impl.client.DefaultHttpClient;

import org.apache.http.message.BasicNameValuePair;

import org.apache.http.protocol.HTTP;

import org.apache.http.util.EntityUtils;


import android.app.Activity;

import android.os.Bundle;


public class MyAndroidWeatherActivity extends Activity {
    //定义需要获取的内容来源地址
    private static final String SERVER_URL = 
        "http://www.webservicex.net/WeatherForecast.asmx/GetWeatherByPlaceName"; 
    
    
    /** Called when the activity is first created. */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        
        HttpPost request = new HttpPost(SERVER_URL); //根据内容来源地址创建一个Http请求
        // 添加一个变量 
        List<NameValuePair> params = new ArrayList<NameValuePair>(); 
        // 设置一个地区名称
        params.add(new BasicNameValuePair("PlaceName", "NewYork"));  //添加必须的参数
        
        
        try { 
            //设置参数的编码
            request.setEntity(new UrlEncodedFormEntity(params, HTTP.UTF_8)); 
            //发送请求并获取反馈
            HttpResponse httpResponse = new DefaultHttpClient().execute(request);
             
            // 解析返回的内容
            if(httpResponse.getStatusLine().getStatusCode() != 404){ 
               String result = EntityUtils.toString(httpResponse.getEntity()); 
               System.out.println(result);
            }
        } catch (Exception e) {
            e.printStackTrace();
       } 
    }
}
```
 
 
   别忘记了在配置文件中设置访问网络权限：
``` <uses-permission android:name="android.permission.INTERNET"/>```


#*六. 小结*



以上5中存储方式，在以后的开发过程中，根据设计目标、性能需求、空间需求等找到合适的数据存储方式。Android 中的数据存储都是私有的，其他应用程序都是无法访问的，除非通过ContentResolver获取其他程序共享的数据。采用文件方式对外共享数据，需要进行文件操作读写数据；采用sharedpreferences共享数据，需要使用sharedpreferences API读写数据。而使用ContentProvider共享数据的好处是统一了数据访问方式。

                     

