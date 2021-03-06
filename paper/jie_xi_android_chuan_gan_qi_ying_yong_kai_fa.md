# 解析Android传感器应用开发

作者：谢峰
学号：1501211009

摘要：Android系统提供对传感器的支持。只要手机设备的硬件提供这些传感器，Android应用可通过传感器获取设备的外界条件，像设备的运行状态，摆放方向，甚至是温度，压力还有磁场等。开发者只要为指定的传感器注册一个监听器即可。外部环境变化时，Android系统通过传感器获得外部环境的数据，并将数据传给监听器的监听方法。

关键字：传感器，监听器

# 正文


科技的发展给人们的工作和生活带来了更多的便利，突出的一点就是便携设备越来越多地来到 我们身边．数码相机、MP3、MP4、掌上电脑等体积小、功能强的电子设备随处可见，而其中的手机更是到了几乎不可缺少的地步．随着技术进步，手 机已经不再仅仅是一个通信工具，开始具备越来越多、越来越强大的功能．人们对手机的要求也越来越高，电话、短信、多媒体等等，都只是基本应用，更智能、更人性化才是用户追求的目标．在此条件下，各种传感器在手机上的应用应运而生。



### 1.传感器的定义：



国家标准GB7665--87对传感器下的定义是：“能感受规定的被测量并按照一定的规律转换成可用信号的器件或装置，通常由敏感元件和转换元件组成”．传感器是一种检测装置，能感受到被测量的信息，并能将检测感受到的信息，按一定规律 变换成为电信号或其他所需形式的信息输出，以满足信息的传输、处理、存储、显示、记录和控制等要求。它是实现自动检测和自动控制的首要环节。

传感器的分类：

可以按不同的观点对传感器进行分类： 

(1)按传感器工作原理，可分为物理传感器和 化学传感器。
(2)按传感器用途，可分为位置传感器、液面传感器、能耗传感器、速度传感器、热敏传感器、加速度传感器、射线辐射传感器、振动传感器、湿敏传感器、磁敏传感器、气敏传感器、真空度传感 器和生物传感器等。 (3)按传感器输出信号，可分为模拟传感器、数字传感器、膺数字传感器和开关传感器。
(4)按传感器应用的材料，可分为以下几类： 按其所用材料的类别分为金属；聚合物、陶瓷和混合物；按材料的物理性质分为导体、绝缘体、半导 体和磁性材料；按材料的晶体结构分为单晶、多晶 和非晶材料。
(5)按照传感器制造工艺，可分为：集成传感器、薄膜传感器、厚膜传感器和陶瓷传感器。


### 2.智能手机中的典型传感器：



1.图像传感器

随着拍照功能成为手机的标准配置，图像传感器继照相机之后在手机上大量应用．用手机拍照、摄像已经成为很多人生活、娱乐的一个重要部分．图像处理技术的发展，使图像传感器有了更广阔的应用空间名片识别：用手机给名片拍张照片，通过 图像处理软件对图像信息加以识别处理，后台处理完毕后，名片上的姓名、年龄、电话、公司地址等就自动进入了手机电话簿的分类条目下．名片识别 功能已经成为商务手机的高端应用代表。
(2)面部识别：用户只需在注册时对自己的面部进行拍照，图像处理软件会根据照片识别并记录 用户的面部特征，作为以后检查用户身份的标准．这样当用户使用有安全需求的手机时无需再记录繁复的密码，只需自我拍照，由手机鉴权即可．这 样既方便了用户，也给移动设备及其信息提供了更高的安全保障。

2.指纹识别传感器

