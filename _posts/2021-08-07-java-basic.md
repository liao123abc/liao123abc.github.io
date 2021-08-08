---
layout: post
title: "Java Basic"
subtitle: "note"
date: 2021-08-07
author: "thomasliao"
header-img: "img/post-bg-2015.jpg"
published: false
tags:
    - java
    - note
    - Anki
---

# Jav Basic

## 有些exception无法捕获？
- 通常已经在native已经崩溃了，java的exception只是表象，底层已经abort
    - 如UnsatisfiedLinkerror


## exception && error

### Exception类图
todo

### defination
- Exception 和 Error 都是继承了 Throwable 类，在 Java 中只有 Throwable 类型的实例才可以被抛出（throw）或者捕获（catch），它是异常处理机制的基本组成类型。
- Exception 是程序正常运行中，可以预料的意外情况，可能并且应该被捕获，进行相应处理
- Error 是指在正常情况下，不大可能出现的情况，绝大部分的 Error 都会导致程序（比如 JVM 自身）处于非正常的、不可恢复状态
    - Error:系统错误，虚拟机出错，我们处理不了，也不需要我们来处理

## checked && unchecked Exception

### checked---编译期间
- 没有继承RuntimeException的Exception属于检查异常
- 必须显式地进行捕获处理
- 编译期检查是否已经写了exception
    - 1.try..catch捕获
    - 2.不断向上抛出，交由jvm来处理
    - force the developer to write try/catch blocks or to rethrow the exception
### unchecked---运行时
- 继承了RuntimeException的Exception，非检查异常也叫运行时异常
- 运行时异常，譬如NullpointerException, ArrayIndexOutOfBoundException
- 通常是可以编码避免的逻辑错误，具体根据需要来判断是否需要捕获，并不会在编译期强制要求
    - 1.try..catch捕获。
    - 2.不断向上抛出
    - 3.不处理

> We should stop using the comprehensively misleading terms of checked/unchecked exceptions. They should be called check-mandated vs check-not-mandated exceptions

## exception便利特性

### try-with-resources
满足使用try-with-resources写法的条件，代表资源的类需要实现java.io.Closeable或java.lang.AutoCloseable接口
在编译时期，会自动生成相应的处理逻辑

```java
FileInputStream fis = new FileInputStream(file);
try {
  // do something
} finally {
  fis.close();
}
```

equals:

```java
try (FileInputStream fis = new FileInputStream(file)) {
  // do something
}
```

### multiple catch

```java
try {
  // do something
} catch (AException e) {
  throw new MyException(e);
} catch (BException e) {
  throw new MyException(e);
}
```

equals

```java
try {
  // do something
} catch (AException | BException e) {
  throw new MyException(e);
}
```

## 正确使用Exception

### 不要捕获通用exception
```java
try {
    // 业务代码
    // …
    Thread.sleep(1000L);
} catch (Exception e) { //应该捕获Thread.sleep() 抛出的 InterruptedException
    // Ignore it
}
```
- 尽量不要捕获类似 Exception 这样的通用异常，而是应该捕获特定异常
- 要保证程序不会捕获到我们不希望捕获的异常。比如，你可能更希望 RuntimeException 被扩散出来，而不是被捕获
- 除非深思熟虑了，否则不要捕获 Throwable 或者 Error，这样很难保证我们能够正确程序处理 OutOfMemoryError
### don't swallow exception

- 生吞异常，往往是基于假设这段代码可能不会发生，或者感觉忽略异常是无所谓的，但是千万不要在产品代码做这种假设
- 如果我们不把异常抛出来，或者也没有输出到日志（Logger）之类，程序可能在后续代码以不可控的方式结束
- 对于分布式系统，如果发生异常，但是无法找到堆栈轨迹（stacktrace），这纯属是为诊断设置障碍。所以，最好使用产品日志，详细地输出到日志系统里


```java
try {
   // 业务代码
   // …
} catch (IOException e) {
    e.printStackTrace(); //标准出错（STERR）不是个合适的输出选项，因为你很难判断出到底输出到哪里去了
}
```
### Throw early, catch late 原则
- throw early
    - "让错误尽早被抛出"，不要等到我们的代码执行到一半再抛出异常，这样很有可能导致一部分的变量处于异常状态，从而引发出更多的错误
