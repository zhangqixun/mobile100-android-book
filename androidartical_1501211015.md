# Android无线网络技术——Bluetooth&WiFi编程（1501211015 杨超）

摘要
自2008年10月发布第一部Android智能手机以来，其市场份额逐年提升，到2013年其全球市场占有率已达到78.1%，发展到当下Android设备已经成为应用最广泛的智能设备，其在电视、数码相机、游戏机、智能家居等领域也有非常多的应用。这些智能设备在使用过程中通常不可避免的需要进行网络互联，与Internet的连接、与其他设备之间的连接等。Android提供了多种交互方式的支持，包括Bluetooth、NFC、WiFi Direct、USB和SIP，本文主要分析了Android在利用无线网络技术进行设备交互的特点和基本开发流程，分为Android Bluetooth和WiFi两大部分。前者主要针对短距离内设备间的直接交互，后者可以通过互联网与远端设备进行交互，实际是网络通信方式的一种。
关键字：Android 蓝牙，WiFi，网络通信

一、Android Bluetooth通信
1.1、Bluetooth的功能与特性
安卓平台提供对蓝牙通讯栈的支持，它允许设备和其他的蓝牙设备进行无线传输数据。 应用程序层通过调用Android Bluetooth 相关的API来调用蓝牙相关功能。这些API使程序能够无线连接蓝牙设备，建立通信连接并进行信息传递，并拥有P2P或多端无线连接的特性。Adnroid Bluetooth API主要提供的功能有：扫描其他蓝牙设备、为可配对蓝牙设备查询蓝牙适配器、建立RFCOMM通道、通过服务搜索来连接其他设备、与其他设备进行数据传输、管理多个连接。
1.2、Bluetooth开发基本流程以及主要的类和接口
在进行蓝牙通信时其基本的也是关键的步骤主要有5步：
1、	开启蓝牙
2、	查找附近已配对或可用的设备
3、	创建通信连接
4、	获取输入输出流并读取或写入数据
5、	关闭连接、释放资源
常用的类和接口：
BluetoothAdapter类
表示本地蓝牙适配器，主要用来对蓝牙进行基本操作，它是所有蓝牙交互操作的入口，如搜索其他的蓝牙设备、查询可配对的设备集合、根据已知的MAC地址来初始化一个BluetoothDevice实例对象、创建一个BluetoothServerSocket类以监听其它设备对本机的连接请求等。
获取BluetoothAdapter实例，在Android4、4.2.2（对应的API级别为17）及之前的版本使用getDefaultAdapter()方法获取，在Android4.3（对应的API级别为18）及其以后的版本中可以使用getSystemService()和BLUETOOTH_SERVICE方式获取。该类常用的方法如下表：
| 0:0 | 1:0 |
| -- | -- |
| 0:2 | 1:2 |
| 0:3 | 1:3 |
| 0:4 | 1:4 |
| 0:5 | 1:5 |

BluetoothAdapter  getDefaultAdapter()	获取本地蓝牙适配器
boolean startDiscovery()	开始搜索附近的蓝牙设备
Set<BluetoothDevice> getBondedDevice()	获取已配过对的蓝牙设备集合
BluetoothServiceSocket listenUsingRfcommWithServiceRecord(String name, UUID uuid)	建立监听请求的Socket(用作服务端)
BluetoothDevice getRemoteDevice()	获取BluetoothDevice对象
