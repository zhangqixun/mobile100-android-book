# 更新今日天气数据


    小组成员: 孙晴 宁旭冉 于昌和   最终完成日期：15年00月00日
# 

**一、简介**

*本实验完成天气预报项目中更新今日天气数据的模块。*

**二、基础知识**

   
* bean包：

      作用：封装数据
      实现： get、set、toString方法


* toString方法：

      toString()源自Object类，重写并不是必须的，需要根据当前程序的
      实际情况而定。在许多场合中,需要打印和比较出对象的内容,所以重写。
      toString()
      譬如一个JavaBean插入数据库时需要打印一段该JavaBean内容的日志
      ，这时在JavaBean里重写toString()就显得很优美了。


* 解析xml数据（PULL）：

      START_DOCUMENT：读取到XML的声明;
      END_DOCUMENT：读取到XML的结束返回；
      START_TAG : 读取到XML的开始标签；
      END_TAG：读渠道XML的结束标签；
      参照XML数据解析。

* 更新UI：

      xxx.setText():
      如果xxx是Text控件，那就是设置Text的控件中的文本内容
      xxx.setImageResource()：
      设置ImageView中的图片，setImageResource的参数是resId，必须是drawable目录下的资源。
      
* 主线程与子线程间的消息传递：

      handler是线程通讯工具类。用于传递消息。它有两个队列：
      1.消息队列
      2.线程队列
      消息队列使用sendMessage和HandleMessage的组合来发送和处理消息。
      handler是Message的主要处理者，负责Message的发送，Message内
      容的执行处理。后台线程就是通过传进来的Handler对象引用来sendMessage(Message)。
      而使用Handler，需要implement 该类的 handleMessage(Message) 
      方法，它是处理这些Message的操作内容，例如Update UI。 
      通常需要子类化Handler来实现handleMessage方法。
   

**三、主要思路及步骤**

**3.1 主要思路**

*完成更新今日天气的功能，需要前序工作：获取网络数据和解析网路数据，见本教程前面的模块*

**3.2 实践步骤**

*1.建立新的package，建立新的bean包--todayweather类*
![](QQ20151129-0@2x.png)

![](QQ20151129-1@2x.png)
todayweather类有如下属性：
    
    private String city;
    private String updatetime;
    private String wendu;
    private String fengli;
    private String shidu;
    private String fengxiang;
    private String pm25;
    private String quality;
    private String date;
    private String high;
    private String low;
    private String type;
为每个属性写get set方法
    
    public String getType() {
        return type;
    }

    public void setType(String type) {
        this.type = type;
    }

    public String getCity() {
        return city;
    }

    public void setCity(String city) {
        this.city = city;
    }

    public String getUpdatetime() {
        return updatetime;
    }

    public void setUpdatetime(String updatetime) {
        this.updatetime = updatetime;
    }

    public String getWendu() {
        return wendu;
    }

    public void setWendu(String wendu) {
        this.wendu = wendu;
    }

    public String getFengli() {
        return fengli;
    }

    public void setFengli(String fengli) {
        this.fengli = fengli;
    }

    public String getShidu() {
        return shidu;
    }

    public void setShidu(String shidu) {
        this.shidu = shidu;
    }

    public String getFengxiang() {
        return fengxiang;
    }

    public void setFengxiang(String fengxiang) {
        this.fengxiang = fengxiang;
    }

    public String getPm25() {
        return pm25;
    }

    public void setPm25(String pm25) {
        this.pm25 = pm25;
    }

    public String getQuality() {
        return quality;
    }

    public void setQuality(String quality) {
        this.quality = quality;
    }

    public String getDate() {
        return date;
    }

    public void setDate(String date) {
        this.date = date;
    }

    public String getHigh() {
        return high;
    }

    public void setHigh(String high) {
        this.high = high;
    }

    public String getLow() {
        return low;
    }

    public void setLow(String low) {
        this.low = low;
    }


**四、常见问题及注意事项**

*1. 在处理数据时注意值为空的情况；*
