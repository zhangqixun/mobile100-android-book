# 详解Android中的重力感应

作者：舒心

学号：1501210982

一.手机传感器

  智能手机发展到今天，内置的各类传感器发挥了不可磨灭的作用，在我们横
屏看照片时，它可以非常智能的把照片一起放大并且一起横过来，有电话来时，当我们把耳朵靠近手机屏幕，屏幕就会自动关闭，除此之外，在玩游戏时，我们左右晃动屏幕，屏幕里的主人公就会随着晃动做出相应的动作。
  在Android2.3系统中，google提供了11种传感器供应用层使用，具体如下：（Sensor类）：

define SENSOR_TYPE_ACCELEROMETER                    //加速度

define SENSOR_TYPE_MAGNETIC_FIELD                   //磁力

define SENSOR_TYPE_ORIENTATION                      //方向

define SENSOR_TYPE_GYROSCOPE                        //陀螺仪

define SENSOR_TYPE_LIGHT                            //光线感应

define SENSOR_TYPE_PRESSURE                         //压力

define SENSOR_TYPE_TEMPERATURE                      //温度

define SENSOR_TYPE_PROXIMITY                        //接近

define SENSOR_TYPE_GRAV                             //重力

define SENSOR_TYPE_LINEAR_ACCELERATION              //线性加速度

define SENSOR_TYPE_ROTATION_VECTOR                  //旋转矢量

