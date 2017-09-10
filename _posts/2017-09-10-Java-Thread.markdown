---
layout: post
title:  "Thread"
date:   2017-09-10 22:18:38 +0800
categories: Java
---

“When you bow deeply to the universe, it bows back; when you call out the name of God, it echoes inside you.”
―Morihei Ueshiba

# Thread

### Creation
There is 1 way to create a Thread but task can be assigned to Thread using 3 different ways either by using.
- Runnable interface (run() method will be invoked)

```java
class ThreadDemo{
    public void test() {
        //Lets create Task first to assign it to the Thread
        ThreadTask threadTask = new ThreadTask();
        //Lets create a Thread and assign task to it.
        //Way to assign task to a Thread is by passing task object(Runnable) to Thread's constructor.
        Thread thread1 = new Thread(threadTask);
        //Start a thread
        thread1.start();
    }
    class ThreadTask implements Runnable {
        @Override
        public void run() {
            //Code present here will be executed in separate independent path.
        }
    }
}
```

- Thread class (run() method will be invoked)

```java
public class ThreadDemo2  extends Thread{
    @Override
    public void run() {
        //Code present here will be executed in separate independent path.
    }
    public void test() {
        //Lets create Task first to assign it to the Thread
        ThreadDemo2 threadTask = new ThreadDemo2();
        //Lets create a Thread and assign task to it.
        //Way to assign task to a Thread is by passing task object(Runnable) to Thread's constructor.
        Thread thread1 = new Thread(threadTask);
        //Start a thread
        thread1.start();
    }
}
```

- Callable interface (call() method will be invoked)

```java
public class ThreadDemo3 {
    public void test() {
        ExecutorService executorService = Executors.newFixedThreadPool(2);
        ThreadTask threadTask = new ThreadTask();
        Future<Result> resultObject = executorService.submit(threadTask);
        Result result = null;
        try {
            result = resultObject.get();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
        System.out.println(result.code + " " + result.message);
        executorService.shutdown();
    }

    class ThreadTask implements Callable<Result> {
        public Result call() {
            Result response = new Result();
            response.code = 200;
            response.message = "SUCCESS";
            return response;
        }
    }
    class Result {
        public int code;
        public String message;
    }
}
```

### synchronized
- 方法声明时使用,放在范围操作符(public等)之后,返回类型声明(void等)之前.即一次只能有一个线程进入该方法,
  其他线程要想在此时调用该方法,只能排队等候,当前线程(就是在synchronized方法内部的线程)执行完该方法后,别的线程才能进入.

- 对某一代码块使用,synchronized后跟括号,括号里是变量,这样,一次只有一个线程进入该代码块

- synchronized后面括号里是一对象,此时,线程获得的是对象锁.如果线程进入,则得到对象锁,那么别的线程在该类所有对象上
  的任何操作都不能进行.在对象级使用锁通常是一种比较粗糙的方法

- synchronized后面括号里是类, 如，synchronized(ArrayWithLockOrder.class)。 如果线程进入,则线程在该类中
  所有操作不能进行,包括静态变量和静态方法

锁是和对象相关联的，每个对象有一把锁，为了执行synchronized语句，线程必须能够获得synchronized语句中表达式指定的对象的锁，一个对象只有一把锁，被一个线程获得之后它就不再拥有这把锁，线程在执行完synchronized语句后，将获得锁交还给对象。在方法前面加上synchronized修饰符即可以将一个方法声明为同步化方法。同步化方法在执行之前获得一个锁。如果这是一个类方法，那么获得的锁是和声明方法的类相关的Class类对象的锁。如果这是一个实例方法，那么此锁是this对象的锁。

### notify, notifyAll
- wait()、notify()和notifyAll()方法是本地方法，并且为final方法，无法被重写。
- 调用某个对象的wait()方法能让当前线程阻塞，并且当前线程必须拥有此对象的monitor（即锁）
- 调用某个对象的notify()方法能够唤醒一个正在等待这个对象的monitor的线程，如果有多个线程都在等待这个对象的monitor，则只能唤醒其中一个线程；
- 调用notifyAll()方法能够唤醒所有正在等待这个对象的monitor的线程；

Warning:
单个生产者和消费者使用notify没有关系，但是多个的时候都要使用notifyAll
Clearly, notify wakes (any) one thread in the wait set, notifyAll wakes all threads in the waiting set.极端情况下， 仅仅调用notify()会导致死锁

