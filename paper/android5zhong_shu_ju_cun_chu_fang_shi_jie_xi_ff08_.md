# Android 5种数据存储方式解析（1501210903-宫翠峰）


# **目录**



前言

一、SharedPreferences存储数据

二.文件存储数据

三.SQLite数据库存储数据

四ContentProvider存储数据

五.网络存储数据

六. 小结


#**前言**

~~~~作为一个完整的应用程序，数据存储操作是必不可少的。Android 提供了5种方式存储数据：使用SharedPreferences存储数据；文件存储数据；SQLite数据库存储数据；使用ContentProvider存储数据；网络存储数据。 无论是什么平台，什么开发环境，什么软件程序，数据都是核心。对于开发平台来讲，如果对数据的存储有良好的支持，那么对应用程序的开发将会有很大的促进作用。因此我们很有必要仔细研究下这5种数据存储方式

## 一、SharedPreferences存储数据


1. 适用范围：保存少量的数据，且这些数据的格式非常简单：字符串型、基本类型的值。比如应用程序的各种配置信息（如是否打开音效、是否使用震动效果、小游戏的玩家积分等），解锁口 令密码等
2. 核心原理：保存基于XML文件存储的key-value键值对数据，通常用来存储一些简单的配置信息。通过DDMS的File Explorer面板，展开文件浏览树,很明显SharedPreferences数据总是存储在/data/data//shared_prefs目录下。SharedPreferences对象本身只能获取数据而不支持存储和修改,存储修改是通过SharedPreferences.edit()获取的内部接口Editor对象实现。 SharedPreferences本身是一 个接口，程序无法直接创建SharedPreferences实例，只能通过Context提供的getSharedPreferences(String name, int mode)方法来获取SharedPreferences实例，该方法中name表示要操作的xml文件名，第二个参数具体如下：Context.MODE_PRIVATE: 指定该SharedPreferences数据只能被本应用程序读、写；Context.MODE_WORLD_READABLE: 指定该SharedPreferences数据能被其他应用程序读，但不能写；Context.MODE_WORLD_WRITEABLE: 指定该SharedPreferences数据能被其他应用程序读，写。
* 
实现SharedPreferences存储的步骤如下：
　　

（一）、根据Context获取SharedPreferences对象
　　

（二）、利用edit()方法获取Editor对象。
　　

（三）、通过Editor对象存储key-value键值对数据。

（四）、通过commit()方法提交数据。

下面是用SharedPreferences存储数据的一个案例:

void ReadSharedPreferences()

{
String strName,strPassword;

SharedPreferences user = getSharedPreferences(“user_info”,0);
strName = user.getString(“NAME”,””);

strPassword = user getString(“PASSWORD”,””);

}

void WriteSharedPreferences(String strName,String strPassword)
{
SharedPreferences user = getSharedPreferences(“user_info”,0);
uer.edit();

user.putString(“NAME”, strName);

user.putString(“PASSWORD” ,strPassword);

user.commit();

}```

以上面的数据存储结果为例，打开后可以看到一个user_info.xml的文件，打开后可以看到：

```<?xml version=”1.0″ encoding=”UTF-8″?>
<map
<string name=”NAME”>moandroid</string>
<string name=” PASSWORD”>SharedPreferences</string>
</map>```
