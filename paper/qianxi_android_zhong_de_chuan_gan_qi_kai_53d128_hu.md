# 浅析Android中的传感器开发(黄汉煜    1501210915)


# 

浅析Android中的传感器开发


## 

作者：黄汉煜
学号：1501210915


### 

摘要：安卓中基于传感器的开发是安卓系统特有的开发形式，本文简要介绍了传感器开发的模式，列举了常用的传感器类型，并利用光照传感器、加速度传感器、地磁传感器实现了一个简易的指南针。

关键词：安卓、传感器、指南针、摇一摇

正文

说起移动开发中的特色开发技术，许多人的第一反应便是基于gps的位置服务，实际上，基于传感器的编程也是当前移动开发中极具特色的一环。所谓传感器，指的是，手机中的一种微型硬件设备，例如：光照传感器、加速度传感器、地磁传感器、压力传感器、温度传感器等。不同的传感器往往具有不同的规格与功能，但是，通过android系统提供的抽象接口，我们可以轻松的读取传感器提供的信息，在许多手机游戏中使用的重力感应技术、微信中的摇一摇等诸多有趣的应用都是基于传感器完成的。


一、	传感器编程的基本用法

Android中的传感器开发时用法大同小异，基本框架是相同的，可以分为以下几步：
首先，获取SensorManager的实例，这是所有传感器的管理器，有了它之后，我们就可以访问传感器类型：

 ```SensorManager sensorManager=(SensorManager)getSystemService(Context.SENSOR_SERVICE);```
 
然后，在该实例上通过调用getDefaultSensor()来获得任意的传感器类型，其参数为具体的传感器类型，我们一光照传感器为例：
 
 ```Sensor sensor=sensorManager.getDefaultSensor(Sensor.TYPE_LIGHT);```
 
接下来，对传感器的输出信号进行监听，借助接口SensorEventListener实现，该接口含两种方法，onSensorChanged()和 onAccuracyChanged()。当传感器的监测数值变化时，调用前者，前者的方法中含SensorEvent参数，该参数含一个values数组，记录了了输出的具体信息，其内容与监听的传感器类型有关。而当传感器精度发生变化时，我们调用后者。

```
SensorEventListener listener=new SensorEventListener()           {
            @Override
            public void onSensorChanged(SensorEvent event) {

            }

            @Override
            public void onAccuracyChanged(Sensor sensor, int accuracy) {

            }
        };
        ```
 
 
此后，我们需要调用SensorManager的 registerListener()方法来注册 SensorEventListener，
registerListener()方法接收三个参数，第一个参数就是 SensorEventListener的实例，第二个参数是Sensor的实例，第三个参数是用于表示传感器输出信息的更新速率， 共有SENSOR_DELAY_UI、 SENSOR_DELAY_NORMAL、SENSOR_DELAY_GAME 和 SENSOR_DELAY_FASTEST 这四种值可选， 它们的更新速率是依次递增的。


```sensorManager.registerListener
(listener,sensor,SensorManager.SENSOR_DELAY_NORMAL)；```
 
 
最后，当传感器使用完毕时，调用unregisterListener ()方法将使用的资源释放掉：
 
 ```sensorManager.unregisterListener(listener);```
 
 
 
 
