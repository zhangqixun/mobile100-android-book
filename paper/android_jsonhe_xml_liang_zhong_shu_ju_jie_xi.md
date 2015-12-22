# android JSON和XML两种数据解析
JSON和XML两种数据解析方式


1. JSON数据解析
 1.1 JSON的定义
JSON(JavaScript Object Notation) 是一种轻量级的数据交换格式。 易于人阅读和编写。同时也易于机器解析和生成。 它基于JavaScript Programming Language, Standard ECMA-262 3rd Edition - December 1999的一个子集。 JSON采用完全独立于语言的文本格式，但是也使用了类似于C语言家族的习惯（包括C, C++, C#, Java, JavaScript, Perl, Python等）。 这些特性使JSON成为理想的数据交换语言。 
1.2 JOSN格式
   JSON是 “名称/值”对的集合（A collection of name/value pairs）。不同的语言中，它被理解为对象（object），纪录（record），结构（struct），字典（dictionary），哈希表（hash table），有键列表（keyed list），或者关联数组 （associative array）。值的有序列表（An ordered list of values）。在大部分语言中，它被理解为数组（array）。 JSON的主要形式有两种，如下：
（1）对象类型
对象是一个无序的“‘名称/值’对”集合。一个对象以“{”（左括号）开始，“}”（右括号）结束。每个“名称”后跟一个“:”（冒号）；“‘名称/值’ 对”之间使用“,”（逗号）分隔。



    （2）数组类型。
数组是值（value）的有序集合。一个数组以“[”（左中括号）开始，“]”（右中括号）结束。值之间使用“,”（逗号）分隔。



值（value）可以是双引号括起来的字符串（string）、数值(number)、true、false、 null、对象（object）或者数组（array）。这些结构可以嵌套。

1.3 JSON解析常用类
1.3.1 JSON解析包 
Android JSON所有相关类，都在org.json包下
JSONObject、JSONArray、JSONException、JSONStringer、JSONTokener      
1.3.2 常见方法
JsonObject 方法，opt* 与 get* 建议使用opt方法，因为get方法如果其内容为空会直接抛出异常。不过JsonArray.opt*(index)会有越界问题需要特别注意。
1.3.3 JSON数据的生成
可用以下代码简单介绍Json格式数据的特点，并且生成对象类型数据和数组类型数据。

打印出的数据如下：



1.3.4 JSON数据的解析
将获取的字符串类型的数据转换成JSON格式，然后获取其中的数据。具体代码如下：

解析结果如下：

1.3.5 第三方解析库
上面介绍都是使用Android提供的原生类解析JSON，最大的好处是项目不需要引入第三方库，但是如果比较注重开发效率而且不在意应用大小增加几百K的话，可以选择Jackson，google-gson，Json-lib。
2. XML数据解析
Android平台下常见的XML解析和创建的方法主要有三种，DOM、SAX和PULL。其中DOM解析XML是先把XML文件读进内存中，再通过接口获取数据，该方法使用相对小的XML文件，移动设备往往受硬件性能影响，如果XML文件比较大使用DOM解析往往效率跟不上；SAX和PULL都是采用事件驱动方式来进行解析，在Android中的事件机制是基于回调函数。
2.1 DOM解析
DOM是基于树形结构的的节点或信息片段的集合，允许开发人员使用DOM API遍历XML树、检索所需数据。分析该结构通常需要加载整个文档和构造树形结构，然后才可以检索和更新节点信息。Android完全支持DOM 解析。利用DOM中的对象，可以对XML文档进行读取、搜索、修改、添加和删除等操作。
DOM的工作原理：使用DOM对XML文件进行操作时，首先要解析文件，将文件分为独立的元素、属性和注释等，然后以节点树的形式在内存中对XML文件进行表示，就可以通过节点树访问文档的内容，并根据需要修改文档——这就是DOM的工作原理。
DOM实现时首先为XML文档的解析定义一组接口，解析器读入整个文档，然后构造一个驻留内存的树结构，这样代码就可以使用DOM接口来操作整个树结构。
由于DOM在内存中以树形结构存放，因此检索和更新效率会更高。但是对于特别大的文档，解析和加载整个文档将会很耗资源。 当然，如果XML文件的内容比较小，采用DOM是可行的。
常用的DoM接口和类：
  Document：该接口定义分析并创建DOM文档的一系列方法，它是文档树的根，是操作DOM的基础。 
Element：该接口继承Node接口，提供了获取、修改XML元素名字和属性的方法。
Node：该接口提供处理并获取节点和子节点值的方法。
NodeList：提供获得节点个数和当前节点的方法。这样就可以迭代地访问各个节点。
DOMParser：该类是Apache的Xerces中的DOM解析器类，可直接解析XML文件。
2.2 SAX解析
SAX(Simple API for XML)解析器是一种基于事件的解析器，事件驱动的流式解析方式是，从文件的开始顺序解析到文档的结束，不可暂停或倒退。它的核心是事件处理模式，主要是围绕着事件源以及事件处理器来工作的。当事件源产生事件后，调用事件处理器相应的处理方法，一个事件就可以得到处理。在事件源调用事件处理器中特定方法的时候，还要传递给事件处理器相应事件的状态信息，这样事件处理器才能够根据提供的事件信息来决定自己的行为。 
  SAX解析器的优点是解析速度快，占用内存少。非常适合在Android移动设备中使用。
