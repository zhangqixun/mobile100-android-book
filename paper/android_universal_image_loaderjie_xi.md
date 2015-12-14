# Android Universal Image Loader解析


# 


## Universal Image Loader简介

Android Universal Image Loader，简称UIL，是Github上一个使用非常广泛的图片异步加载库，为我们提供了全面的图片异步加载解决方案，且性能良好，因此得到了广泛的使用。
相信做过Android应用开发的同学都知道，Android在加载大量图片时很容易出现Out Of Memory（内存溢出）异常。这是因为系统分配给图片加载的内存大小有限，所以如果加载图片量非常大的话会报OOM异常。当然，关于这个异常有很多的解决方案，这里想给大家介绍一下UIL这个开源项目的解决方案。

# 

UIL的主要功能
1. 
提供多线程下载图片。图片可以来源于网络、文件系统、项目文件夹assets中以及drawable中等
1. 
可以根据需要进行图片加载（ImageLoader）的相关配置。例如，线程池、图片下载器、内存缓存策略、硬盘缓存策略、图片显示选项以及其他的一些配置
1. 
支持图片的多种缓存方式。包括内存缓存、文件系统缓存和SD卡缓存三种缓存图片方式
1. 
对图片的下载过程进行监听
1. 
跟据控件（ImageView）的大小对Bitmap（位图）进行裁剪，以减少Bitmap占用过多的内存
1. 
能够对图片的加载过程进行控制。如暂停图片加载、重新开始加载图片等
1. 
提供在较慢的网络下对图片进行加载的方式

## 


## UIL总体架构

UIL主要由五部分构成，分别为ImageLoaderEngine、Cache（MemoryCache和DiskCache）、ImageDownloader、ImageDecoder、BitmapDisplayer以及BitmapProcessor。 具体可参见下图。 

[](paper/im1.png)
由上图可以看出UIL的处理流程：
1. 
ImageLoader收到加载及显示图片的任务，并将任务提交给ImageLoaderEngine；
1. 
ImageLoaderEngine分发任务到具体的线程池去执行；
1. 
任务通过Cache及ImageDownloader获取图片。根据情况，中间可能需要BitmapProcessor和ImageDecoder处理，最终转换为Bitmap交给BitmapDisplayer在ImageAware中显示。
每一个图片的加载和显示任务都运行在独立的线程中除非这个图片缓存在内存中，这种情况下图片会立即显示。如果需要的图片缓存在本地，他们会开启一个独立的线程队列。如果在缓存中没有正确的图片，任务线程会从线程池中获取，因此，快速显示缓存图片时不会有明显的障碍。
