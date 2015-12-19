# android数据持久化存储和跨程序共享数据技术(陈曾华 1501210879）
                                            
                                            
                                            摘要
本文主要介绍在Android开发中实现数据持久化存储的三种方法：文件存储、SharedPreferences 存储和SQLite数据库存储技术以及如何在不同程序之间实现跨程序数据共享的技术，其中介绍了内容提供器的使用方法。
关键词：数据持久化  数据共享  内容提供器


#一、Android数据持久化存储
数据持久化就是指将那些内存中的瞬时数据保存到存储设备中，保证即使在手机或电脑关机的情况下，这些数据仍然不会丢失。保存在内存中的数据是处于瞬时状态的，而保存在存储设备中的数据是处于持久状态的，持久化技术则是提供了一种机制可以让数据在瞬时状态和持久状态之间进行转换。持久化技术被广泛应用于各种程序设计的领域当中，Android系统中主要提供了三种方式用于简单地实现数据持久化功能，即文件存储、SharedPreference存储以及数据库存储。

##1.文件存储
文件存储是Android中最基本的一种数据存储方式，它不对存储的内容进行任何的格式化处理，所有数据都是原封不动地保存到文件当中的，因而它比较适合用于存储一些简单的文本数据或二进制数据。

###1.1将数据存储到文件中
Context类中提供了一个openFileOutput()方法，可以用于将数据存储到指定的文件中。这个方法接收两个参数，第一个参数是文件名，在文件创建的时候使用的就是这个名称，注意这里指定的文件名不可以包含路径，因为所有的文件都是默认存储到/data/data/<packagename>/files/目录下的。第二个参数是文件的操作模式，主要有两种模式可选，MODE_PRIVATE和MODE_APPEND。其中 MODE_PRIVATE是默认的操作模式，表示当指定同样文件名的时候，所写入的内容将会覆盖原文件中的内容，而MODE_APPEND则表示如果该文件已存在就往文件里面追加内容，不存在就创建新文件。其实文件的操作模式本来还有另外两种，MODE_WORLD_READABLE和MODE_WORLD_WRITEABLE，这两种模式表示允许其他的应用程序对我们程序中的文件进行读写操作，不过由于这两种模式过于危险，很容易引起应用的安全性漏洞，现已在Android 4.2版本中被废弃。
openFileOutput()方法返回的是一个FileOutputStream对象，得到了这个对象之后就可以使用Java流的方式将数据写入到文件中了。代码如下： 
    public void save() {
        String data = "Data to save";
        FileOutputStream out = null;
        BufferedWriter writer = null;
        try {
          out = openFileOutput("data", Context.MODE_PRIVATE);
          writer = new BufferedWriter(new OutputStreamWriter(out));
          writer.write(data);
        } catch (IOException e) {
             e.printStackTrace();
        } finally {
             try {
                 if (writer != null) {
                 writer.close();
                 }
             } catch (IOException e) {
                    e.printStackTrace();
             }
        }
}

###1.2从文件中读取数据
类似于将数据存储到文件中，Context类中还提供了一个openFileInput()方法，用于从文件中读取数据。这个方法要比openFileOutput()简单一些，它只接收一个参数，即要读取的文件名，然后系统会自动到/data/data/<package name>/files/目录下去加载这个文件，并返回一个FileInputStream对象，得到了这个对象之后再通过Java流的方式就可以将数据读取出来了。如下代码展示了如何从文件中读取文本数据：
    public String load() {
        FileInputStream in = null;
        BufferedReader reader = null;
        StringBuilder content = new StringBuilder();
        try {
             in = openFileInput("data");
             reader = new BufferedReader(new InputStreamReader(in));
             String line = "";
             while ((line = reader.readLine()) != null) {
                 content.append(line);
             }
        } catch (IOException e) {
              e.printStackTrace();
        } finally {
             if (reader != null) {
                 try {
                      reader.close();
                 } catch (IOException e) {
                         e.printStackTrace();
                 }
             }
        }
        return content.toString();
    }
