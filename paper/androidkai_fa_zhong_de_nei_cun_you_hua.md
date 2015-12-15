# Android开发中的内存优化（1501211012 徐荣涛）



# 一、简介



  在本学期的课程中，我们已经充分学习了android平台应用开发的基本方法，掌握了部分常用组件的使用以及改造，而通过天气预报项目的实践使得我们对于应用的开发流程也有了一定的了解。但是，在实际应用开发过程中，我们不能够仅仅停留在“能用就行”的阶段，在实际发布的应用程序中，性能的好坏也是衡量质量的重要指标，而在所有影响Android应用性能的原因中，内存占用过多绝对是其中的巨大影响因子，所以学习一些常用的内存优化原理并将其运用在程序编写中是理所当然的事情。本文介绍了我们在应用开发中常犯的对内存占用有很大影响的不良习惯，并简单提出了解决建议。
  

# 二、Android的内存机制



Android的程序由Java语言编写，所以Android的内存管理与Java的内存管理相似。程序员通过new为对象分配内存，所有对象在java堆内分配空间；然而对象的释放是由垃圾回收器来完成的。C／C++中的内存机制是“谁污染，谁治理”，java的就比较人性化了，给我们请了一个专门的清洁工（GC）。
 
 那么GC怎么能够确认某一个对象是不是已经被废弃了呢？Java采用了有向图的原理。Java将引用关系考虑为图的有向边，有向边从引用者指向引用对象。线程对象可以作为有向图的起始顶点，该图就是从起始顶点开始的一棵树，根顶点可以到达的对象都是有效对象，GC不会回收这些对象。如果某个对象 (连通子图)与这个根顶点不可达(注意，该图为有向图)，那么我们认为这个(这些)对象不再被引用，可以被GC回收。
 
在采用了GC来管理运行内存的情况下，其会不定期的对内存进行管理（包括对对象的分配和释放），这直接导致了Android应用的界面卡顿现象。所以，我们常见的开发内存占用的原因包括：

    1、长期保持某些资源（如Context）的引用，造成内存泄露，资源造成得不到释放
    2、保存了多个耗用内存过大的对象（如Bitmap），造成内存超出限制

# 三、Android内存优化方法




##  3.1避免创建过多的对象（多重用）


 
每次创建对象都是有所消耗的，而且每个线程给他的对象分配地址来进行创建的，分配开销，对于所有在同一JVM中运行的代码，只要他们包含相同的字符串字面常量，该对象就会被重用，比较下面两个如果多次反复的执行的话，效率的区别：

* Boolean c = Boolean.valueOf("true");    
Boolean c = new Boolean("true");

Boolean c = Boolean.valueOf("true");
是利用静态工厂的方法来返回一个对象，因为这种都是true或false其中一个的布尔型，所以使用这种方式很好用。因为，valueOf的这个将一定会返回Boolean.TRUE或Boolean.FALSE两个的其中一个。

Boolean c = new Boolean("true");的构造方法，每次需要的时候都是new一个新的布尔的实例对象。如果次数少还好，但是如果需要多次调用后，那么就需要建立多个对象，给系统很大的压力，而且还给回收的机制很大的负担。 在实际的开发中，每次在用户的界面周期内分配对象，系统就会分配一个周期性的垃圾回收机制，这个会给用户的体验一个卡顿的时间。并发的回收机制可能会有用。但即使这样，我们对于不必要的工作也是应当减少的。所以应该避免创建不必要的对象，多用静态工程等的方式来实例化。比如和上面布尔型的一样：如果要有一个返回String的方法，并且他的返回结果是附加在一个StringBuffer上，可以改变实现，可以让他直接在后面就给返回，不要在new一个或是实例化。

