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