在这段代码中，首先通过openFileInput()方法获取到了一个 FileInputStream对象，然后借助它又构建出了一个 InputStreamReader对象，接着再使用InputStreamReader构建出一个BufferedReader对象，这样我们就可以通过BufferedReader进行一行行地读取，把文件中所有的文本内容全部读取出来并存放在一个StringBuilder对象中，最后将读取到的内容返回就可以了。

##2. SharedPreferences 存储
不同于文件的存储方式，SharedPreferences是使用键值对的方式来存储数据的。也就是说当保存一条数据的时候，需要给这条数据提供一个对应的键，这样在读取数据的时候就可以通过这个键把相应的值取出来。而且SharedPreferences 还支持多种不同的数据类型存储，如果存储的数据类型是整型，那么读取出来的数据也是整型的，存储的数据是一个字符串，读取出来的数据仍然是字符串。

###2.1将数据存储到 SharedPreferences 中
要想使用SharePreferences来存储数据，首先需要获取到SharePreferences对象。Android中主要提供了三种方法用于得到SharePreferences对象。
(1)Context类中的getSharedPreferences()方法
此方法接收两个参数，第一个参数用于指定 SharedPreferences文件的名称，如果指定的文件不存在则会创建一个，SharedPreferences 文件都是存放在/data/data/<package name>/shared_prefs/目录下的。第二个参数用于指定操作模式，主要有两种模式可以选择，MODE_PRIVATE 和 MODE_MULTI_PROCESS。MODE_PRIVATE仍然是默认的操作模式，和直接传入0效果是相同的，表示只有当前的应用程序才可以对这个SharedPreferences文件进行读写。 MODE_MULTI_PROCESS则一般是用于会有多个进程中对同一个 SharedPreferences文件进行读写的情况。类似地，MODE_WORLD_READABLE和 MODE_WORLD_WRITEABLE这两种模式已在Android 4.2版本中被废弃。
(2) Activity 类中的 getPreferences()方法
这个方法和Context中的getSharedPreferences()方法很相似，不过它只接收一个操作模式参数，因为使用这个方法时会自动将当前活动的类名作为 SharedPreferences的文件名。
(3) PreferenceManager类中的getDefaultSharedPreferences()方法
这是一个静态方法，它接收一个Context参数，并自动使用当前应用程序的包名作为前缀来命名 SharedPreferences 文件。
得到了SharedPreferences对象之后，就可以开始向SharedPreferences文件中存储数据了，主要可以分为三步实现。
1.调用SharedPreferences对象的edit()方法来获取一个 SharedPreferences.Editor 对象。
2.向SharedPreferences.Editor对象中添加数据，比如添加一个布尔型数据就使用putBoolean 方法，添加一个字符串则使用 putString()方法，以此类推。
3.调用commit()方法将添加的数据提交，从而完成数据存储操作。
主要实例代码如下：
public class MainActivity extends Activity {
private Button saveData;
@Override
protected void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);
saveData = (Button) findViewById(R.id.save_data);
saveData.setOnClickListener(new OnClickListener() {
@Override
public void onClick(View v) {
SharedPreferences.Editor editor = getSharedPreferences("data",MODE_PRIVATE).edit();
editor.putString("name", "Tom");
editor.putInt("age", 28);
editor.putBoolean("married", false);
editor.commit();
}
});
}
}
可以看到，这里首先给按钮注册了一个点击事件，然后在点击事件中通过
getSharedPreferences()方法指定SharedPreferences的文件名为data，并得到了SharedPreferences.Editor对象。接着向这个对象中添加了三条不同类型的数据，最后调用commit()方法进行提交，从而完成了数据存储的操作。切换到DDMS视图，并点击File Explorer切换卡，然后进入到/data/data/com.example.sharedpreferencestest/shared_prefs/目录下，可以看到生成了一个 data.xml文件。

