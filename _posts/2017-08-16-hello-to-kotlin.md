---
layout: post
title: "Kotlin:从对比
Java开始"
date: 2017-08-16 11:00:00 +0800 
categories: Kotlin
tag: [Kotlin,Android,Java]
---
* content
{:toc}

## 介绍
今年初，甲骨文再次对谷歌所谓的Android侵权使用Java提起诉讼，要求后者赔偿高达90亿美元。随后便传出谷歌因此计划将主力语言切换到苹果主导的Swift，不过这事后来没了跟进。

但后来,在Google IO 2017上,谷歌宣布Kotlin成为Androi官方开发语言.

Kotlin由JetBrains公司开发，于2010年首次推出，次年开源。它与Java 100%互通，并具备诸多Java尚不支持的特性，下一版的Android Studio（3.0）将提供支持。


<!-- more -->

## 特点
谷歌选择Kotlin当然不仅仅是因为与甲骨文的官司,Kotlin做为一种编程语言还是有很多长处与特点的.

如标题所讲,接下来我们来将Kotlin与Java做些对比

### POJO

java:

```yaml

public class User {
    private long id;
    private String userName;
    private String pwd;

    public long getId() {
        return id;
    }

    public void setId(long id) {
        this.id = id;
    }

    public String getUserName() {
        return name;
    }

    public void setUserName(String name) {
        this.name = name;
    }

    public String getPwd() {
        return blog;
    }

    public void setPwd(String blog) {
        this.blog = blog;
    }
}
```
这是一个简单的POJO,属性+设置+获取,三个属性的类写了将近30行代码

Kotlin:
```yaml
data class User(
var id: Long,
	var uerName: String,
	var pwd: String)
```
在Kotlin中,4行代码,包括了属性,设置,获取,不仅如此,它还包含了equals（），hashcode（），toString（）和copy（）函数

### 创建对象
Java:
```
User user = new User(1,"xuyh","adsdc234efUsd90");
```
Kotlin:
```
var user = User(1,"xuyh","adsdc234efUsd90")
```
Kotlin对象创建不用加new关键字，而且语句后面不用加分号";"（即使加上也会被忽略）。


### 避免NullPointerException

我相信所有的开发者都曾遇到一个问题-空指针异常.

对于Java代码，编译器不会强制每次使用引用变量之前进行null判断，即异常往往会在运行时报出，而这正是危险所在。

```
var str1: String = null  //Null can not be a value of a non-null type String
var str2: String? = null  //str2 can be null
var str3 = "testNull"  //non-null--String type
var str4 = null  //null
var str5: String  //non-null--String type
str5 = "testNull"  //assigned String value
var str6  //no type or initialization
var str7: String? = "testNull"
```


结合代码中的注释，我们来看这四行代码想表达的意思。

第1行，编译错误，kotlin规定如果显式指明了str1的类型，这里是String，声明时必须同时指定是否允许为空值（null），不加问号"?"表示不允许为null；

第2行，编译通过，作第一行代码的另一种情况，加了问号，并赋值为null；

第3行，编译通过，隐式赋值为"testNull"，Kotlin会自动推断出str3类型为String，之后便不可再更改了，即不可再赋值为1这种整形数据；

第4行，编译通过，隐式赋值为null，那么str4就一直为null了；

第5-6行，编译通过，前者只是指定类型，没有赋值；后者赋予str5 String类型值"testNull"同样不能赋值为其他类型值；

第7行，编译错误，既没有指定类型，也没有隐式地进行初始化，错误的原因应该是编译器不知道str6类型是什么，不能对其分配空间；

第8行，不需多解释，str7可为null，同时赋值为"testNull"；

**注意：此文为了格式统一，没有将编译或运行出错的代码注释**

解释完变量定义时关于空的概念，接下来就该看看这种保护机制能否真的让我们省心。就拿获取字串的长度为例，Kotlin中String类有个length属性，即调用方式为strObject.length。

talk is cheap show me the code

```
1  var str2: String? = null
2  println("str2.length: " + str2.length)  //compile error
3  println("str2?.length: " + str2?.length)  //print null
4  println("str2!!.length: " + str2!!.length)  //run exception
5  if (str2 != null) {
6    println("str2!!.length: " + str2!!.length)  //don't run
7  } 
8  str2 = "testNull"  //assign
9  println("str2.length: " + str2.length)  //print 8
10 println("str2?.length: " + str2?.length)  //print 8
11 println("str2!!.length: " + str2!!.length)  //print 8
12 if (str2 != null) {
13    println("str2!!.length: " + str2.length)  //print 8
14 }
```
第2行，编译错误，因为之前只是将str2声明为可以是null同时赋值为null，所以紧接着访问其length属性是不允许的；

第3行，输出"null"，加了问号就会先检查str2的赋值情况，如果是null，就不继续执行后半部分（.length），直接返回null；

第4行，运行异常，不检查的后果就是通过null引用去访问length属性；

第5-7行，不会执行到if代码块中，这里用了类似Java中的做法；

第9行，输出"8"，到这里，相比能体会到Kotlin的智能之处了，在第八行对str2赋值之后，就不会再像第二行那样报编译错误了；

第10-14行，不需多解释，不为null的str2，通过三种方式均可访问length属性；

那么这里有一个疑问，用"!!"来访问属性是不明智的选择，好像"?"更稳妥一些？毕竟后者在变量是否null的情况下都能做出相应的处理。我所能想到的需要用"!!"的场景之一是：当一个变量在声明时不能马上初始化，而在真正用到时又必须是非null的。这种情况应该并不少见吧，那次此时"!!"就派上用场了。

先举一个简单粗暴的列子：
```
var str: String? = null
//do something to assign str
val str2: String = str!!
```
当声明str的时候还需后面的处理结果给它赋值，而声明str2为非null，就必须以str!!的形式才能通过编译。

### 类方法扩展

这个特性支持在现有类的基础上扩展方法，特别是系统库中的类，因为如果是我们自定义的类，那么扩展和添加方法没有什么差别。

#### 方法定义
```
fun getArtict(): User? {
	return null
}
```
Kotlin中是以fun关键字声明方法，没有返回值时不需要在方法名后面写任何类型，默认是Unit类型（可写可不写，但其和null不是一回事，所以不写返回值类型或者写了Unit后不能够返回null）。

#### 扩展
```
fun String.printStr() {
	println("printStr: " + this)
}

var str = "testExtend"
str.printStr()
```
上面代码为类String扩展了一个printStr()，这在Java中是不可能的。因为Java中如果既不能改变原有类，又想在其基础上添加方法，就得通过新建类来继承的方式。而现实是Java中只能是单继承，这个机会太珍贵了，更残酷的是有些类还是不能继承的。

## 总结
以上主要介绍了Kotlin,通过POJO,变量空安全,类方法扩展和java做了一个简单的比较.
我们可以看出,Kotlin还是有很大的优点的,虽然本质上只是些代码糖,但是,能够提高效率的代码糖,何乐而不为呢.