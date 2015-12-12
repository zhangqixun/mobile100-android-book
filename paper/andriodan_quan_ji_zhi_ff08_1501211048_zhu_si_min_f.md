# Andriod权限机制（1501211048朱思敏）

作者：朱思敏<br>
学号：1501211048<br>
学院：北京大学软件与微电子学院


# 1 Android 安全机制概述

Android 是一个权限分离的系统 。 这是利用 Linux 已有的权限管理机制，通过为每一个 Application 分配不同的 uid 和 gid ， 从而使得不同的 Application 之间的私有数据和访问（ native 以及 java 层通过这种 sandbox 机制，都可以）达到隔离的目的 。 与此 同时， Android 还 在此基础上进行扩展，提供了 permission 机制，它主要是用来对 Application 可以执行的某些具体操作进行权限细分和访问控制，同时提供了 per-URI permission 机制，用来提供对某些特定的数据块进行 ad-hoc 方式的访问。