- Don’t catch an exception unless you are in the best position to do something useful with it
    - you should only catch exceptions if
        - 1.perform a useful action on the exception (possibly just logging) and then rethrow it
        - 2.wrap the exception into a new exception that makes more sense to your caller and throw that new exception.
        - 3.make a final decision regarding what must happen to fully handle the exception

### Providing context with exceptions
- provide a meaningful message explaining the intent of the operation that failed and the reason for failure

### Client-first design for exception classes
- 是否需要定义成 Checked Exception，因为这种类型设计的初衷更是为了从异常情况恢复，作为异常设计者，我们往往有充足信息进行分类
- 在保证诊断信息足够的同时，也要考虑避免包含敏感信息
    - 类似 java.net.ConnectException，出错信息是类似“ Connection refused (Connection refused)”，而不包含具体的机器名、IP、端口等
    -用户数据一般是不可以输出到日志里面的
- try to consider the point of view of the caller
    - 不同错误封装成不同exception
    - 调用的函数如果可能抛出多个exception，考虑单独抽成一个函数专门处理该函数调用和exception处理
```java
public void open() {
    try {
        this.wrapped.open();
    } catch (Type1 | Type2 | Type3 ex) {
        throw new WrappedException(ex);
    }
}
```

## Exception的缺点
### 业界有一种争论（甚至可以算是某种程度的共识），Java 语言的 Checked Exception 也许是个设计错误
- Checked Exception 的假设是我们捕获了异常，然后恢复程序。但是，其实我们大多数情况下，根本就不可能恢复。Checked Exception 的使用，已经大大偏离了最初的设计目的
- Checked Exception 不兼容 functional 编程，如果你写过 Lambda/Stream 代码，相信深有体会
### 性能角度来审视一下 Java 的异常处理机制
- try-catch 代码段会产生额外的性能开销，或者换个角度说，它往往会影响 JVM 对代码进行优化
    - 远比我们通常意义上的条件语句（if/else、switch）要低效
- 当我们的服务出现反应变慢、吞吐量下降的时候，检查发生最频繁的 Exception 也是一种思路
- Java 每实例化一个 Exception，都会对当时的栈进行快照，这是一个相对比较重的操作。如果发生的非常频繁，这个开销可就不能被忽略了
    - 对于部分追求极致性能的底层类库，有种方式是尝试创建不进行栈快照的 Exception

## NoClassDefFoundError vs ClassNotFoundException

- ClassNotFoundException
    - Java支持使用Class.forName方法来动态地加载类，如果这个类在类路径中没有被找到，那么此时就会在运行时抛出ClassNotFoundException异常。
        - 解决该问题需要确保所需的类连同它依赖的包存在于类路径中，常见问题在于类名书写错误。
    - 当一个类已经某个类加载器加载到内存中了，此时另一个类加载器又尝试着动态地从同一个包中加载这个类。
        - 通过控制动态类加载过程，可以避免上述情况发生。

- NoClassDefFoundError
    - 如果JVM或者ClassLoader实例尝试加载（可以通过正常的方法调用，也可能是使用new来创建新的对象）类的时候却找不到类的定义。要查找的类在编译的时候是存在的，运行的时候却找不到了。这个时候就会导致NoClassDefFoundError.
        - 造成该问题的原因可能是打包过程漏掉了部分类，或者jar包出现损坏或者篡改。或者混淆没有加入keep
        - 解决这个问题的办法是查找那些在开发期间存在于类路径下但在运行期间却不在类路径下的类。


## catch 不是必须的

- try/catch/finally语法中，catch不是必需的，也就是可以只有try/finally，表示不捕获异常，异常自动向上传递，但finally中的代码在异常发生后也执行
- Returning from inside a `finally` block will cause `exceptions` to be lost.
   - 不要在finally写return
```kotlin
    /**
     * Returning from inside a finally block will cause exceptions to be lost.
     * 不会抛出异常
     * 会直接返回10
     */
    private fun throwFinally(): Int {
        try {
            return 1
            throw RuntimeException("testing")
        } catch (e: Exception) {
            e.printStackTrace()
        } finally {
            return 10
        }
    }

    /**
     * Returning from inside a finally block will cause exceptions to be lost.
     * 不会抛出异常
     * 会直接返回10
     */
    private fun throwFinally1(): Int {
        try {
            return 1
            throw RuntimeException("testing")
        } finally {
            return 10
        }
    }
```


## finally && autoclosable

