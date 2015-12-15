# 解析Android传感器应用开发

作者：谢峰
学号：1501211009

摘要：Android系统提供对传感器的支持。只要手机设备的硬件提供这些传感器，Android应用可通过传感器获取设备的外界条件，像设备的运行状态，摆放方向，甚至是温度，压力还有磁场等。开发者只要为指定的传感器注册一个监听器即可。外部环境变化时，Android系统通过传感器获得外部环境的数据，并将数据传给监听器的监听方法。

关键字：传感器，监听器

开发传感器应用的步骤如下：

1.调用Context的getSystemService（Context.SENSOR_SERVICE）方法获取SensorManager对象，SensorManager对象代表系统的传感器服务管理。

2.调用SensorManager的getDefaultSensor(int type)方法获得指定类型的传感器。

3.在Activity的onResume()方法中调用SensorManager的registerListener(SensorEventListener listener,Sensor sensor,int rate)为指定传感器注册监听即可。应用程序通过实现监听器即可获得传感器传回的数据。

该方法三个参数作用如下：

listener:监听传感器事件的监听器。该监听器需要实现SensorEventListener接口。

sensor:传感器对象。

rate:指定获取传感器数据的频率。

该方法中的rate参数能支持如下几个频率值：1.SensorManager.SENSOR_DELAY_FASTEST:最快。延迟最小，只有特别依赖于传感器数据的应用推荐采取这种频率，该种模式可能造成手机电量大量消耗，由于传递的为原始数据，算法不处理好将会影响应用的性能。2.SensorManager.SENSOR_DELAY_GAME:适合游戏的频率。在一般实时性要求的应用上使用该频率。3.SensorManager.SENSOR_DELAY_NORMAL:正常频率。一般实时性要求不是很高的应用适合该频率。4.SensorManager.SENSOR_DELAY_UI:适合普通用户界面的频率。这种模式比较省电，而且系统应用上开销也很小，但延迟比较大，因此只适合在普通的小程序上使用。

下面程序代码是关于是几种常用传感器的用法：


1.Activity实现SensorEventListener接口并实现onSensorChanged(Sensor arg0)方法。

![](技术文章1.png)

2.在onCreate方法内获取传感器管理服务对象。

![](技术文章2.png)

3.onResume()中注册相关传感器的监听器。

![](技术文章3.png)

4.onStop()和onPause()中取消注册传感器监听器。

![](技术文章4.png)

5.效果图.

![](技术文章5.png)

小结：Android系统的特色之一就是支持传感器，通过传感器可以使程序获取手机设备运行时的外界信息，包括手机运动的加速度，手机摆放方向等等。要重点掌握Android传感器支持的API,包括如何通过SensorManager注册传感器监听器，如何使用SensorEventListener监听传感器数据等。除此之外，需要掌握Android的常用的传感器，比如Android的加速度传感器，方向传感器，磁场传感器，温度传感器，光传感器和压力传感器等传感器的功能和用法。