SAX的工作原理：SAX的工作原理简单地说就是对文档进行顺序扫描，当扫描到文档(document)开始与结束、元素(element)开始与结束、文档(document)结束等地方时通知事件处理函数，由事件处理函数做相应动作，然后继续同样的扫描，直至文档结束。
  在SAX接口中，事件源是org.xml.sax包中的XMLReader，它通过parser()方法来解析XML文档，并产生事件。事件处理器是org.xml.sax包中ContentHander、DTDHander、ErrorHandler，以及EntityResolver这4个接口。XMLReader通过相应事件处理器注册方法setXXXX()来完成的与ContentHander、DTDHander、ErrorHandler，以及EntityResolver这4个接口的连接。
常用的SAX接口和类：
Attrbutes：用于得到属性的个数、名字和值。 
ContentHandler：定义与文档本身关联的事件(例如，开始和结束标记)。大多数应用程序都注册这些事件。
DTDHandler：定义与DTD关联的事件。它没有定义足够的事件来完整地报告DTD。如果需要对DTD进行语法分析，请使用可选的DeclHandler。
DeclHandler是SAX的扩展。不是所有的语法分析器都支持它。
EntityResolver：定义与装入实体关联的事件。只有少数几个应用程序注册这些事件。
ErrorHandler：定义错误事件。许多应用程序注册这些事件以便用它们自己的方式报错。
DefaultHandler：它提供了这些接LI的缺省实现。在大多数情况下，为应用程序扩展DefaultHandler并覆盖相关的方法要比直接实现一个接口更容易。



2.3 PULL解析
Android并未提供对Java StAX API的支持。但是，Android附带了一个pull解析器，其工作方式类似于StAX。它允许用户的应用程序代码从解析器中获取事件，这与SAX解析器自动将事件推入处理程序相反。PULL解析器的运行方式和SAX类似，都是基于事件的模式。不同的是，在PULL解析过程中返回的是数字，且我们需要自己获取产生的事件然后做相应的操作，而不像SAX那样由处理器触发一种事件的方法，执行我们的代码。
    读取到xml的声明返回 START_DOCUMENT;
读取到xml的结束返回 END_DOCUMENT ;
读取到xml的开始标签返回 START_TAG
读取到xml的结束标签返回 END_TAG
读取到xml的文本返回 TEXT 
PULL解析器小巧轻便，解析速度快，简单易用，非常适合在Android移动设备中使用，Android系统内部在解析各种XML时也是用PULL解析器，Android官方推荐开发者们使用Pull解析技术。Pull解析技术是第三方开发的开源技术，它同样可以应用于JavaSE开发。
  PULL 的工作原理：XML pull提供了开始元素和结束元素。当某个元素开始时，我们可以调用parser．nextText从XML文档中提取所有字符数据。当解释到一个文档结束时，自动生成EndDocument事件。
      常用的XML pull的接口和类：
XmlPullParser：XML pull解析器是一个在XMLPULL VlAP1中提供了定义解析功能的接口。
XmlSerializer：它是一个接口，定义了XML信息集的序列。
XmlPullParserFactory：这个类用于在XMPULL V1 API中创建XML Pull解析器。
XmlPullParserException：抛出单一的XML pull解析器相关的错误。

2.4 解析实例
创建river.xml文件放在assets文件夹中
一对 2015/12/20 19:41:32
<?xml version="1.0" encoding="utf-8"?>
<rivers>
 <river name="灵渠" length="605">
     <introduction>
      灵渠在广西壮族自治区兴安县境内</introduction>
      <imageurl>
      http://up.2cto.com/2012/0228/20120228094142711.jpg
     </imageurl>
   </river> 
   <river name="胶莱运河" length="200">
     <introduction>
      胶莱运河南起黄海灵山海口，
     </introduction>
      <imageurl>
      http://up.2cto.com/2012/0228/20120228094142711.jpg
     </imageurl>
   </river>
   <river name="苏北灌溉总渠" length="168">
     <introduction>
      位于淮河下游江苏省北部     </introduction>
      <imageurl>
      http://up.2cto.com/2012/0228/20120228094142711.jpg
     </imageurl>
   </river>
 </rivers>
建立River类


采用DOM解析时具体处理步骤是：
1 首先利用DocumentBuilderFactory创建一个DocumentBuilderFactory实例
2 然后利用DocumentBuilderFactory创建DocumentBuilder
3 然后加载XML文档（Document）,
4 然后获取文档的根结点(Element)，
5 然后获取根结点中所有子节点的列表（NodeList），
6 然后使用再获取子节点列表中的需要读取的结点。



采用SAX解析时具体处理步骤是：
1 创建SAXParserFactory对象
2 根据SAXParserFactory.newSAXParser()方法返回一个SAXParser解析器
3 根据SAXParser解析器获取事件源对象XMLReader
4 实例化一个DefaultHandler对象
5 连接事件源对象XMLReader到事件处理类DefaultHandler中
6 调用XMLReader的parse方法从输入源中获取到的xml数据
7 通过DefaultHandler返回我们需要的数据集合。
 
代码如下：


采用pull解析步骤如下
1：当导航到XmlPullParser.START_DOCUMENT，可以不做处理，当然你可以实例化集合对象等等。
2：当导航到XmlPullParser.START_TAG，则判断是否是river标签，如果是，则实例化river对象，并调用getAttributeValue方法获取标签中属性值。
3：当导航到其他标签，比如Introduction时候，则判断river对象是否为空，如不为空，则取出Introduction中的内容，nextText方法来获取文本节点内容
4：当然啦，它一定会导航到XmlPullParser.END_TAG的，有开始就要有结束嘛。在这里我们就需要判读是否是river结束标签，如果是，则把river对象存进list集合中了，并设置river对象为null.
代码如下：



执行结果如下：

