# 安卓图形图像处理（1501210927 孔祥稀）

图形图像的处理技术在android 中非常重要，特别是在开发2D游戏或其他应用程序时，都离不开图形图像处理技术的支持。本文档将对安卓中的图形图像处理技术进行详细介绍。
1.Android绘图的基石--常用绘图类
在Android中，绘制图像时，最常应用的就是Paint类、Canvas类、Bitmap类和BitmapFactory类。下面对这四个类进行简单的介绍。
1.1Paint类
Paint类代表画笔，用来描述图形的颜色和风格，如线宽，颜色，透明度和填充效果等信息。使用Paint类时首先要创建该类的对象，这可以通过该类提供的构造方法来实现。通常情况下，只需要使用Paint（）方法来创建一个使用默认设置的Paint对象：
Paint paint=new Paint();
Paint类的常用方法
setARGB(int a,int r,int g,int b)：
用于设置颜色，各参数值0~255的整数，分别用于表示透明度、红色、绿色和蓝色值。
setColor(int color)：
用于设置颜色，参数color可以通过Color类提供的颜色常量指定，也可以通过Color.rgb(int red,int green,int blue)方法指定。
setAlpha(int a)：
用于设置透明度，值为0~255的整数。
setAntiAlias(boolean aa)：
用于指定是否使用抗锯齿功能，如果使用会使绘图速度变慢。
setDither(boolean dither)：
用于指定是否使用图像抖动处理，如果使用会使图像颜色更加饱满，更加清晰。
setPathEffect(PathEffect effect)：
用于设置绘制路径时的路径效果，例如点划线。
setShader(Shader shader)：
用于设置渐变，可以使用LinearGradient（线性渐变）、RadialGradient（径向渐变）或者SweepGradient（角度渐变）。
setShadowLayer(float radius,float dx,float dy,int color)：
用于设置阴影，参数radius为阴影的角度，dx和dy为阴影在x轴和y轴上的距离，Color为阴影的颜色。如果参数radius的值为0，那么就将没有阴影。
setStrokeCap(Paint.Cap cap)：
用于当画笔的填充样式为STROKE或FILL_AND_STROKE时，设置笔刷的图形样式，参数值可以是Cap.BUTT、Cap.ROUND或Cap.SQUARE。主要体现在线的端点上。
setStrokeJoin(Paint.Join join):
用于设置画笔转弯处的连接风格，参数值为Join.BEVEL，Join.MITER或Join.ROUND。
setStrokeWidth（float width）：
用于设置笔触的宽度。
setStyle（Paint.Style style）：
设置填充风格，参数为Style.FILL,Style.FILL_AND_STROKE或Style.STROKE。
setXfermode（Xfermode xfermode）：
设置图像重叠时的处理方式，如合并，取交集并集，常用来制作橡皮擦除效果。

例：分别定义一个径向渐变，线性渐变，角度渐变的画笔，用这三支画笔绘制三个矩形。

```
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.Menu;
import android.view.MenuItem;





import android.app.Activity;
import android.content.Context;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.LinearGradient;
import android.graphics.Paint;
import android.graphics.RadialGradient;
import android.graphics.Shader;
import android.graphics.SweepGradient;
import android.os.Bundle;
import android.view.View;
import android.widget.FrameLayout;

public class MainActivity extends Activity {

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        FrameLayout ll=(FrameLayout)findViewById(R.id.frameLayout1);
        ll.addView(new MyView(this));
    }
    public class MyView extends View{

        public MyView(Context context) {
            super(context);
        }

        @Override
        protected void onDraw(Canvas canvas) {
            Paint paint=new Paint();         //定义一个默认的画笔
            //线性渐变
            Shader shader=new LinearGradient(0, 0, 50, 50, Color.RED, Color.GREEN, Shader.TileMode.MIRROR);
            paint.setShader(shader);   //为画笔设置渐变器
            canvas.drawRect(10, 70, 100, 150, paint);  //绘制矩形
            //径向渐变
            shader=new RadialGradient(160, 110, 50, Color.RED, Color.GREEN, Shader.TileMode.MIRROR);
            paint.setShader(shader);   //为画笔设置渐变器
            canvas.drawRect(115,70,205,150, paint);    //绘制矩形
            //角度渐变
            shader=new SweepGradient(265,110,new int[]{Color.RED,Color.GREEN,Color.BLUE},null);
            paint.setShader(shader);
            canvas.drawRect(220, 70, 310, 150, paint); //绘制矩形

            super.onDraw(canvas);
        }


    }
}
```程序运行效果如图所示：

