# Android  网络编程  （ 1501210913   胡玉  ）

	

###【   摘   要   】    

随着智能手机的快速普及，智能手机操作系统市场风生水起。在如今这个智能手机系统群雄纷争的时候，2008年Google推出了一款名为Android的开源智能手机操作系统，它采用Linux内核，开放手机联盟(OHA)成员可以任意使用和修改SDK包，系统的开源性使其具有良好的拓展性。这款软件包括了操作系统、用户界面和应用程序，即智能手机工作所需要的全部软件。Android的最大特点是其开放性体系架构，不仅具有非常好的开发、调试环境，而且还支持各种可扩展的用户体验，包括丰富的图形组件、多媒体支持功能以及强大的浏览器。
在Android应用程序开发过程中，避免不了会用到网络编程，从网上获取一些数据，或者向网络中发送信息。在本文中，介绍了两种Android的网络编程技术来实现安卓程序与互联网的信息交换。

关键词：安卓，网络编程。




###【   目   录   】



1  引言	

2  几个基本问题	
    2.1 Android平台网络相关API接口	
        2.1.1 java.net.*(标准Java接口)	
        2.1.2 Org.apache接口	
        2.1.3 Android.net.*(Android网络接口)	
    2.2 网络架构主要有两种模式B/S,C/S	
    2.3 服务器端返回客户端的内容有三种方式

3  Android的两种网络编程	
    3.1 HTTP协议——基于TCP/IP协议之上的协议	
        3.1.1 HttpURLConnection连接URL	
        3.1.2 向服务器端发送请求参数	
        3.1.3 向服务器端发送xml数据（也称为实体Entity）	
        3.1.4 利用Apache的HttpClient实现Android客户端发送实体Entity	
    3.2 基于Socket通信	
    3.3 其它网络相关技术	
        3.3.1 Android WebView控件	
        3.3.2基于SOAP协议与Webservice网络交互——KSoap2	
        3.3.3 实现服务器推送	
    3.4 PhoneGap	

4  结论	

5 参考文献	




# 1  引言


Android作为首款开源代码的操作系统，已成为国内智能手机市场的主流平台，因此，基于Android平台的网络编程可以让广大公众迅速的从手机上获得互联网上面的信息。
据Gartner最新数据显示，2013年第一季度， Android成为这一季度最大赢家，共卖出1.56亿台，市场份额为74.4%，占有绝对优势；苹果IOS则占18.2%份额，排在第二位；黑莓位列第三，市场份额为3%；微软WP以2.9%份额排在第四位置。
安卓是首款开源代码的操作系统，采用Linux内核，开放手机联盟（OHA）成员可以任意使用和修改安卓的SDK软件包。系统的开源性使其具有良好的拓展性，一方面，为众多移动应用开发者提供了良好的系统性平台，有利于移动应用的集合；另一方面，终端厂商可以针对自身的特殊需要“定制化开发”。 



# 2  几个基本问题



## 2.1 Android平台网络相关API接口



### 2.1.1 java.net.*(标准Java接口) 


 java.net.* 提供与联网有关的类，包括流、数据包套接字(socket)、Internet协议、常见Http处理等。比如：创建URL，以及 URLConnection/HttpURLConnection对象、设置链接参数、链接到服务器、向服务器写数据、从服务器读取数据等通信。这些在 Java网络编程中均有涉及。

### 2.1.2 Org.apache接口


对于大部分应用程序而言JDK本身提供的网络功能已远远不够，这时就需要Android提供的Apache HttpClient了。它是一个开源项目，功能更加完善，为客户端的Http编程提供高效、最新、功能丰富的工具包支持。
###2.1.3 Android.net.*(Android网络接口)
常常使用此包下的类进行Android特有的网络编程，如：访问WiFi，访问Android联网信息，邮件等功能。




## 2.2 网络架构主要有两种模式B/S,C/S 


  B/S就是浏览器/服务器端模式了，通过应用层的HTTP协议通信，不需要特定客户端软件，而是需要统一规范的客户端，简而言之就是Android网络浏览器（如chrome,UcWeb，QQ浏览器等等）访问web服务器端的方式了。
  C/S就客户端/服务器端模式，通过任意的网络协议通信，需要特定的客户端软件。

## 2.3 服务器端返回客户端的内容有三种方式
a）以HTML代码的形式返回。

b）以XML字符串的形式返回，做Android开发时这种方式比较多。返回的数据需要通过XML解析（SAX、DOM，Pull,等）器进行解析（必备知识）。