消费类产品对个人数据安全性的需求日益升高，再加上指纹识别传感器在尺寸、成本及准确度 等各方面都获得了巨大进展，指纹识别技术被越来越多地应用到多种多样的移动设备中。目前高端商务手机中很多已经都具备了此类功能．
目前市场上主要有两种固态指纹传感器：接触式传感器(touchSenSOr)和滑动式传感器(slidesensor)。接触式传感器要求手指在指纹采集区进行可靠的触摸；滑动式传感器需要用手指在传感器表面擦过。传感器会采集一套特定的数据，然后进行 快速分析和认证。随着感测技术与算法的进步，加上消费市场对成本的敏感要求，以及便携设备对体积轻薄的固有要求，滑动式传感器已经成为市场主流。指纹识别在手机上的应用最初主要是身份识别，即相当于密码识别．只有主人的指纹信息得到验证才能允许使用设备。随着半导体和软件技术的发展，手机将逐渐成为一种可随时随地获取个人和公司数据的移动终端，因此需要确保用户访问的安全性，以防止未授权访问．比如在电子商务中，指纹识别的应用将免去用户多重繁琐的密码操作，即简化流程又具备更高的安全级别．在行业应用中， 指纹识别也可以有广泛的应用．例如，今后警察可在一个犯罪高发区截住一名嫌疑人，要求其提供指纹而不是身份证或汽车驾照．取得指纹后可以通过 手机上网，访问指纹数据库，得到嫌疑人真实身份 信息。

3.光电传感器

光电传感器在手机上的应用主要是根据环境光线明暗来判断用户的使用条件，从而对手机进行 智能调节，达到节能和方便用户使用的目的．黑暗环境下自动降低背光亮度，以免背光太亮刺眼．太阳下自动增加屏幕亮度，使显示更清楚．手机移到耳边打电话时，自动关闭屏幕和背光，可以延长手机续航时间，同时关闭触摸屏，又可以达到防止打电话过程中误触屏幕挂断电话的误操作．还有的利用光线亮度来控制铃声音量，如手机装在衣服口袋或是皮包里时，就大声振铃，而取出时，环境光改变了，振铃就随着减小．这个功能很有意思；一方 面可以避免铃声过小误接电话，一方面又可以适应环境的需要，避免影响他人，同时还能节省电量。

4.加速度传感器

加速度有两种，一个是静态的加速度，把加速度传感器倾斜一个角度，重力场会在感应场上产生一个分量，通过这个分量，可以测量出手机倾斜了多少角度，由此实现一些前后左右的控制；另外一种就是所谓的动态加速度，可以侦测速度、撞击等．加速度传感器可以用来检测角度．手机上下左右摆动，加速度传感器会将对应信息传送给中央处理器，通过软件实现菜单选择、翻页、图像切换等 操作．当手机反转900时，可以自动在竖屏显示和横屏显示之间切换，方便用户使用．在赛车、滑雪等游戏中，加速度传感器甚至可以取代方向键，通过前后左右的角度变换实现转弯、加速、刹车等动 作，使游戏更具娱乐性。
加速度传感器还可以用于记步器，检测并记录走路或跑步的步数，从而计算路程．当结合个人特征， 如身高、体重等信息，可以通过软件测算热量消耗， 看到锻炼效果．这样，手机又具备了健身功能．加速度传感器还可以使手机具有应急报警功 能．如果用户经受重大撞击，或者跌到，加速度传感器会立刻将相关信息传送给中央处理器．如果事故后一段时间内(如lrain)用户没有动作，则判定用 户受到伤害，立刻通过手机自动报警．如果手机中有GPS。还可以自动启动GPS，并将位置信息传送给救援部门，达到急救的目的．这一点对越来越多的老人大有裨益．加速度传感器和地磁传感器结合能实现更强大的功能．“空间透视”技术就是主要通过这两种传感器的结合来实现．由两种传感器来检测水平位 移、垂直位移和方向．当用户手持这样的手机在建筑物中遍历一次，大量的水平位移、垂直位移和方 向的信息传送给中央处理器，利用图像渲染技术，
一个透视的楼宇就会出现在大家面前．利用这项技术，还可以对GPS导航手机做一个有效补充．在特殊的位置，如隧道和高楼大厦中，GPS信号丢失，无法准确定位．这时通过加速度传感器和地磁传感器，得到多方位的位移信息，在没有GPS信号的情 况下继续维持轨迹的移动，使导航得以延续。




### 3.开发传感器应用的步骤如下：



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
