# 城市切换、搜索及天气更新（1501210908 郝建勇）

## 
作者：郝建勇

学号：1501210908


在Android天气预报中，城市的切换、搜索以及天气的更新是整个App很大的一个模块，Android天气预报中城市切换、搜索主要在selectCity中来完成，天气更新主要在Service和主线程里面来更新。

在介绍这些部分之前，先要介绍一下Android里面数据传递方法以及Activity之间数据的传输，Android开发中，在不同模块(如Activity)间经常会有各种各样的数据需要相互传递，常用的几种方法都它们各有利弊，有各自的应用场景。

Android里面数据传递方法：

1、	利用Intent对象携带简单数据：利用Intent的Extra部分来存储我们想要传递的数据，可以传送int, long, char等一些基础类型,对复杂的对象就无能为力了。利用Intent对象携带如ArrayList之类复杂些的数据，利用Intent的Extras部分来传递，利用Intent来传递值对象或者List<object>时，要求这些对象的类都要实现Serializable或者Parcelable接口序列化，然后再传输的时候，还要对list强制转化为序列化以后的数据，才能够放到Intent里面，就像下面这样：

 发送方：
 
    Intent intent=new Intent();
    Bundle bundle=new Bundle();
    bundle.putSerializable("todayWeather",todayWeather);
    bundle.putSerializable("forecastWeather",(Serializable) forecastWeatherList);
    intent.putExtras(bundle);
接收方：

    Bundle bundle=intent.getExtras();
    TodayWeather todayWeather=(TodayWeather)bundle.getSerializable("todayWeather");
    List<ForecastWeather>forecastWeatherList=(List<ForecastWeather>)bundle.getSerializable("forecastWeather");

2、	通过实现Serializable接口，利用Java语言本身的特性，通过将数据序列化后，再将其传递出去。这种发放实现简单，只需要在类中声明Serializable接口即可，但是效率可能会受到一定影响。

3、	通过实现Parcelable接口：这个是通过实现Parcelable接口，把要传的数据打包在里面，然后在接收端自己分解出来。这个是Android独有的，在其本身的源码中也用得很多，效率要比Serializable相对要好。

4、	通过单例模式实现参数传递：单例模式的特点就是可以保证系统中一个类有且只有一个实例。这样很容易就能实现，在A中设置参数，在B中直接访问了。这是几种方法中效率最高的。由于这种方式不大常见，所以列举代码如下：
   4.1  定义一个单实例的类

    //单例模式 
    public class XclSingleton  
     {   //单例模式实例  
        private static XclSingleton instance = null;
        //synchronized 用于线程安全，防止多线程同时创建实例
        public synchronized static XclSingleton getInstance(){  
        if(instance == null){  
            instance = new XclSingleton();  
        } 
        return instance;  
        }
    
    final HashMap<String, Object> mMap;  
    public XclSingleton() 
    {   mMap = new HashMap<String,Object>();  }
    
    public void put(String key,Object value){  
        mMap.put(key,value);  
    } 
    
    public Object get(String key) {  return mMap.get(key);  }  
    }  
4.2 设置参数

     //通过单例模式传参数的例子  
     XclSingleton.getInstance().put("key1", "value1");  
     XclSingleton.getInstance().put("key2", "value2");  
     Intent intentSingleton = new Intent();                
     intentSingleton.setClass(MainActivity.this,SingletonActivity.class); 
     startActivity(intentSingleton);
4.3 接收参数

       HashMap<String,Object> map = XclSingleton.getInstance().mMap;
       String sResult = "map.size() ="+map.size();       
        //遍历参数 
         Iterator iter = map.entrySet().iterator(); 
         while(iter.hasNext())  
         {  Map.Entry entry = (Map.Entry)iter.next();  
            Object key = entry.getKey();  
            Object value = entry.getValue();  
               sResult +="\r\n key----> "+(String)key; 
           sResult +="\r\n value----> "+(String)value;
        }
Activity之间数据的传输：

1、	基于消息的传输：利用Intent传输。

2、	基于外部存储的传输：在Android中，预设了一些快捷便利类和模块，更好的支持不同类别数据的存取。如果，需要存储的是一些小数据量的配置信息，可以选择Preference，它等同于传统意义上的设置文件。Preference提供了一些基于key/value的存取接口，可以放置一些简单的基本数据或者派生了Parcelable接口的对象。一个很好的应用场景是Cookie的存放。你在登录界面获得了一份Cookie，你可以把它扔进Preference，谁想要谁去拿，再也不要来来回回的折腾了。Preference适合于小数据、设置信息，如果大数据，你可以考虑使用数据库。在Android中，使用的是Sqlite，相关的类，堆放在android.database名字空间下

