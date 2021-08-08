---
layout: post
title:  "Thread"
date:   2017-09-10 22:18:38 +0800
catalog: true
tags:
    - Java
---

“When you bow deeply to the universe, it bows back; when you call out the name of God, it echoes inside you.”
―Morihei Ueshiba

# Thread

## Java thread定义∏

### what?
- 比进程更加轻量级的调度执行单位
- 线程是cpu调度的基本单位
- 各个线程可以共享进程资源又可以独立调度



### 操作系统线程实现

- 使用内核线程实现，由内核管理，对外接口是LWP(Light Weight Process)轻量级进程
   - 由内核通过scheduler对线程进行调度
   - 程序不直接使用内核线程，而是使用内核线程的高级接口轻量级进程(LWP)
   - 每个Light Weight Process都由一个内核线程支持
   - 需要系统调用，调用代价相对较高
- 使用用户线程实现(Java, Ruby曾经使用过，最终放弃)， 
   - 完全建立在用户空间，不需要内核帮助
   - 所有线程操作用户程序自己管理（复杂）：
      - 线程切换、创建、调度
      - 阻塞如何处理
      - 多处理器系统如何将线程映射到其他处理器上
- 使用用户线程和轻量级进程混合实现
   - 用户线程完全建立在用户空间，用户线程的创建、切换、析构等操作依然廉价
   - 用户线程的调度使用轻量级进程来完成
   - 用户线程和轻量级进程是多对多关系，N:M



### Java线程实现

- < JDK1.2 基于"绿色线程"的用户线程实现
- JDK1.2开始, 基于操作系统原生线程模型
   - Windows版和Linux版都是使用一对一的线程模型实现， 一条java线程映射到一条轻量级进程中
   - Solaris平台同时支持一对一和多对多



## MainThread vs child thread

- no relationship of parent and child
- inherit from 'parent thread'
   - thread daemon property
   - thread priority



## how many ways create thread

one way create thread: new Thread() , but three way to assign task:

- java.lang.Runnable 
- java.lang.Thread class itself.
- java.util.concurrent.Callable Interface.
   - A callable returns a <V>, which will become a Future<V> when submitted via an ExecutorService



```java
//extends thread
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Thread assign task of new Thread");
    }
}

//by Runnable
class ThreadTask implements Runnable {
    @Override
    public void run() {
        //Code present here will be executed in separate independent path.
        System.out.println("Thread assign task of Runnable interface");
    }
}
//Lets create Task first to assign it to the Thread
ThreadTask threadTask = new ThreadTask();
//Lets create a Thread and assign task to it.
//Way to assign task to a Thread is by passing task object(Runnable) to Thread's constructor.
Thread thread1 = new Thread(threadTask);
//Start a thread
thread1.start();
```


## start() vs run()

- 直接跑run() , 则在本线程跑
- 调用start， 会在新的线程跑



## Java常用线程类




## start thread twice
illegalThreadStateException will be thrown


## 调用子线程的join()方法
主线程会等待子线程执行完才会结束


## Thread._yield_()
**yield() **basically means that the thread is not doing anything particularly important and if any other threads or processes need to be run, they should run. Otherwise, the current thread will continue to run.

- gives hint to the thread scheduler that it is ready to pause its execution
- thread scheduler checks if there is any thread with same or high priority than this thread, then move the current thread to Ready/Runnable state and give processor to other thread



> 一般比较少用该方法，一般是debug或者测试的时候使用



## join()


> waits for this thread to die

