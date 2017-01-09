---
layout: post
title:  "Java 8 new features!"
date:   2016-12-21 12:21:22 +0800
categories: java
---



This is a post introducing the new Features of Java8.



## Why we need JDK?

Why do we need JDK when we are already using Android SDK? After all, we are not developing for JVM.
The Android build process depends on a number of tools from the JDK. Check out the build system overview documentation. 

- The first big piece we need from JDK is javac- all your source code written in Java needs to be compiled before it can be converted to the DEX foramt.
- Once your code has been compiled, dexed, and packaged into an APK, we need jarsigner to sign the APK.
- Is there any difference between using JDK 1.6, 1.7 and 1.8? That depends on what features you are using from each. Older projects that don't use Java 7 features can use Java 6 without issue. 

There are some efforts out there to bring Java 8 features to Android, most notably gradle-retrolambda. Some of these require JDK 8 to compile properly.



## Android配置

enable Java8

```
android {
  ...
  defaultConfig {
    ...
    jackOptions {
      enabled true
    }
  }
  compileOptions {
    sourceCompatibility JavaVersion.VERSION_1_8
    targetCompatibility JavaVersion.VERSION_1_8
  }
}
```

## Android supported Java 8 Language Features and APIs

- Default and static interface methods

- Lambda expressions (also available on API level 23 and lower)

- Repeatable annotations

- Method References (also available on API level 23 and lower)

- Type Annotations (also available on API level 23 and lower)

## Default and static interface methods

- Java interface default methods
	- Defender Methods / Virtual extension methods

- Java Interface Static Method
	- similar to default method except that we can’t override them in the implementation classes

### why java 8 import default interface methods


- 设计接口需要小心翼翼(Tough)
	- 随着功能扩展和时间推进，如果要在接口添加
新方法，那么实现该接口的类也要响应实现该
函数

- 大部分框架都会提供一个实现类xxImpl,
    - 再extends该类选择自己需要的接口

