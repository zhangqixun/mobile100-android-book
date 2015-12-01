#移动平台软件开发（Android）面试题汇总参考答案

## Question About NDK

###**声明**

本册面试题及答案大部分摘自网络，版权归原创者所有，如有侵权请您及时联系：

邮箱：1596888285@qq.com

参考用书：《第一行代码》（Android）、《疯狂Android讲义》、《Android4编程入门经典》

*本答案仅供参考，如有错误，请您指正*


---
**1、请简述Android中.jni的调用过程**

1. 安装和下载Cygwin，下载 Android NDK
2. 在ndk项目中JNI接口的设计
3. 使用C/C++实现本地方法
4. JNI生成动态链接库.so文件
5. 将动态链接库复制到java工程，在java工程中调用，运行java工程即可

**2、请简述何为NDK**

NDK是一些列工具集合，NDK提供了一系列的工具，帮助开发者迅速的开发C/C++的动态库，并能自动将so和java应用打成apk包。
NDK集成了交叉编译器，并提供了相应的mk文件和隔离cpu、平台等的差异，开发人员只需简单的修改mk文件就可以创建出so.