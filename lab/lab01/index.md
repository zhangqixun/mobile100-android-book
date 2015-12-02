# Android开发环境搭建

起草人: 李佳佳1501210933  日期：15年12月03日

起草人: 张喜涛（本实验参考助教提供的实验手册） 

# 



**一、实验目的**

1.经过本次实验，成功搭建安卓开发环境。

2.学会新建项目并开始开发项目

**二、实验详情**

目录

一、Java开发环境的搭建	
1.1 JDK（Java Development Kit）的下载与安装	

1.2 配置环境变量	

二、android studio的安装与使用	

2.1 Windows下android studio的下载与安装	

2.2 使用android studio创建你的第一个Android程序。	

三、Intel Atom x86模拟器的安装与使用	

四、SDK更新	26

五、创建一个AVD（Android Virtual Device）	

六、搭建环境过程中的一些问题	

6.1  无法访问外网（android studio无法下载及SDK更新无法联网）

6.2 Intel HAXM 安装不成功	

6.3 ADB启动失败	

6.4 Failure [INSTALL_FAILED_OLDER_SDK]	

6.5 android studio安装卡在SDK下载界面不动（Setup Wizard - Downloading Components）	

6.6运行模拟器时错误	

6.7 高版本程序运行在低版本模拟器	

6.8点击开始新Android项目无反应	

6.9虚拟机启动后卡在开机界面	

6.10  SDK、AVD闪退	

6.11安卓虚拟机找不到system image	

6.12.Android Studio不识别移动设备	

6.13.Fetching android sdk component information	

6.14.failed to create jvm error code:不能启动java虚拟机	

6.15.Android Studio安装后发现所有的中文都变成小框框	

6.16.建立项目时及运行时均报错，出现中文乱码	

6.17.Android Studio Gradle project sync failed	

6.18.No resource found that matches the given name'android:Widget.Material.ActionButton'	

6.19.主板BIOS并不支持开启intel virtual technology	

6.20.Failed to configure driver: unknown error. Failed to open drive（安装 intel HAXM 模拟器加速器异常）	

6.21.启动X86模拟器时出现异常	

6.22.res/layout/目录下的所有布局文件必须小写字母	

6.23.无法下载SDK，日志信息显示网络访问失败	

6.24.Axtivity_main.xml的Design中没有预览图	

6.25.如何自己搭建x86模拟器	

6.26.HAXM is not working and emulator runs in emulation mode

6.27.界面过大，导致看不到按钮	

6.28.在Android Studio中打开DDMS	

6.29.Android studio中快捷键的学习	



一、Java开发环境的搭建
1.1 JDK（Java Development Kit）的下载与安装
JDK（Java Development Kit）顾名思义是java开发工具包，是程序员使用java语言编写java程序所需的开发工具包，是提供给程序员使用的。JDK包含了JRE，同时还包含了编译java源码的编译器javac，还包含了很多java程序调试和分析的工具：jconsole，jvisualvm等工具软件，还包含了java程序编写所需的文档和demo例子程序。
如果你需要运行java程序，只需安装JRE就可以了。如果你需要编写java程序，需要安装JDK。
大家可以在oracle官网上下载JDK安装包。
网址：（http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html）该网址打开后即为JDK的下载界面，根据自己的系统选择相应的版本，以32位windows7举例（如图1.1.1）：
1、点击“Accept License Agreement”选项，以接受下载许可；
2、点击相应版本下载（本机为windows 32位系统）。

图 1.1.1





3、下载后，双击运行。在等待安装包采集完信息后出现以下界面（如图1.1.2），点击下一步按钮继续。

图 1.1.2
4、在安装路径界面（如图1.1.3），你可以根据你的需要选择路径（一般默认就好），如果非默认路径，你一定要记住，以便以后配置环境变量时候用到。点击“下一步”继续。

图 1.1.3
5、等待几分钟后，JDK已经安装完毕，点击“关闭”按钮来结束安装。

图 1.1.4
6、为了验证JDK是否安装成功，我们可以打开DOS窗口，输入“java -version”。若出现以下界面（如图1.1.5），即为安装成功。

图 1.1.5
1.2 配置环境变量
JDK安装完毕后我们需要配置环境变量。
1、右键点击【计算机】，选择【属性】，出现属性界面，点击【高级系统设置】（如图 1.2.1）。

图 1.2.1
2、弹出“高级系统设置”界面后，点击【环境变量】（如图 1.2.2）
   图1.2.2