- 如需要关闭的连接等资源，
- 更推荐使用 Java 7 中添加的 try-with-resources 语句，因为通常 Java 平台能够更好地处理异常情况
    - Any object that implements java.lang.AutoCloseable, which includes all objects which implement java.io.Closeable, can be used as a resource
    - BufferedReader等， in Java SE 7 and later, implements the interface java.lang.AutoCloseable
    - If an exception is thrown from the try block and one or more exceptions are thrown from the try-with-resources statement, then those exceptions thrown from the try-with-resources statement are suppressed
        -  retrieve these suppressed exceptions by calling the Throwable.getSuppressed method from the exception thrown by the try block
    - Closeable && AutoCloseable
        - The Closeable interface extends the AutoCloseable interface
        - The close method of the Closeable interface throws exceptions of type IOException 
        - while the close method of the AutoCloseable interface throws exceptions of type Exception.
        - subclasses of the AutoCloseable interface can override this behavior of the close method to throw specialized exceptions, 
            - such as IOException, or no exception at all.


列几个 fianlly 不会被执行的情况:
### try-cach 异常退出

```
try{
system.exit(1)
}finally{
print(abc)
}
```

### 无限循环
```
try{
  while(ture){
    print(abc)
  }
}finally{
print(abc)
}
```

### 线程被杀死
当执行 try，finally 的线程被杀死时。finally 也无法执行。


### 总结
- 不要在 finally 中使用 return 语句。
- finally 总是执行，除非程序或者线程被中断。

## return vs finally

> **_其实return与finally并没有明显的谁强谁弱。在执行时，是return语句先把返回值写入但内存中，然后停下来等待finally语句块执行完，return再执行后面的一段_**

   - return语句返回的是一个值，
      - 当这个值是基本类型时，在后续finally修改也不会改变
      - 当这个值是引用类型时，在后续finally修改，值肯定改变
```java
//返回基本类型，该函数返回的是10    
    public int fun()
    {
        int i = 10;
        try
        {
            //doing something

            return i;
        }catch(Exception e){
            return i;
        }finally{
            i = 20;
        }
    }

    //返回HelloWordFinally，因为返回的引用
    public StringBuilder fun()
    {
        StringBuilder s = new StringBuilder("Hello");
        try
        {
            //doing something
            s.append("Word");

            return s;
        }catch(Exception e){
            return s;
        }finally{
            string.append("finally");
        }
    }
```

## final

### final 可以用来修饰类、方法、变量，分别有不同的意义，
- final 修饰的 class 代表不可以继承扩展，
- final 的变量是不可以修改的，
- final 的方法也是不可以重写的（override）

### final 也许会有性能的好处
- 利用 final 可能有助于 JVM 将方法进行内联，可以改善编译器进行条件编译的能力等等
 - 并不一定，  很多类似的结论都是基于假设得出的
 - 现代高性能 JVM（如 HotSpot）判断内联未必依赖 final 的提示，要相信 JVM 还是非常智能的
 - final 字段对性能的影响，大部分情况下，并没有考虑的必要
 - 和 JVM 的实现很相关的，未经验证比较难以把握， 不然最好不要指望这种小技巧带来的所谓性能好处，程序最好是体现它的语义目的

 ### final 并不等同于 immutable
 final 只能约束 list 这个引用不可以被赋值，但是 list 对象行为不被 final 影响，添加元素等操作是完全正常的

### 匿名内部累，访问局部变量时，局部变量要用final来修饰
Java inner class实际会copy一份，不是去直接使用局部变量，final可以防止出现数据一致性问题


## reference引用

- 强引用 (Strong Reference)-- 普通我们使用的引用
- 软引用(soft Reference) -- 系统将要发生内存溢出之前会将这些对象列进回收范围之中进行第二次回收
- 弱引用(weak Reference) -- 被弱引用的内存只能活到下一次垃圾收集发生之前
- 虚引用(phantom Reference)-- 为对象设置虚引用关联唯一目的就是能在这个对象被回收器回收之前收到一个系统通知