###2.2从 SharedPreferences 中读取数据
SharedPreferences对象中提供了一系列的get方法用于对存储的数据进行读取， 每种get方法都对应了SharedPreferences.Editor 中的一种put方法，比如读取一个布尔型数据就使用getBoolean()方法，读取一个字符串就使用 getString()方法。这些get方法都接收两个参数，第一个参数是键，传入存储数据时使用的键就可以得到相应的值了，第二个参数是默认值，即表示当传入的键找不到对应的值时，会以什么样的默认值进行返回。主要示例代码如下：
public class MainActivity extends Activity {
private Button saveData;
private Button restoreData;
@Override
protected void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);
saveData = (Button) findViewById(R.id.save_data);
restoreData = (Button) findViewById(R.id.restore_data);

restoreData.setOnClickListener(new OnClickListener() {
@Override
public void onClick(View v) {
SharedPreferences pref = getSharedPreferences("data",
MODE_PRIVATE);
String name = pref.getString("name", "");
int age = pref.getInt("age", 0);
boolean married = pref.getBoolean("married", false);
Log.d("MainActivity", "name is " + name);
Log.d("MainActivity", "age is " + age);
Log.d("MainActivity", "married is " + married);
}
});
}
}
可以看到，在还原数据按钮的点击事件中首先通过getSharedPreferences()方法得到了SharedPreferences对象，然后分别调用它的 getString()、getInt()和getBoolean()方法去获取前面所存储的姓名、年龄和是否已婚，如果没有找到相应的值就会使用方法中传入的默认值来代替，最后通过Log将这些值打印出来。

##3. SQLite 数据库存储
Android 系统内置了SQLite数据库，SQLite 是一款轻量级的关系型数据库， 它的运算速度非常快，占用资源很少，通常只需要几百K的内存就足够了，因而特别适合在移动设备上使用。SQLite不仅支持标准的SQL语法，还遵循了数据库的ACID事务，所以只要以前使用过其他的关系型数据库，就可以很快地上手 SQLite。而SQLite又比一般的数据库要简单得多，它甚至不用设置用户名和密码就可以使用。Android正是把这个功能极为强大的数据库嵌入到了系统当中，使得本地持久化的功能有了一次质的飞跃。

