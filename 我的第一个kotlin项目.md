入职公司第三周，项目组让我用kotlin去完成一个内部使用的app。下面记录一下kotlin相关笔记。因为app比较简单，可能很多kotlin特性没有用出来。学习kotlin过程中查找的资料放在了最后。

<!-- vim-markdown-toc GFM -->
[TOC]: 生成目录
----
[开发环境](#开发环境)

[kotlin几个直观的特性](#kotlin几个直观的特性)

[kotlin的基础](#kotlin的基础)

* [kotlin基本数据类型](#kotlin基本数据类型)

* [如何定义一个常量和变量](#如何定义一个常量和变量)

* [基本数据类型的转换](#基本数据类型的转换)

* [数组的定义](#数组的定义)

* [字符串输出](#字符串输出)

* [kotlin中创建一个对象不需要使用`new`关键字](#kotlin中创建一个对象不需要使用`new`关键字)

[包](#包)

[控制流](#控制流)

* [if else](#ifelse)

* [when](#when)

* [while](#while)

* [for](#for)

[类和对象](#类和对象)

* [类的定义](#类的定义)

* [定义函数：使用***fun***关键字](#定义函数：使用***fun***关键字)

* [函数参数列表的默认值](#函数参数列表的默认值)

[继承](#继承)

[抽象类的定义](#抽象类的定义)

[sealed修饰的密封类](#sealed修饰的密封类)

[备用字段或者叫做幕后字段:field](#备用字段或者叫做幕后字段:field)

[空安全](#空安全)

[kotlin中接口的定义](#kotlin中接口的定义)

* [接口的实现](#接口的实现)

* [继承一个类和实现多个接口](#继承一个类和实现多个接口)

* [解决重写冲突](#解决重写冲突)

[可见性修饰词](#可见性修饰词)

[伴随对象](#伴随对象)

[扩展函数和伴随对象的扩展](#扩展方法和伴随对象的扩展)

* [扩展函数](#扩展函数)

* [伴随对象的扩展](#伴随对象的扩展)

[数据对象](#数据对象)

[内部类](#内部类)

[匿名内部类](#匿名内部类)

[代理属性](#代理属性)

[函数](#函数)

* [单表达式函数](#单表达式函数)

* [中缀符号](#中缀符号)


* [默认参数](#默认参数)

* [变长参数](#变长参数)

* [函数内部定义函数](#函数内部定义函数)

* [函数可以赋值给另一个函数](#函数可以赋值给另一个函数)

[闭包](#闭包)

[自定义dsl](#自定义dsl)

[kotlin与java的对比](#kotlin与java的对比)

* [java有的而kotlin没有](#java有的而kotlin没有)

* [kotlin有的而java没有](#kotlin有的而java没有)

[学习时查找的资料](#查找的资料)




#### 开发环境
AS version： `android stdio 3.0 bate 5 `
android stdio 3.0以上是支持直接创建kotlin项目的，如果是android stdio 3.0以下，需要额外的添加一些依赖。

#### kotlin几个直观的特性 
如果看不懂下面的内容没关系，后面会详细的讲，我也是菜鸟，看不懂就比较慌！
1. java中必须使用`;`结尾。 而kotlin中一行代码结束是不需要使用`;`结尾的。


2. 一个文件下，不一定需要一个类名，只要新建一个kotlin类型的文件(文件扩展名 为`.kt `的文件)，就可以直接写代码。
eg: 定义一个名为`BaseExtension.kt`的文件，功能是对类扩展了一些方法。
```
package manage.ad.yiba.com.admanage.common.extension

import android.content.Context
import android.support.v7.app.AlertDialog
import android.util.Log
import android.widget.Toast
import manage.ad.yiba.com.admanage.common.BaseActivity


/**
 * Created by yh on 8/24/17.
 *  该kt文件用来对类的扩展
 */

//Context 基础类的扩展
fun Context.toast(content: CharSequence, time: Int = Toast.LENGTH_LONG){
    Toast.makeText(this,content,time).show()
}

//扩展一个log方法
fun Any.log(tag: Any = "Tag",content: String?){

    Log.i(tag.toString(),content)
}
fun Any?.toString(): String {
    if (this == null) return "null"
// 在空检查之后,`this` 被自动转为非空类型,因此 toString() 可以被解
//    析到任何类的成员函数中
//    ChinaPerson("",2,"","","")
//    ChinaPerson("",2,"","","","")
    return toString()
}

fun BaseActivity.showDialogMessage(title: String = "", body: String, rightCode: ()->Unit = {System.exit(0)},leftCode: ()->Unit = {}) {
    var exitDialog: AlertDialog? = null
    val builder = AlertDialog.Builder(this)
    builder.setTitle(title).setMessage(body).setPositiveButton("OK") { dialog, which ->
        rightCode.invoke()
    }.setNeutralButton("Cancel"){dialog, which ->
        leftCode.invoke()
        try {
            exitDialog?.dismiss()
        }catch (e: Exception){
        }
    }

    exitDialog = builder.create()
    exitDialog.show()
}
```
3. 一个扩展名为`.kt`文件中，可以定义多个类，这里定义的类跟javaBean是一个功能，这是在kotlin中的写法。
eg:  名为`Response.kt`的文件中，包含多个类
```
package manage.ad.yiba.com.admanage.moudle.adDeploy

import manage.ad.yiba.com.admanage.retrofit.BaseResponse
import java.io.Serializable

/**
 * Created by yh on 9/19/17.
 */
data class AdDeployResponse(var data: MutableList<AdDeployData>): BaseResponse()

data class AdDeployData(var app: String = "", var appName: String = "", var token: String = "", var tokenStatus: Int = 0, var date: String = "", var type: Int = 0, var ids: String = "", var adPositions: String = "",var title: String = "",var viewType: Int = 1): Serializable


//详情页面

data class AppDetailResponse(var data: AppDetails):BaseResponse()

data class AppDetails(var app: String,var appName: String,var token: String,var tokenStatus: Int,var appDetails: MutableList<AppDetail>)

data class AppDetail(var adPlaceId: String,var app: String,var adStatus: Int,var serverId: Int,var sources: MutableList<Source>?):Serializable

data class Source(var id: String = "1",var packageName: String = "com.wifi.cool",var adPositionId: String = "1",var adCenterId: Int = 23,var level: Int =2,var centerName: String = "xxxx",var size: String = "RECT",var type: String = "DO"):Serializable
```

#### kotlin的基础
######  kotlin基本数据类型：
|类型  |                  字宽|
|:--:|:--:|
|Double            |        64|
|Float   |                   32|
|Long     |                 64|
|Int          |                32|
|Short      |                16|
|Byte        |                8|
在 Kotlin 中,所有的类型都是一个引用类型。在java中，分为基本数据类型和引用数据类型,这里需要注意的是，这些类型的***首字母都是大写的***。

###### 如何定义一个常量和变量
1. 定义常量的关键字为`val` 
```
val  a: Double = 27  //val + 变量名 +`:` + 空格+"类型" +" = " +"值"
val a = 27                //如果知道具体的类型，可以不带类型，kotlin会自动类型推断
```
注： a. 前面默认添加了public。    
   

2. 定义变量的关键字`var`
```
var name: String = "yhai"   //var + 变量名 +`:` + 空格+"类型" +" = " +"值"
var name = "yhai"              //如果知道具体的类型，可以不带类型，kotlin会自动类型推断
```
注意：
1.上面的空格是为了好的代码格式
2. 对于变量，如果需要延迟初始化,需要在var 前面添加***lateinit***关键字。延迟初始化后面还会详细讲解。
```
lateinit var name: String

name = "yhai"
```
3. 使用***var***修饰的变量，实际上也就拥有了get和set方法，而val修饰的没有set方法。


###### 基本数据类型的转换
```
val value0: Int  = 23
val value1: Long = value0.toLong()
val value2: Double = value0.toDouble()
val value3: Float = value0.toFloat()
```
kotlin中的基本数据类型转换需要显示的调用对应的方法

###### 数组的定义
java
```
Object[] objects = new String[2];
objects[0] = "0";
objects[1] = 1;
```

kotlin 
```
    var array: Array<Any>
    var strings: Array<Any> = arrayOf("1", "2")
    array = strings
```
  kotlin中数组使用Array<TYPE>  定义。上面的Any是kotlin中的最根上的类，可以理解为跟java中的Object一样。

###### 字符串输出
```
val value = "hello，world"
println("str ==  $value")

val person = Person()
println("str ==  ${person.age}")
```
在""中，使用`$value`就能把value给打印出来，如果person 是一个复杂的对象，需要使用`${表达式}`。

###### kotlin中创建一个对象不需要使用`new`关键字
|java  |                  kotlin|
|:--:|:--:|
| Persion person  = new Person()    |     val person: Persoin = Person()   |
|| val person = Person() `自动类型推断`|

#### 包
kotlin 中package  比较特殊，文件上的package 可以随便定义,例如：
```kotlin
package com.xxx.xxx.xxx
class  Person{
}
```
而这个文件真实的路径在`com.yhai.kotlin`目录下，在导入包的时候，需要注意下，否者看到这个文件在某个目录下，但是导入这个文件的包名并不是`com.yhai.kotlin`,而是`com.xxx.xxx.xxx`。


####  控制流
###### ifelse
```kotlin
//传统用法
var max = a
if (a < b)
max = b
//带 else var max: Int if (a > b)
max = a
else
max = b
//作为表达式
val max = if (a > b) a else b
```
if 分支可以作为块,最后一个表达是是该块的值:
```kotlin
val max = if (a > b){
    print("Choose a")
    a
} else{
    print("Choose b")
b }
```
注：kotlin 的if判断跟java里面的使用方式差不多，唯一不同的是可以作为一个表达式有返回值。
###### when
用法一
```kotlin
when (x) {
    1 -> print("x == 1")
    2 -> print("x == 2")
    else -> { //Note the block
        print("x is neither 1 nor 2")
    }
}
 

or
    when (item.itemId) {
            R.id.nav_setting->{

            }
            R.id.nav_share -> {

            }
            R.id.nav_send -> {

            }
        }
```
用法一
```kotlin
when (x) {
    1 -> print("x == 1")
    2 -> print("x == 2")
    else -> { //Note the block
        print("x is neither 1 nor 2")
    }
}
 
```
用法二
```kotlin
when (x) {
    0,1 -> print("x == 0 or x == 1")
    else -> print("otherwise")
}
 
```

用法三
```kotlin
when (x) {
    parseInt(s) -> print("s encode x")
    else -> print("s does not encode x")
}
```
用法四:作为表达式
```kotlin
val hasPrefix = when (x) {
    is String -> x.startsWith("prefix")
    else -> false
}
```
###### while
```kotlin
while (x > 0) {
    x--
}
do {
    val y = retrieveData()
} while (y != null) // y 在这是可见的
```
###### for
```kotlin
for(item in collection){
}
```

对于各种循环，如何正确的终止呢？ 
[参考学习资料](https://juejin.im/post/58fd31dd8d6d8100589813bf)

#### 类和对象
##### 类的定义
定义一个类更java中一样，使用class关键字。 
 第一种定义类的方式：没有参数，没有类体
 ```kotlin
class Person 
```
这样就定义了一个Person类，定义的这个Person类实际上是没有写错的。kotlin中如果这个类中没有属性，没有方法(后面会讲怎么跟这个类添加一个方法),类名后面的***(){}***就可以省略掉
第二种定义类的方式：有类体，没有参数
```kotlin
class Person{
}
```
第三种种定义类的方式：有参数，有类体
```kotlin
class Person(var name: String,var age: Int){
  //TODO 做些事情
}
```
在使用java的时候，我们在构造函数中传入参数，然后在构造函数体内做一些初始化的事情，而上面的构造函数的参数是直接跟在类名后面，在哪里做初始化的事情呢？实际上上面有个***init{}***代码块没写，完整的如下：
```kotlin
class Person(var name: String,var age: Int){
      init{
      //在这个位置做类似于java中构造函数体类做的事情。    
    }
}
```
这上面有个隐藏的知识点：
1. 在构造函数中定义的参数使用var修饰，该参数将会在当前类全局都可以使用。不使用var修饰，那么该参数的使用范围只会在***init{}***中。这个我是通过将kotlin编译成.class,然后再翻译成java代码知道的。具体操作后面讲。

第四中定义类的方式：有参数，没有类体
```kotlin
class Person(val name: String,val age: Int)
```
如何定义多个构造函数：如果类有主构造函数,每个二级构造函数都要,或直接或间接通过另一个二级构造 函数代理主构造函数。在同一个类中代理另一个构造函数使用 this 关键字:
```kotlin
class Person(val name: String,val age: Int ) {
    init{
          //这里会是最后调用的地方
      }
    constructor (name: String,age: Int,var other: String) : this(name,age) {
    }
}
```

注：在上面第一种定义类的地方，存在一个默认的构造函数，这个构造函数是不带参数的。
```kotlin
class Person(){
}
```
###### 定义函数：使用***fun***关键字
格式： fun + 函数名+(参数列表)+"空格"+":" +"空格" 返回类型+{//函数体}
```kotlin
fun  dataIsModify(cache: List<String>,data: MutableList<String>) : Boolean{
    //...
    return false
}
```
注：1.  参数列表不能使用var 或val修饰，默认是有val修饰了。
2. 空格是为了代码格式 
###### 函数参数列表的默认值
我们定义函数的时候，调用函数的时候，必须要传入对应的参数，kotlin中，可以对参数列表设置默认的值，也就是说，有些参数可传可不传。
```
fun showDialogMessage(title: String = "", body: String = "this is a dialog") {
    //create a dialog 
}

```
调用上面这个函数：
```
showDialogMessage() //使用默认值
//或者只修改 body的值
showDialogMessage(body = "change dialog hint")//body 参数不使用默认值
```


##### 继承
在kotlin中，类默认情况下是不能被继承的，需要在定义类的前面加上***open***关键字，该类才能被继承。同理，如果函数可以被继承，也需要使用***open***修饰,默认不能被继承。
继承的语法：使用`:` + 被继承的类名+()，***()***如果有参数的话，需要传递进去。
```
open class Base(var x: Int,var b: Int){
    open fun add() : Int{
        return x + b
    }
}
class Person(x: Int, b: Int) : Base(x,b){
}
```
注：在这里，Person类的构造中带有两个参数，没有使用***var***或者***val***修饰，原因是父类已经定义了x，b两个属性。在Person中定义就冲突了。如果一定要使用***var***或***val***修饰，需要改变参数名；
```kotlin 
open class Base(var x: String,var b: Int)

class Person(var xx: String,var  bb: Int) : Base(xx,bb){
}
```
//这里我遇到一个类似的场景，我在后面讲。
注： 如果要重写Base中的方法,那么该方法需要用***override***修饰，表示是重写的父类的方法。java中使用的是***@override***注解表示被重写的方法。
```
class Person(x: Int, b: Int) : Base(x,b){
    override fun add(): Int {
        return super.add()
    }
}
```

###### 抽象类的定义
使用***abstract*** 关键字修饰，跟java中一样。同理，里面的抽象方法也需要***abstract***修饰。和java一样，也可以定义具体的方法。
```
abstract class Derived  {
    abstract fun f()  
    
    fun f2(){   
    }
}
```
###### sealed修饰的密封类
类的定义中，有一种密封类的概念
[密封类相关资料](https://wangjiegulu.gitbooks.io/kotlin-for-android-developers-zh/mi_feng_lei.html)

###### 类的属性
之前有讲到定义一个变量和一个常量的方法，他们的完整的语法如下：
```
var <propertyName>: <PropertyType> [ = <property_initializer> ]
    <getter>
    <setter>
```
如果属性类型可以从初始化语 句或者类的成员函数中推断出来,那么他的类型也是忽略的。
语法中的初始化语句,getter 和 setter 都是可选的，他们是什么意思呢？如下：
```kotlin
var name : String
    get() = name// 可以对获取这个做一些修改。类似于java中的get方法体重做了一些事情。
    set(value) {
        //设置值做一些事情
    }
```
######  备用字段或者叫做幕后字段:field
[这个概念的资料](https://hltj.gitbooks.io/kotlin-reference-chinese/content/txt/properties.html)
我还没有怎么使用，所以放个链接，自己去看。

##### 空安全
是时候讲下空安全是什么东西了。在kotln中，有一个很屌的功能，就是避免java中报null错误的处理方式。
```kotlin
var name: String = null  //语法不通过。必须对该属性赋值,而且这个值不能是null
```
 //这样定义是语法检查不过的，因为你对name赋值为null，那么如果你一定要让这个name可以为null怎么做呢
```kotlin
var name: String? = null
```
//对，在类型String后面跟上一个?,大概意思是说，这个那么可以为空。
那么这个name使用的过程中，就有可能是个null，意味着要报null异常。为了防止报null，如下使用：
```
name?.toString()
```
在name上使用***?.*** 调用它的方法。这样就能保证使用name避免出现null异常。
如果你定义了一个变量，这个变量为null，你还是坚决要调用他，怎么办呢？使用***!!***
```
   var name = null
    name!!.toString() // 这样就必然会报null异常
```
这里只要记住，？的意思是说可能为空，在调用可能为空的时候，使用***?.***避免异常和使用***!!***为空也要调用。

#### kotlin中接口的定义
 kotlin中的接口中可以定义方法体，跟抽象类一样，这样接口和抽象类就一样了，我没有搞懂什么意思！接口的定义还是使用***interface***关键字
 ```
interface MyInterface {
    val property: Int // abstract
    val propertyWithImplementation: String
        get() = "foo"
    fun foo() {
        print(property)
} }
```
###### 接口的实现
```
class Child : MyInterface {
    override val property: Int = 29
}
```
接口实现和基础类的区别在于是有后面跟***()***

###### 继承一个类和实现多个接口
```
class Base(){

}
interface  MyInterface0 {
    override val property: Int = 29
}
interface MyInterface1 {
     fun  test(){
    }
}
class Child : Base(), MyInterface0, MyInterface1 {

}

```
###### 解决重写冲突
多个接口中有相同的方法
 在文档中直接copy的代码如下：
```
interface A {
    fun foo() { print("A") }
    fun bar() 
}
interface B {
    fun foo() { print("B") }
    fun bar() { print("bar") }
}
class C : A {
    override fun bar() { print("bar") }
}
class D : A, B {
    override fun foo() {
        super<A>.foo()
        super<B>.foo()
    }
}
```
#### 可见性修饰词
在 Kotlin 中有四种修饰：private，protected，internal，以及public，默认的为public。

private 只在该类(以及它的成员)中可见

protected 和 private 一样但在子类中也可见

internal  在本模块的所有可以访问到声明区域的均可以访问该类的所有成员 ( internal — any client inside this module who sees the declaring class sees its internal members;)

public 任何地方可见 (public — any client who sees the declaring class sees its public members.)

#### 伴随对象
这个相当于java中的static 修饰的方法和属性.因为kotlin没有static关键字，实现静态方法的方式如下：在***companion object{} ***中de ***{}***定义方法和属性就可以。
```
class Person7{
    companion object{
        var name = ""
        fun test(){  
        }
    }
}
Person7.test()//使用方式
```

#### 扩展方法和伴随对象的扩展
关于属性的扩展没怎么搞懂，下面不讲，项目中我暂时也没有用到。
在类定义的第一种定义方法中，定义了如下一个类
```
class Person
```
显然，定义了这么一个类是没有什么实际用途的。我们使用kotlin的扩展***extension***特性对其扩展一些方法。也就是说，我可以给所有的类添加方法。实现方式如下：


###### 扩展函数
fun  + "要对扩展的类的名字" + "."+方法名()+ "{} //方法体"
```
fun Person.eat(name: String){
    System.out.println("吃的是什么 = $name")
}
```
这样Person这个类就已经有了一个叫做***eat(name: String)***方法了。

###### 伴随对象的扩展
格式：fun +类名+"."+"Companion"+"."+"方法名"+"(`参数列表`)"+"{}"
```
fun Person7.Companion.test2(){

}
```
##### 数据对象
java中有个javaBean的概念，在kotlin中有自己的定义方法使用***data***修饰的的class类
```
data class Source(var id: String = "1",var packageName: String = "com.wifi.cool",var adPositionId: String = "1",var adCenterId: Int = 23,var level: Int =2,var centerName: String = "xxxx",var size: String = "RECT",var type: String = "DO"): Serializable
```
上面有如下几个特点：
1. 参数需要有var或val修饰，可以转换成java代码对比。
2. 参数有默认值
3. 实现了Serializable接口

##### 内部类
类可以标记为 inner 这样就可以访问外部类的成员。内部类拥有外部类的一个对象 引用:
```
class Outer {
    private val bar: Int = 1
    inner class Inner {
        fun foo(): Int{ return bar}
    }
}

```
##### 匿名内部类
使用object关键字
```
window.addMouseListener(object: MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) {
// ...
}
    override fun mouseEntered(e: MouseEvent) {
        // ...
} })
```
##### 代理属性
代理属性的意思我理解为你的属性交给我来管理。什么意思呢？
```
class User(){
var name: String  = ""
var age: Int = 23
}
val  user = User()
user.name = "yhai"
user.age = 27
```
上面这个User对象的name和age属性的值发生改变时，我要把这个值通过SharePerference存着本地。那我就可以把这个属性代理起来，保存在本地的这个事情就在这个代理类中实现。这个类需要继承ReadWriteProperty<Any?, T>类。下面这个代理类是用来操作sp的。
```

import android.content.Context
import manage.ad.yiba.com.admanage.App
import kotlin.properties.ReadWriteProperty
import kotlin.reflect.KProperty

/**
 * Created by yh on 8/25/17.
 */
class Preference<T>(context: Context = App.instance, val name: String?, val default: T) : ReadWriteProperty<Any?, T> {

    val prefs by lazy {
       context.getSharedPreferences("yiba",Context.MODE_PRIVATE)
    }
    override fun getValue(thisRef: Any?, property: KProperty<*>): T = with(prefs){
        when (default) {
            is Long -> {
                getLong(name, default)
            }
            is String -> {
                getString(name, default)
            }
            is Float -> {
                getFloat(name, default)
            }
            is Int -> {
                getInt(name, default)
            }
            is Boolean -> {
                getBoolean(name, default)
            }
            else -> {
                throw IllegalArgumentException("This type can be saved into Preferences")
            }
        } as T
    }

    override fun setValue(thisRef: Any?, property: KProperty<*>, value: T) = with(prefs.edit()){
        when (value) {
            is Long -> putLong(name, value)
            is String -> putString(name, value)
            is Float -> putFloat(name, value)
            is Int -> putInt(name, value)
            is Boolean -> putBoolean(name, value)
            else -> {
                throw  IllegalArgumentException("存储的类型不存在，请确认储存类型")
            }
        }.apply()
    }

}
```
实际上就是代理这个属性的get和set方法。
上面有两个东西没有提到：
1. is 相当于java中的instanceof
2. as 相当于java的强制，例如：
java
```java
TextView  tv = (TextView)findViewById(id);
```
kotlin
```kotlin
TextView  tv =findViewById(id) as TextView
```
3. with(obj) 相当于在obj的内部。
```
val user = User()
with(user){
  //在这里可以直接操作User的属性和方法。而不需要user.name或者user.eat("xxxx")，省略user.
}
```


#### 函数
之前对已经讲了怎么定义一个函数，但是在kotlin中函数作为一等公民，显示还有很多高级的用法。
###### 单表达式函数
当函数只返回单个表达式时,大括号可以省略并在 = 后面定义函数体
```
fun test(){
return  23
}
```
可以写成下面
```
fun test() =23
```
###### 中缀符号
在满足以下条件时,函数也可以通过中缀符号进行调用:
 它们是成员函数或者是扩展函数  只有一个参数 使用 infix 关键词进行标 记
```
//给 Int 定义一个扩展方法
infix fun Int.shl(x: Int): Int { ...
}
1 shl 2 //用中缀注解调用扩展函数 
1.shl(2)
```
这个在我第一次看的时候，感觉屌爆了。
###### 默认参数
```kotlin 
fun read(b: Array<Byte>, off: Int = 0, len: Int = b.size() ) {
...
}
```

注：函数如果没有返回值的时候是一个Unit类型，默认可以不写，例如上面的函数也可以写成
```
fun read(b: Array<Byte>, off: Int = 0, len: Int = b.size() )  : Unit{
...
}
```

###### 变长参数
```
fun asList<T>(vararg ts: T): List<T> {
    val result = ArrayList<T>()
    for (t in ts)
        result.add(t)
    return result
}
```
使用如下：
```kotlin
 val list = asList(1, 2, 3)
```
java中使用的是...表示可变参数，kotlin中使用的是vararg。

###### 函数内部定义函数
什么优缺点现在我也没有搞懂。
```
fun dfs(graph: Graph) {
    val visited = HashSet<Vertex>()
    fun dfs(current: Vertex) {
        if (!visited.add(current)) return
        for (v in current.neighbors)
dfs(v) }
    dfs(graph.vertices[0])
}
```
###### 函数可以赋值给另一个函数

```
//定义了一类
class Person6
//给该类扩展了一个方法
fun Person6.eat(name: String){

}
//创建Person6的对象
val v = Person6()
//将v.eat(xxx: String)赋值给另一个函数
fun test(xx: String) = v.eat(xx)
``` 
#### 闭包
 ```
private val value = {xx: String,bb: String->Unit

}
```
上面的代码，定义了一个闭包，并且把这个闭包赋值给了一个value的变量。
怎么使用它了，有两种方式：
```
//第一种方法使用invoke，后面括号是参数
value.invoke("value0","value1")
//第二种方法类似于函数的调用
value("value0","value1")
```

闭包实际上就是一个使用***{}***包起来的代码块，在上面这个闭包中，***->***左边是闭包的参数列表，右边是返回类型。在闭包的最后一行作为默认的返回值。
```
{xx: String,bb: String->Unit
  {
   //闭包体
  }
}
```

闭包可以作为一个函数的参数如下: 
test方法有一个闭包作为参数，这个闭包的参数列表为空，返回值为Unit
```
fun test(code: ()->Unit){
    code.invoke()
}
//调用写成下面这样
val clourse = { 

}
test(clourse)
 //在可以写成
test({})
//如果一个函数的参数只有一个闭包参数可以省略()
test{xx: String ->{
  }
}

//这个闭包体可以省略,这个以闭包作为参数的函数可以写成下面这样。
test{
}
```
#### 自定义dsl
这个知识点不理解不影响android的开发
在我们的gradle中，有类型与下面的代码，他是怎么实现的呢？
```
  email{
        address = "1454025171@qq.com"

        baseInfo()
        from("this is  from")
        to("this is to fun")
        body{
            p("this is body fun")
        }

    }
```
#### dsl代码实现
下面这段代码是网上的一个哥们实现的，我忘了保存其地址！！

```

class EmailSpec{
    var address: String  = ""

    fun baseInfo() = println("address = $address")
    fun from(from: String) = println("From: $from")
    fun to(to: String) = println("To: $to")
    fun subject(subject: String) = println("Subject: $subject")
    fun body(init: BodySpec.() -> Unit): BodySpec { //这个方法的参数为一个函数，该参数函数返回Unit。该方法返回BodySpec
        val body = BodySpec()
        body.init()
        return body
    }
}


class BodySpec {
    fun p(p: String) = println("P: $p")
}

/**
 * 1. 定义一个方法函数作为参数的方法
 * 2. 对EmailSpec 扩展了一个方法，该方法没有参数，没有返回值；目的是可以调用内部的其他方法
 * 3. 该方法返回一个EmailSpec 的对象
 *
 */

fun email(init: EmailSpec.()->Unit) {
    val email = EmailSpec() //创建一个对象
    email.init()
    //调用的是参数中定义的init函数，不是系统的init函数
}
```
上面这个dsl理解了email这个方法，就理解了怎么定义dsl。
首先定义一个反复，这个方法有一个参数，这个参数是对EmailSpec类扩展了一个空函数，返回值也为空。在这个email方法的体中，创建了EmailSpec的实例，然后这个实例调用了这个扩展方法。EmailSpec.()->Unit 这个扩展方法是在方法中定义的，所以他的范围也只在这个email方法内部区域。

#### 关于操作符的使用
关于操作符，有点想Rxjava里面的操作符的用法。
[关于操作符的使用](http://blog.csdn.net/coder_pig/article/details/76024728)


那些东西没有说：
对象表达式和声明,代理类，异常，this表达式，运算符重载 ...

#### kotlin与java的对比
copy的内容
###### java有的而kotlin没有
 异常检查
 原始类型不是类
 静态成员
 非私有成员
 通配符类型
###### kotlin有的而java没有
字面函数+内联函数=高性能自定义控制结构 扩展函数 空安全 智能转换 String 模板 性能 一级构造函数 First-class delegation 变量和属性类型的类型接口 单 例模式 变量推断和类型预测 范围表达式 运算符重载 伴随对象
   

本来准备一次性写完，太累了，分成两篇来写！！！下一篇将如何写一个android项目，如果你有使用java开发android经验，那将会是非常的容易。
下一篇将如下类容
#### kotlin 开发android项目的相关经验
###### anko相关的知识点 布局和其他
###### 网络请求
###### 基础类的扩展
###### 定义组合式控件
###### 点击事件,

#### 查找的资料

[kotlin 语法相关资料](https://github.com/huanglizhuo/kotlin-in-chinese)

[kotlin 官方原文资料](http://kotlinlang.org/docs/reference/delegated-properties.html)

在学习go语言的时候，有多返回值，在kotlin中，可能用解构声明比较
[解构声明](https://www.kotlincn.net/docs/reference/multi-declarations.html)

[关于操作符的使用](http://blog.csdn.net/coder_pig/article/details/76024728)

[kotlin for  android](https://www.gitbook.com/book/wangjiegulu/kotlin-for-android-developers-zh/details)

[kotlin for  android](https://wangjiegulu.gitbooks.io/kotlin-for-android-developers-zh/nei_bu_lei.html)

[kotlin 资料大全](http://blog.csdn.net/cenzr/article/details/72674849)

[kotlin基础资料](http://www.kotlincn.net/docs/reference/lambdas.html)

[什么是内联函数](http://www.cnblogs.com/QG-whz/p/4641479.html)

[kotlin和java的对比文档](https://fabiomsr.github.io/from-java-to-kotlin/classes.html)

[关于dsl的资料](http://ice1000.org/2016/09/28/LearnKotlin5/#extension-%E6%98%AF%E5%95%A5)

#### [这篇文章让我读出了闭包的味道，Kotlin语法基础，函数与闭包](https://www.kotlintc.com/articles/1714)
//闭包也是函数，是一个可以读取其他函数内部变量的函数。