3、	基于Service的传输：既然存在外部太慢，那么还是在内存级别解决问题好了，这时候，你可能就需要请出Android四大组件之一的Service了。Service设计的本意，就是提供一些后台的服务，数据存取，也可以归于其职责的一部分。Service是提供了直连机制，调用的Activity，可以通过bindService方法，与目标Service建立一条数据通路，拿到IBinder。这样，通过Android提供的IPC模型，就可以进行远程方法的调用和数据的传输了。 如上，通过这种模式，可以解决一定问题。 

4、	利用Application传输：如果你需要在不同页面之间共有某个内存对象，很合适的一种方式是把它们扔到Application里面。Application是Context的一个子类。它的生命周期会贯穿整个应用所有组件的生命旅途，因此，放在其中的对象，不会被处理掉。在Activity中，可以通过getApplication接口，随时获得Application对象的引用，用于实现一些全局对象的存储，和处理，真是最合适不过的地方了。

那么熟悉了以上概念，来看我们的天气预报程序，这些数据传输的方法会贯穿在整个天气预报项目当中。先来看城市的切换：

城市切换：

    if(v.getId()==R.id.title_city_manager){
            Intent intent = new Intent(MainActivity.this, SelectCity.class);
            startActivityForResult(intent, 1);
        }
当用户点击了切换城市按钮之后，启动SelectCity并等待返回结果。

      mlistView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
            @Override
            public void onItemClick(AdapterView<?> adapterView, View view, int i, long l) {
                Intent intent = new Intent();
                if(!listItems.get(i).get("city").equals(""))
                    mTitle_city.setText("当前城市：" + listItems.get(i).get("city"));
                intent.putExtra("cityCode", listItems.get(i).get("number"));
                intent.putExtra("city", listItems.get(i).get("city"));
                setResult(RESULT_OK, intent);
                finish();
            }
        });
        
当用户点击了一个Item之后，根据Item的位置，在数据源相应位置把数据取出来，在天气预报项目中，也就是在listItems中把相应位置的城市信息取出来，放到intent中返回，由MainActivity中的onActivityResult进行处理。


      protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == 1 && resultCode == RESULT_OK) {
            cityCode= data.getStringExtra("cityCode");
            String cityString = data.getStringExtra("city");
            SharedPreferences sharedPreferences = getSharedPreferences("cityCode",MODE_PRIVATE);
            SharedPreferences.Editor editor = sharedPreferences.edit();
            editor.putString("cityCode", cityCode);
            editor.putString("cityName", cityString);//存入数据
            editor.commit();//提交修改
            if (cityString.equals("")){
                Intent intent = new Intent(MainActivity.this, SelectCity.class);
                startActivity(intent);
                finish();
            }
            updateTodayWeather(cityCode);
        }
    }
当Activity得到返回结果以后，取出其中的城市名称和城市代码，存入SharedPreferences中，然后用updateTodayWeather(cityCode)进行天气更新。
将城市代码和名称存入SharedPreferences中，下次程序进入读取默认城市，就会默认本次读取的城市。这样就实现了城市的切换。

城市搜索

对城市编辑搜索框search_edit添加TextChange监听，当搜索框search_edit内容改变时，listview的内容重新加载有关输入内容的城市

    search_edit.addTextChangedListener(new TextWatcher() {
            @Override
            public void onTextChanged(CharSequence s, int start, int before, int count) {
                dispalyList(s.toString()); //展示结果
            }
        });
        
    void dispalyList(String s){
        if (s == null || s.trim().length() == 0){
            listItems=application.getCityItemsList();
            SimpleAdapter adapter = new SimpleAdapter(SelectCity.this,listItems,R.layout.city_items,new String[] {"province","city"},new int[]{R.id.list_province,R.id.list_city});
            ListView mlistView = (ListView)findViewById(R.id.city_list);
            mlistView.setAdapter(adapter);
        }
        listItems=application.getCityItemsList();
        List<Map<String,String>> temp_listItems=new ArrayList<Map<String, String>>();
        for (Map<String,String> item : listItems) {
            if (item.get("city").indexOf(s) >= 0) {
                temp_listItems.add(item);
            }
        }
        listItems=temp_listItems;
        SimpleAdapter adapter = new SimpleAdapter(SelectCity.this,listItems,R.layout.city_items,new String[] {"province","city"},new int[]{R.id.list_province,R.id.list_city});
        ListView mlistView = (ListView)findViewById(R.id.city_list);
        mlistView.setAdapter(adapter);
        mlistView.invalidateViews();
}