###3.1创建数据库
Android专门提供了一个SQLiteOpenHelper帮助类管理SQLite数据库，借助这个类就可以非常简单地对数据库进行创建和升级。SQLiteOpenHelper是一个抽象类，这意味着如果使用它,就需要创建一个自己的帮助类去继承它。SQLiteOpenHelper中有两个抽象方法，分别是onCreate()和onUpgrade()，必须在自己的帮助类里面重写这两个方法，然后分别在这两个方法中去实现创建、升级数据库的逻辑。SQLiteOpenHelper中还有两个非常重要的实例方法,getReadableDatabase()和getWritableDatabase()。这两个方法都可以创建或打开一个现有的数据库（如果数据库已存在则直接打开，否则创建一个新的数据库），并返回一个可对数据库进行读写操作的对象。区别是，当数据库不可写入的时候（如磁盘空间已满）getReadableDatabase()方法返回的对象将以只读的方式去打开数据库，而getWritableDatabase()方法则将出现异常。
SQLiteOpenHelper中有两个构造方法可供重写，一般使用参数少一点的那个构造方法。这个构造方法中接收四个参数，第一个参数是Context，必须要有它才能对数据库进行操作。第二个参数是数据库名，创建数据库时使用的就是这里指定的名称。第三个参数允许在查询数据的时候返回一个自定义的Cursor，一般都是传入null。第四个参数表示当前数据库的版本号，可用于对数据库进行升级操作。构建出SQLiteOpenHelper的实例之后，再调用它的 getReadableDatabase()或 getWritableDatabase()方法就能够创建数据库了，数据库文件会存放在/data/data/<package name>/databases/目录下。同时，重写的onCreate()方法也会得到执行，所以通常会在这里去处理一些创建表的逻辑。
新建 MyDatabaseHelper类继承自SQLiteOpenHelper帮助类，主要示例代码如下：
public class MyDatabaseHelper extends SQLiteOpenHelper {
public static final String CREATE_BOOK = "create table book ("
+ "id integer primary key autoincrement, "
+ "author text, "
+ "price real, "
+ "pages integer, "
+ "name text)";
private Context mContext;
public MyDatabaseHelper(Context context, String name, CursorFactory factory, int version) {
super(context, name, factory, version);
mContext = context;
}
@Override
public void onCreate(SQLiteDatabase db) {
db.execSQL(CREATE_BOOK);
Toast.makeText(mContext, "Create succeeded", Toast.LENGTH_SHORT).show();
}
@Override
public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
}
}
在这里可以看到，首先把建表语句定义成了一个字符串常量，然后在 onCreate()方法中又调用了SQLiteDatabase的execSQL()方法去执行这条建表语句，并弹出一个Toast提示创建成功，这样就可以保证在数据库创建完成的同时还能成功创建 Book 表。最后修改 MainActivity 中的代码，如下所示：
public class MainActivity extends Activity {
private MyDatabaseHelper dbHelper;
@Override
protected void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);
dbHelper = new MyDatabaseHelper(this, "BookStore.db", null, 1);
Button createDatabase = (Button) findViewById(R.id.create_database);
createDatabase.setOnClickListener(new OnClickListener() {
@Override
public void onClick(View v) {
dbHelper.getWritableDatabase();
}
});
}
}
可以看出在onCreate()方法中构建了一个MyDatabaseHelper对象，并且通过构造函数的参数将数据库名指定为BookStore.db，版本号指定为1，然后在Create database按钮的点击事件里调用了getWritableDatabase()方法。这样当第一次点击 Createdatabase按钮时，就会检测到当前程序中并没有 BookStore.db这个数据库，于是会创建该数据库并调用MyDatabaseHelper中的 onCreate()方法，这样Book表也就得到了创建，然后会弹出一个Toast提示创建成功。再次点击Create database按钮时，会发现此时已经存在BookStore.db 数据库了，因此不会再创建一次。

###3.2升级数据库
目前已经有一张 Book 表用于存放书的各种详细数据，如果再添加一张 Category表用于记录书籍的分类就需要用到onUpgrade()方法对数据库进行升级，主要示例代码如下：
首先修改MyDatabaseHelper中的代码，如下：
public class MyDatabaseHelper extends SQLiteOpenHelper {
……
@Override
public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
db.execSQL("drop table if exists Book");
db.execSQL("drop table if exists Category");
onCreate(db);
}
}
可以看到，在 onUpgrade()方法中执行了两条DROP语句，如果发现数据库中已经存在Book表或Category表了，就将这两张表删除掉，然后再调用 onCreate()方法去重新创建。这里先将已经存在的表删除掉，是因为如果在创建表时发现这张表已经存在了，就会直接报错。
然后将SQLiteOpenHelper的构造方法里接收的第四个参数（表示当前数据库的版本号），由之前的1改成一个比1大的数， 就可以让 onUpgrade()方法得到执行了。修改 MainActivity 中的代码，如下所示：
public class MainActivity extends Activity {
private MyDatabaseHelper dbHelper;
@Override
protected void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);
dbHelper = new MyDatabaseHelper(this, "BookStore.db", null, 2);
Button createDatabase = (Button) findViewById(R.id.create_database);
createDatabase.setOnClickListener(new OnClickListener() {
@Override
public void onClick(View v) {
dbHelper.getWritableDatabase();
}
});
}
}