3、出现环境变量界面后我们需要配置系统变量中的三个变量，它们是“JAVA_HOME环境变量”、“PATH环境变量”、” CLASSPATH环境变量”。 
（一）JAVA_HOME环境变量
首先，配置 “JAVA_HOME”。从这个变量的名字就知道，它是java的 “家”。那么，java的 “家”在哪里呢？其实，它就是一个路径，这个路径就是我们刚刚安装的JDK的安装路径。因为JDK里面包含了所有java编译、运行的程序包。所以，我们把这个路径下的文件夹称为java的家。Android Studio/Eclipse/NetBeans/Tomcat等软件就是通过搜索JAVA_HOME变量来找到并使用安装好的JDK。这么一来，JAVA_HOME变量是不是有点门牌号的意思呢？
既然是JDK的安装目录，我们就找到JDK的安装目录。右击地址栏上的地址，选择【复制地址】。如图（1.2.3）

图  1.2.3
复制地址后，我们返回到环境变量配置界面，系统变量下面的【新建】。弹出新建窗口，变量名填入”JAVA_HOME”，变量值填入你刚刚复制的JDK的地址。（如图 1.2.4）

图 1.2.4
（二）PATH环境变量
接下来，我们来配置“PATH环境变量”。PATH环境变量作用是指定命令搜索路径，在shell下面执行命令时，它会到PATH变量所指定的路径中查找看是否能找到相应的命令程序。我们需要把 jdk安装目录下的bin目录增加到现有的PATH变量中，bin目录中包含经常要用到的可执行文件如javac/java/javadoc等待，设置好 PATH变量后，就可以在任何目录下执行javac/java等工具了。
在“系统变量”选项区域中查看PATH变量，如果不存在，则新建变量 PATH，否则选中该变量，单击“编辑”按钮，在“变量值”文本框的起始位置添加“%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;”或者是直接“%JAVA_HOME%\bin;”(不要落下分号，分号是用来与其他变量值隔开来的)单击确定按钮。（如图 1.2.5）。

图 1.2.5
（三）CLASSPATH环境变量
CLASSPATH环境变量作用是指定类搜索路径，如果我们要使用已经编写好的类，首先我们需要找到它们，JVM就是通过CLASSPTH来寻找类的。我们 需要把jdk安装目录下的lib子目录中的dt.jar和tools.jar设置到CLASSPATH中。当然，当前目录“.”也必须加入到该变量中。
在“系统变量”选项区域中查看CLASSPATH 变量，如果不存在，则新建变量CLASSPATH，否则选中该变量，单击“编辑”按钮，在“变量值”文本框的起始位置添加“.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;”。（不要落下前面的“ . ”，）（如图1.2.6）
图 1.2.6
(四) 验证环境变量是否配置成功
打开DOS窗口，输入“javac/java”等java代码编辑程序。若出现其用法，说明已经配置成功。如图（1.2.7）

图 1.2.7
至此，Java开发环境搭建完毕。

二、android studio的安装与使用
2.1 Windows下android studio的下载与安装
1、下载地址：http://developer.android.com/sdk/index.html
2、点击“Develop”在Tools菜单下选择“Download”，网站可以识别到当前电脑的系统，如实
验电脑是Windows系统，所以提供了Windows系统的下载链接。如下图所示：

图 2.1.1
3、点击“Download Android Studio for Windows”后，勾选“I have read and agree with the above terms and conditions”，开始下载。如下图所示：

如果需要安装其他版本可以选择“Other Download Options”，如下图所示：

下载后，双击下载完成的安装包，进行安装，如图所示：

点击“Next”继续。


出现以下界面，继续点击“Next”继续。

点击“I Agree”继续安装。




在这里，根据你的硬盘容量进行安装。但是，除非你的C盘有足够大的空间，否则我不建议你安装在C盘。图中是我为自己选择的安装路径。分别为android studio和SDK选择好路径后，点击“Next”继续。

继续点击“Next”。




点击“Install”执行安装命令，这一步根据电脑硬件的不同，可能需要耗费一小会儿的时间。


点击“Next”继续。




点击“finish”按钮来完成安装。






如果之前有安装过Android Studio 的其他版本，可以选择导入设置（第一个选项），否则选择第二个选项。然后点击“OK”按钮。













接下来是选择主题界面，如图：

选择你喜欢的主题后，点击“Next”继续。
接下来的界面，点击“finish”来完成安装。