当search_edit的内容改变以后，首先获得改变以后的内容，然后对数据源进行逐条对比，如果数据源的某一项数据包含了改变后的内容，就把该数据加入到新的List数据源中，然后将原数据源清空，把新的数据列表赋值给原数据源，让listview进行重新加载。就实现了城市的搜索。不过要注意的是当search_edit变为空以后，要重新加载城市列表，以及每次mlistView加载的内容改变以后，要调用invalidateViews()方法来动态刷新数据。

天气更新

当用户点击了更新按钮以后，执行updateTodayWeather(cityCode)，其中cityCode是事先从SharedPreferences中读取出来的城市代码。

     public void updateTodayWeather(final String cityCode){
        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    List<ForecastWeather> forecastWeatherList=null;
                    TodayWeatherDao todayWeatherDao=new TodayWeatherDao();
                    TodayWeather todayWeather=todayWeatherDao.getTodayWeather(cityCode);
                    forecastWeatherList=todayWeatherDao.getForecastWeather();
                    //Log.d("更新天气：", todayWeather.toString());
                    if(forecastWeatherList!=null && todayWeather!=null){
                        Message msg = new Message();
                        msg.what = UPDATE_TODAY_WEATHER;
                        msg.obj = forecastWeatherList;
                        Bundle data = new Bundle();
                        data.putSerializable("todayWeather", todayWeather);
                        msg.setData(data);
                        handler.sendMessage(msg);
                        Log.d("myweather","未来天气获取成功");
                    }
                }
                catch (Exception e){
                    Log.d("updateTodayWeather","更新天气失败！");
                }
            }
        }).start();
        }

     private Handler handler=new Handler(){
        public void handleMessage(Message message){
            switch (message.what){
                case UPDATE_TODAY_WEATHER:
                    TodayWeather todayWeather = (TodayWeather)message.getData().get("todayWeather");
                    List<ForecastWeather> list = (List<ForecastWeather>)message.obj;
                    setWeather(todayWeather,list);
                    break;
                default:
                        break;
            }
        }
    };
由updateTodayWeather(cityCode)查询得到的List<ForecastWeather>和TodayWeather以消息的方式把他们封装起来发送给消息处理队列handler，handler得到数据之后，执行setWeather(todayWeather,list)进行UI的更新。其中TodayWeatherDao根据cityCode进行天气预报的数据的解析，并提供了返回List<ForecastWeather>和TodayWeather的方法。

天气更新的另一种方法是在Service中进行更新，在MainActivity的oncreate方法中，当在有网的状态下，启动后台更新Service，代码如下：

    if(NetUtil.getNetworkState(this)!=NetUtil.NETWORK_NONE){
            Log.d("myWeather","网络OK");
            Intent intent=new Intent(getBaseContext(),AutoUpdateService.class);
            startService(intent);//发送Intent启动Service
        }
        
在Service的onStartCommand中，查询默认城市代码，进行天气的更新：Service中的代码如下：

    public int onStartCommand(Intent intent, int flags, int startId) {
        Toast.makeText(this,"正在后台更新天气......",Toast.LENGTH_SHORT).show();
        if(NetUtil.getNetworkState(this)!=NetUtil.NETWORK_NONE){
            Log.d("myWeather","网络OK");
            SharedPreferences sharedPreferences=getSharedPreferences("cityCode",MODE_PRIVATE);
            String cityCode=sharedPreferences.getString("cityCode","101010100");
            updateTodayWeather(cityCode);
        }
        else {
            Log.d("myWeather","网络挂了");
        }
        return super.onStartCommand(intent, flags, startId);
        }
    public void updateTodayWeather(final String cityCode){
        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    Intent intent=new Intent();
                    intent.setAction("com.example.asus.weather.MainActivity");
                    TodayWeatherDao todayWeatherDao=new TodayWeatherDao();
                    TodayWeather todayWeather=todayWeatherDao.getTodayWeather(cityCode);
                    List<ForecastWeather> forecastWeatherList=todayWeatherDao.getForecastWeather();
                    Log.d("后台更新天气1：", todayWeather.toString());
                    Bundle bundle=new Bundle();
                    bundle.putSerializable("todayWeather",todayWeather);
                    bundle.putSerializable("forecastWeather",(Serializable) forecastWeatherList);
                    intent.setAction("com.example.asus.weather.MainActivity");
                    intent.putExtras(bundle);
                    sendBroadcast(intent);
                }
                catch (Exception e){
                    Log.d("updateTodayWeather","更新天气失败！");
                }
            }
        }).start();
    }
    
此处的updateTodayWeather跟MainActivity中的updateTodayWeather基本类似，唯一不同的是，Service中updateTodayWeather把查询到的TodayWeather和List<ForecastWeather>以序列化的方式封装到bundle中用intent传送，然后以sendBroadcast(intent)给MainActivity发送广播。

