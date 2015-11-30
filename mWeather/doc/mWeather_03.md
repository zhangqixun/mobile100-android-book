# 网络状态检查


    小组成员: 第八组   最终完成日期：15年11月30日
# 

**一、简介**

   手机的网络状态有三种:没有网络,wifi,还有就是移动流量。天气预报项目中需要连接网络获取网络数据,这一节就是讲解如何判断手机网络处于哪种网络状态下。

**二、基础知识**


   
* 知识点1：

    要想获得手机的网络状态我们需要在AndroidManifest.xml文件中加入相应的权限。
       <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
 

* 知识点2：

    ConnectivityManager主要管理和网络连接相关的操作。在天气预报项目中，主要用的方法是getNetworkInfo(int networkType)和getActiveNetworkInfo().
    getNetworkInfo(int networkType) 获取特定网络类型的链接状态信息getActiveNetworkInfo() 获取当前连接可用的网络。
    但是getNetworkInfo(int networkType)方法显示过时，所以我们可以getActiveNetworkInfo()代替。


* 知识点3：

     NetworkInfo类包含了对wifi和mobile两种网络模式连接的详细描述,通过其getState()方法获取的State对象则代表着连接成功与否等状态。NetworkInfo有两个枚举类型的成员，NetworkInfo.DetailedState和NetworkInfo.State，用于查看当前网络的状态。其中NetworkInfo.State的值包括CONNECTING,CONNECTED,SUSPENDED,DISCONNECTING,DISCONNECTED,UNKNOWN。NetworkInfo类中还包括了getType()方法，用于获取当前网络的类型是属于MOBILE或者是WIFI。


   

**三、主要思路及步骤**

**3.1 主要思路**

主要思路就是通过ConnectivityManager对象获得当前网络的状态，并判断当前网络状态属于哪一种。

**3.2 实践步骤**

**1.getNetworkInfo()方法 **
    
    public class NetUtil {
    public static final int NETWORK_NONE = 0;
    public static final int NETWORK_WIFI = 1;
    public static final int NETWORK_MOBILE = 2;

    public static int getNetworkState(Context contetx) {
        ConnectivityManager conManager = (ConnectivityManager) contetx.getSystemService(Context.CONNECTIVITY_SERVICE);
        //WIFI
        NetworkInfo info = conManager.getActiveNetworkInfo();
        if(info.getType() == ConnectivityManager.TYPE_WIFI)
            return NETWORK_WIFI;
        NetworkInfo[] networkInfo = conManager.getAllNetworkInfo();
        State state=conManager.getNetworkInfo(ConnectivityManager.TYPE_WIFI).getState();
        if(state==State.CONNECTED||state==State.CONNECTING)
            return NETWORK_WIFI;

        //MOBILE
         state=conManager.getNetworkInfo(ConnectivityManager.TYPE_MOBILE).getState();
        if(state==State.CONNECTED||state==State.CONNECTING)
            return NETWORK_MOBILE;
        return NETWORK_NONE;
    }
}


**2.getActiveNetworkInfo()**方法 

    ConnectivityManager conManager = (ConnectivityManager) contetx.getSystemService(Context.CONNECTIVITY_SERVICE);   
    NetworkInfo info = conManager.getActiveNetworkInfo();  
    if (info == null)
        return NETWORK_NONE; 
    //WIFI
    if (info.getType() == ConnectivityManager.TYPE_WIFI)  
        return NETWORK_WIFI;  
    //MOBILE  
    if (info.getType() == ConnectivityManager.TYPE_MOBILE)              
      return NETWORK_MOBILE;


**四、常见问题及注意事项**

本部分开发过成中就是遇到的关于getNetworkInfo()过时的问题，可以使用getActiveNetworkInfo()方法代替。