2.2 使用android studio创建你的第一个Android程序。
点击”Start a new Android Studio project”来开始创建一个android程序。如图：

为你的程序起一个名字，“Application name”就是将来你做的这个App的名字。


这里选择你的代码服务与什么类型的安卓设备和最小支持的SDK版本。

这里我们选择“Blank Activity”，这个选项，android studio会帮我们自动建一个“Hello World”的Activity以及一些默认的设置。我们可以在这个程序上进行修改。如果你想自己创建这些东西，你应该选择“Add No Activity”.


在这里你可以修改这些文件的名字，然后点击“finish”来完成工程的创建。


至此，你已经创建了一个“Hello World”工程并且可以对这个工程进行修改。






三、Intel Atom x86模拟器的安装与使用
Intel Atom x86的模拟器比标准的Android模拟器具有更高的执行效率，所以推荐安装 Intel Atom x86模拟器。
首先要下载Intel硬件加速执行管理器加速模拟器。
下载地址： https://software.intel.com/zh-cn/ 

1、点击“工具”菜单中的“安卓”选项，


2、点击”英特尔HAXM”选项。

3、找到适合自己的操作系统。（本机为windows）然后点击相应的下载包。


4、点击“接受许可”进行下载


5、下载后进行安装（首次安装可能会出现安装失败）如图：

这是因为电脑的主板未开启”Virtualization”选项。
解决办法：进入你的BIOS。找到Virtualization。将他打开。
由于这一步不同的品牌机操作步骤不同。可以自己摸索。
如图：

6、重启后继续安装，双击安装包，点击”Next”继续。



7、继续点击”Next”继续。



8、点击“Install”执行安装命令。


9、点击“Finish”以完成安装。




四、SDK更新
下面介绍更新SDK步骤：
点击android studio面板上的SDK Manager按钮（或直接打开你本机SDK安装路径中的SDK Manager）如图（图中标识“SDK”的位置）

值得注意的是：如果你在android studio里面打开SDK Manager的话，可能需要等上一段时间。之后才会出现下图：

这幅图片将你已经安装或是部分安装的不同版本的SDK。如果需要添加，请点击“Launch Standlone SDK Manager”来打开SDK Manager。（或是直接在你SDK安装路径下打开SDK Manager）打开后如下图：

这里面有你想要更新的SDK组件。（此步骤需要连接谷歌服务器，请大家自行解决）。

五、创建一个AVD（Android Virtual Device）
AVD（Android Virtual Device）是一个可以安卓虚拟设备。我们可以用它来当做测试机来调试我们的程序。
同更新SDK一样，你有两种方式去创建一个AVD。你可以点击android studio面板上的AVD图标，如下图，也可以直接找到SDK安装路径下的“AVD Manager”.点击运行。



在android studio里面点击AVD Manager图标会出现下图：

这时点击“Create Virtual Device”.来创建一个AVD。


接下来会”Select Hardware”界面，选择合适的硬件后，点击“Next”.




这里为“System Image”界面，是让我们选择一个合适的安卓系统镜像。由于我们已经安装Intel HAXM（Intel Atom x86模拟器）。这里我们选择一个X86的ABI。点击“Next”继续。

这里你可以为你的AVD命名，并且设置它的一些参数。然后点击Finish完成。


当你创建一个或几个不同的AVD时候，你需要启动并使用它。点击右方“Action”中的绿色三角形来运行它。

根据PC的不同，启动时间会有所不同，有些机器第一次启动会失败，这时候我们可以先关闭，再次启动。（首次启动时间比较长）

启动完毕后，我们可以点击android studio中的运行按钮，如下图：




选择一个可以利用的设备，点击OK

我们的HelloWorld可以运行在设备上。



至此，安卓环境搭建完毕。谢谢

六、搭建环境过程中的一些问题
6.1  无法访问外网（android studio无法下载及SDK更新无法联网）
由于大陆地区正常情况下无法访问google网站。然而，SDK更新需要链接google服务器。所以，这对于我们来说是一个难题。下面我来介绍一些方法去解决。当然，大家可能有更好的方法，你可以通过我们的微信群或者QQ群来分享你的方法。

1、启动 Android SDK Manager ，打开主界面，依次选择「Tools」、「Options...」，弹出『Android SDK Manager - Settings』窗口，对话框如下图所示。

