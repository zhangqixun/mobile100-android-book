# TextWatcher

起草人: 刘艺霞   日期：15年11月26日

修改完善：OOOO   日期：15年00月00日
# 


    小组成员: OOOO   最终完成日期：15年00月00日
# 

**一、实验目的**

    掌握TextWatcher的基本用法，使用TextWatcher限制输入字符个数以及实现EditText和TextView的同步

**二、基础知识**

*简要介绍本次实验所需掌握的基础知识*
   
* 知识点1：

      TextWatcher是一个接口，里面有三个函数：
      
public void beforeTextChanged(CharSequence s, int start, int count, int after);
//s表示改变之前的内容，start表示开始的位置，count表示被改变的旧内容数，after表示改变后新的内容的数量

public void onTextChanged(CharSequence s, int start, int before, int count);
//s表示改变之后的内容，start表示开始的位置，before表示改变前的内容数量，count表示新增数

public void afterTextChanged(Editable s);
//表示最终内容

* 知识点2：

      知识点介绍


* 知识点3：

      知识点介绍


   

**三、实验内容及步骤**

**3.1 实验内容**

1、对布局中的EditText限制其输入字符个数

2、在EditText的文本发生改变的时候让TextView的内容跟着发生改变

**3.2 实验步骤**

1、在布局文件activity_main.xml中添加TextView和EditText控件：

![](1.png)

2、在MainActivity.java中实现TextWatcher的三个方法，并对EditText添加事件监听函数

![](2.png)
![](3.png)
![](4.png)

3、运行测试

![](6.png)

![](7.png)
……

![](8.png)

**四、常见问题及注意事项**

1、对EditText添加监听事件：
mEditText.addTextChangedListener(mTextWatcher);