## Type Introspection vs Reflection
[ref](https://stackoverflow.com/questions/37628/what-is-reflection-and-why-is-it-useful)

- Introspection
    - The ability to inspect the code in the system and see object types
- Reflection
    - the ability to make modifications at runtime by making use of introspection

> some languages support introspection, but do not support reflection, e.c, C++


## 动态代理

- 静态代理：事先写好代理类，可以手工编写，也可以用工具生成。缺点是每个业务类都要对应一个代理类，非常不灵活。
- 动态代理：运行时自动生成代理对象。缺点是生成代理代理对象和调用代理方法都要额外花费时间。
    - JDK动态代理：基于Java反射机制实现，必须要实现了接口的业务类才能用这种办法生成代理对象。新版本也开始结合ASM机制。
    - cglib动态代理：基于ASM机制实现，通过生成业务类的子类作为代理类。

> Java反射机制的常见应用：动态代理（AOP、RPC）、提供第三方开发者扩展能力（Servlet容器，JDBC连接）、第三方组件创建对象（DI）……


## 动态代理sample
```

public class MyDynamicProxy {
    public static  void main (String[] args) {
        HelloImpl hello = new HelloImpl();
        MyInvocationHandler handler = new MyInvocationHandler(hello);
        // 构造代码实例
        Hello proxyHello = (Hello) Proxy.newProxyInstance(HelloImpl.class.getClassLoader(), HelloImpl.class.getInterfaces(), handler);
        // 调用代理方法
        proxyHello.sayHello();
    }
}
interface Hello {
    void sayHello();
}
class HelloImpl implements  Hello {
    @Override
    public void sayHello() {
        System.out.println("Hello World");
    }
}
 class MyInvocationHandler implements InvocationHandler {
    private Object target;
    public MyInvocationHandler(Object target) {
        this.target = target;
    }
    @Override
    public Object invoke(Object proxy, Method method, Object[] args)
            throws Throwable {
        System.out.println("Invoking sayHello");
        Object result = method.invoke(target, args);
        return result;
    }
}

```

## aop--aspect-oriented programming (AOP)
简单来说它可以看作是对 OOP 的一个补充，因为 OOP 对于跨越不同对象或类的分散、纠缠逻辑表现力不够，比如在不同模块的特定阶段做一些事情，类似日志、用户鉴权、全局性异常处理、性能监控，甚至事务处理等
![aop](images/aop.webp)

> AOP 通过（动态）代理机制可以让开发者从这些繁琐事项中抽身出来，大幅度提高了代码的抽象程度和复用度


## auto-box:原始数据类型和 Java 泛型并不能配合使用
> Primitive Types 和Generic 不能混用

### auto-boxing/unboxing机制
primitive value 与 object之间的隐式转换机制

### primitive value vs object
- primitive value在内存中存的是值，所以找到primitive value的内存位置，就可以获得值；
    - 原始数据类型和 Java 泛型并不能配合使用
    - 无法高效地表达数据，也不便于表达复杂的数据结构，比如 vector 和 tuple
- object存的是reference，找到object的内存位置，还要根据reference找下一个内存空间，
    - 要产生更多的IO，所以计算性能比primitive value差，
    - 但是object具备generic的能力，更抽象，解决业务问题编程效率高
- object提供了各种ficility
    - 主要包括各种基础的常量，比如最大值、最小值、位数等
    - 各种静态工厂方法 valueOf()
    - 获取环境变量数值的方法；
    - 各种转换方法，比如转换为不同进制的字符串，如 8 进制，或者反过来的解析方法等
- when to use
    - 如果开发者要做计算，就应该使用primitive value
    - 如果开发者要处理业务问题，就应该使用object，采用Generic机制
        - JAVA有auto-boxing/unboxing机制，对开发者来讲也不需要注意什么。
        - 为了弥补object计算能力的不足，还设计了static valueOf()方法提供缓存机制，算是一个弥补

## auto-box: 

### auto-boxing/unboxing 发生在编译阶段
- 自动装箱实际上算是一种语法糖, 编译的时候生成一样的字节码， 如Integer
    - javac 替我们自动把装箱转换为 Integer.valueOf()，
    - 把拆箱替换为 Integer.intValue()

### 缓存机制
> 类加载后，IntegerCache缓存了一定范围的对象，提高性能
```
    public static Integer valueOf(int i) {
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
    }
```

- 其他类也有
    - Boolean，缓存了 true/false 对应实例，确切说，只会返回两个常量实例 Boolean.TRUE/FALSE。
    - Short，同样是缓存了 -128 到 127 之间的数值。
    - Byte，数值有限，所以全部都被缓存。
    - Character，缓存范围’\u0000’ 到 ‘\u007F’

### 注意事项
- 建议避免无意中的装箱、拆箱行为，尤其是在性能敏感的场合


## Java IO

### BIO,NIO,AIO