detail:
[https://stackoverflow.com/questions/15956231/what-does-this-thread-join-code-mean](https://stackoverflow.com/questions/15956231/what-does-this-thread-join-code-mean)


```java
Thread t1 = new Thread(new EventThread("e1"));
t1.start();
Thread t2 = new Thread(new EventThread("e2"));
t2.start();
while (true) {  //这个while是为了保证出异常的时候t2继续运行
   try {
      t1.join();
      t2.join();
      break;
   } catch (InterruptedException e) {
      e.printStackTrace();
   }
}
```


- When join method is invoked, current thread would release its resources and goes in waiting state
- thread1 thread2 run in parallel--of course, 他们先调用了start()
- After run() method of thread is completed, it doesn't mean thread task is completed,  It has to do many other tasks like 
   - Destroying the associated stack, 
   - Setting the necessary threadStatus etc.

One of the task is notifying the waiting threads, So that Thread waiting on join() method will be notified that thread has completed its task and joined threads can resume.

   - Above task are executed inside native thread call, so it wont be visible in java thread API.
- 原理
   - 检查join线程是否存活，如果存活则让当前线程wait
   - join线程中止后，该线程的this.notifyAll()会被调用



[what join mean](https://stackoverflow.com/questions/15956231/what-does-this-thread-join-code-mean/15956265)


```java
            while (isAlive()) {  //这个while是为了保证lock.notifyAll的时候判断线程是否还存活
                lock.wait(0); //target thread's lock
            }
```


## Can Thread be created without any ThreadGroup

- No

![java-thread-group-hierarchy.png](../images/threadgroup.png)


## 子线程抛异常，主线程是否会继续运行
yes， In general, Exception thrown by one thread will not affect another thread, as all threads are independent and have different stack.


## 线程状态
![image.png](../images/threadstates.png)


- BLOCKED 是指线程正在等待获取锁
- WAITING 是指线程正在等待其他线程的“唤醒动作”
- Thread.sleep
   - 告诉操作系统“在未来的多少毫秒内我不参与CPU竞争”
   - Thread.Sleep(0)的作用，就是“触发操作系统立刻重新进行一次CPU竞争”
   - 不会释放锁
- object.wait会释放锁
   - Waiting only releases the lock for the object you call wait() on. It doesn't release any other locks
```java
//You don't actually need to call sleep from within a lock - 
//locks and wait/notify go hand in hand but locks and sleep are unrelated
synchronized(LOCK) {
    Thread.sleep(1000); // LOCK is held
}

synchronized(LOCK) {
    LOCK.wait(); // LOCK is not held
}
```


## 线程状态转换


![image.png](../images/thread_state_change.png)




- _WAITING [never](https://stackoverflow.com/a/13664082/791406) goes directly to RUNNABLE_
- _The thread is WAITING until it is notified...Then it [becomes](https://stackoverflow.com/a/2534158/791406) BLOCKED..._
- _Once this thread is notified, it [will not be runnable](https://stackoverflow.com/a/15680579/791406)...This is..Blocked State._



- 操作系统隐藏 Java虚拟机（JVM）中的 RUNNABLE 和 RUNNING 状态，它只能看到 RUNNABLE 状态



## wait-notify


when we call wait() – this forces the current thread to wait until some other thread invokes notify() or notifyAll() on the same object.


current thread must own the object’s monitor, 3 ways--synchronized方法

- executed synchronized instance method for the given object
- executed the body of a synchronized block on the given object
- by executing _synchronized static _methods for objects of type _Class_



> **only one active thread can own an object’s monitor at a time.**



## synchronized


- 方法声明时使用,放在范围操作符(public等)之后,返回类型声明(void等)之前.即一次只能有一个线程进入该方法,其他线程要想在此时调用该方法,只能排队等候,当前线程(就是在synchronized方法内部的线程)执行完该方法后,别的线程才能进入.
- 对某一代码块使用,synchronized后跟括号,括号里是变量,这样,一次只有一个线程进入该代码块
- synchronized后面括号里是一对象,此时,线程获得的是对象锁.如果线程进入,则得到对象锁,那么别的线程在该类所有对象上的任何操作都不能进行.在对象级使用锁通常是一种比较粗糙的方法
- synchronized后面括号里是类, 如，synchronized(ArrayWithLockOrder.class)。 如果线程进入,则线程在该类中所有操作不能进行,包括静态变量和静态方法。
- 锁是和对象相关联的，每个对象有一把锁，为了执行synchronized语句，线程必须能够获得synchronized语句中表达式指定的对象的锁，一个对象只有一把锁，被一个线程获得之后它就不再拥有这把锁，线程在执行完synchronized语句后，将获得锁交还给对象。
- 在方法前面加上synchronized修饰符即可以将一个方法声明为同步化方法。
- 同步化方法在执行之前获得一个锁。如果这是一个类方法，那么获得的锁是和声明方法的类相关的Class类对象的锁。如果这是一个实例方法，那么此锁是this对象的锁。



## notify, notifyAll


- wait()、notify()和notifyAll()方法是本地方法，并且为final方法，无法被重写。
- 调用某个对象的wait()方法能让当前线程阻塞，并且当前线程必须拥有此对象的monitor（即锁）
- 调用某个对象的notify()方法能够唤醒一个正在等待这个对象的monitor的线程，如果有多个线程都在等待这个对象的monitor，则只能唤醒其中一个线程；
- 调用notifyAll()方法能够唤醒所有正在等待这个对象的monitor的线程；
- 一个线程被唤醒不代表立即获取了对象的monitor，只有等调用完notify()或者notifyAll()并退出synchronized块，释放对象锁后，其余线程才可获得锁执行。

为何这三个不是Thread类声明中的方法，而是Object类中声明的方法（当然由于Thread类继承了Object类，所以Thread也可以调用者三个方法）？

- 由于每个对象都拥有monitor（即锁），所以让当前线程等待某个对象的锁，当然应该通过这个对象来操作了。 而不是用当前线程来操作，因为当前线程可能会等待多个线程的锁，如果通过线程来操作，就非常复杂了。
- 调用某个对象的wait()方法，当前线程必须拥有这个对象的monitor（即锁），因此调用wait()方法必须在同步块或者同步方法中进行（synchronized块或者synchronized方法）。
- 调用某个对象的wait()方法，相当于让当前线程交出此对象的monitor，然后进入等待状态，等待后续再次获得此对象的锁（Thread类中的sleep方法使当前线程暂停执行一段时间，从而让其他线程有机会继续执行，但它并不释放对象锁）；
- notify()方法能够唤醒一个正在等待该对象的monitor的线程，当有多个线程都在等待该对象的monitor的话，则只能唤醒其中一个线程，具体唤醒哪个线程则不得而知。
- 同样地，调用某个对象的notify()方法，当前线程也必须拥有这个对象的monitor，因此调用notify()方法必须在同步块或者同步方法中进行（synchronized块或者synchronized方法）。
- nofityAll()方法能够唤醒所有正在等待该对象的monitor的线程，这一点与notify()方法是不同的。 这里要注意一点：notify()和notifyAll()方法只是唤醒等待该对象的monitor的线程，并不决定哪个线程能够获取到monitor。



## Multithreading Exceptions


![image.png](../images/exceptions_handle.png)


> Current Thread -> ThreadGroup -> Jvm Level(Global Thread level)
> 如果都没有，则调用Threadgroup class ("main" threadgroup to which main thread is part of) provide default implementation



```
System.err.print("Exception in thread \"" + t.getName() + "\" ");
```


- At Thread Level



```
Thread t1 = new Thread(new WorkerThread());
t1.setName("T4");
 
t1.setUncaughtExceptionHandler(new Thread.UncaughtExceptionHandler(){
        @Override
 public void uncaughtException(Thread t, Throwable e){
    System.out.println("Thread Exception Handler :Thread Name :"+t.getName() + " Message :"+e.getMessage());
        }
});
t1.start();
```


- At ThreadGroup Level



```
ThreadGroup tr = new ThreadGroup("MyGroup"){
 @Override
 public void uncaughtException(Thread t, Throwable e) {
  System.out.println("ThreadGroup Exception Handler :Thread Name :"+t.getName() + " Message :"+e.getMessage());
 }
};
 
Thread t1 = new Thread(tr, new WorkerThread());
t1.setName("T1");
```


- At Global Thread Level



```
Thread.setDefaultUncaughtExceptionHandler(new Thread.UncaughtExceptionHandler(){     
 @Override
 public void uncaughtException(Thread t, Throwable e){
  System.out.println("Default Exception Handler :Thread Name :"+t.getName() + " Message :"+e.getMessage());
 }
```




- 线程发生异常是会释放锁的--monitorexit



## To kill a thread

- Thread.stop() is Deprecated
   - 不安全，unlock所有锁
- 使用标志位



```java
class MyThread extends Thread
{
  volatile boolean finished = false;

  public void stopMe()
  {
    finished = true;
  }

  public void run()
  {
    while (!finished)
    {
      //do dirty work
    }
  }
}
```




## Thread.interrupt()

[参考](https://www.zhihu.com/question/41048032)

一个线程不应该由其他线程来强制中断或停止，而是应该由线程自己自行停止。而 Thread.interrupt 的作用其实也不是中断线程，而是「通知线程应该中断了」。调用interrupt()时

- 如果线程处于被阻塞状态（例如处于sleep, wait, join 等状态），那么线程将立即退出被阻塞状态，并抛出一个InterruptedException
- 线程处于正常活动状态，将会将终端标志设置为true



中断是协作机制，需要程序猿自己实现。


```java
Thread t1 = new Thread( new Runnable(){
    public void run(){
        // 若未发生中断，就正常执行任务
        while(!Thread.currentThread.isInterrupted()){
            // 正常任务代码……
        }

        // 中断的处理代码……
        doSomething();
    }
} ).start();
```


触发中断


```java
t1.interrupt();
```




## 线程调度
主流的调度各不相同，

- 优先级调度--priority schedule
   - 高优先级先执行
   - 线程优先级改变的三种方式
      - 用户指定优先级
      - 系统根据进入等待状态的频繁程度提升或者降低优先级
      - 长时间得不到执行而被提升优先级
- 轮转法 -- Round Robin
   - 让各个线程轮流执行一小段时间

## IO Bound Thread vs CPU bound Thread

- IO Bound
   - 频繁等待的线程--频繁进入等待状态
   - 容易得到系统的优先级的提升



- CPU Bound
   - 很少等待的线程
   - 频繁进行大量计算



## Time Slice
> approximately 20 milliseconds

- The period of time for which a process is allowed to run in a preemptive multitasking system is generally called the _time slice_ or _quantum_. 
- The length of each time slice can be critical to balancing system performance vs process responsiveness
   - too long
      - processes will take longer to respond to input
   - too short
      - the scheduler will consume too much processing time
- An [interrupt](https://en.wikipedia.org/wiki/Interrupt) is scheduled to allow the [operating system](https://en.wikipedia.org/wiki/Operating_system) [kernel](https://en.wikipedia.org/wiki/Kernel_(computer_science)) to switch between processes when their time slices expire



## Semaphore

- Semaphore 是一个计数信号量，必须由获取它的线程释放。
- 常用于限制可以访问某些资源的线程数量，例如通过 Semaphore 限流。







## CountDownLatch vs CyclicBarrier

- Latches are for waiting for events; 
   - 不能重复利用，只能用一次
- barriers are for waiting for other threads.
   - cyclicBarrier.await() goes into a blocking state
   - CyclicBarrier的计数器更像一个阀门，需要所有线程都到达，然后继续执行，计数器递增，提供reset功能，可以多次使用
   - 执行完之后会自动reset，可以重复利用，所以叫做CyclicBarrier