方法一：使用国内某些开源软件镜像站， 域名mirrors.neusoft.edu.cn，端口号为80。
方法二：使用已有的代理，这是可以使用自己设置的代理，域名和端口号视情况而定。

2、购买VPN代理实现获得访问外网的权限。
3、可以通过一些论坛去下载使用。
例如：http://www.androiddevtools.cn/
		这个网站有你需要的安卓开发几乎所有的开发工具。

6.2 Intel HAXM 安装不成功
在首次安装 Intel HAXM的时候可能会出现失败的情况，如下图：

这是因为Intel HAXM (Hardware Accelerated Execution Manager) 使用基于 Intel(R) Virtualization Technology (VT) 的硬件加速， 因此需要 CPU 支持 VT ， 所以是BIOS里面的“Virtualization Technology”选项没有打开，去BIOS里面打开就可以了。
英特尔官方给出的解决办法如下：
Intel VT-x not enabled
In some cases, Intel VT-x may be disabled in the system BIOS and must be enabled within the BIOS setup utility. To access the BIOS setup utility, a key must be pressed during the computer’s boot sequence. This key is dependent on which BIOS is used but it is typically the F2, Delete, or Esc key. Within the BIOS setup utility, Intel VT may be identified by the terms "VT", "Virtualization Technology", or "VT-d." Make sure to enable all of the Virtualization features.
6.3 ADB启动失败
安装AndroidStudio的时候，当启动虚拟机的时候出现了ADB没有反应的问题，ADB not responding. You can wait more,or kill"abd.exe" process manually and click 'Restart'
截图如下：

出现这个提示的时候，事实上任务管理器不存在一个"abd.exe"的进程，也就没法kill "abd.exe"，这时，应该查下abd.exe使用的端口5037被“谁"占用，然后kill这个进程，再重新编译就行了。
步骤1：查询端口占用的方法：开始——运行——cmd：打开命令窗口，输入：netstat -aon|findstr “5037” 回车，正常的话会显示出来：

表明PID = 4632的进程占用了这个端口号


打开任务管理器，显示出PID列，找到这个4632的进程，kill掉，再在Android Studio重新编译即可。

6.4 Failure [INSTALL_FAILED_OLDER_SDK]
解决方法：在工程的build.gradle文件中设置安卓sdk的最小版本minSdkVersion，不能高于目前模拟器所支持的sdk版本。

6.5 android studio安装卡在SDK下载界面不动（Setup Wizard - Downloading Components）
安装后第1次启动的时候会首先显示“Fetching Android SDK component information”

等一会儿在Setup Wizard - Downloading Components界面下面开始下载Andorid SDK，
还是由于被墙的缘故，下载不了。
解决方法：
1、关闭安装向导。
2、打开Android Studio安装目录的bin目录下面的idea properties文件，添加一条禁用开始运行向导的配置项：disable.android.first.run=true.
3、启动程序打开项目向导界面，此时点击Start a new Android Studio project没有反应，并且在Configure下面的SDK Manager也是灰色的，这是因为没有安装Android SDK 的原因。

4、这个时候需要打开Configure-Settings，在查找框里面输入proxy，找到下面的HTTP Proxy，设置代理服务器，然后退出将上面在idea.properties配置文件中添加的那条配置项注释掉重新打开Android Studio等刚开始的向导把Android SDK下载安装完成就可以了。

6.6运行模拟器时错误
当我们启动一个选择x86的虚拟机的会后，有可能会出现这个界面

这是由于安装 Intel HAXM版本过低。
解决方法一：去引文官网去下载新版本。
解决方法二：进入“<Android SDK>\extras\intel\Hardware_Accelerated_Execution_Manager\”这个文件夹，运行intelhaxm-android.exe来更新。
6.7 高版本程序运行在低版本模拟器
创建“Hello world”程序出错！
	
 

原因分析以及解决方法:jar包的原因，之前创建的模拟器是基于Android 4.4.2的，但是创建的HelloWorld默认是基于Android 5.0重新添加jar包，在工程区域右键->Build Path ->Configure Build Path->Add External JARs...选定jar包后点击“OK”即可。
6.8点击开始新Android项目无反应
在欢迎界面的面板，点击ConfigureProject DefaultsProject Structure，然后会发现Android SDK location的路径是空的。点击右边的浏览按钮，然后找到相应的SDK路径，并保存。回到欢迎界面，单击Start a new Android Studio project，会发现可以弹出创建工程的界面了，问题得到解决。
6.9虚拟机启动后卡在开机界面
重启电脑后进行一下操作：
1）将system-images里的x86从android-19defaultx86目录，移动到了android-19x86。
2）修改了虚拟机的配置信息，RAM设置从1536改为512（设置大的想法是想让虚拟机快一些，多点内存资源），SD card从0修改为Size 100，如下图所示。


