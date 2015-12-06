# Json数据解析

起草人: 黄志达   日期：15年11月29日

修改完善：孔润   日期：15年12月05日
# 

**一、实验目的**

*了解和使用Json格式*

**二、基础知识**

*Json格式的定义，封装和解析*
   
* Json格式：

      json格式是javascript对象与数组相结合形成的一种数据组织方式，主要优势在于体积小，
      如：{success: 0, result:{ a:z, b:y, c: [{name:jack, id:12}, {name:Tom, id:13}]}

* Json解析：

      在Android中解析JSON数据解析有多种：
      官方提供的JSONObject
      谷歌开源的GSON
      第三方开源库Jackson, FastJSON


* Json封装：

      使用JSONObject进行对象的封装，使用JSONArray进行数组的封装。
      如：public JSONObject makJsonObject(int id[], String name[], String year[],
            String curriculum[], String birthday[], int numberof_students)
            throws JSONException {
            JSONObject obj = null;
            JSONArray jsonArray = new JSONArray();
            for (int i = 0; i < numberof_students; i++) {
                obj = new JSONObject();
                try {
                    obj.put("id", id[i]);
                    obj.put("name", name[i]);
                    obj.put("year", year[i]);
                    obj.put("curriculum", curriculum[i]);
                    obj.put("birthday", birthday[i]);
    
                } catch (JSONException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }
                jsonArray.put(obj);
            }
    
            JSONObject finalobject = new JSONObject();
            finalobject.put("student", jsonArray);
            return finalobject;
    }


   

**三、实验内容及步骤**

**3.1 实验内容**

*解析Json格式*

**3.2 实验步骤**

1. GSON的使用：
   把gson-*.*.*.jar放入libs文件夹，右击它，然后点击'Add as library'
    确保build.gradle文件中的依赖文件是正确的
2. 使用返回Json格式天气预报的接口，返回的数据格式如下图：
   ![](1.JPG)
3. 编写代码解析返回的天气信息
   ![](2.JPG)


**四、常见问题及注意事项**

*详细描述本此实验的可能会遇到的问题以及相关的注意事项*