b）另外一个是当从输入的数据的集合来读取所以需要的内容时候，这时候因为不是一定要创建一个新的数据集合，所以可以考虑和原来的数据共享一个地址，不如就考虑返回之前的数据，不要再创立一个新的拷贝内容，用这样的方法即使看起来是new了一个新的对象，但是他们共享同样数据，地址，但是，这样也有他的局限性，比如只需要他的一部分的数据，但是你需要把所有的内容放在内存中。看到网上说一个更彻底的方案是将多维数组切割成平行一维数组更好，比如说Int类型的数组常有余Integer类型的。推而广之，两个平行的int数组要比一个（int,int）型的对象数组高效。
 
## 3.2尽量少地使用静态


static是Java中的一个关键字，当用它来修饰成员变量时，那么该变量就属于该类，而不是该类的实例。所以用static修饰的变量，它的生命周期是很长的，如果用它来引用一些资源耗费过多的实例（Context的情况最多），这时就要谨慎对待了。

1.public class ClassName{

2.private static Context mContext;  
3.     //省略  
4.}  ```
以上的代码是很危险的，如果将Activity赋值到么mContext的话。那么即使该Activity已经onDestroy，但是由于仍有对象保存它的引用，因此该Activity依然不会被释放。
    我们举Android文档中的一个例子。

1.private static Drawable sBackground;  
2.     
3. @Override  
4. protected void onCreate(Bundle state) {  
5.   super.onCreate(state);  
6.     
7.   TextView label = new TextView(this);  
8.   label.setText("Leaks are bad");  
9.     
10.   if (sBackground == null) {  
11.     sBackground = getDrawable(R.drawable.large_bitmap);  
12.   }  
13.   label.setBackgroundDrawable(sBackground);  
14.     
15.   setContentView(label);  
16. }  ```
    sBackground, 是一个静态的变量，但是我们发现，我们并没有显式的保存Contex的引用，但是，当Drawable与View连接之后，Drawable就将View设置为一个回调，由于View中是包含Context的引用的，所以，实际上我们依然保存了Context的引用。这个引用链如下：
    Drawable->TextView->Context
    所以，最终该Context也没有得到释放，发生了内存泄露。
    如何才能有效的避免这种引用的发生呢？
    第一，应该尽量避免static成员变量引用资源耗费过多的实例，比如Context。
    第二、Context尽量使用Application Context，因为Application的Context的生命周期比较长，引用它不会出现内存泄露的问题。
    第三、使用WeakReference代替强引用。比如可以使用WeakReference mContextRef;
 3.3注意线程的使用
  线程也是造成内存泄露的一个重要的源头。线程产生内存泄露的主要原因在于线程生命周期的不可控。我们来考虑下面一段代码。
1.public class MyActivity extends Activity {  
2.    @Override  
3.    public void onCreate(Bundle savedInstanceState) {  
4.        super.onCreate(savedInstanceState);  
5.        setContentView(R.layout.main);  
6.        new MyThread().start();  
7.    }  
8.  
9.    private class MyThread extends Thread{  
10.        @Override  
11.        public void run() {  
12.            super.run();  
13.            //do somthing  
14.        }  
15.    }  
16.}  
    这段代码很平常也很简单，是我们经常使用的形式。我们思考一个问题：假设MyThread的run函数是一个很费时的操作，当我们开启该线程后，将设备的横屏变为了竖屏，一般情况下当屏幕转换时会重新创建Activity，按照我们的想法，老的Activity应该会被销毁才对，然而事实上并非如此。
    由于我们的线程是Activity的内部类，所以MyThread中保存了Activity的一个引用，当MyThread的run函数没有结束时，MyThread是不会被销毁的，因此它所引用的老的Activity也不会被销毁，因此就出现了内存泄露的问题。
    有些人喜欢用Android提供的AsyncTask，但事实上AsyncTask的问题更加严重，Thread只有在run函数不结束时才出现这种内存泄露问题，然而AsyncTask内部的实现机制是运用了ThreadPoolExcutor,该类产生的Thread对象的生命周期是不确定的，是应用程序无法控制的，因此如果AsyncTask作为Activity的内部类，就更容易出现内存泄露的问题。
    这种线程导致的内存泄露问题应该如何解决呢？
    第一、将线程的内部类，改为静态内部类。
    第二、在线程内部采用弱引用保存Context引用。
    解决的模型如下：