###3.3添加数据
SQLiteDatabase中提供了一个insert()方法，这个方法就是专门用于添加数据的。它接收三个参数，第一个参数是表名，表示向哪张表里添加数据，这里就传入该表的名字。第二个参数用于在未指定添加数据的情况下给某些可为空的列自动赋值NULL，一般用不到这个功能，直接传入null即可。第三个参数是一个ContentValues对象，它提供了一系列的put()方法重载，用于向 ContentValues 中添加数据，只需要将表中的每个列名以及相应的待添加数据传入即可。主要示例代码如下：
public class MainActivity extends Activity {
private MyDatabaseHelper dbHelper;
@Override
protected void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);
dbHelper = new MyDatabaseHelper(this, "BookStore.db", null, 2);
……
Button addData = (Button) findViewById(R.id.add_data);
addData.setOnClickListener(new OnClickListener() {
@Override
public void onClick(View v) {
SQLiteDatabase db = dbHelper.getWritableDatabase();
ContentValues values = new ContentValues();
//  开始组装第一条数据
values.put("name", "The Da Vinci Code");
values.put("author", "Dan Brown");
values.put("pages", 454);
values.put("price", 16.96);
db.insert("Book", null, values); //  插入第一条数据
values.clear();
//  开始组装第二条数据
values.put("name", "The Lost Symbol");
values.put("author", "Dan Brown");
values.put("pages", 510);
values.put("price", 19.95);
db.insert("Book", null, values); //  插入第二条数据
}
});
}
}

###3.4更新数据
SQLiteDatabase 中也是提供了一个非常好用的 update()方法用于对数据进行更新，这个方法接收四个参数，第一个参数和 insert()方法一样，也是表名，在这里指定去更新哪张表里的数据。第二个参数是 ContentValues 对象，要把更新数据在这里组装进去。第三、第四个参数用于去约束更新某一行或某几行中的数据，不指定的话默认就是更新所有行。主要示例代码如下：
public class MainActivity extends Activity {
private MyDatabaseHelper dbHelper;
@Override
protected void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);
dbHelper = new MyDatabaseHelper(this, "BookStore.db", null, 2);
……
Button updateData = (Button) findViewById(R.id.update_data);
updateData.setOnClickListener(new OnClickListener() {
@Override
public void onClick(View v) {
SQLiteDatabase db = dbHelper.getWritableDatabase();
ContentValues values = new ContentValues();
values.put("price", 10.99);
db.update("Book", values, "name = ?", new String[] { "The Da
Vinci Code" });
}
});
}
}

###3.5删除数据
SQLiteDatabase 中提供了一个 delete()方法专门用于删除数据，这个方法接收三个参数，第一个参数仍然是表名，第二、第三个参数又是用于去约束删除某一行或某几行的数据，不指定的话默认就是删除所有行。主要示例代码如下：
public class MainActivity extends Activity {
private MyDatabaseHelper dbHelper;
@Override
protected void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);
dbHelper = new MyDatabaseHelper(this, "BookStore.db", null, 2);
……
Button deleteButton = (Button) findViewById(R.id.delete_data);
deleteButton.setOnClickListener(new OnClickListener() {
@Override
public void onClick(View v) {
SQLiteDatabase db = dbHelper.getWritableDatabase();
db.delete("Book", "pages > ?", new String[] { "500" });
}
});
}
}

###3.6查询数据
SQLiteDatabase中还提供了一个 query()方法用于对数据进行查询。
这个方法的参数非常复杂，最短的一个方法重载也需要传入七个参数。这七个参数各自的含义吧，第一个参数表示表名，表示我们希望从哪张表中查询数据。第二个参数用于指定去查询哪几列，如果不指定则默认查询所有列。第三、第四个参数用于去约束查询某一行或某几行的数据，不指定则默认是查询所有行的数据。第五个参数用于指定需要去group by的列，不指定则表示不对查询结果进行group by操作。第六个参数用于对group by之后的数据进行进一步的过滤，不指定则表示不进行过滤。第七个参数用于指定查询结果的排序方式，不指定则表示使用默认的排序方式。主要示例代码如下：
public class MainActivity extends Activity {
private MyDatabaseHelper dbHelper;
@Override
protected void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);
dbHelper = new MyDatabaseHelper(this, "BookStore.db", null, 2);
……
Button queryButton = (Button) findViewById(R.id.query_data);
queryButton.setOnClickListener(new OnClickListener() {
@Override
public void onClick(View v) {
SQLiteDatabase db = dbHelper.getWritableDatabase();
//  查询Book 表中所有的数据
Cursor cursor = db.query("Book", null, null, null, null, null, null);
if (cursor.moveToFirst()) {
do {
//  遍历Cursor 对象，取出数据并打印
String name = cursor.getString(cursor.
getColumnIndex("name"));
String author = cursor.getString(cursor.
getColumnIndex("author"));
int pages = cursor.getInt(cursor.getColumnIndex
("pages"));
double price = cursor.getDouble(cursor.
getColumnIndex("price"));
Log.d("MainActivity", "book name is " + name);
Log.d("MainActivity", "book author is " + author);
Log.d("MainActivity", "book pages is " + pages);
Log.d("MainActivity", "book price is " + price);
} while (cursor.moveToNext());
}
cursor.close();
}
});
}
}


