---
layout: post
title:  "Java Class basic!"
date:   2016-12-24 11:41:22 +0800
categories: java
---

��When you bow deeply to the universe, it bows back; when you call out the name of God, it echoes inside you.��
�DMorihei Ueshiba

### Description
����Java Basic�Ķ�̬�ԣ�
- override����д��ָ�������༯�ɸ�������д�������еķ������������ơ��������������ͣ�˳�򣬷���ֵ���Ͷ��Ǳ���͸��෽��һ�µġ�
- overload�����أ�ֵ������ͬһ������д��һ��������һ��������һëһ���ķ����������ǲ�ͬ�ķ����������߷���ֵ����--��Ϊ��������һ�����ܵ�ʵ�֣����ǲ��������ж�������


### sample ���������
- [overload](https://github.com/liao123abc/AndroidSnacks/tree/master/app/src/main/java/liao/thomas/androidsnacks/javabasic/overload)

- [override](https://github.com/liao123abc/AndroidSnacks/tree/master/app/src/main/java/liao/thomas/androidsnacks/javabasic/override)

### public protected default private

  modifier| ���ڲ� | ����| ����| �ⲿ��
---|---|---|---|---
public| �� |��|��|��
protected | ��|��|��|��
default|��|��|��|��
private |��|��|��|��

- public 
����public���η������������ķ���Ȩ�ޣ����Է����κ�һ����CLASSPATH�µ��ࡢ�ӿڡ��쳣�ȡ����������ڶ���������Ҳ���Ƕ����������һ�ֽӿڵ���ʽ��(��ʵ����ʲô�����Է���)

- protected
����protected���η�������protected�ķ������߱�����ֻ�б����ͱ�������������ά������ʵ���Ƕ��ⲻ��������˼����������������ⲿ��дһ������override protected
����

   ��accessing�и�����test1

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
   ����һ�����и�����

```
    Foo2 foo2 = new Foo2();
    //�ٺ٣����ü̳к��test2����,���Foo2û��override test2����������ᱨ���������һ��ע�͵�Foo2��test2����
    foo2.test2();
```




- default����Ա���������ƣ�������ͬһ�����ڵĶ����Է��ʣ������ľͲ������ˣ�һ������ĳЩ��������ֻ�ڱ����ڱ��޸ģ����ڰ��⣬�Ǻǣ�������˼����ûȨ�޿��������Ӳ�Ҫ˵�޸��ˡ�

   ��accessing�и�����test1

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
   ����һ�����и�����

```
    Foo foo = new Foo();
    //foo.test1(); //�ᱨ����Ϊtest1��default�������޷��ڱ�İ�����
        
```


- private ɶҲ��Ҫ˵�ˣ�private���εķ������߱���ֻ�б�����ܹ����ʺ��޸ģ�����Ҳ����