c）以json对象的方式返回。


#3  Android的两种网络编程
##3.1 HTTP协议——基于TCP/IP协议之上的协议
###3.1.1 HttpURLConnection连接URL
1)创建一个URL对象：

    URL url = new URL(http://www.baidu.com);

2)利用HttpURLConnection对象从网络中获取网页数据：

    HttpURLConnection conn = (HttpURLConnection) url.openConnection();

3)设置连接超时：

    conn.setConnectTimeout(6*1000);

4)对响应码进行判断：

    if (conn.getResponseCode() != 200)    
        //从Internet获取网页,发送请求,将网页以流的形式读回来
    throw new RuntimeException("请求url失败");

5)得到网络返回的输入流：

    InputStream is = conn.getInputStream();

6) 

    String result = readData(is, "GBK"); //文件流输入出文件用outStream.write

7)

    conn.disconnect();
    
    
####3.1.1.1 总结
--记得设置连接超时,如果网络不好,Android系统在超过默认时间会收回资源中断操作.

--返回的响应码200,是成功.

--在Android中对文件流的操作和JAVA SE上面是一样的.

--在对大文件的操作时,要将文件写到SDCard上面,不要直接写到手机内存上.

--操作大文件是,要一遍从网络上读,一遍要往SDCard上面写,减少手机内存的使用.这点很重要,面试经常会
被问到.

--对文件流操作完,要记得及时关闭.

###3.1.2 向服务器端发送请求参数
1)创建URL对象:
    
    URL realUrl = new URL(requestUrl);

2)通过HttpURLConnection对象,向网络地址发送请求：

    HttpURLConnection conn = (HttpURLConnection) realUrl.openConnection();

3)设置容许输出:

    conn.setDoOutput(true);

4)设置不使用缓存:

    conn.setUseCaches(false);

5)设置使用POST的方式发送:

    conn.setRequestMethod("POST");

6)设置维持长连接:

    conn.setRequestProperty("Connection", "Keep-Alive");

7)设置文件字符集:

    conn.setRequestProperty("Charset", "UTF-8");

8)设置文件长度:

    conn.setRequestProperty("Content-Length", String.valueOf(data.length));

9)设置文件类型:


    conn.setRequestProperty("Content-Type","application/x-www-form-urlencoded");

10）设置HTTP请求头：


    conn.setRequestProperty("Accept “，” image/gif, image/jpeg, image/pjpeg, image/pjpeg, application/x- shockwave-flash, application/xaml+xml, application/vnd.ms- xpsdocument, application/x-ms-xbap, application/x-ms- application, application/vnd.ms-excel, application/vnd.ms- powerpoint, application/msword, */*"）；
设置语言：
    
    conn.setRequestProperty("Accept-Language“，"zh-CN"）；
    
11)以流的方式输出.
####3.1.2.1 总结
--发送POST请求必须设置允许输出--不要使用缓存,容易出现问题.

--在开始用HttpURLConnection对象的setRequestProperty()设置,就是生成HTML文件头.

###3.1.3 向服务器端发送xml数据（也称为实体Entity）
XML格式是通信的标准语言,Android系统也可以通过发送XML文件传输数据.

1)将生成的XML文件写入到byte数组中,并设置为：

    UTF-8:byte[] xmlbyte = xml.toString().getBytes("UTF-8");

2)创建URL对象,并指定地址和参数:

    URL url = new URL(http://localhost:8080/itcast/contanctmanage.do?method=readxml);

3)获得链接:

    HttpURLConnection conn = (HttpURLConnection) url.openConnection();

4)设置连接超时:

    conn.setConnectTimeout(6* 1000);

5)设置允许输出

    conn.setDoOutput(true);

6)设置不使用缓存:

    conn.setUseCaches(false);

7)设置以POST方式传输:

    conn.setRequestMethod("POST");           

8)维持长连接:

    conn.setRequestProperty("Connection", "Keep-Alive");

9)设置字符集:

    conn.setRequestProperty("Charset", "UTF-8");

10)设置文件的总长度:

    conn.setRequestProperty("Content-Length", String.valueOf(xmlbyte.length));

11)设置文件类型:

    conn.setRequestProperty("Content-Type","text/xml; charset=UTF-8");

12)以文件流的方式发送xml数据:

    outStream.write(xmlbyte);
####3.1.3.1 总结
--我们使用的是用HTML的方式传输文件,这个方式只能传输一般在5M一下的文件.