![](picture.png)

1.2Canvas 类
 Canvas，在英语中，这个单词的意思是帆布。在Android中，则把Canvas当做画布，只要我们借助设置好的画笔(Paint类)就可以在画布上绘制我们想要的任何东西；另外它也是显示位图(Bitmap类)的核心类。随用户的喜好，Canvas还可设置一些关于画布的属性，比如，画布的颜色、尺寸等。Canvas提供了如下一些方法：
Canvas(): 创建一个空的画布，可以使用setBitmap()方法来设置绘制具体的画布。
Canvas(Bitmap bitmap): 以bitmap对象创建一个画布，则将内容都绘制在bitmap上，因此bitmap不得为null。
Canvas(GL gl): 在绘制3D效果时使用，与OpenGL相关。
       drawColor: 设置Canvas的背景颜色。
    setBitmap:  设置具体画布。
    clipRect: 设置显示区域，即设置裁剪区。
    isOpaque:检测是否支持透明。
    rotate:  旋转画布
    translate：移动画布
    scale：缩放画布
    setViewport:  设置画布中显示窗口。
    skew:  设置偏移量。
    restore: 用来恢复上一次save之前的状态
    save：用来保存Canvas的当前状态
canvas.drawRect(RectF,Paint)
用于画矩形，第一个参数为图形显示区域，第二个参数为画笔，设置好图形显示区域Rect和画笔Paint后，即可画图；

canvas.drawRoundRect(RectF, float, float, Paint) 
用于画圆角矩形，第一个参数为图形显示区域，第二个参数和第三个参数分别是水平圆角半径和垂直圆角半径。

canvas.drawLine(startX, startY, stopX, stopY, paint)：
前四个参数的类型均为float，最后一个参数类型为Paint。表示用画笔paint从点（startX,startY）到点（stopX,stopY）画一条直线；

canvas.drawArc(oval, startAngle, sweepAngle, useCenter, paint)：
第一个参数oval为RectF类型，即圆弧显示区域，startAngle和sweepAngle均为float类型，分别表示圆弧起始角度和圆弧度数,3点钟方向为0度，useCenter设置是否显示圆心，boolean类型，paint为画笔；

canvas.drawCircle(float,float, float, Paint)
用于画圆，前两个参数代表圆心坐标，第三个参数为圆半径，第四个参数是画笔；

下面将通过一个具体的例子来说明如何创建用于绘图的画布。
例：Canvas画布做出一个安卓机器人图形。