6.10  SDK、AVD闪退
压缩包解压之后，SDK Manager和AVD Manager文件点击会发生闪退无法打开
解决方案：
1、右键以管理员身份运行：失败，依旧闪退；
2、打开压缩包解压后tools文件夹里的android.bat文件：打开后会自动打开SDK Manager；
6.11安卓虚拟机找不到system image
更新SDK相应镜像。
6.12.Android Studio不识别移动设备
解决方法：由于我是使用真机进行测试，在测试过程中遇到的问题是编译器没有识别手机，经过搜索问题解决方案，最终通过下载Google USB Driver以及将手机设置成USB调试模式得以解决该问题，顺利完成测试。
6.13.Fetching android sdk component information
解决方法:这可能是网络连接的有问题，经查找资料，可以修改配置Hosts的方式来解决。
在Android Studio安装目录下的bin目录下，找到idea.properties文件，在文件最后追加：disable.android.first.run=true，保存就ok了。


6.14.failed to create jvm error code:不能启动java虚拟机
解决方法：这可能是配置的内存太小的原因，所以我们需要修改一下配置的内存，在Android Studio安装目录下的bin目录下，查找并打开文件studio.exe.vmoptions修改代码：
-Xmx512m 为 -Xmx256m。




6.15.Android Studio安装后发现所有的中文都变成小框框


解决方法：这个只是编码格式的问题，菜单File > Settings > Appearance & Behavior > Appearance，将default fonts修改一下保存即可。如下图：


6.16.建立项目时及运行时均报错，出现中文乱码
 解决方案：SDK目录必须是全英文目录。
6.17.Android Studio Gradle project sync failed
解决方案：打开项目gradle.properties文件,在末尾添加代码:org.gradle.jvmargs=-Xmx512m -XX:MaxPermSize=512m，重试一次。
6.18.No resource found that matches the given name'android:Widget.Material.ActionButton'
解决方案：如果安装SDK API为 android-23.0.0以上版本，在创建项目后会出现No resource found that matches the given name 'android:Widget.Material.ActionButton'问题，
有两种解决思路：
进入\extras\android\m2repository\com\android\support\appcompat-v7文件夹，在你的SDK安装目下，将23.0.0文件夹删除，打开maven-metadata.xml文件，删除<version>23.0.0</version>，在android studio中，clean project。
改下build.gradle文件，将compile 'com.android.support:appcompat-v7:23.0.1'的版本改为：'com.android.support:appcompat-v7:22.2.1'
6.19.主板BIOS并不支持开启intel virtual technology
解决方法：老师提供的模拟器，需要电脑设置开启intel virtual technology，我进入自己电脑的BIOS设置中并没有找到有关选项，网上查询后了解到是我的电脑CPU虽然支持虚拟化，但主板BIOS并不支持，要用的话需要去升级BIOS，有一定风险，考虑到这个情况，我还是决定使用真机调试。
另外程序运行时打开虚拟机如下报错：

设想是因为内存不足的原因，然后修改了虚拟机的配置信息，RAM设置从 1536MB改为1024MB，但依然不能打开。

继续设置更小的内存，改成512MB，终于成功打开。虽然打开了，但没有HAXM的支持模拟器始终无法流畅运行，以后还是使用真机进行测试。
6.20.Failed to configure driver: unknown error. Failed to open drive（安装 intel HAXM 模拟器加速器异常）
解决方案：
下载haxm_extra_workaround.zip 附件
解压后提取hax_extract.cmd 文件到 HAXM 的解压文件路径中
hax_extract.cmd 右键用管理员权限运行。
参考文献：http://blog.csdn.net/u012938203/article/details/47041669
6.21.启动X86模拟器时出现异常

解决方案：升级HAEM version 1.0.6 update to 1.1.4












参考文献：
http://stackoverflow.com/questions/17024538/how-do-i-fix-failed-to-sync-vcpu-reg-error

6.22.res/layout/目录下的所有布局文件必须小写字母