然后MainActivity接收到数据后，将TodayWeather和List<ForecastWeather>数据取出来，用setWeather(todayWeather,forecastWeatherList)进行UI的更新，MainActivity中的代码如下：

    private class DataReceiver extends BroadcastReceiver {//继承自BroadcastReceiver的子类
        @Override
        public void onReceive(Context context, Intent intent) {//重写onReceive方法
            Bundle bundle=intent.getExtras();
            TodayWeather todayWeather=(TodayWeather)bundle.getSerializable("todayWeather");
            List<ForecastWeather> forecastWeatherList=(List<ForecastWeather>)bundle.getSerializable("forecastWeather");
            setWeather(todayWeather,forecastWeatherList);
            Log.d("hjy","Service后台更新数据成功！");
        }
    }
    @Override
    protected void onStart() {
        dataReceiver = new DataReceiver();
        IntentFilter filter = new IntentFilter();//创建IntentFilter对象
        filter.addAction("com.example.asus.weather.MainActivity");
        registerReceiver(dataReceiver, filter);//注册Broadcast Receiver
        super.onStart();
    }


以上就是这几个模块的基本实现。下面来介绍一下模块实现过程中遇到的一些问题。

在Android中，Activity主要负责前台页面的展示，Service主要负责需要长期运行的任务，所以在我们实际开发中，就会常常遇到Activity与Service之间的通信，我们一般在Activity中启动后台Service，通过Intent来启动，Intent中我们可以传递数据给Service，而当我们Service执行某些操作之后想要更新UI线程，我们应该怎么做呢？接下来我就介绍两种方式来实现Service与Activity之间的通信问题。

•	通过Binder对象

当Activity通过调用bindService(Intent service, ServiceConnection conn,int flags),我们可以得到一个Service的一个对象实例，然后我们就可以访问Service中的方法。这种方法在我的天气预报中项目中没有具体实现，后期将会用这种方式实现更新UI。

Service：

    public class LocalService extends Service {  
       private final IBinder binder = new LocalBinder();  

       public class LocalBinder extends Binder {  
          LocalService getService() {  
            return LocalService.this;  
         }  
       }  
  
       public IBinder onBind(Intent intent) {  
        return binder;  
      }  
    }  
Activity：
    
    public class BindingActivity extends Activity {  
    LocalService localService;  

        private ServiceConnection mConnection = new ServiceConnection() {  
            public void onServiceConnected(ComponentName className,IBinder localBinder) 
            { 
               localService = (LocalBinder) localBinder.getService(); 
            }  
            public void onServiceDisconnected(ComponentName arg0) 
            {
               localService = null;  
            }  
        }; 

        protected void onStart() {  
            super.onStart();  
            Intent intent = new Intent(this, LocalService.class);  
            bindService(intent, mConnection, Context.BIND_AUTO_CREATE);  
        }  
  
        protected void onStop() {  
            super.onStop();  
            unbindService(mConnection);  
            }   

        public void printRandomNumber{  
          int num = localService.getRandomNumber();  
          System.out.println(num);
        }
    }  
    
使用context.bindService()启动Service会经历：

context.bindService()->onCreate()->onBind()->Service running 

onUnbind() -> onDestroy() ->Service stop

Activity能进行绑定得益于Service的接口onBind()。Service和Activity的连接可以用ServiceConnection来实现，需要实现一个新的ServiceConnection，重写onServiceConnected和onServiceDisconnected方法。执行绑定，调用bindService方法，传入一个选择了要绑定的Service的Intent（显式或隐式）和一个你实现了的ServiceConnection实例。一旦连接建立，你就能通Service的接口onBind()得到serviceBinder实例进而得到Service的实例引用。一旦Service对象找到，就能得到它的公共方法和属性。但这种方式，一定要在同一个进程和同一个Application里。

•	通过broadcast(广播)的形式

我在实现Service与Activity通信时，也就是在上面的例子中通过Service更新天气时，用的就是广播，通过sendBroadcast(intent)这种方式来发送广播。

      Intent intent=new Intent();
      intent.setAction("com.example.asus.weather.MainActivity");
      TodayWeatherDao todayWeatherDao=new TodayWeatherDao();
      TodayWeather todayWeather=todayWeatherDao.getTodayWeather(cityCode);
      List<ForecastWeather> forecastWeatherList=todayWeatherDao.getForecastWeather();
      Bundle bundle=new Bundle();
      bundle.putSerializable("todayWeather",todayWeather);
      bundle.putSerializable("forecastWeather",(Serializable) forecastWeatherList);
      intent.setAction("com.example.asus.weather.MainActivity");
      intent.putExtras(bundle);
      sendBroadcast(intent);
MainActivity接收数据的代码就不再赘述了。



