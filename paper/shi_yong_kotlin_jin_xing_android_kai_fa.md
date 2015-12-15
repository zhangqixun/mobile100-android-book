#使用Kotlin进行Android开发
####作者：胡光洲
####学号：1501210909
![kotlin-logo](http://upload-images.jianshu.io/upload_images/620824-8ef93e15d5ef27c1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
##一、Kotlin是什么？
[Kotlin](http://www.kotlinlang.org/)  是一个基于 JVM 的新的编程语言，由 [JetBrains](https://www.jetbrains.com/) 开发。
JetBrains，作为目前广受欢迎的 Java IDE [IntelliJ](https://www.jetbrains.com/idea/) 的提供商，在 Apache 许可下已经开源其Kotlin 编程语言。
Kotlin是JVM上的静态类型语言。可以和java相互调用。

##二、为何要学这门新语言
函数是第一公民，更简洁

类型安全，正确的处理了null类型

多面手，可以用来开发服务端应用，或是安卓应用，或是浏览器运行的前端代码

有良好的IDE支持，开发效率有保障。

##三、使用Kotlin来进行安卓开发
本段参考官网的两篇教程：

[Getting started with Android and Kotlin](http://www.kotlinlang.org/docs/tutorials/kotlin-android.html) 

  [Kotlin Android Extensions](http://www.kotlinlang.org/docs/tutorials/android-plugin.html) 
  
其中第一篇介绍了如何把一个现有的android项目的java代码转换成Kotlin代码。第二篇给出了一个Kotlin编写的安卓helloworld的例子。
从该例子中可以看出Kotlin的简洁之处。
```kotlin

// Using R.layout.activity_main from the main source set
import kotlinx.android.synthetic.main.activity_main.*

public class MyActivity : Activity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // Instead of findView(R.id.textView) as TextView
        textView.setText("Hello, world!") 
    }
}


```
##四、更进一步，使用Anko框架
[Anko](https://github.com/Kotlin/anko/) 是Kotlin项目的一部分，旨在更快更轻松的开发安卓应用。是一个用Kotlin编写的库，也是专门用来进行安卓开发的DSL（领域特定语言）。 使用该库的一个显著的优势就是大幅的简化了界面的编写。
```kotlin
verticalLayout {
    val name = editText()
    button("Say Hello") {
        onClick { toast("Hello, ${name.text}!") }
    }
}
```
以上的代码对应的界面如下
![helloworld](https://raw.githubusercontent.com/Kotlin/anko/master/doc/helloworld.png)

Anko还支持很多其他的特性，比如界面动画，与数据库的交互，实现同步操作，Service,Intent等等。都实现了用更少的代码实现同样的逻辑。

官方给出了一个[Anko Example Project](https://github.com/yanex/anko-example) 。展示了anko的一些应用场景。在该范例中，介绍了诸如同步，编写UI,编写简单动画等特性。
```kotlin
override fun createView(ui: AnkoContext<MainActivity>) = with(ui) {
        verticalLayout {
            padding = dip(32)

            imageView(android.R.drawable.ic_menu_manage).lparams {
                margin = dip(16)
                gravity = Gravity.CENTER
            }

            val name = editText {
                hintResource = R.string.name
            }
            val password = editText {
                hintResource = R.string.password
                inputType = TYPE_CLASS_TEXT or TYPE_TEXT_VARIATION_PASSWORD
            }

            button("Log in") {
                onClick {
                    ui.owner.tryLogin(ui, name.text, password.text)
                }
            }

            myRichView()
        }.style(customStyle)
    }
```
上面的这一段实现了一个较为复杂的界面。有图片，有两个输入框。还有一个自定义的myRichView()。可见，借由Anko框架，能够有效的实现自定义界面的复用。且可读性比XML负责UI，java来负责对应逻辑的方式好了很多。也更简洁。


另外，需要指出的是，Anko目前的版本是0.8.1，还没有推出正式版。相关的使用资料也很少，主要依靠于项目官网的文档以及IDE的提示。虽然DSL很简洁，但是想要扩展其功能，往往需要明白其背后的实现逻辑，这也会带来新的学习成本。所以建议暂时只使用Anko来开发界面，享受其带来的便利就足够好了。

##五、结语
Kotlin作为一门新兴的基于JVM的语言。各种新特性确实令人耳目一新。借助jetbrains强大的支持。也显著的降低了它的学习成本(越来越完善的文档，强大的IDE支持，足够好的社区反馈)。从Anko框架的快速迭代也能看出jetbrain把Kotlin当作Android开发新语言的决心。

学习一门新语言，投入大量的精力，往往被称作是投入时间成本。以我个人的愚见，学习Kotlin或许是个不错的投资。