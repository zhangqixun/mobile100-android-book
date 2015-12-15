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
    u4  stringIdsOff;
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

magic字段中显示的是DEX文件的魔数以及版本号，前三个字符为dex，后续还存放着版本号035。
checksum字段中存放着DEX文件的较验和，一旦我们修改了DEX文件，这个字段中的数值必须要重新进行计算，否则虚拟机在载入DEX文件时将会报错。