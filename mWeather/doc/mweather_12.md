# 通过Service后台获取数据


    小组成员: 姜昊、杨超、黄凯凯、刘冶、邹勇杰    最终完成日期：15年11月30日
# 

**一、简介**

通过Service后台获取数据。

**二、基础知识**

*简要介绍本模块所需掌握的基础知识*
   
* 知识点1：Service的生命周期解析 

    **1：使用context.startService() 启动Service **  
    其生命周期为context.startService() -->onCreate()-->onStartCommand()-->Service is running-->context.stopService()-->onDestroy() ->Service shut down,生命周期如图  
    ![](life1.PNG)      
    如果Service还没有运行，则android先调用onCreate()然后调用onStart()；     
    如果Service已经运行，则只调用onStart()，所以一个Service的onStart方法可能会重复调用多次。    
    程序验证结果如图    
    ![](runresult.png)  
    由图中可得结论  
    1）：如果没有调用stopService(),再次调用startService()时只会调用onStartCommand(),不会调用onCreate()    
    2）：如果调用了stopService(),再次调用startService()时会再次调用onCreate()   
    **2：bindService()启动Service **    
    其生命周期context.bindService()->onCreate()->onBind()->Service running-->onUnbind() -> onDestroy() ->Service stop，其生命周期如图所示  
    ![](life2.PNG)  
    onBind将返回给客户端一个IBind接口实例，IBind允许客户端回调服务的方法，比如得到Service运行的状态或其他操作。  
    这个时候把调用者（Context，例如Activity）会和Service绑定在一起，Context退出了，
    Service就会调用onUnbind->onDestroy相应退出。    
    所以调用bindService()的生命周期为：onCreate --> onBind(只一次，不可多次绑定) --> onUnbind --> onDestory。     
    一但销毁activity它就结束，如果按home把它放到后台，那他就不退出。    
    程序验证结果如图：  
    ![](runresult2.png)

    由图中可得结论      
    1）：bindService()启动service时不会调用onStartCommand()方法     
    2）unbindService()会先调用onUnbind()方法，然后调用onDestory()方法   
    3）：当Service onBind之后，再点击startService按钮 去绑定服务，不会调用onCreate()方法和onBind()方法


* 知识点2：Service的实现 
    实现Service需要继承android.app.Service类， 然后重写onStartCommand 、 onBind和onCreate等方法。
        – 如果在Activity等组件中通过Context.startService()方法启动Service，需要重写onStartCommand方法。
        – 如果在Activity等组件中通过Context.bindService()方法绑定Service， 需要重写onBind方法。



* 知识点3：Service的调用    
使用context.startService() 启动Service      
        // 启动一个 Activity
        startActivity(new Intent(this, LocalService1.class));
        
        // 停止一个 Activity
        stopService(new Intent(this, LocalService1.class)); 
bindService()启动Service时，需先定义一个ServiceConnection实例   
        private ServiceConnection connection = new ServiceConnection() {
            @Override
            public void onServiceConnected(ComponentName name, IBinder service) {
                System.out.println("Service Connected");
                try {
                    myService = ((MyService.MyBinder)service).getMyservice();
                    startService(serviceIntent);
                }catch (Exception e){
                    e.printStackTrace();
                }

            }

            @Override
            public void onServiceDisconnected(ComponentName name) {
                System.out.println("Service Disconnected");
                myService = null;
            }
        };
        protected void serviceStart(){//启动服务
            serviceIntent = new Intent(MainActivity.this,MyService.class);
            bindService(serviceIntent,connection, Context.BIND_AUTO_CREATE);
        }

        protected void serviceStop(){//解除绑定
            myService.onUnbind(new Intent());
        }


   

**三、主要思路及步骤**

**3.1 主要思路**

1：新建WeatherService类，继承android.app.Service类  
2：重写 OnBind ()、onStartCommand()、onDestory()函数    
3：MainActivity中检查网络是否连接，若已连接，启动服务    


**3.2 实践步骤**

1：新建WeatherService类，继承android.app.Service类      

         public class WeatherService extends Service 
2：重写 OnBind ()、onStartCommand()、onDestory()函数    
    声明了两个变量--计时器和时间间隔，在Weather类的构造函数中内置了获取数据的函数，也可以有其他的实现方法

        static final int UPDATE_INTERVAL = 2*60*60*1000;//更新时间间隔
        private Timer timer = new Timer();//计时器

        @Nullable
        @Override
        public IBinder onBind(Intent intent) {
            System.out.println("onBind Service");
            return null;
        }
        @Override
        public int onStartCommand(final Intent intent, int flags, int startId) {
            System.out.println("Service Start");

                timer.scheduleAtFixedRate(new TimerTask() {
                    @Override
                    public void run() {
                       //获取天气数据
                       SharedPreferences sp = getSharedPreferences("positionData", Context.MODE_PRIVATE);
                       String current_position = sp.getString("current_position", "北京");
                       String supCity = sp.getString("supCity", "北京");
                       Weather weather = new Weather(current_position, supCity);
                       if (weather.getStatus().equals("ok")) {
                           weatherNotify(weather);
                       }
                    }
                }, UPDATE_INTERVAL, UPDATE_INTERVAL);
            return START_STICKY;
        }
        @Override
        public void onDestroy() {
            System.out.println("Service Destroy");
            super.onDestroy();
        }
3：MainActivity中检查网络是否连接，若已连接，启动服务   

        if(NetUtil.getNetWorkState(this)==NetUtil.NETWORK_NONE){
            Toast.makeText(MainActivity.this,"无网络，请检查网络连通性",Toast.LENGTH_SHORT).show();
        }else {
            updateWeather();//更新天气数据
            startService(new Intent(MainActivity.this, WeatherService.class));//启动后台数据更新服务
        }
        }catch (Exception e){
            e.printStackTrace();
        }


**四、常见问题及注意事项**

1：startService 和bindService 的区别     

| 类别 |  区别  |
| -- | -- |
| startService 启动的服务| 主要用于启动一个服务执行后台任务，不进行通信。停止服务使用stopService |
| bindService 启动的服务 | 该方法启动的服务要进行通信。停止服务使用unbindService |
| startService 同时也 bindService 启动的服务 | 停止服务应同时使用stepService与unbindService |   
2：Service 与 Thread 的区别

1). Thread：Thread 是程序执行的最小单元，它是分配CPU的基本单位。可以用 Thread 来执行一些异步的操作。

2). Service：Service 是android的一种机制，当它运行的时候如果是Local Service，那么对应的 Service 是运行在主进程的 main 线程上的。如：onCreate，onStart 这些函数在被系统调用的时候都是在主进程的 main 线程上运行的。如果是Remote Service，那么对应的 Service 则是运行在独立进程的 main 线程上。  
Thread 的运行是独立于 Activity 的，也就是说当一个 Activity 被 finish 之后，如果你没有主动停止 Thread 或者 Thread 里的 run 方法没有执行完毕的话，Thread 也会一直执行。因此这里会出现一个问题：当 Activity 被 finish 之后，你不再持有该 Thread 的引用。另一方面，你没有办法在不同的 Activity 中对同一 Thread 进行控制。

