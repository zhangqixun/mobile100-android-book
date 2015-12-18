# android数据持久化存储和跨程序共享数据技术(陈曾华 1501210879）

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
2. SharedPreferences 存储
不同于文件的存储方式，SharedPreferences是使用键值对的方式来存储数据的。也就是说当保存一条数据的时候，需要给这条数据提供一个对应的键，这样在读取数据的时候就可以通过这个键把相应的值取出来。而且SharedPreferences 还支持多种不同的数据类型存储，如果存储的数据类型是整型，那么读取出来的数据也是整型的，存储的数据是一个字符串，读取出来的数据仍然是字符串。
2.1将数据存储到 SharedPreferences 中
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
2.2从 SharedPreferences 中读取数据
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
3. SQLite 数据库存储
Android 系统竟然是内置了数据库
的！ 好吧， 是我太孤陋寡闻了。 SQLite 是一款轻量级的关系型数据库， 它的运算速度非常快，
占用资源很少， 通常只需要几百 K 的内存就足够了， 因而特别适合在移动设备上使用。 SQLite
不仅支持标准的 SQL 语法，还遵循了数据库的 ACID 事务，所以只要你以前使用过其他的
关系型数据库，就可以很快地上手 SQLite。而 SQLite 又比一般的数据库要简单得多，它甚
至不用设置用户名和密码就可以使用。Android 正是把这个功能极为强大的数据库嵌入到了
系统当中，使得本地持久化的功能有了一次质的飞跃。


