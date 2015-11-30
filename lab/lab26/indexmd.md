# 位置服务

起草人: 陈曾华   日期：15年11月30日

修改完善：徐荣涛   日期：15年11月30日

# 

**一、实验目的**

*学习一下基于位置的服务究竟是如何实现的，学会使用LocationManager找到自己的位置，能够使用反向地理编码对经纬度进行解析。*

**二、基础知识**

*简要介绍本次实验所需掌握的基础知识*
   
* 知识点1：

      熟悉LocationManager的基本用法，确定自己位置的经纬度。
      
 1.获取 LocationManager的实例可以写成：
LocationManager locationManager = (LocationManager);
getSystemService(Context.LOCATION_SERVICE);
使用LocationManager必须要先获取到它的实例， 可以调用Context的 getSystemService()方法获取到。 getSystemService()方法接收一个字符串参数用于确定获取系统的哪个服务， 这里传入 Context.LOCATION_SERVICE 即可。
 2.接着需要选择一个位置提供器来确定设备当前的位置。Android中一般有三种位置提供器可供选择，GPS_PROVIDER、NETWORK_PROVIDER 和 PASSIVE_PROVIDER。其中前两种使用的比较多，分别表示使用GPS定位和使用网络定位。这两种定位方式各有特点，GPS定位的精准度比较高，但是非常耗电，而网络定位的精准度稍差，但耗电量比较少。
将选择好的位置提供器传入到 getLastKnownLocation()方法中， 就可以得到一个 Location对象，如下所示
String provider = LocationManager.NETWORK_PROVIDER;
Location location = locationManager.getLastKnownLocation(provider);
这个 Location 对象中包含了经度、纬度、海拔等一系列的位置信息，然后从中取出需要的那部分数据即可。如果想让定位的精度尽量高一些，但又不确定GPS定位的功能是否已经启用，此时就可以先判断一下有哪些位置提供器可用，如下所示：
List<String> providerList = locationManager.getProviders(true);
 3.LocationManager 提供了一个requestLocationUpdates()方法，只要传入一个LocationListener的实例，并简单配置几个参数就可以实现随时随地获取当前设备的位置信息，写法如下：
    locationManager.requestLocationUpdates(LocationManager.GPS_PROVIDER, 5000, 10,new LocationListener() {
        @Override
         public void onStatusChanged(String provider, int status, Bundle extras) {
         }
        @Override
        public void onProviderEnabled(String provider) {
        }
        @Override
        public void onProviderDisabled(String provider) {
        }
        @Override
        public void onLocationChanged(Location location) {
        }
    });
这里 requestLocationUpdates()方法接收四个参数，第一个参数是位置提供器的类型，第二个参数是监听位置变化的时间间隔，以毫秒为单位，第三个参数是监听位置变化的距离间隔，以米为单位，第四个参数则是LocationListener监听器。这样的话，LocationManager每隔5秒钟会检测一下位置的变化情况，当移动距离超过10米的时候，就会调用 LocationListener的 onLocationChanged()方法，并把新的位置信息作为参数传入。


* 知识点2：

      熟悉GeocodingAPI的用法，使用反向地理编码的接口，对经纬度进行解析。
 1.谷歌提供了一套 Geocoding API，使用它可以完成反向地理编码的工作，这里只介绍一下GeocodingAPI的简单用法，更详细的用法请参考官方文档： https://developers.google.com/maps/documentation/geocoding/。GeocodingAPI利用了HTTP 协议。在手机端可以向谷歌的服务器发起一条HTTP请求，并将经纬度的值作为参数一同传递过去，然后服务器会将这个经纬值转换成看得懂的位置信息，再将这些信息返回给手机端，最后手机端去解析服务器返回的信息，并进行处理就可以了。GeocodingAPI 中规定了很多接口，其中反向地理编码的接口如下：
http://maps.googleapis.com/maps/api/geocode/json?latlng=40.714224,-73.961452&sensor=true_or_false
其中 http://maps.googleapis.com/maps/api/geocode/是固定的，表示接口的连接地址。json表示希望服务器能够返回JSON格式的数据，这里也可以指定成xml。latlng=40.714224,-73.96145表示传递给服务器去解码的经纬值是北纬40.714224度，西经73.96145度。 sensor=true_or_false 表示这条请求是否来自于某个设备的位置传感器，通常指定成 false 即可。
 2.如果发送 http://maps.googleapis.com/maps/api/geocode/json?latlng=40.714224,-73.96145&sensor=false这样一条请求给服务器，我们将会得到一段非常长的 JSON 格式的数据，其中会包括如下部分内容："formatted_address" : "277 Bedford Avenue,布鲁克林纽约州11211美国"。从这段内容中就可以看出北纬40.714224度，西经73.96145度对应的地理位置是在哪里了。然后就是从服务器返回的JSON数据中解析出想要的那部分信息。


* 知识点3：

      知识点介绍
**三、实验内容及步骤**

**3.1 实验内容**

*1.找到设备当前位置的经纬度

*2.使用反向地理编码，获得中文地理位置。

**3.2 实验步骤**

1.新建一个 LocationTest项目，修改 activity_main.xml 中的代码，如下所示：
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:layout_width="match_parent"
android:layout_height="match_parent" >
<TextView
android:id="@+id/position_text_view"
android:layout_width="wrap_content"
android:layout_height="wrap_content" />
</LinearLayout>

2.然后修改 MainActivity 中的代码，如下所示：
public class MainActivity extends Activity {
private TextView positionTextView;
private LocationManager locationManager;
private String provider;
@Override
protected void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);
positionTextView = (TextView) findViewById(R.id.position_text_view);
locationManager = (LocationManager) getSystemService(Context.
LOCATION_SERVICE);
// 获取所有可用的位置提供器
List<String> providerList = locationManager.getProviders(true);
if (providerList.contains(LocationManager.GPS_PROVIDER)) {
provider = LocationManager.GPS_PROVIDER;
} else if (providerList.contains(LocationManager.NETWORK_PROVIDER)) {
provider = LocationManager.NETWORK_PROVIDER;
} else {
// 当没有可用的位置提供器时，弹出Toast提示用户
Toast.makeText(this, "No location provider to use",
Toast.LENGTH_SHORT).show();
return;
}
Location location = locationManager.getLastKnownLocation(provider);
if (location != null) {
// 显示当前设备的位置信息
showLocation(location);
}
locationManager.requestLocationUpdates(provider, 5000, 1,
locationListener);
}
protected void onDestroy() {
super.onDestroy();
if (locationManager != null) {
// 关闭程序时将监听器移除
locationManager.removeUpdates(locationListener);
}
}
LocationListener locationListener = new LocationListener() {
@Override
public void onStatusChanged(String provider, int status, Bundle
extras) {
}
@Override
public void onProviderEnabled(String provider) {
}
@Override
public void onProviderDisabled(String provider) {
}
@Override
public void onLocationChanged(Location location) {
// 更新当前设备的位置信息
showLocation(location);
}
};
private void showLocation(Location location) {
String currentPosition = "latitude is " + location.getLatitude() + "\n"
+ "longitude is " + location.getLongitude();
positionTextView.setText(currentPosition);
}
}

3.获取设备当前的位置信息需要声明权限，因此修改AndroidManifest.xml中的代码，如下所示：
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
package="com.example.locationtest"
android:versionCode="1"
android:versionName="1.0" >
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
</manifest>
4.运行程序就可以看到手机当前位置的经纬度信息了，如下图所示：


**四、常见问题及注意事项**

*详细描述本此实验的可能会遇到的问题以及相关的注意事项*






