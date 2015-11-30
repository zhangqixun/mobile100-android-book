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
      
1. 获取 LocationManager的实例可以写成：
LocationManager locationManager = (LocationManager);
getSystemService(Context.LOCATION_SERVICE);
使用LocationManager必须要先获取到它的实例， 可以调用Context的 getSystemService()方法获取到。 getSystemService()方法接收一个字符串参数用于确定获取系统的哪个服务， 这里传入 Context.LOCATION_SERVICE 即可。
2.接着需要选择一个位置提供器来确定设备当前的位置。Android中一般有三种位置提供器可供选择，GPS_PROVIDER、NETWORK_PROVIDER 和 PASSIVE_PROVIDER。其中前两种使用的比较多，分别表示使用GPS定位和使用网络定位。这两种定位方式各有特点，GPS定位的精准度比较高，但是非常耗电，而网络定位的精准度稍差，但耗电量比较少。



* 知识点2：

      熟悉GeocodingAPI的用法，使用反向地理编码的接口，对经纬度进行解析。


* 知识点3：

      知识点介绍
**三、实验内容及步骤**

**3.1 实验内容**

*简要介绍本次实验的主要内容*

**3.2 实验步骤**

*详细描述本次实验的具体步骤*



**四、常见问题及注意事项**

*详细描述本此实验的可能会遇到的问题以及相关的注意事项*