1.public abstract class WeakAsyncTaskProgress, Result, WeakTarget> extends  
2.        AsyncTaskProgress, Result> {  
3.    protected WeakReference mTarget;  
4.  
5.    public WeakAsyncTask(WeakTarget target) {  
6.        mTarget = new WeakReference(target);  
7.    }  
8.  
9.      
10.    @Override  
11.    protected final void onPreExecute() {  
12.        final WeakTarget target = mTarget.get();  
13.        if (target != null) {  
14.            this.onPreExecute(target);  
15.        }  
16.    }  
17.  
18.      
19.    @Override  
20.    protected final Result doInBackground(Params... params) {  
21.        final WeakTarget target = mTarget.get();  
22.        if (target != null) {  
23.            return this.doInBackground(target, params);  
24.        } else {  
25.            return null;  
26.        }  
27.    }  
28.  
29.      
30.    @Override  
31.    protected final void onPostExecute(Result result) {  
32.        final WeakTarget target = mTarget.get();  
33.        if (target != null) {  
34.            this.onPostExecute(target, result);  
35.        }  
36.    }  
37.  
38.    protected void onPreExecute(WeakTarget target) {  
39.        // No default action  
40.    }  
41.  
42.    protected abstract Result doInBackground(WeakTarget target, Params... params);  
43.  
44.    protected void onPostExecute(WeakTarget target, Result result) {  
45.        // No default action  
46.    }  
47.}  
  在使用了此种方法管理线程后，虽然会增加额外的代码量（代表着工作量的提高，但其实也有限），但是却会使得线程使用更加安全可靠，大大降低了内存泄露的风险
 3.4高效地利用内存缓存技术
对于图片缓存的方式就是内存缓存技术。在Android中，开发中通常会有大量的图片，所以对于图片的处理也对性能有很大的改变，自带的有一个叫做LruCache类专门用处理来图片缓存的。这个类的特性是，当缓存的图片达到了预先设定的最大数量的时候，就会把近期比较少使用的图片给回收掉，步骤是这样的：
a)第一步，设置缓存图片的内存大小，我的是设置为手机内存的1/0,手机内存的获取方式：int MAXMEMONRY = (int) (Runtime.getRuntime() .maxMemory() / 1024);
 b）LruCache里面的键值对对应的分别是URL和他的图片资源
 c）重写了一个叫做sizeOf的方法，这个方法返回出来图片的数字。
private LruCache<String, Bitmap> mMemoryCache;
private LruCacheUtils() {
        if (mMemoryCache == null)
            mMemoryCache = new LruCache<String, Bitmap>(
                    MAXMEMONRY / 8) {
                @Override
                protected int sizeOf(String key, Bitmap bitmap) {
                    // 重写此方法来衡量每张图片的大小，默认返回图片数量。
                    return bitmap.getRowBytes() * bitmap.getHeight() / 1024;
                }

                @Override
                protected void entryRemoved(boolean evicted, String key,
                        Bitmap oldValue, Bitmap newValue) {
                    Log.v("tag", "hard cache is full , push to soft cache");
                   
                }
            };
    }

