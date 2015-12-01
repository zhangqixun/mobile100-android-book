#移动平台软件开发（Android）面试题汇总参考答案

## Question About Commnication

###**声明**

本册面试题及答案大部分摘自网络，版权归原创者所有，如有侵权请您及时联系：

邮箱：1596888285@qq.com

参考用书：《第一行代码》（Android）、《疯狂Android讲义》、《Android4编程入门经典》

*本答案仅供参考，如有错误，请您指正*


---
**1、请简述android中解析xml文件数据的几种方法，并且简单介绍这几种方法**

常见的XML解析器分别为DOM解析器、SAX解析器和PULL解析器。

第一种方式：DOM解析器

DOM是基于树形结构的的节点或信息片段的集合，允许开发人员使用DOM API遍历XML树、检索所需数据。分析该结构通常需要加载整个文档和构造树形结构，然后才可以检索和更新节点信息。Android完全支持DOM 解析。利用DOM中的对象，可以对XML文档进行读取、搜索、修改、添加和删除等操作。

DOM的工作原理：使用DOM对XML文件进行操作时，首先要解析文件，将文件分为独立的元素、属性和注释等，然后以节点树的形式在内存中对XML文件进行表示，就可以通过节点树访问文档的内容，并根据需要修改文档——这就是DOM的工作原理。

DOM实现时首先为XML文档的解析定义一组接口，解析器读入整个文档，然后构造一个驻留内存的树结构，这样代码就可以使用DOM接口来操作整个树结构。由于DOM在内存中以树形结构存放，因此检索和更新效率会更高。但是对于特别大的文档，解析和加载整个文档将会很耗资源。当然，如果XML文件的内容比较小，采用DOM是可行的。

常用的DoM接口和类：

Document：该接口定义分析并创建DOM文档的一系列方法，它是文档树的根，是操作DOM的基础。

Element：该接口继承Node接口，提供了获取、修改XML元素名字和属性的方法。

Node：该接口提供处理并获取节点和子节点值的方法。

NodeList：提供获得节点个数和当前节点的方法。这样就可以迭代地访问各个节点
。

DOMParser：该类是Apache的Xerces中的DOM解析器类，可直接解析XML文件。

第二种方式：SAX解析器：

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

第三种方式：PULL解析器：

Android并未提供对Java StAX

API的支持。但是，Android附带了一个pull解析器，其工作方式类似于StAX。它允许用户的应用程序代码从解析器中获取事件，这与SAX解析器自动将事件推入处理程序相反。

PULL解析器的运行方式和SAX类似，都是基于事件的模式。不同的是，在PULL解析过程中返回的是数字，且我们需要自己获取产生的事件然后做相应的操作，而不像SAX那样由处理器触发一种事件的方法，执行我们的代码。

读取到xml的声明返回 START_DOCUMENT;

读取到xml的结束返回 END_DOCUMENT ;

读取到xml的开始标签返回 START_TAG

读取到xml的结束标签返回 END_TAG

读取到xml的文本返回 TEXT

PULL解析器小巧轻便，解析速度快，简单易用，非常适合在Android移动设备中使用，Android系统内部在解析各种XML时也是用PULL解析器，Android官方推荐开发者们使用Pull解析技术。Pull解析技术是第三方开发的开源技术，它同样可以应用于JavaSE开发。

PULL 的工作原理：
XMLpull提供了开始元素和结束元素。当某个元素开始时，我们可以调用parser．nextText从XML文档中提取所有字符数据。当解释到一个文档结束时，自动生成EndDocument事件。

常用的XML pull的接口和类：

XmlPullParser：XML pull解析器是一个在XMLPULLVlAP1中提供了定义解析功能的接口。

XmlSerializer：它是一个接口，定义了XML信息集的序列。

XmlPullParserFactory：这个类用于在XMPULL V1 API中创建XML Pull解析器。

XmlPullParserException：抛出单一的XML pull解析器相关的错误。