![why need default and static method](http://i.imgur.com/lDJTlUc.png)

### default method sample
![default method sample](http://i.imgur.com/KJt94Io.png)
Java8之前，那么每个实现了该interface的类都要实现sort这接口

### Default method好处多多
- 扩展接口不用担心会破坏当前的实现类

- Default interface 使abstract class和interface更加泾渭分明

- 避免大量的Utility classes

- 避免编写大量实现类

- 优化Collections API，以便支持Lambda

- Default method不能重载Object基类的方法

### Java 8才支持(否则会提示出错)
![support in java8](http://i.imgur.com/q8oX9vl.png)


### 发散思维
- Java 不支持多继承—就是说一个类只能继承另外一个类。想同时继承两个类？Forget it！
	- 为什么？

- Java接口(Interface)支持多重继承
	- 接口中所有的方法必须是抽象方法。(java 7及以前)

- Java8 Interface 支持default method (java 8)
	- 难道没有问题么？

### Diamond problem
![Diamond problem](http://i.imgur.com/nTjxtWD.png)

### default methods - Diamond Problem
![Diamond Problem](http://i.imgur.com/1pCXIHe.png)
如果B或者C其中有一个并没有实现sayHi() ， 编译器不会报错

### have got to override to avoid diamond problem
![override to avoid diamond problem](http://i.imgur.com/uwHyUlr.png)
Avoid Diamond problem , we have to override sayHi()


### cannot override method from Object
![cannot override method from Object](http://i.imgur.com/3WWJRPA.png)

### Static Method
- Static method[如果有的话]是interface领土不可分割的一部分
	- Class不能implement这个方法
	- Security—保证方法的唯一性

- 方便interface提供utility方法
	- Null check
	- Collection sorting

- 可以直接通过接口的方法调用来调用static method

### Abstract Classes Vs Default/Static Methods
- 抽象类可能包含变量，接口[interface]只包含方法和常量

- 类只能单继承，interface可以多继承

- 不是所有地方都推荐用Default/Static Methods

## Java Annotations

### short description
- What
	- a form of metadata, provide data about a program that is not part of the program itself.
	- Metadata is data about data. So Annotations are metadata for code.

- Why
	- Free of boilerplate Java code
	- Less likely to contain bugs

- When
	- Introduced in JDK5

### java reflection
- Performance Overhead
	- Generally though the JVM may optimize the normal instantiation but could not make optimizations for the reflective use case.

- Security Restrictions

- Exposure of Internals

Performance Overhead
Because reflection involves types that are dynamically resolved, certain Java virtual machine optimizations can not be performed. Consequently, reflective operations have slower performance than their non-reflective counterparts, and should be avoided in sections of code which are called frequently in performance-sensitive applications.
Security Restrictions
Reflection requires a runtime permission which may not be present when running under a security manager. This is in an important consideration for code which has to run in a restricted security context, such as in an Applet.
Exposure of Internals
Since reflection allows code to perform operations that would be illegal in non-reflective code, such as accessing private fields and methods, the use of reflection can result in unexpected side-effects, which may render code dysfunctional and may destroy portability. Reflective code breaks abstractions and therefore may change behavior with upgrades of the platform.


### custom annotation
![annotation policy](http://i.imgur.com/CUcEG2y.png)
You apply @Documented when defining an annotation, to ensure that classes using your annotation show this in their generated JavaDoc.

### Repeatable annotations
![Repeatable annotations](http://i.imgur.com/jvOljJV.png)


## java8 Stream
- InputStream and OutputStream from Java I/O? NO!

- Monads—java函数式编程
[ˈmɑ:næd]

- Stream() / parallelStream()  create a sequential or a parallel stream

- Stream operations一般和lambda结合使用
	- intermediate operations
	- terminal operation

- Stream operations
	- non-interfering
	- stateless

A function is non-interfering when it does not modify the underlying data source of the stream

A function is stateless when the execution of the operation is deterministic, e.g. in the above example no lambda expression depends on any mutable variables or states from the outer scope which might change during execution.

### java8 Stream

- 元素集

- 数据源

- 聚合操作(aggregate operations) ： filter, map, limit, reduce, find, match等等

- 管道(pipelining)

Stream 不是集合元素，它不是数据结构并不保存数据，它是有关算法和计算的，它更像一个高级版本的 Iterator。
Stream 就如同一个迭代器（Iterator），单向，不可往复，数据只能遍历一次，遍历过一次后即用尽了，就好比流水从面前流过，一去不复返。

![Java8 Stream](http://i.imgur.com/eZ1Kv3z.png)

### Stream 有丰富的接口
![Stream API](http://i.imgur.com/jUyfcaZ.png)


### Java8 Predicate接口
- 方便单元测试

- 方便代码管理

- 可读性

- Predicate 接口只有一个参数，返回boolean类型。该接口包含多种默认方法来将Predicate组合成其他复杂的逻辑（比如：与，或，非）


## Lambda

### Introduction
- What is lambda
	- Lambda (uppercase Λ, lowercase λ) is the 11th letter of the Greek alphabet
- Lambda 表达式”是一个匿名函数
![Lambda icon](http://i.imgur.com/IcU4yic.png)
![lambda tips](http://i.imgur.com/uP0HUSz.png)

### Lambda Syntax
- 包含三个部分
	- 一个括号内用逗号分隔的形式参数，参数是函数式接口里面方法的参数

	- 一个箭头符号：->

	- 方法体，可以是表达式和代码块，方法体函数式接口里面方法的实现，如果是代码块，则必须用{}来包裹起来，且需要一个return 返回值，但有个例外，若函数式接口里面方法返回值是void，则无需{}

- (parameters) -> expression 或者 (parameters) -> { statements; }


### why Lambda
- Lambda expression provides a good alternative to Closure.

- adds that missing link of functional programming to Java. 

- in Java, the lambda expressions are represented as objects

### Lambdas VS Closure
- Lambda 是匿名函数的别称
	- Instance of function
	- Non-local variables have been bound

- Closure闭包，是指可以包含自由（未绑定到特定对象）变量的代码块；这些变量不是在这个代码块内或者任何全局上下文中定义的，而是在定义代码块的环境中定义（局部变量）。

A lambda is just an anonymous function - a function defined with no name.

A closure is any function which closes over the environment in which it was defined.

An anonymous function is a function literal without a name, while a closure is an instance of a function, a value, whose non-local variables have been bound either to values or to storage locations

### Lambda 作用域
- 可以直接在lambda表达式中访问外层的局部变量

- 声明为final，或者不在代码块里面修改变量

- lambda内部对于实例的字段以及静态变量是即可读又可写(和匿名对象是一致的)

### Android Studio 会提醒你可以用Lambda
![Android Studio Suggestion](http://i.imgur.com/glkmxfH.png)


### Lambda usage

#### 
![Lambda case 0](http://i.imgur.com/1PZTAA4.png)

----------

#### 
![Lambda case 1](http://i.imgur.com/3i3Yw6G.png)

----------

#### 
![Lambda case 2](http://i.imgur.com/iOB4bMN.png)

----------

#### 
![Lambda case 3](http://i.imgur.com/3fJPPuy.png)

----------

#### case 4
![Lambda case 4](http://i.imgur.com/jIIyiQM.png)
![Lambda calculator code](http://i.imgur.com/KODvwvO.png)


