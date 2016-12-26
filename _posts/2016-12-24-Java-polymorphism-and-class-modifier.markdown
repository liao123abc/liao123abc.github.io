---
layout: post
title:  "Java polymorphism and class modifier!"
date:   2016-12-24 11:41:22 +0800
categories: java
---

“When you bow deeply to the universe, it bows back; when you call out the name of God, it echoes inside you.”
―Morihei Ueshiba

### Description
介绍Java Basic的多态性：
- override是重写，指的是子类集成父类重新写父类已有的方法，方法名称、参数个数，类型，顺序，返回值类型都是必须和父类方法一致的。
- overload是重载，值得是在同一个类中写多一个和另外一个方法名一毛一样的方法，不过是不同的方法参数或者返回值调用--因为方法就是一个功能的实现，但是参数可能有多种类型


### sample 点击看代码
- [overload](https://github.com/liao123abc/AndroidSnacks/tree/master/app/src/main/java/liao/thomas/androidsnacks/javabasic/overload)

- [override](https://github.com/liao123abc/AndroidSnacks/tree/master/app/src/main/java/liao/thomas/androidsnacks/javabasic/override)

### public protected default private

| modifier  | 类内部  | 本包   | 子类   | 外部包  |
| --------- | ---- | ---- | ---- | ---- |
| public    | √    | √    | √    | √    |
| protected | √    | √    | √    | ×    |
| default   | √    | √    | ×    | ×    |
| private   | √    | ×    | ×    | ×    |

- public 
  对于public修饰符，它具有最大的访问权限，可以访问任何一个在CLASSPATH下的类、接口、异常等。它往往用于对外的情况，也就是对象或类对外的一种接口的形式。(其实就是什么都可以访问)

- protected
  对于protected修饰符，对于protected的方法或者变量就只有本包和本类或者子类可以维护（其实就是对外不公开的意思），但是你可以在外部包写一个子类override protected
  方法

   包accessing有个方法test1

```
package liao.thomas.androidsnacks.javabasic.accessing;

/**
 * Created by Administrator on 2016/12/24.
 */

public class Foo {

    /**
     * default
     */
    void test1() {
        //do something
    }

    /**
     * protected
     */
    protected void test2() {

    }
}

```
   另外一个包有个方法

```
    Foo2 foo2 = new Foo2();
    //嘿嘿，调用继承后的test2方法,如果Foo2没有override test2方法，这里会报错，你可以试一下注释掉Foo2的test2方法
    foo2.test2();
```




- default，针对本包访问设计，就是在同一个包内的都可以访问，其他的就不可以了，一般用于某些方法我们只在本包内被修改，对于包外，呵呵，不好意思，你没权限看到，更加不要说修改了。

   包accessing有个方法test1

```
package liao.thomas.androidsnacks.javabasic.accessing;

/**
 * Created by Administrator on 2016/12/24.
 */

public class Foo {

    /**
     * default
     */
    void test1() {
        //do something
    }

    /**
     * protected
     */
    protected void test2() {

    }
}

```
   另外一个包有个方法

```
    Foo foo = new Foo();
    //foo.test1(); //会报错，因为test1是default方法，无法在别的包访问
        
```


- private 啥也不要说了，private修饰的方法或者变量只有本类才能够访问和修改，子类也不行
