# DEX文件混淆原理(1501210988 汤炀)
## 引言
DEX文件是安卓系统中JAVA虚拟机的可执行文件。在安卓中，APK是
实际上并不能够直接的被虚拟机执行，其中包含的不仅仅有DEX文件，同时好包括了其他资源文件，例如图片、数据库等。当一个安卓程序呗运行时，JAVA虚拟机将会打开APK文件，并从中读取DEX，最用在虚拟机中运行的是DEX文件在虚拟机内存中的一个副本。

由于DEX文件是由JAVA中的CLASS文件经过编译产生的，而且保留了一些CLASS文件的特性，因此可以轻易的被反编译成可读性极强的JAVA代码。一旦程序被反编译，源码将会被暴露在外，因此如何保护DEX文件不被反编译成为了保护源代码的重要一环。现今，保护DEX文件的方式有很多，其中对DEX文件中的类名、方法名和变量名进行混淆是一种比较有效的方式。因为在原来的DEX文件中保存着源代码中的类名、方法名和变量名，这样方便了使用JAVA的反射机制编写程序。但是这样也使得通过反编译得到的代码更具有可读性，在反编译之后可以轻易的去解读源码的设计思想、算法等。因此，通过混淆DEX文件中的字符串，增加反编译代码的阅读成本，可以有效的防止自己的程序被破解。

## DEX文件格式

DEX文件的格式的定义保存在安卓源代码中的platform/dalvik文件夹中，属于dalvik虚拟机的一部分。

1.1  DEX文件头

类似于Windows平台下的PE程序，DEX文件拥有自己的文件头，并以文件头为线索，向下索引DEX文件的各个部分，每个部分都有相应的作用。

```
struct DexHeader {
    u1  magic[8];          
    u4  checksum;       
    u1  signature[kSHA1DigestLen]; 
    u4  fileSize;       
    u4  headerSize;       
    u4  endianTag;
    u4  linkSize;
    u4  linkOff;
    u4  mapOff;
    u4  stringIdsSize;
    u4  stringIdsOff保存;
    u4  typeIdsSize;
    u4  typeIdsOff;
    u4  protoIdsSize;
    u4  protoIdsOff;
    u4  fieldIdsSize;
    u4  fieldIdsOff;
    u4  methodIdsSize;
    u4  methodIdsOff;
    u4  classDefsSize;
    u4  classDefsOff;
    u4  dataSize;
    u4  dataOff;
};

```
![](屏幕快照 2015-12-03 下午12.32.37.png)
magic字段中显示的是DEX文件的魔数以及版本号，前三个字符为dex，后续还存放着版本号035。

checksum字段中存放着DEX文件的较验和，一旦我们修改了DEX文件，这个字段中的数值必须要重新进行计算，否则虚拟机在载入DEX文件时将会报错。

signature[kSHA1DigestLen]字段的长度是20字节，用来保存签名，在文件修改后，这个字段同样也需要被重新计算。

fileSize字段保存着整个DEX文件的大小，如果我们在修改的过程中增加了文件的长度，就应当修改此项数值。

headerSize字段保存着DEX文件头部的大小，此项数值一般为0x70，因为在DEX文件的头部中，所有上述字段都是必须的。因此，此字段的数值并不会改变。但是，DEX文件存在着一个优化版本，在优化版本中，这个数值可能会改变。

endianTag字段可以显示出该DEX文件是针对大端机器编译的还是针对小端机器。图中显示的是小端存储。

linkSize和linkOff字段中储存着链接的大小和链接的偏移量。

mapOff字段所存放的偏移量是数据类型DexMapList的偏移量，这个数据类型在整个文件中只出现一次，用来存放DEX文件中每一个数据块的类型、大小和偏移量。其中type为枚举类型。每一个数据块都存放着不同的数据，有着不同的含义。
```
enum {
    kDexTypeHeaderItem               = 0x0000,
    kDexTypeStringIdItem             = 0x0001,
    kDexTypeTypeIdItem               = 0x0002,
    kDexTypeProtoIdItem              = 0x0003,
    kDexTypeFieldIdItem              = 0x0004,
    kDexTypeMethodIdItem             = 0x0005,
    kDexTypeClassDefItem             = 0x0006,
    kDexTypeMapList                  = 0x1000,
    kDexTypeTypeList                 = 0x1001,
    kDexTypeAnnotationSetRefList     = 0x1002,
    kDexTypeAnnotationSetItem        = 0x1003,
    kDexTypeClassDataItem            = 0x2000,
    kDexTypeCodeItem                 = 0x2001,
    kDexTypeStringDataItem           = 0x2002,
    kDexTypeDebugInfoItem            = 0x2003,
    kDexTypeAnnotationItem           = 0x2004,
    kDexTypeEncodedArrayItem         = 0x2005,
    kDexTypeAnnotationsDirectoryItem = 0x2006,
};

struct DexMapItem {
    u2 type;            
    u2 unused;
    u4 size;            
    u4 offset;         
};

struct DexMapList {
    u4  size;             
    DexMapItem list[1];  
};```


stringIdsSize和stringIdsOff保存着DEX文件中所有字符串的数量和其列举的偏移量。这里的偏移量并不是DEX文件中字符串的直接偏移量，而是另一个指向真正字符串的数据类型。在这个数据类型中存放着字符串真正的偏移量。同时，这里的字符串也并不是我们日常所见到的UTF-8或ASCII编码，而是另一种数据结构，这种数据结构由字符串长度和字符串数据组成。字符串长度使用的是LEB128编码表示。这种编码是不定长的，能够使用最短一位、最长五位来表示一个32位整数。在DEX文件中广泛采用了这种编码，其原因是这种不定长编码能够减少一点的文件长度，对于缺少内存的移动平台来说，这无疑是一个巨大的优点。此外，DEX文件中，字符串使用的是UTF-16编码，不仅仅能够表示英文，还可以表示中文。而在此处，一个中文字符也表示一个字符。在本文中，最重要的就是修改该处的stringdata。修改stringdata后，如果DEX文件被反编译，那么得到的JAVA文件中的类名、方法名和变量名都将会改变，失去原先的可读性，达到混淆的目的，增加反编译的成本。
```
struct DexStringId {
    u4 stringDataOff;    
};
struct DexStringData {
    u1 lens[1];
    u1 stringdata[1];
}

```

typeIdsSize和typeIdsOff字段中存放的是DEX中所有使用到的类型，其中包括了一些类。而经过typeIds索引得到的数据类型中保存着一个字符串索引号，该索引用来简要的描述这个数据类型。
```
struct DexTypeId {
    u4  descriptorIdx;     
};
```
protoIdsSize和protoIdsOff字段中存放的是方法原型，其数据结构中也包含着一些类型列表和字符串索引号。
```
struct DexProtoId {
    u4  shortyIdx;       
    u4  returnTypeIdx;   
    u4  parametersOff;    
};
```
fieldIdsSize和fieldIdsOff以及methodIdsSize和methodIdsOff都与protoIdsSize和protoIdsOff类似，存放着变量和方法相关的索引。
```
struct DexFieldId {
    u2  classIdx;        
    u2  typeIdx;        
    u4  nameIdx;       
};

struct DexMethodId {
    u2  classIdx;          
    u2  protoIdx;        
    u4  nameIdx;       
};
```
classDefsSize和classDefsOff字段