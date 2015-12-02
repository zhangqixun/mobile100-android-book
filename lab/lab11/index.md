# HttpClient

起草人: 谢峰   1501211009        日期：15年00月00日

修改完善： 盛啸然  1501210979      日期：15年00月00日
# 

**一、实验目的**

了解HttpClient,并使用HttpClient访问网络资源。

**二、基础知识**

*简要介绍本次实验所需掌握的基础知识*
   
* 知识点1：

      HttpClient相当于一个增强版的HttpURLConnection。HttpURLConnection提供的功能，HttpClient都能提供。HttpClient并能提供对Session以及Cookie的相关处理。

* 知识点2：

      如果需要发送GET请求，创建HttpGet对象；发送POST请求，创建HttpPost对象。要发送请求参数时，调用HttpGet与HttpPost的setParams（HttpParams params）


* 知识点3：

      调用HttpClient对象的execute(HttpURLRequest request)发送请求，返回HttpResponse；调用HttpResponse的getAllHeaders()获取响应头，getEntity（）获取HttpEntity对象，该对象包括服务器的相应内容。


   

**三、实验内容及步骤**

**3.1 实验内容**

使用HttpClient的方法访问网络资源

**3.2 实验步骤**



**四、常见问题及注意事项**

*详细描述本此实验的可能会遇到的问题以及相关的注意事项*