解决方案：res/layout/目录下的所有布局文件，文件名中都不能出现大写字母，否则会出错。
如果布局文件中包含任何大写字母，R.java文件更新会出错。（还没有了解机制）
6.23.无法下载SDK，日志信息显示网络访问失败

解决方法：修改C:\Windows\System32\drivers\etc\hosts文件。添加如下三行代码：
64.233.162.83    dl.google.com
64.233.162.83    dl.l.google.com
64.233.162.83    dl-ssl.google.com
参考文献：http://www.jb51.net/article/37175.htm
6.24.Axtivity_main.xml的Design中没有预览图
解决办法：原因在于使用安卓版本过高，而使用的模拟器不支持高级的API，通过调整API版本即可。
6.25.如何自己搭建x86模拟器
1、打开Android studio，在工具栏中找到AVD manager的图标，点击打开AVD管理器。

2、在“Device definitions”中选择合适的机型和尺寸，或者自己通过“Create Device”创建模拟机型，选好后，点击“Create AVD”。

3、接下来配置一些参数，需要配置的一般有三项，CPU/ABI,skin,以及SD卡的模拟容量，至于摄像头一般不用，没必要选，如果这三项没有选项可选或者无法点击“ok”，说明有的文件没有下载齐全，需要在SDK manager中下载。
选好后点击“OK”出现配置信息，确认一下就行。


4、确认后可以在“Android virtual devices”中找到创建好的模拟器，点击“start”启动模拟器。

5、点击“launch”开始启动模拟器，正常情况下进度条会正常结束并不报错，如果报错可能是环境变量没有设置好，模拟器的路径没有正确访问，需要重新配置一下环境变量。


6、进度条结束后，稍等一会就可以看到模拟器已经开始开机了，说明模拟器创建成功，等着开机完就行了。
6.26.HAXM is not working and emulator runs in emulation mode
虚拟器开不了机，且在点击运行时，并没有可以选择的模拟器，并且显示HAXM is not working and emulator runs in emulation mode，如下所示：

解决办法：打开SDK安装目录，找到SDK中Intel模拟器下载的地方：
D:\software\SpeSoft\Android\sdk\extras\intel\Hardware_Accelerated_Execution_Manager，以管理员权限运行该文件：

则会出现一个haxm_silent_run文件，如下所示：

再在SDK中的system-images文件中，将其default文件夹中的X86移动到和default同级目录中，如下：

再次开启模拟器，等到起开机，点击运行按钮，即可出现可以选择的模拟器，如下所示：

6.27.界面过大，导致看不到按钮
最简单暴力的方法：
1.一般来说OK对应的快捷键是Alt+O
2.直接回车
3.隐藏任务栏
4.终极解决：

6.28.在Android Studio中打开DDMS
Tools->Android->Android Device monitor

最终界面如下图所示：

经过查找，在默认安装路径下，Android Studio集成的adb工具在C:\Users\BUS\AppData\Local\Android\sdk\platform-tools 路径中，在命令行中打开adb即可。
6.29.Android studio中快捷键的学习
Android studio的快捷键是非常重要的，在实际开发中运用快捷键加快代码的编写速度，我从网上找到了一个整理好的Android studio快捷键的表格。
Action	Mac OSX	Win/Linux
注释代码(//)	Cmd + /	Ctrl + /
注释代码(/**/)	Cmd + Option + /	Ctrl + Shift + /
格式化代码	Cmd + Option + L	Ctrl + Alt + L
清除无效包引用	Option + Control + O	Alt + Ctrl + O
查找	Cmd + F	Ctrl + F
查找+替换	Cmd + R	Ctrl + R
上下移动代码	Option + Shift + Up/Down	Alt + Shift + Up/Down
删除行	Cmd + Delete	Ctrl + Y
扩大缩小选中范围	Option + Up/Down	Ctrl + W/Ctrl + Shift + W
快捷生成结构体	Cmd + Option + T	Ctrl + Alt + T
快捷覆写方法	Cmd + O	Ctrl + O
快捷定位到行首/尾	Cmd + Left/Right	Ctrl + Left/Right
折叠展开代码块	Cmd + Plus,Minus	Ctrl + Plus/Minus
折叠展开全部代码块	Cmd + Shift + Plus,Minus	Ctrl + Shift + Plus,Minus
文件方法结构	Cmd + F12	Ctrl + F12
查找调用的位置	Ctrl + Option + H	Ctrl + Alt + H
大小写转换	Cmd + Shift + U	Ctrl + Shift + U