### 为何这三个不是Thread类声明中的方法，而是Object类中声明的方法（当然由于Thread类继承了Object类，所以Thread也可以调用者三个方法）？
其实这个问题很简单，由于每个对象都拥有monitor（即锁），所以让当前线程等待某个对象的锁，当然应该通过这个对象来操作了。
而不是用当前线程来操作，因为当前线程可能会等待多个线程的锁，如果通过线程来操作，就非常复杂了。
上面已经提到，如果调用某个对象的wait()方法，当前线程必须拥有这个对象的monitor（即锁），因此调用wait()方法必须在同步块或者同步方法中进行（synchronized块或者synchronized方法）。
调用某个对象的wait()方法，相当于让当前线程交出此对象的monitor，然后进入等待状态，等待后续再次获得此对象的锁（Thread类中的sleep方法使当前线程暂停执行一段时间，从而让其他线程有机会继续执行，但它并不释放对象锁）；
notify()方法能够唤醒一个正在等待该对象的monitor的线程，当有多个线程都在等待该对象的monitor的话，则只能唤醒其中一个线程，具体唤醒哪个线程则不得而知。
同样地，调用某个对象的notify()方法，当前线程也必须拥有这个对象的monitor，因此调用notify()方法必须在同步块或者同步方法中进行（synchronized块或者synchronized方法）。
nofityAll()方法能够唤醒所有正在等待该对象的monitor的线程，这一点与notify()方法是不同的。
这里要注意一点：notify()和notifyAll()方法只是唤醒等待该对象的monitor的线程，并不决定哪个线程能够获取到monitor。
一个线程被唤醒不代表立即获取了对象的monitor，只有等调用完notify()或者notifyAll()并退出synchronized块，释放对象锁后，其余线程才可获得锁执行。



### 生产者消费者问题

- use BlockQueue

```java
public class UseBlockQueue {

    private static final String TAG = UseBlockQueue.class.getSimpleName();

    private LinkedBlockingQueue<Object> queue = new LinkedBlockingQueue<Object> (10);

    public UseBlockQueue() {

    }

    public void start() {
        new Producer().start();
        new Consumer().start();
    }

    public static void main(String[] args) throws Exception {
        UseBlockQueue test = new UseBlockQueue();
        test.start();
    }

    class Producer extends Thread {
        public void run() {
            while(true ) {
                try {
                    Object o = new Object();
                    queue.put(o);
                    Log.d(TAG,"Producer: " + o);
                } catch (InterruptedException e) {
                    Log.d(TAG,"producer is interrupted!");
                }
            }
        }
    }

    class Consumer extends Thread {
        public void run() {
            while (true ) {
                try {
                    Object o = queue.take();
                    Log.d(TAG,"Consumer: " + o);
                } catch (InterruptedException ie) {
                    Log.d(TAG,"consumer is interrupted!");
                }
            }
        }
    }
}
```

- use ReEntrantLock

```java
public class UseReentrantLock {

    private static final String TAG = UseReentrantLock.class.getSimpleName();

    private LinkedList<Object> myList = new LinkedList<Object>();
    private int MAX = 10;
    private final Lock lock = new ReentrantLock();
    private final Condition full = lock.newCondition();
    private final Condition empty = lock.newCondition();

    public UseReentrantLock() {

    }

    public void start() {
        new Producer().start();
        new Consumer().start();
    }

    public void test() {
        UseReentrantLock test = new UseReentrantLock();
        test.start();
    }

    class Producer extends Thread {
        public void run() {
            while(true) {
                lock.lock();
                try {
                    while(myList.size() == MAX) {
                        Log.d(TAG,"warning: list is full");
                        full.await();
                    }
                    Object o = new Object();
                    if(myList.add(o)) {
                        Log.d(TAG,"Producer: " + o);
                        empty.signal();
                    }
                } catch (InterruptedException ie){
                    Log.d(TAG,"producer is interrupted!");
                } finally {
                    lock.unlock();
                }
            }
        }
    }

    class Consumer extends Thread {
        public void run() {
            while (true) {
                lock.lock();
                try {
                    while (myList.size() == 0) {
                        Log.d(TAG,"warning: it's empty!");
                        empty.await();
                    }
                    Object o = myList.removeLast();
                    Log.d(TAG,"Consumer: " + o);
                    full.signal();
                } catch (InterruptedException ie) {
                    Log.d(TAG,"consumer is interrpted!");
                } finally {
                    lock.unlock();
                }
            }
        }
    }
}
```

- use wait notify

```java
public class UseWaitNotify {

    private static final String TAG = UseWaitNotify.class.getSimpleName();

    private LinkedList<Object> storeHouse = new LinkedList<Object>();
    private final static int MAX = 10;

    public UseWaitNotify() {
    }

    public void start() {
        new Producer().start();
        new Consumer().start();
    }

    class Producer extends Thread {
        @Override
        public void run() {
            while (true) {
                synchronized (storeHouse) {
                    try {
                        while (storeHouse.size() == MAX) {
                            Log.d(TAG, "storeHouse is full , plz wait");
                            storeHouse.wait();
                        }

                        Object newObject = new Object();
                        if (storeHouse.add(newObject)) {
                            Log.d(TAG, "Producer put a Object to storeHouse");
                            Thread.sleep((long) (Math.random()*3000));
                            storeHouse.notify();
                        }

                    } catch (InterruptedException e) {
                        Log.d(TAG, "Producer is interrupted");
                    }
                }
            }
        }
    }

    class Consumer extends Thread {
        @Override
        public void run() {
            while (true) {
                synchronized (storeHouse) {
                    try {
                        while (storeHouse.size() == 0) {
                            Log.d(TAG, "storehouse is empty, plz wait");
                            storeHouse.wait();
                        }
                        storeHouse.removeLast();
                        Log.d(TAG, "consumer get a object from storehouse");
                        Thread.sleep((long) (Math.random() * 3000));
                        storeHouse.notify();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                        Log.d(TAG, "Producer is interrupted");
                    }
                }
            }
        }
    }

    public static void test() {
        UseWaitNotify use = new UseWaitNotify();
        use.start();
    }

}
```