#二、Android跨程序共享数据技术
使用这些持久化技术所保存的数据都只能在当前应用程序中访问，虽然文件和SharedPreferences存储中提供了MODE_WORLD_READABL和 MODE_WORLD_WRITEABLE 这两种操作模式，用于供给其他的应用程序访问当前应用的数据，但这两种模式在 Android 4.2版本中都已被废弃了。因为Android官方已经不再推荐使用这种方式来实现跨程序数据共享的功能，而是应该使用更加安全可靠的内容提供器技术。
内容提供器（Content Provider）主要用于在不同的应用程序之间实现数据共享的功能，它提供了一套完整的机制，允许一个程序访问另一个程序中的数据，同时还能保证被访数据的安全性。目前，使用内容提供器是Android实现跨程序共享数据的标准方式。不同于文件存储和SharedPreferences存储中的两种全局可读写操作模式，内容提供器可以选择只对哪一部分数据进行共享，从而保证程序中的隐私数据不会有泄漏的风险。
内容提供器的用法一般有两种， 一种是使用现有的内容提供器来读取和操作相应程序中的数据，另一种是创建自己的内容提供器给应用程序的数据提供外部访问接口。

##1.访问其他程序中的数据
对于每一个应用程序来说，如果想要访问内容提供器中共享的数据，就一定要借助ContentResolve类，可以通过Context中的getContentResolver()方法获取到该类的实例。ContentResolver中提供了一系列的方法用于对数据进行 CRUD操作，其中insert()方法用于添加数据，update()方法用于更新数据，delete()方法用于删除数据，query()方法用于查询数据。不同于 SQLiteDatabase，ContentResolver 中的增删改查方法都是不接收表名参数的，而是使用一个 Uri 参数代替，这个参数被称为内容 URI。内容URI给内容提供器中的数据建立了唯一标识符，它主要由两部分组成，权限（authority）和路径（path）。权限是用于对不同的应用程序做区分的，一般为了避免冲突，都会采用程序包名的方式来进行命名。比如某个程序的包名是 com.example.app，那么该程序对应的权限就可以命名为 com.example.app.provider。路径则是用于对同一应用程序中不同的表做区分的，通常都会添加到权限的后面。比如某个程序的数据库里存在两张表，table1 和 table2，这时就可以将路径分别命名为/table1和/table2，然后把权限和路径进行组合，内容URI就变成了 com.example.app.provider/table1和com.example.app.provider/table2。不过，目前还很难辨认出这两个字符串就是两个内容URI，还需要在字符串的头部加上协议声明。因此，内容URI最标准的格式写法如下：
content://com.example.app.provider/table1
content://com.example.app.provider/table2
可以发现，内容URI可以非常清楚地表达出想要访问的哪个程序中哪张表里的数据。因此，ContentResolver 中的增删改查方法才都接收 Uri 对象作为参数，因为使用表名的话系统将无法得知期望访问的是哪个应用程序里的表。在得到了内容 URI 字符串之后，还需要将它解析成Uri对象才可以作为参数传入。
解析方法的代码如下所示：
Uri uri = Uri.parse("content://com.example.app.provider/table1")
只需要调用Uri.parse()方法，就可以将内容URI字符串解析成Uri对象了。