d）对图片的处理分别有下面的方法，清空图片缓存、添加缓存、从缓存中获得图片、从缓存中移除图片。毫无疑问，移除缓冲和清除缓存是必不可少的，但是为了不内存溢出。
public void clearCache() {
        if (mMemoryCache != null) {
            if (mMemoryCache.size() > 0) {
                Log.d("CacheUtils",
                        "mMemoryCache.size() " + mMemoryCache.size());
                mMemoryCache.evictAll();
                Log.d("CacheUtils", "mMemoryCache.size()" + mMemoryCache.size());
            }
            mMemoryCache = null;
        }
    }

    public synchronized void addBitmapToMemoryCache(String key, Bitmap bitmap) {
        if (mMemoryCache.get(key) == null) {
            if (key != null && bitmap != null)
                mMemoryCache.put(key, bitmap);
        } else
            Log.w(TAG, "the res is aready exits");
    }

    public synchronized Bitmap getBitmapFromMemCache(String key) {
        Bitmap bm = mMemoryCache.get(key);
        if (key != null) {
            return bm;
        }
        return null;
    }

    /**
     * 移除缓存
     * 
     * @param key
     */
    public synchronized void removeImageCache(String key) {
        if (key != null) {
            if (mMemoryCache != null) {
                Bitmap bm = mMemoryCache.remove(key);
                if (bm != null)
                    bm.recycle();
            }
        }
    }
3.5正确使用Cursor
  Cursor是Android查询数据后得到的一个管理数据集合的类，正常情况下，如果查询得到的数据量较小时不会有内存问题，而且虚拟机能够保证Cusor最终会被释放掉。
    然而如果Cursor的数据量特表大，特别是如果里面有Blob信息时，应该保证Cursor占用的内存被及时的释放掉，而不是等待GC来处理。并且Android明显是倾向于编程者手动的将Cursor close掉，因为在源代码中我们发现，如果等到垃圾回收器来回收时，会给用户以错误提示。
    所以我们使用Cursor的方式一般如下：
1.Cursor cursor = null;  
2.try {  
3.    cursor = mContext.getContentResolver().query(uri,null, null,null,null);  
4.    if(cursor != null) {  
5.        cursor.moveToFirst();  
6.        //do something  
7.    }  
8.} catch (Exception e) {  
9.    e.printStackTrace();    
10.} finally {  
11.    if (cursor != null) {  
12.       cursor.close();  
13.    }  
14.}  
    有一种情况下，我们不能直接将Cursor关闭掉，这就是在CursorAdapter中应用的情况，但是注意，CursorAdapter在Acivity结束时并没有自动的将Cursor关闭掉，因此，你需要在onDestroy函数中，手动关闭。
1.@Override  
2.protected void onDestroy() {        
3.    if (mAdapter != null && mAdapter.getCurosr() != null) {  
4.        mAdapter.getCursor().close();  
5.    }  
6.    super.onDestroy();   
7.}  
  CursorAdapter中的changeCursor函数，会将原来的Cursor释放掉，并替换为新的Cursor，所以你不用担心原来的Cursor没有被关闭。
  你可能会想到使用Activity的managedQuery来生成Cursor，这样Cursor就会与Acitivity的生命周期一致了，多么完美的解决方法！然而事实上managedQuery也有很大的局限性。
    managedQuery生成的Cursor必须确保不会被替换，因为可能很多程序事实上查询条件都是不确定的，因此我们经常会用新查询的Cursor来替换掉原先的Cursor。因此这种方法适用范围也是很小。
3.5使用9-patch图片
  android的.9.png是android系统中一种特殊的图片格式，专门用来用来处理图片大小变化后（如拉伸）的失真，不正常，如我们看到的qq聊天中的文字气泡，不管你输入的文字多少，发送后背景气泡四边的圆角是不会变样的。图片经过9patch处理后保存后名为：xxx.9.png,打开时边缘会有一圈空像素边框，上面有黑线或黑点。
  使用9-patch图片不仅能使得图片能够很好的适应屏幕显示，而且其避免了调用整张图片导致的内存占用过多。

  以上就是个人总结的关于Android开发中的内存优化原理及建议，在实际开发过程中，遵循这些开发准则，不仅能使得你的代码简洁易懂，而且能获得更优的内存性能。


参考文献：
http://mobile.51cto.com/android-410883.htm http://developer.android.com/guide/practices/design/performance.html
google的官方api等