--传输大文件不适合用HTML的方式,传输大文件我们要面向Socket编程.确保程序的稳定性

--将地址和参数存到byte数组中:byte[] data = params.toString().getBytes();

###3.1.4 利用Apache的HttpClient实现Android客户端发送实体Entity
以上为直接利用HTTP协议来实现的，其实Android已经集成了第三方开源项目org.apache.http.client.HttpClient，可以直接参考它提供的API使用。

HTTP clients encapsulate a smorgasbord of objects required to execute HTTP requests while handling cookies, authentication, connection management, and other features. Thread safety of HTTP clients depends on the implementation and configuration of the specific client.

使用POST方法进行参数传递时，需要使用NameValuePair来保存要传递的参数。另外，还需要设置所使用的字符集。

##3.2 基于Socket通信
  Socket编程比基于URL的网络编程提供了更高的传输效率，更强大的功能和更灵活的控制。在Java中已经是层次最低的网络编程接口，在Java中要直接操作协议中更低的层次，那就只有使用JNI了，那基本属于本地语言的范畴。

##3.3 其它网络相关技术
###3.3.1 Android WebView控件
在android app 中嵌入网页的形式。

通过webview可以实现：HTML<-->javascript<-->Android Java 交互，访问本地手机硬件。例如：

1）这个方法使得Java方法可以在javascript中被调用：

    webview.addJavascriptInterface(Object obj, String interfaceName))
    
2）Java中调用Javascript脚本中的方法：


    webview.loadUrl("javascript:show('"+json+"')");//调用js的show方法

###3.3.2基于SOAP协议与Webservice网络交互——KSoap2
   简单对象访问协议，简单对象访问协议（SOAP）是一种轻量的、简单的、基于 XML 的协议。
通过第三方提供的架包ksoap2-Android-assembly-2.4-jar-with-dependencies.jar，我们可以向服务器进行请求调用自己需要的服务。

###3.3.3 实现服务器推送
通过建立持久连接的方法，服务器端发送信息给手机Android用户。
####3.3.3.1 MQTT协议（实例android+php）
1、服务器端需下载安装IBM的 Really Small Message Broker (RSMB)（MQTT协议代理），并运行broker;

下载地址：http://www.alphaworks.ibm.com/tech/rsmb

2、PHP服务器端使用SAM 针对MQTT写的PHP库（下载链接为Tokudu PHPMQTT通信项目），其中send_mqtt.php是一个通过POST接收消息并且通过SAM将消息发送给RSMB的PHP脚本;

3、实例下载：

说明：http://tokudu.com/2010/how-to-implement-push-notifications-for-android/

android客户端：
https://github.com/tokudu/AndroidPushNotificationsDemo

php服务器端：https://github.com/tokudu/PhpMQTTClient

####3.3.3.2 XMPP协议（实例android+jsp）
XMPP : The Extensible Messaging and Presence Protocol (可扩展通讯和表示协议) XMPP 以 Jabber 协议为基础，而 Jabber 是即时通讯中常用的开放式协议。

下载地址: http://sourceforge.net/projects/androidpn/files/

解压服务器端，点击bin/run.bat运行，访问：http://127.0.0.1:7070/index.do，
就可以看服务器端的管理页面，用这个管理页面，就要向客户端push消息。
####3.3.3.3使用APNS (Android Push Notification Service)
http://www.push-notification.org/
APNS (Android Push Notification Service) 是一种在 android 上轻松实现 push notification 的功能的解决方案. 只需申请一个 API Key, 经过简单的步骤即可实现 push notification 的功能.

##3.4 PhoneGap 
   提到移动3G时代网络应用，不得不提一下PhoneGap了。
   phonegap利用HTML，CSS，javascript, 支持Android、iPhone,Windows Phone、Palm OS、Sybian....
   如果考虑开发一款应用，适用于大多数智能手机，phonegap是可以考虑的开发框架之一。


#4  结论
本文以基于Android平台的网络编程为题目，介绍了两种网络编程技术，基于HTTP协议的网络编程和基于Socket的网络编程。在安卓的App开发中，我们经常会需要连接到网络，在使用的时候我们一定要注意一些逻辑上面的问题，减少错出错率。


#5 参考文献
[1] 陈文，郭依正.深入理解Android网络编程：技术详解与最佳实践.机械工业出版社, 2013.8

[2] 王家林.Android4.0网络编程详解.电子工业出版社, 2012.1