###1.1查询数据
现在我们就可以使用这个 Uri 对象来查询 table1 表中的数据了，代码如下所示：
Cursor cursor = getContentResolver().query(uri,projection,
selection,
selectionArgs,
sortOrder);
查询完成后返回的仍然是一个 Cursor 对象，这时就可以将数据从 Cursor 对象中逐个读取出来了。 读取的思路仍然是通过移动游标的位置来遍历 Cursor 的所有行， 然后再取出每一行中相应列的数据，代码如下所示：
if (cursor != null) {
while (cursor.moveToNext()) {
String column1 = cursor.getString(cursor.getColumnIndex("column1"));
int column2 = cursor.getInt(cursor.getColumnIndex("column2"));
}
cursor.close();
}

###1.2添加数据
如何向 table1 表中添加一条数据，代码如下所示：
ContentValues values = new ContentValues();
values.put("column1", "text");
values.put("column2", 1);
getContentResolver().insert(uri, values);
可以看到，仍然是将待添加的数据组装到 ContentValues 中，然后调用 ContentResolver
的 insert()方法，将 Uri 和 ContentValues作为参数传入即可。

###1.3更新数据
如果想要更新这条新添加的数据，把 column1 的值清空，可以借助ContentResolver 的 update()方法实现，代码如下所示：
ContentValues values = new ContentValues();
values.put("column1", "");
getContentResolver().update(uri, values, "column1 = ? and column2 = ?", new
String[] {"text", "1"});
注意上述代码使用了 selection 和 selectionArgs 参数来对想要更新的数据进行约束， 以防止所有的行都会受影响。

###1.4删除数据
最后，可以调用 ContentResolver 的 delete()方法将这条数据删除掉，代码如下所示：
getContentResolver().delete(uri, "column2 = ?", new String[] { "1" });


##2.创建自己的内容提供器
可以通过新建一个类去继承 ContentProvider 的方式来创建一个自己的内容提供器。
ContentProvider 类中有六个抽象方法，我们在使用子类继承它的时候，需要将这六个方法全
部重写。新建 MyProvider 继承自 ContentProvider，代码如下所示：
public class MyProvider extends ContentProvider {
@Override
public boolean onCreate() {
return false;
}
@Override
public Cursor query(Uri uri, String[] projection, String selection,
String[] selectionArgs, String sortOrder) {
return null;
}
@Override
public Uri insert(Uri uri, ContentValues values) {
return null;
}
@Override
public int update(Uri uri, ContentValues values, String selection,
String[] selectionArgs) {
return 0;
}
@Override
public int delete(Uri uri, String selection, String[] selectionArgs) {
return 0;
}
@Override
public String getType(Uri uri) {
return null;
}
}
(1).onCreate()
初始化内容提供器的时候调用。通常会在这里完成对数据库的创建和升级等操作，
返回 true 表示内容提供器初始化成功，返回 false 则表示失败。只有当存在
ContentResolver 尝试访问我们程序中的数据时，内容提供器才会被初始化。
(2).query()
从内容提供器中查询数据。使用uri参数来确定查询哪张表，projection参数用于确定查询哪些列，selection和selectionArgs参数用于约束查询哪些行，sortOrder 参数用于对结果进行排序，查询的结果存放在 Cursor 对象中返回。
(3).insert()
向内容提供器中添加一条数据。使用uri参数来确定要添加到的表，待添加的数据保存在 values 参数中。添加完成后，返回一个用于表示这条新记录的URI。
(4).update()
更新内容提供器中已有的数据。使用uri参数来确定更新哪一张表中的数据，新数据保存在values参数中，selection和selectionArgs参数用于约束更新哪些行，受影响的行数将作为返回值返回。
(5).delete()
从内容提供器中删除数据。使用uri参数来确定删除哪一张表中的数据，selection和selectionArgs参数用于约束删除哪些行，被删除的行数将作为返回值返回。
(6).getType()
根据传入的内容URI来返回相应的MIME类型。
可以看到，几乎每一个方法都会带有Uri这个参数，这个参数也正是调用ContentResolver的增删改查方法时传递过来的。现在，需要对传入的Uri参数进行解析，从中分析出调用方期望访问的表和数据。如前所述，一个标准的内容 URI 写法如下：
content://com.example.app.provider/table1
这就表示调用方期望访问的是com.example.app这个应用的table1表中的数据。另外，还可以在这个内容 URI 的后面加上一个 id，如下所示：
content://com.example.app.provider/table1/1
这就表示调用方期望访问的是 com.example.app 这个应用的 table1 表中 id 为 1 的数据。
内容 URI 的格式主要就只有以上两种，以路径结尾就表示期望访问该表中所有的数据，以id结尾就表示期望访问该表中拥有相应id的数据。我们可以使用通配符的方式来分别匹配这两种格式的内容URI，规则如下：
     1. *：表示匹配任意长度的任意字符
     2. #：表示匹配任意长度的数字
