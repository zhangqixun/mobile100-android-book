# Andriod权限机制（1501211048朱思敏）

作者：朱思敏<br>
学号：1501211048<br>
学院：北京大学软件与微电子学院


# 1、 Android 安全机制概述

Android 是一个权限分离的系统 。 这是利用 Linux 已有的权限管理机制，通过为每一个 Application 分配不同的 uid 和 gid ， 从而使得不同的 Application 之间的私有数据和访问（ native 以及 java 层通过这种 sandbox 机制，都可以）达到隔离的目的 。 与此 同时， Android 还 在此基础上进行扩展，提供了 permission 机制，它主要是用来对 Application 可以执行的某些具体操作进行权限细分和访问控制，同时提供了 per-URI permission 机制，用来提供对某些特定的数据块进行 ad-hoc 方式的访问。

## 1.1 uid、gid、gids


在 Android 上，一个用户 UID 标示一个应用程序。应用程序在安装时被分配用户 UID，应用程序在设备上的存续期间内，用户 UID 保持不变。对于普通的应用程序，GID即等于UID。GIDS 是由框架在 Application 安装过程中生成，与 Application 申请的具体权限相关。 如果 Application 申请的相应的 permission 被 granted ，而且有对应的GIDS， 那么 这个Application 的 gids 中将 包含这个 gids。记住权限(GIDS)是关于允许或限制应用程序（而不是用户）访问设备资源。

## 1.2 permission

Android 框架提供一套默认的权限存储在android.anifest.permission 类中，同时也允许我们自己定义新的权限。我们在写应用程序时声明权限，程序安装时新权限被引入系统。权限授权在应用程序被安装时执行。当在设备上安装应用程序时，程序将请求完成任务必需的权限集合。被请求的权限列单显示在设备屏幕上以待用户审查只有用户同意授权后，程序才会被安装，该应用程序获得所有被请求的权限。所以，Android 系统实施的主要安全准则是应用程序只有得到权限许可后，才能执行可能会影响到系统其它部分的操作。每个权限被定义成一个字符串，用来传达权限以执行某个特殊的操作。所有权限可以分为两个类别:一种是执行程序时被应用程序所请求的权限，一种是应用程序的组件之间通信时被其它组件请求的权限。开发者通过在AndroidManifest.xml文件中编写权限标签来定义以上两种类别的权限策略。

## 2、 Android permission 管理机制


