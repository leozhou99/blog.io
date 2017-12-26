---
layout: post
title: String、StringBuilder、StringBuffer的区别
date: 2017-12-26
categories: blog
tags: [String,StringBuilder,StringBuffer]
description: String是字符串常量，StringBuilder在单线程中操作比较快，StringBuffer是线程安全的。
---

（参考来自https://www.cnblogs.com/A_ming/archive/2010/04/13/1711395.html）
# 1. 三者在执行速度方面的比较：

   StringBuilder >  StringBuffer  >  String，其中String是效率最低的。

# 2. String效率最低的原因：
     
    String：字符串常量
	StringBuffer：字符串变量
	StringBuilder：字符串变量

在String的源码中，构造函数是由final来修饰的。

在操作String时，例如在String后面追加字符串时，JVM会新建一个同名的String对象进行追加字符串的操作。

每当用String操作字符串时，实际上是在不断的创建新的对象，而原来的对象就会变为垃圾被ＧＣ回收掉，所以效率会比较低。

# 3.例外

## 1) String对于普通字符串进行操作时，速度不亚于StringBuilder和StringBuffer:

	String str = "This is only a" + "simple" + "test";===等价于===
	String str = "This is only a simple test";

## 2)当String对String对象进行操作时，还是有创建新String对象的过程，所以速度比较慢:

	String str2 = “This is only a”;
	String str3 = “ simple”;
	String str4 = “ test”;
	String str1 = str2 +str3 + str4;
	此时速度就比较慢了。

# 4.StringBuilder和StringBuffer的区别

## 线程安全问题

   StringBuilder：线程非安全的</br>

StringBuffer：线程安全的</br>

   当我们在字符串缓冲去被多个线程使用是，JVM不能保证StringBuilder的操作是安全的，虽然他的速度最快，但是可以保证StringBuffer是可以正确操作的。当然大多数情况下就是我们是在单线程下进行的操作，所以大多数情况下是建议用StringBuilder而不用StringBuffer的，就是速度的原因。

# 5.对于三者使用的总结

## 1). 如果要操作少量的数据用：String

## 2).单线程操作字符串缓冲区下操作大量数据：StringBuilder

## 3).多线程操作字符串缓冲区下操作大量数据：StringBuffer