所以，一个能够匹配任意表的内容 URI 格式就可以写成：
content://com.example.app.provider/*
而一个能够匹配 table1 表中任意一行数据的内容 URI 格式就可以写成：
content://com.example.app.provider/table1/#
接着，再借助UriMatcher这个类就可以轻松地实现匹配内容URI的功能。 UriMatcher中提供了一个addURI()方法，这个方法接收三个参数，可以分别把权限、路径和一个自定义代码传进去。这样，当调用UriMatcher的match()方法时，就可以将一个Uri对象传入，返回值是某个能够匹配这个Uri对象所对应的自定义代码，利用这个代码，就可以判断出调用方期望访问的是哪张表中的数据了。修改 MyProvider 中的代码，如下所示：
public class MyProvider extends ContentProvider {
public static final int TABLE1_DIR = 0;
public static final int TABLE1_ITEM = 1;
public static final int TABLE2_DIR = 2;
public static final int TABLE2_ITEM = 3;
private static UriMatcher uriMatcher;
static {
uriMatcher = new UriMatcher(UriMatcher.NO_MATCH);
uriMatcher.addURI("com.example.app.provider", "table1", TABLE1_DIR);
uriMatcher.addURI("com.example.app.provider ", "table1/#", TABLE1_ITEM);
uriMatcher.addURI("com.example.app.provider ", "table2", TABLE2_ITEM);
uriMatcher.addURI("com.example.app.provider ", "table2/#", TABLE2_ITEM);
}
……
@Override
public Cursor query(Uri uri, String[] projection, String selection,
String[] selectionArgs, String sortOrder) {
switch (uriMatcher.match(uri)) {
case TABLE1_DIR:
//  查询table1 表中的所有数据
break;
case TABLE1_ITEM:
//  查询table1 表中的单条数据
break;
case TABLE2_DIR:
//  查询table2 表中的所有数据
break;
case TABLE2_ITEM:
//  查询table2 表中的单条数据
break;
default:
break;
}
……
}
……
}
可以看到，MyProvider中新增了四个整型常量，其中TABLE1_DIR表示访问table1表中的所有数据，TABLE1_ITEM表示访问table1表中的单条数据，TABLE2_DIR 表示访问table2表中的所有数据，TABLE2_ITEM表示访问table2 表中的单条数据。接着在静态代码块里创建了UriMatcher的实例，并调用addURI()方法，将期望匹配的内容URI格式传递进去，注意这里传入的路径参数是可以使用通配符的。然后当query()方法被调用的时候，就会通过UriMatcher的match()方法对传入的Uri对象进行匹配，如果发现UriMatcher中某个内容URI格式成功匹配了该Uri对象，则会返回相应的自定义代码，然后就可以判断出调用方期望访问的到底是什么数据了。
上述代码只是以query()方法为例做了个示范，其实insert()、update()、delete()这几个方法的实现也是差不多的，它们都会携带Uri这个参数，然后同样利用UriMatcher的match()方法判断出调用方期望访问的是哪张表，再对该表中的数据进行相应的操作就可以了。