```
import android.graphics.Canvas;
 
 
public interface  drawGraphics {
public void  draw(Canvas canvas);
}

import android.content.Context;
import  android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Paint;
import android.view.View;
public classGameView  extends View  implements  Runnable{
      //声明Paint对象
       private  Paint mPaint= null;
       privatedrawGraphics drawGraphics= null;
       public GameView(Context context) {
            super(context);
           // TODOAuto-generated constructor stub
           //构建对象
           mPaint= new Paint();
           //开启线程
           new  Thread(this).start();
       }
       public void  onDraw(Canvas canvas) {
           super.onDraw(canvas);
           //设置画布为黑色背景
           //canvas.drawColor(Color.BLACK);
           //消除锯齿
           mPaint.setAntiAlias(true);
          //设置图形为空心
           mPaint.setStyle(Paint.Style.STROKE);
           //绘制空心几何图形
           drawGraphics=  new  DrawCircle();
           drawGraphics.draw(canvas);
           drawGraphics=  new  DrawLine();
           drawGraphics.draw(canvas);
           drawGraphics= newDrawRect();
           drawGraphics.draw(canvas);
       }
 
        
          @Override
          public void run() {
               // TODOAuto-generated method stub
               while(!Thread.currentThread().isInterrupted()) {
                    try{
                          Thread.sleep(1000);
                    } catch(InterruptedException e) {
                      // TODO: handle exception
                      Thread.currentThread().interrupt();
                    }
                     //使用postInvalidate 可以直接在线程中更新界面
                      postInvalidate(); 
               }
         }
 
}

//DrawRect.java
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Paint;
import android.graphics.RectF;
public class DrawRect  implements  drawGraphics{
       private  Paint paint=  null;
       public DrawRect(){
       paint= new  Paint();
}
         @Override
         public void  draw(Canvas canvas) {
             // TODOAuto-generated method stub
             //定义圆角矩形对象
             RectF rectF1 = newRectF(120,170,370,500);
             RectF rectF2 = newRectF(40,150,90,400);
             RectF rectF3 = newRectF(390,150,440,400);
             RectF rectF4 = newRectF(140,520,200,650);
             RectF rectF5 = newRectF(290,520,350,650);
             paint.setAntiAlias(true);
             //设置画笔颜色为BLUE
             paint.setColor(Color.GREEN);
             //在画布上绘制圆角矩形/圆弧/直线
             canvas.drawRoundRect(rectF1, 20, 20, paint);
             canvas.drawRoundRect(rectF2, 20, 20, paint);
             canvas.drawRoundRect(rectF3, 20, 20, paint);
             canvas.drawRoundRect(rectF4, 20, 20, paint);
             canvas.drawRoundRect(rectF5, 20, 20, paint);
        }
}

//DrawLine.java
import android.graphics.Canvas;
import  android.graphics.Color;
import android.graphics.Paint;
public class DrawLine  implements  drawGraphics{
 private Paint paint=  null;
        public  DrawLine(){
           paint=  new  Paint();
       }
              @Override
        publicvoiddraw(Canvas canvas) {
            // TODOAuto-generated method stub
            paint.setAntiAlias(true);
            //绘制直线
            paint.setColor(Color.GREEN);
            //设置线条粗细
            paint.setStrokeWidth(12);
            canvas.drawLine(120,40,170,90, paint);
            canvas.drawLine(320,90,370,40, paint);
       }
}

//DrawCircle.java
import android.graphics.Canvas;
import  android.graphics.Color;
import  android.graphics.Paint;
import android.graphics.RectF;
public class DrawCircle  implements  drawGraphics{
        private  Paint paint=  null;
        private  Paint paint_eye=  null;
 
        public  DrawCircle(){
        paint= new  Paint();
        paint_eye= new  Paint();
}
        @Override
        public  void draw(Canvas canvas) {
             // TODOAuto-generated method stub
             //绘制圆形(圆心x，圆心y，半径r，画笔p)
             paint_eye.setAntiAlias(true);
             paint.setAntiAlias(true);
             RectF rectF = newRectF(120,60,370,240);
             paint_eye.setColor(Color.WHITE);
             paint.setColor(Color.GREEN);
             canvas.drawCircle(190, 110, 18, paint_eye);
             canvas.drawCircle(300, 110, 18, paint_eye);
             canvas.drawArc(rectF, 180, 180,true, paint);
        }
}

//GameStart.java
package  com.scgm.android.drawable;
import  android.app.Activity;
import android.os.Bundle; 
public class GameStart  extends  Activity {
      private  GameView mGameView=  null; 
      @Override
      public  void  onCreate(Bundle  savedInstanceState) { 
          super.onCreate(savedInstanceState);
          this.mGameView= newGameView(this);
          setContentView(mGameView);
      }
}
```

程序运行结果如图所示：

