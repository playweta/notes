## 1、什么是JUC
指的是java.util包下的三个工具类
1. java.util.concurrent
2. java.util.concurrent.atomic
3. java.util.concurrent.locks

实现多线程的三种方式
1. 继承Thread类
2. 实现Runnable接口
3. 实现Callable接口

**Runnable**没有返回值、效率相比于Callable相对较低！
**业务**：普通的现成代码 Thread

>本篇笔记篇幅较长，若想一气呵成地看完就看本篇就行。如果想一点点的看，把其中的部分抽取出来做成4个子笔记，点击链接看对应子笔记就行。两种方式的内容相同。

**1.什么是JUC?（内容较少，就未抽取）**
**2.java线程和进程**[演绎法5919：java线程和进程学习子笔记](https://zhuanlan.zhihu.com/p/266623029)
**3.Lock锁中的synchonized部分**[演绎法5919：synchronized学习笔记](https://zhuanlan.zhihu.com/p/266547210)
**4.8锁现象**[演绎法5919：8锁现象学习子笔记](https://zhuanlan.zhihu.com/p/266623470)
**5.不安全的集合类**[演绎法5919：不安全的集合类学习子笔记](https://zhuanlan.zhihu.com/p/266624188)

## 2、线程和进程
>线程、进程，如果不能使用一句话说出来的技术，不扎实！

进程：一个程序往往可以包含多个线程，至少包含一个！
Java默认有几个线程？2个 main、GC
线程：开了一个线程Typora，写字，自动保存（线程负责的）
对于Java而言：Trhead、Runnable、Callable
**Java真的可以开启线程吗？**开不了
```java
/**  
 * Causes this thread to begin execution; the Java Virtual Machine * calls the <code>run</code> method of this thread.  
 * <p>  
 * The result is that two threads are running concurrently: the  
 * current thread (which returns from the call to the * <code>start</code> method) and the other thread (which executes its  
 * <code>run</code> method).  
 * <p>  
 * It is never legal to start a thread more than once.  
 * In particular, a thread may not be restarted once it has completed * execution. * * @exception  IllegalThreadStateException  if the thread was already  
 *               started. * @see        #run()  
 * @see        #stop()  
 */public synchronized void start() {  
    /**  
     * This method is not invoked for the main method thread or "system"     * group threads created/set up by the VM. Any new functionality added     * to this method in the future may have to also be added to the VM.     *     * A zero status value corresponds to state "NEW".     */    if (threadStatus != 0)  
        throw new IllegalThreadStateException();  
  
    /* Notify the group that this thread is about to be started  
     * so that it can be added to the group's list of threads     * and the group's unstarted count can be decremented. */    group.add(this);  
  
    boolean started = false;  
    try {  
        start0();  
        started = true;  
    } finally {  
        try {  
            if (!started) {  
                group.threadStartFailed(this);  
            }  
        } catch (Throwable ignore) {  
            /* do nothing. If start0 threw a Throwable then  
              it will be passed up the call stack */        }  
    }  
}  
  
private native void start0();
```

>并行 并发

并发编程：并发 并行
并发（多线程操作同一个资源）
* CPU一核，模拟出来多条线程，天下武功，唯快不破，快速交替
并行（多个人一起行走）
* CPU多核，多个线程可以同时执行；线程池
```java
package com.code.demo01;  
  
public class Test1 {  
  
    // 获取CPU的核数  
    //CPU密集型，IO密集型  
    public static void main(String[] args) {  
        System.out.println(Runtime.getRuntime().availableProcessors());  
    }  
}
```
并发编程的本质：**充分利用CPU的资源**
所有的公司都很看重！
企业，挣钱=>提高效率，裁员，找一个厉害的人顶替三个不怎么样的人；
人员（减）、技术成本（高）

>线程的几个状态

```java
public enum State {  
	
	// 新生
	NEW,  
	
	// 运行
    RUNNABLE,  
	
	// 阻塞
    BLOCKED,  
	
	//等待
    WAITING,  
    
    //超时等待
    TIMED_WAITING,  
	
	//终止
    TERMINATED;  
}
```

>wait/sleep区别

**1、来自不同的类**
wait=>Object
sleep=>Thread
**2、关于锁的释放**
wait 会释放锁，sleep睡觉了，抱着锁睡觉，不会释放！
**3、使用的范围是不同的**
wait必须在同步代码块中
sleep 可以再任何地方睡
**4、是否需要捕获异常**
wait不需要捕获异常
sleep必须要捕获异常

## 3、Lock锁（重点）
>传统Synchronized

```java
package com.code.demo01;  
  
// 基本的卖票例子  
  
/**  
 * @author liubr  
 * 真正的多线程开发，公司中的开发，降低耦合性  
 * 线程就是一个单独的资源类，没有任何附属的操作  
 * 1、属性、方法  
 */  
public class SaleTickDemo1 {  
    public static void main(String[] args) {  
        // 并发：多线程操作同一个资源类，把资源类丢入线程  
        Ticket ticket = new Ticket();  
        new Thread(() -> {  
            for (int i = 0; i < 40; i++) {  
                ticket.sale();  
            }  
        }, "A").start();  
  
        new Thread(() -> {  
            for (int i = 0; i < 40; i++) {  
                ticket.sale();  
            }  
        }, "B").start();  
  
        new Thread(() -> {  
            for (int i = 0; i < 40; i++) {  
                ticket.sale();  
            }  
        }, "C").start();  
    }  
}  
  
class Ticket {  
    private int number = 30;  
  
    // 卖票的方式  
    // synchronized 本质：队列，锁  
    public synchronized void sale() {  
        if (number > 0) {  
            System.out.println(Thread.currentThread().getName() + "卖出来" + (number--) + "票，剩余：" + number);  
        }  
    }  
}
```

>Lock接口

![](../images/Pasted%20image%2020231110213550.png)
公平锁：十分公平；可以先来后到
**非公平锁：**十分不公平：可以插队（默认）

```java
public class SaleTicketDemo02 {  
  
    public static void main(String[] args) {  
        // 并发：多线程操作同一个资源类，把资源类丢入线程  
        Ticket2 ticket = new Ticket2();  
  
        // @FunctionalInterface 函数式接口，jdk1.8  lambda表达式 (参数)->{ 代码 }        new Thread(() -> {  
            for (int i = 0; i < 40; i++) {  
                ticket.sale();  
            }  
        }, "A").start();  
  
        new Thread(() -> {  
            for (int i = 0; i < 40; i++) {  
                ticket.sale();  
            }  
        }, "B").start();  
  
        new Thread(() -> {  
            for (int i = 0; i < 40; i++) {  
                ticket.sale();  
            }  
        }, "C").start();  
    }  
}  
  
// 资源类 OOPclass Ticket2 {  
    private int number = 30;  
  
    Lock lock = new ReentrantLock();  
    // 卖票的方式  
    // synchronized 本质：队列，锁  
    public void sale() {  
        // 加锁  
        lock.lock();  
        if (number > 0) {  
            System.out.println(Thread.currentThread().getName() + "卖出来" + (number--) + "票，剩余：" + number);  
        }  
  
        // 解锁  
        lock.unlock();  
    }  
}
```

>Synchronized 和 Lock 区别

1、Synchronized 内置的Java关键字，Lock是一个Java类
2、Synchronized 无法判断获取锁的状态，Lock 可以判断是否 获取到了锁
3、Synchronized 会自动释放锁，locck必须要手动释放锁！如果不释放锁，**死锁**
4、Synchronized 线程 1 （获取锁，阻塞）、线程2（等待，傻傻的等待）；Lock锁就是不一定会等待下去；
5、Synchronized 可重入锁，不可以中断的、非公平；Lock，可重入锁，可以判断锁，非公平（可以自己设置）；
6、Synchronized 适合锁少量的代码同步问题，Lock 适合锁大量的同步代码！

>锁是什么，如何判断锁的是谁！

## 4、生产者和消费者问题
面试的：单例模式、排序算法、生产者和消费者、死锁

>生产者和消费者问题Synchronized 版

```java
public class A {  
  
    public static void main(String[] args) {  
        Data data = new Data();  
        new Thread( () -> {  
            for (int i = 0; i < 10; i++) {  
                try {  
                    data.increment();  
                } catch (InterruptedException e) {  
                    e.printStackTrace();  
                }  
            }  
        }, "A").start();  
  
        new Thread(() -> {  
            for (int i = 0; i < 10; i++) {  
                try {  
                    data.decrement();  
                } catch (InterruptedException e) {  
                    e.printStackTrace();  
                }  
            }  
        }, "B").start();  
    }  
}  
  
class Data {  
    private int number = 0;  
  
    public synchronized void increment() throws InterruptedException {  
  
        if (number != 0) {  
            this.wait();  
        }  
  
        number++;  
        System.out.println(Thread.currentThread().getName() + "=>" + number);  
        // 通知其他线程，我+1完毕了  
        this.notifyAll();  
    }  
  
    public synchronized void decrement() throws InterruptedException {  
        if (number == 0) {  
            this.wait();  
        }  
  
        number--;  
        System.out.println(Thread.currentThread().getName() + "=>" + number);  
        this.notifyAll();  
    }  
}
```

>问题存在，A  B  C D  4 个线程！ 虚假唤醒
![](../images/Pasted%20image%2020231111160042.png)

**if 改为 while 判断**
```java
/**
 * 线程之间的通信问题：生产者和消费者问题！  等待唤醒，通知唤醒
 * 线程交替执行  A   B 操作同一个变量   num = 0 
 * A num+1
 * B num-1
 */
public class A {  
  
    public static void main(String[] args) {  
        Data data = new Data();  
        new Thread( () -> {  
            for (int i = 0; i < 10; i++) {  
                try {  
                    data.increment();  
                } catch (InterruptedException e) {  
                    e.printStackTrace();  
                }  
            }  
        }, "A").start();  
  
        new Thread(() -> {  
            for (int i = 0; i < 10; i++) {  
                try {  
                    data.decrement();  
                } catch (InterruptedException e) {  
                    e.printStackTrace();  
                }  
            }  
        }, "B").start();  
        new Thread( () -> {  
            for (int i = 0; i < 10; i++) {  
                try {  
                    data.increment();  
                } catch (InterruptedException e) {  
                    e.printStackTrace();  
                }  
            }  
        }, "c").start();  
  
        new Thread(() -> {  
            for (int i = 0; i < 10; i++) {  
                try {  
                    data.decrement();  
                } catch (InterruptedException e) {  
                    e.printStackTrace();  
                }  
            }  
        }, "d").start();  
    }  
}  
  
class Data {  
    private int number = 0;  
  
    public synchronized void increment() throws InterruptedException {  
  
        while (number != 0) {  
            this.wait();  
        }  
  
        number++;  
        System.out.println(Thread.currentThread().getName() + "=>" + number);  
        // 通知其他线程，我+1完毕了  
        this.notifyAll();  
    }  
  
    public synchronized void decrement() throws InterruptedException {  
        while (number == 0) {  
            this.wait();  
        }  
  
        number--;  
        System.out.println(Thread.currentThread().getName() + "=>" + number);  
        this.notifyAll();  
    }  
}
```

>JUC版的生产者和消费者问题

![](../images/Pasted%20image%2020231111161734.png)

通过Lock找到Condition
![](../images/Pasted%20image%2020231111161817.png)
代码实现

```java
package com.code.add;  
  
import java.util.concurrent.locks.Condition;  
import java.util.concurrent.locks.Lock;  
import java.util.concurrent.locks.ReentrantLock;  
  
public class B  {  
    public static void main(String[] args) {  
        Data2 data = new Data2();  
  
        new Thread(()->{  
            for (int i = 0; i < 10; i++) {  
                try {  
                    data.increment();  
                } catch (InterruptedException e) {  
                    e.printStackTrace();  
                }  
            }  
        },"A").start();  
  
        new Thread(()->{  
            for (int i = 0; i < 10; i++) {  
                try {  
                    data.decrement();  
                } catch (InterruptedException e) {  
                    e.printStackTrace();  
                }  
            }  
        },"B").start();  
  
        new Thread(()->{  
            for (int i = 0; i < 10; i++) {  
                try {  
                    data.increment();  
                } catch (InterruptedException e) {  
                    e.printStackTrace();  
                }  
            }  
        },"C").start();  
  
        new Thread(()->{  
            for (int i = 0; i < 10; i++) {  
                try {  
                    data.decrement();  
                } catch (InterruptedException e) {  
                    e.printStackTrace();  
                }  
            }  
        },"D").start();  
  
    }  
}  
  
// 判断等待，业务，通知  
class Data2{ // 数字 资源类  
  
    Lock lock = new ReentrantLock();  
    Condition condition = lock.newCondition();  
  
    private int number = 0;  
  
    //condition.await(); // 等待  
    //condition.signalAll(); // 唤醒全部  
    //+1  
    public void increment() throws InterruptedException {  
        lock.lock();  
        try {  
            // 业务代码  
            while (number!=0){  //0  
                // 等待  
                condition.await();  
            }  
            number++;  
            System.out.println(Thread.currentThread().getName()+"=>"+number);  
            // 通知其他线程，我+1完毕了  
            condition.signalAll();  
        } catch (Exception e) {  
  
            e.printStackTrace();  
        } finally {  
            lock.unlock();  
        }  
  
    }  
  
    //-1  
    public synchronized void decrement() throws InterruptedException {  
        lock.lock();  
        try {  
            while (number==0){ // 1  
                // 等待  
                condition.await();  
            }  
            number--;  
            System.out.println(Thread.currentThread().getName()+"=>"+number);  
            // 通知其他线程，我-1完毕了  
            condition.signalAll();  
        } catch (Exception e) {  
            e.printStackTrace();  
        } finally {  
            lock.unlock();  
        }  
    }  
  
}
```

![](../images/Pasted%20image%2020231111220055.png)

代码测试：
```java
package com.code.pc;  
  
import java.util.concurrent.locks.Condition;  
import java.util.concurrent.locks.Lock;  
import java.util.concurrent.locks.ReentrantLock;  
  
public class C {  
    public static void main(String[] args) {  
  
        Data3 data3 = new Data3();  
  
        new Thread(()->{  
            for (int i = 0; i < 10; i++) {  
                data3.printA();  
            }  
        }, "A").start();  
  
        new Thread(()->{  
            for (int i = 0; i < 10; i++) {  
                data3.printB();  
            }  
        }, "B").start();  
  
  
        new Thread(()->{  
            for (int i = 0; i < 10; i++) {  
                data3.printC();  
            }  
        }, "B").start();  
  
  
  
    }  
}  
  
class Data3{  
  
    private Lock lock = new ReentrantLock();  
    private Condition condition1 = lock.newCondition();  
    private Condition condition2 = lock.newCondition();  
    private Condition condition3 = lock.newCondition();  
    private int number = 1; // 1A 2B 3C  
  
    public void printA(){  
        lock.lock();  
        try {  
            // 业务，判断->执行->通知  
            while (number != 1) {  
                // 等待  
                condition1.await();  
            }  
            System.out.println(Thread.currentThread().getName() + "=>AAAAA");  
            // 唤醒指定的人：B  
  
            number = 2;  
            condition2.signal();  
        } catch (Exception e) {  
            e.printStackTrace();  
        } finally {  
            lock.unlock();  
        }  
    }  
  
    public void printB(){  
        lock.lock();  
        try {  
            // 业务，判断->执行->通知  
            while (number != 2) {  
                // 等待  
                condition2.await();  
            }  
            System.out.println(Thread.currentThread().getName() + "=>BBBB");  
            // 唤醒指定的人：B  
  
            number = 3;  
            condition3.signal();  
        } catch (Exception e) {  
            e.printStackTrace();  
        } finally {  
            lock.unlock();  
        }  
    }  
  
    public void printC(){  
        lock.lock();  
        try {  
            while (number != 3) {  
                condition3.await();  
            }  
            System.out.println(Thread.currentThread().getName() + "=>CCCC");  
            number = 1;  
            condition1.signal();  
        } catch (Exception e) {  
            e.printStackTrace();  
        } finally {  
            lock.unlock();  
        }  
    }  
}
```

## 5、8锁现象
如何判断锁的是谁！永远的知道什么是锁，锁到底锁的是谁！
**深刻理解我们的锁**
```java
package com.code.lock8;  
  
import java.util.concurrent.TimeUnit;  
  
public class Test1 {  
    public static void main(String[] args) {  
  
        Phone phone = new Phone();  
  
        new Thread(() -> {  
            phone.sendSms();  
        }, "A").start();  
  
        try {  
            TimeUnit.SECONDS.sleep(1);  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }  
        new Thread(() -> {  
            phone.call();  
        }, "B").start();  
    }  
}  
  
class Phone {  
  
    public synchronized void sendSms() {  
        try {  
            TimeUnit.SECONDS.sleep(4);  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }  
        System.out.println("发短信");  
    }  
  
    public synchronized void call() {  
        System.out.println("打电话");  
    }   
}
```

```java
package com.code.lock8;  
  
import java.util.concurrent.TimeUnit;  
  
public class Test3 {  
    public static void main(String[] args) {  
  
        Phone3 phone1 = new Phone3();  
        Phone3 phone2 = new Phone3();  
  
        new Thread(() -> {  
            phone1.sendSms();  
        }, "A").start();  
  
        try {  
            TimeUnit.SECONDS.sleep(1);  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }  
  
        new Thread(() -> {  
            phone2.call();  
        }, "B").start();  
  
    }  
}  
  
class Phone3 {  
  
    public synchronized void sendSms() {  
        try {  
            TimeUnit.SECONDS.sleep(4);  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }  
        System.out.println("发消息");  
    }  
  
    public synchronized void call() {  
        System.out.println("打电话");  
    }  
}
```

```java
package com.code.lock8;  
  
import java.util.concurrent.TimeUnit;  
  
public class Test4 {  
    public static void main(String[] args) {  
  
        Phone4 phone1 = new Phone4();  
        Phone4 phone2 = new Phone4();  
  
        new Thread(() -> {  
            phone1.sendSms();  
        }, "A").start();  
  
        try {  
            TimeUnit.SECONDS.sleep(1);  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }  
  
        new Thread(() -> {  
            phone2.call();  
        }, "B").start();  
  
    }  
}  
  
class Phone4 {  
  
    public synchronized void sendSms() {  
        try {  
            TimeUnit.SECONDS.sleep(4);  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }  
        System.out.println("发消息");  
    }  
  
    public synchronized void call() {  
        System.out.println("打电话");  
    }  
}
```
>小结

new this 具体的一个手机
static Class唯一的一个模板

## 6、集合类不安全
>List不安全

```java
public class ListTest {  
  
    public static void main(String[] args) {  
        List<String> list = new CopyOnWriteArrayList<>();  
  
        for (int i = 0; i < 10; i++) {  
            new Thread(()->{  
                list.add(UUID.randomUUID().toString().substring(0, 5));  
                System.out.println(list);  
            }, String.valueOf(i)).start();  
        }  
    }  
}
```

>Set不安全

```java
public class SetTest {  
    public static void main(String[] args) {  
  
        Set<String> set = new CopyOnWriteArraySet<>();  
  
        for (int i = 0; i < 30; i++) {  
            new Thread(()->{  
                set.add(UUID.randomUUID().toString().substring(0, 5));  
                System.out.println(set);  
            }, String.valueOf(i)).start();  
        }  
    }  
}
```

hashSet底层是什么？
```java
public HashSet() {  
    map = new HashMap<>();  
}

// add set 本质就是 map key 是无法重复的！
public boolean add(E e) {  
    return map.put(e, PRESENT)==null;  
}

private static final Object PRESENT = new Object(); //不变的值
```

>Map不安全

回顾Map基本操作
![](../images/Pasted%20image%2020231112222840.png)
```java
public class MapTest {  
  
    public static void main(String[] args) {  
  
        Map<String, String> map = new ConcurrentHashMap<>();  
  
        for (int i = 0; i < 30; i++) {  
            new Thread(()->{  
                map.put(Thread.currentThread().getName(), UUID.randomUUID().toString().substring(0, 5));  
                System.out.println(map);  
            }, String.valueOf(i)).start();  
        }  
  
    }  
}
```

## 7、Callable（简单）

![](../images/Pasted%20image%2020231112222936.png)

1、可以有返回值
2、可以抛出异常
3、可以不同，run()/call()
>代码测试

![](../images/Pasted%20image%2020231112223047.png)
![](../images/Pasted%20image%2020231112223100.png)
![](../images/Pasted%20image%2020231112223111.png)

```java
public class CallableTest {  
    public static void main(String[] args) throws ExecutionException, InterruptedException {  
        new Thread().start();  
        MyThread thread = new MyThread();  
        FutureTask<Integer> task = new FutureTask<>(thread);  
  
        new Thread(task, "A").start();  
        new Thread(task, "B").start();  
  
        Integer o = task.get();  
  
        System.out.println(o);  
    }  
  
  
}  
class MyThread implements Callable<Integer> {  
  
    public Integer call(){  
        System.out.println("call()");  
        // 耗时的操作  
        return 1024;  
    }  
  
}
```

***
**细节**
1、有缓存
2、结果可能需要等待，会阻塞！

## 8、常用的辅助类（必会）
### 8.1、CountDownLatch

![](../images/Pasted%20image%2020231112224308.png)

```java
public class CountDownLatchDemo {  
    public static void main(String[] args) throws InterruptedException {  
        CountDownLatch downLatch = new CountDownLatch(6);  
        for (int i = 0; i < 7; i++) {  
            new Thread(()->{  
                System.out.println(Thread.currentThread().getName() + "Go out");  
                downLatch.countDown(); // 数量-1  
            },String.valueOf(i)).start();  
        }  
        downLatch.await(); // 等待计时器归零，然后再向下执行  
  
        System.out.println("Close Door");  
    }  
}
```

原理：
`downLatch.countDown();` // 数量-1  
` downLatch.await();` // 等待计时器归零，然后再向下执行  
每次有线程调用countDown() 数量-1，假设计时器变为0，countDownlach.await() 就会被唤醒，继续执行！

## 8.2、CyclicBarrier
![](../images/Pasted%20image%2020231112225829.png)
加法计数器
```java
  
public class CyclicBarrierDemo {  
  
    public static void main(String[] args) {  
  
        /**  
         * 集齐7课龙珠召唤神龙  
         */  
        // 召唤龙珠的线程  
        CyclicBarrier cyclicBarrier = new CyclicBarrier(7,()->{  
            System.out.println("召唤神龙成功");  
        });  
  
        for (int i = 0; i < 8; i++) {  
            final int temp = i;  
  
            new Thread(()->{  
                System.out.println(Thread.currentThread().getName() + "收集" + temp + "个龙珠");  
                try {  
                    cyclicBarrier.await(); // 等待  
                } catch (InterruptedException e) {  
                    e.printStackTrace();  
                } catch (BrokenBarrierException e) {  
                    e.printStackTrace();  
                }  
            }).start();  
        }  
    }  
}
```

## 8.3Semaphore
Semaphore : 信号量
![](../images/Pasted%20image%2020231112232801.png)
抢车位
6车---3个停车位置
```java
public class SemaphoreDemo {  
    public static void main(String[] args) {  
        Semaphore semaphore = new Semaphore(3);  
        for (int i = 0; i < 7; i++) {  
            new Thread(() -> {  
                try {  
                    semaphore.acquire();  
                    System.out.println(Thread.currentThread().getName() + "抢到车位");  
  
                    TimeUnit.SECONDS.sleep(2);  
                    System.out.println(Thread.currentThread().getName() + "离开车位");  
  
                } catch (InterruptedException e) {  
                    e.printStackTrace();  
                } finally {  
                    semaphore.release();  
                }  
            }, String.valueOf(i)).start();  
        }  
    }  
}
```
**原理：**
`semaphore.acquire()`获得，假设如果已经满了，等待，等待被释放为止
`semaphore.release();`释放，会将当前的信号释放+1，然后唤醒等待的线程！
作用：多个共享资源互斥的使用！并发限流，控制最大的线程数！

## 9、读写锁
ReadWriteLock
![](../images/Pasted%20image%2020231113161345.png)

```java
public class ReadWriteLockDemo {  
  
    public static void main(String[] args) {  
        MyCache myCache = new MyCache();  
  
        for (int i = 0; i < 6; i++) {  
            final int temp = i;  
            new Thread(() -> {  
                myCache.put(temp + "", temp + "");  
            }, String.valueOf(i)).start();  
        }  
  
        // 读取  
        for (int i = 0; i < 6; i++) {  
            final int temp = i;  
            new Thread(() -> {  
                myCache.get(temp + "");  
            }, String.valueOf(i)).start();  
        }  
    }  
}  
  
class MyCacheLock {  
  
    private final Map<String, Object> map = new HashMap<>();  
    // 读写锁：更加细粒度的控制  
    private final ReadWriteLock readWriteLock = new ReentrantReadWriteLock();  
    private final Lock lock = new ReentrantLock();  
  
    //存写入的时候，只希望同时只有一个线程写  
    public void put(String key, Object value) {  
        readWriteLock.writeLock().lock();  
        try {  
            System.out.println(Thread.currentThread().getName() + "写入" + key);  
            map.put(key, value);  
            System.out.println(Thread.currentThread().getName() + "写入OK");  
        } catch (Exception e) {  
            e.printStackTrace();  
        } finally {  
            readWriteLock.writeLock().unlock();  
        }  
    }  
  
    // 取读的时候，所有人都可以读  
    public void get(String key) {  
        readWriteLock.readLock().lock();  
        try {  
            System.out.println(Thread.currentThread().getName() + "读取" + key);  
            Object obj = map.get(key);  
            System.out.println(Thread.currentThread().getName() + "读取ok");  
        } catch (Exception e) {  
            e.printStackTrace();  
        } finally {  
            readWriteLock.readLock().unlock();  
        }  
    }  
}  
  
/**  
 * 自定义缓存  
 */  
class MyCache {  
    private final Map<String, Object> map = new HashMap<>();  
  
    public void put(String key, Object value) {  
        System.out.println(Thread.currentThread().getName() + "写入" + key);  
        map.put(key, value);  
        System.out.println(Thread.currentThread().getName() + "写入ok");  
    }  
  
    public void get(String key) {  
        System.out.println(Thread.currentThread().getName() + "读取" + key);  
        Object obj = map.get(key);  
        System.out.println(Thread.currentThread().getName() + "读取OK");  
    }  
  
}
```

## 10、阻塞队列
![](../images/Pasted%20image%2020231113161437.png)
![](../images/Pasted%20image%2020231113161448.png)

**BlockingQueue** BlockingQueue不是新的东西
![](../images/Pasted%20image%2020231113161551.png)
什么情况下我们会使用 阻塞队列 ：多线程并发处理，线程池！
**学会使用队列**
添加、移除
**四组API**
![](../images/Pasted%20image%2020231113162554.png)

```java
public class test1 {  
    public static void main(String[] args) {  
        ArrayBlockingQueue blockingQueue = new ArrayBlockingQueue<>(4);  
//        System.out.println(blockingQueue.add("a"));  
        // java.lang.IllegalStateException        System.out.println(blockingQueue.add("B"));  
        System.out.println(blockingQueue.add("V"));  
        System.out.println(blockingQueue.add("C"));  
        System.out.println(blockingQueue.add("x"));  
  
        System.out.println("=============");  
  
//        System.out.println(blockingQueue.remove());  
        // java.util.NoSuchElementException        System.out.println(blockingQueue.remove());  
        System.out.println(blockingQueue.remove());  
        System.out.println(blockingQueue.remove());  
        System.out.println(blockingQueue.remove());  
    }  
}
```

```java
public class test2 {  
    public static void main(String[] args) {  
        ArrayBlockingQueue<Object> blockingQueue = new ArrayBlockingQueue<>(3);  
        System.out.println(blockingQueue.offer("a"));  
        System.out.println(blockingQueue.offer("v"));  
        System.out.println(blockingQueue.offer("n"));  
  
        // false  
        // System.out.println(blockingQueue.offer("m"));  
        System.out.println("=======================");  
  
        System.out.println(blockingQueue.poll());  
        System.out.println(blockingQueue.poll());  
        System.out.println(blockingQueue.poll());  
          
        // null  
        // System.out.println(blockingQueue.poll());    }  
}
```

```java
public class text3 {  
  
    public static void main(String[] args) throws InterruptedException {  
  
        ArrayBlockingQueue<Object> blockingQueue = new ArrayBlockingQueue<>(3);  
        blockingQueue.put("a");  
        blockingQueue.put("b");  
        blockingQueue.put("c");  
        // 一直阻塞  
        // blockingQueue.put("d");  
  
        System.out.println("===============");  
  
        System.out.println(blockingQueue.take());  
        System.out.println(blockingQueue.take());  
        System.out.println(blockingQueue.take());  
          
        // 没有元素一直阻塞  
        // System.out.println(blockingQueue.take());  
        }  
}
```

```java
public class test4 {  
  
    public static void main(String[] args) throws InterruptedException {  
        ArrayBlockingQueue<Object> blockingQueue = new ArrayBlockingQueue<>(3);  
        blockingQueue.offer("a");  
        blockingQueue.offer("b");  
        blockingQueue.offer("c");  
        // 等待超过两秒就退出  
        // blockingQueue.offer("c", 2, TimeUnit.SECONDS);  
        System.out.println("==========");  
        System.out.println(blockingQueue.poll());  
        System.out.println(blockingQueue.poll());  
        System.out.println(blockingQueue.poll());  
        System.out.println(blockingQueue.poll());  
        blockingQueue.poll(2, TimeUnit.SECONDS); // 等待超过两秒就退出  
    }  
}
```

>SnchronousQueue同步队列

没有容量，
进去一个元素，必须等待取出来之后，才能再往里面放元素！
put、take
```java
package com.code.bp;  
  
import java.util.concurrent.SynchronousQueue;  
import java.util.concurrent.TimeUnit;  
  
public class SynchronousQueueDemo {  
    public static void main(String[] args) {  
        // 同步队列  
        SynchronousQueue<Object> queue = new SynchronousQueue<>();  
        new Thread(() -> {  
            try {  
                System.out.println(Thread.currentThread().getName() + "put 1");  
                queue.put("1");  
                System.out.println(Thread.currentThread().getName() + "put 2");  
                queue.put("2");  
                System.out.println(Thread.currentThread().getName() + "put 3");  
                queue.put("3");  
            } catch (InterruptedException e) {  
                throw new RuntimeException(e);  
            }  
        }, "T1").start();  
  
        new Thread(() -> {  
            try {  
                TimeUnit.SECONDS.sleep(3);  
                System.out.println(Thread.currentThread().getName() + "=>" + queue.take());  
                TimeUnit.SECONDS.sleep(3);  
                System.out.println(Thread.currentThread().getName() + "=>" + queue.take());  
                TimeUnit.SECONDS.sleep(3);  
                System.out.println(Thread.currentThread().getName() + "=>" + queue.take());  
  
            } catch (InterruptedException e) {  
                e.printStackTrace();  
            }  
        }, "T2").start();  
    }  
}
```

## 11、线程池（重点）
线程池：三大方法、7大参数、4中拒绝策略
>池化技术

程序的运行，本质：占用系统的资源！优化资源的使用！=>池化技术
线程池、连接池、内存池、对象池///...... 创建、销毁。十分浪费资源
池化技术：事先准备好一些资源，有人要用，就来我这里拿，用完之后还给我。

**内存池的好处**
1、降低资源的消耗
2、提高响应速度
3、方便管理
**线程复用、可以控制最大并发数、管理线程**
>线程池：三大方法

![](../images/Pasted%20image%2020231114205046.png)

```java
public class Demo01 {  
    public static void main(String[] args) {  
        // 单个线程  
//        ExecutorService service = Executors.newSingleThreadExecutor();  
        // 创建一个固定的线程池的大小  
//        ExecutorService service = Executors.newFixedThreadPool(5);  
  
        // 可伸缩的  
        ExecutorService service = Executors.newCachedThreadPool();  
  
  
        for (int i = 0; i < 100; i++) {  
            service.execute(() -> {  
                System.out.println(Thread.currentThread().getName() + "   ok");  
            });  
        }  
        service.shutdown();  
    }  
}
```

>7大参数

源码分析
```java
public static ExecutorService newFixedThreadPool(int nThreads, ThreadFactory threadFactory) {  
    return new ThreadPoolExecutor(nThreads, nThreads,  
                                  0L, TimeUnit.MILLISECONDS,  
                                  new LinkedBlockingQueue<Runnable>(),  
                                  threadFactory);  
}
public static ExecutorService newSingleThreadExecutor(ThreadFactory threadFactory) {  
    return new FinalizableDelegatedExecutorService  
        (new ThreadPoolExecutor(1, 1,  
                                0L, TimeUnit.MILLISECONDS,  
                                new LinkedBlockingQueue<Runnable>(),  
                                threadFactory));  
}

public static ExecutorService newCachedThreadPool() {  
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,  
                                  60L, TimeUnit.SECONDS,  
                                  new SynchronousQueue<Runnable>());  
}

// 本质ThreadPoolExecutor()
public ThreadPoolExecutor(int corePoolSize,    // 核心线程池大小
                          int maximumPoolSize,  // 最大核心线程池大小
                          long keepAliveTime,   // 超过了没有人调用就会释放
                          TimeUnit unit,   // 超时单位
                          BlockingQueue<Runnable> workQueue, // 阻塞队列 
                          ThreadFactory threadFactory,     // 线程工厂：创建线程的，一般不用动
                          RejectedExecutionHandler handler // 拒绝策略) {  
    if (corePoolSize < 0 ||  
        maximumPoolSize <= 0 ||  
        maximumPoolSize < corePoolSize ||  
        keepAliveTime < 0)  
        throw new IllegalArgumentException();  
    if (workQueue == null || threadFactory == null || handler == null)  
        throw new NullPointerException();  
    this.acc = System.getSecurityManager() == null ?  
            null :  
            AccessController.getContext();  
    this.corePoolSize = corePoolSize;  
    this.maximumPoolSize = maximumPoolSize;  
    this.workQueue = workQueue;  
    this.keepAliveTime = unit.toNanos(keepAliveTime);  
    this.threadFactory = threadFactory;  
    this.handler = handler;  
}

```

![](../images/Pasted%20image%2020231114210424.png)

![](../images/Pasted%20image%2020231114210435.png)

>手动创建一个线程池

```java
public class Demo02 {  
  
    public static void main(String[] args) {  
  
        ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(2, 5, 3, TimeUnit.SECONDS, new LinkedBlockingDeque<>(3), Executors.defaultThreadFactory(),  
                // 队列满了，尝试去和最早的竞争，也不会抛弃异常！  
                new ThreadPoolExecutor.DiscardOldestPolicy());  
  
        for (int i = 0; i < 100; i++) {  
            threadPoolExecutor.execute(() -> {  
                System.out.println(Thread.currentThread().getName() + "ok");  
            });  
        }  
        threadPoolExecutor.shutdown();  
  
    }  
  
}
```

>四种拒绝策略

![](../images/Pasted%20image%2020231114211158.png)

```java
new ThreadPoolExecutor.AbortPolicy(); // 银行满了，还是有人进来，不处理这个人的，抛出异常  
new ThreadPoolExecutor.CallerRunsPolicy(); // 哪来的去哪里  
new ThreadPoolExecutor.DiscardPolicy(); // 队列满了，丢掉任务，不会抛出异常  
new ThreadPoolExecutor.DiscardOldestPolicy(); // 队列满了，尝试去和最早的竞争，也不会抛出异常
```

>小结和拓展

池的最大的大小如何去设置！
了解：io密集型，CPU密集型：（调优）

```java
    public static void main(String[] args) {  
        // 获取CPU的核数  
        System.out.println(Runtime.getRuntime().availableProcessors());  
        ThreadPoolExecutor executor = new ThreadPoolExecutor(2, Runtime.getRuntime().availableProcessors(), 3, TimeUnit.SECONDS, new LinkedBlockingDeque<>(3), Executors.defaultThreadFactory(), new ThreadPoolExecutor.DiscardOldestPolicy() // 队列满了，尝试去和最找的竞争，也不会抛出异常  
        );  
        for (int i = 0; i < 10; i++) {  
            executor.execute(() -> {  
                System.out.println(Thread.currentThread().getName() + "ok");  
            });  
        }  
        executor.shutdown();  
    }  
}
```

## 12、四大函数式接口（必须掌握）
新时代的程序员：lambda表达式、链式编程、函数式接口、Stream流计算
>函数式接口：只有一个方法的接口

```java
@FunctionalInterface  
public interface Runnable {  
     public abstract void run();  
}

// 泛型、枚举、反射
// lambda表达式、链式编程、函数式接口、stream流式计算
```

![](../images/Pasted%20image%2020231115110138.png)

![](../images/Pasted%20image%2020231115110414.png)

```java
public class Demo01{  
    public static void main(String[] args) {  
        Function<String, String> function = (str) -> str;  
        System.out.println(function.apply("asd"));  
    }  
}
```

>断定型接口：有一个输入参数，返回值只能是 布尔值！

![](../images/Pasted%20image%2020231115111517.png)

```java
public class Demo02 {  
    public static void main(String[] args) {  
//        Predicate<String> predicate = (str) -> {  
//            return str.isEmpty();  
//        };  
        Predicate<String> predicate = String::isEmpty;  
        System.out.println(predicate.test(""));  
    }  
}
```

>Consumer 消费型接口

![](../images/Pasted%20image%2020231115111849.png)

```java
public class Demo03 {  
    public static void main(String[] args) {  
//        Consumer<String> consumer = (str)->{System.out.println(str);};  
//        consumer.accept("sdadasd");  
        Consumer<String> consumer = System.out::println;  
        consumer.accept("sssddd");  
    }  
}
```

>Supplier 供给型接口

![](../images/Pasted%20image%2020231115112146.png)

```java
public class Demo04 {  
  
    public static void main(String[] args) {  
  
//        Supplier supplier = new Supplier() {  
//            @Override  
//            public Object get() {  
//                System.out.println("get()");  
//                return 1024;  
//            }  
//        };  
        Supplier supplier = () -> {  
            System.out.println("get()");  
            return 1024;  
        };  
        System.out.println(supplier.get());  
    }  
}
```

## 13、Stream流式计算

>什么是Stream流式计算

大数据：存储 + 计算
集合、MySQL 本质就是存储东西的；
计算都应该交给流来操作！

![](../images/Pasted%20image%2020231115112712.png)

```java
public class Test {  
  
    public static void main(String[] args) {  
        User u1 = new User(1, "a", 21);  
        User u2 = new User(1, "a", 21);  
        User u3 = new User(1, "a", 21);  
        User u4 = new User(1, "a", 21);  
  
        List<User> list = Arrays.asList(u1, u2, u3, u4);  
  
        list.stream().filter(u -> {  
            return u.getId() % 2 == 0;  
        }).filter(u -> {  
            return u.getNumber() > 23;  
        }).map(u -> {  
            return u.getName().toUpperCase();  
        }).sorted((uu1, uu2) -> {  
            return uu2.compareTo(uu1);  
        }).limit(1).forEach(System.out::println);  
    }  
}
```

## 14、ForkJoin
>什么是ForkJoin

ForkJoin在JDK1.7，并行执行任务！提高效率。大数据量！
大数据：Map Reduce （把大任务拆分为小任务）
![](../images/Pasted%20image%2020231115114438.png)

>FoekJoin 特点：工作窃取

这个里面维护的都是双端队列

![](../images/Pasted%20image%2020231115114537.png)

>ForkJoin

![](../images/Pasted%20image%2020231115114601.png)

![](../images/Pasted%20image%2020231115114611.png)

```java
public class ForkJoinDemo extends RecursiveTask<Long> {  
    private final Long start; // 1  
    private final Long end; // 1900001000  
    public ForkJoinDemo(Long start, Long end) {  
        this.start = start;  
        this.end = end;  
    }  
    @Override  
    protected Long compute() {  
        long temp = 10000L;  
        if ((end - start) < temp) {  
            long sum = 0L;  
            for (Long i = start; i < end; i++) {  
                sum += i;  
            }  
            return sum;  
        } else { // forkjoin 递归  
            long middle = (start + end) / 2;  
            ForkJoinDemo forkJoinDemo = new ForkJoinDemo(start, middle);  
            forkJoinDemo.fork(); // 拆分任务，把任务压入线程队列  
            ForkJoinDemo joinDemo = new ForkJoinDemo(middle + 1, end);  
            joinDemo.fork(); // 拆分任务，把任务压入线程队列  
            return forkJoinDemo.join() + joinDemo.join();  
        }  
    }  
}
```

测试：
```java
  
public class Test {  
    public static void main(String[] args) throws ExecutionException, InterruptedException {  
//        test1();  
        test2();  
//        test3();  
    }  
  
    // 普通程序员  
    // sum = 499999999500000000时间：345  
    public static void test1(){  
        long sum = 0L;  
        long start = System.currentTimeMillis();  
        for (long i = 0L; i < 1000_0000_0000L; i++) {  
            sum += i;  
        }  
        long end = System.currentTimeMillis();  
        System.out.println("sum = " + sum + "时间：" + (end - start));  
    }  
  
    // 10789  
    public static void test2() throws ExecutionException, InterruptedException {  
        long start = System.currentTimeMillis();  
        ForkJoinPool forkJoinPool = new ForkJoinPool();  
        ForkJoinTask<Long> task = new ForkJoinDemo(0L, 1000_0000_0000L);  
        ForkJoinTask<Long> submit = forkJoinPool.submit(task); // 提交任务  
        Long sum = submit.get();  
        long end = System.currentTimeMillis();  
        System.out.println("sum" + sum + "时间：" + (end - start));  
    }  
  
    // sum = 500000000500000000时间：194  
    public static void test3(){  
        long start = System.currentTimeMillis();  
        // Stream并行流()  
        long sum = LongStream.rangeClosed(0L,  
                1000_0000_0000L).parallel().reduce(0L, Long::sum);  
        long end = System.currentTimeMillis();  
        System.out.println("sum = " + sum + "时间：" + (end - start));  
    }  
}
```

## 15、异步回调
>Future 设计的初衷，对将来的某个事件的结果进行建模

![](../images/Pasted%20image%2020231116151245.png)
```java
public class Demo01 {  
  
    public static void main(String[] args) throws ExecutionException, InterruptedException {  
  
        // 没有返回值的 runAsync        /*CompletableFuture<Void> completableFuture =                CompletableFuture.runAsync(()->{                    try {                        TimeUnit.SECONDS.sleep(2);                    } catch (InterruptedException e) {                        throw new RuntimeException(e);                    }                    System.out.println(Thread.currentThread().getName() + "runAsync=>Void");                });        System.out.println("1111");        try {            completableFuture.get(); // 获取阻塞执行结果  
        } catch (InterruptedException | ExecutionException e) {            throw new RuntimeException(e);        }*/  
        // 有返回值的supplyAsync 异步回调  
        // ajax，成功和失败的回调  
  
        //返回的是错误信息  
        CompletableFuture<Integer> completableFuture = CompletableFuture.supplyAsync(() -> {  
            System.out.println(Thread.currentThread().getName() + "supplyAsync=>Integer");  
            int i = 10;  
            return 200;  
        });  
  
        System.out.println(completableFuture.whenComplete((t, u) -> {  
            System.out.println("t=>" + t);  
            System.out.println("u=>" + u);  
        }).exceptionally((e) -> {  
            System.out.println(e.getMessage());  
            return 404;  
        }).get());  
  
        /**  
         * succee Code 200         * erre Code 404 500         */    }  
}
```

## 16、JMM
>请你谈谈你对 Volatile 的理解

Volatile 是 Java 虚拟机提供**轻量化的同步机制**
1、保证可见性
2、不保证原子性
3、禁止指令重排

>什么是JMM

JMM：Java内存模型，不存在的东西，概念！约定！

**关于JMM的一些同步的约定：**
1、线程解锁前，必须把共享内存立刻刷回主存
2、线程加锁前，必须读取主存中的最新值到工作内存中！
3、加锁和解锁是同一把锁

线程   **工作内存**、**主内存**

**8种操作：**
![](../images/Pasted%20image%2020231116162543.png)
![](../images/Pasted%20image%2020231116162558.png)

**内存交互操作有8种，虚拟机实现必须保证每一个操作都是原子的，不可在分的（对于double和long类型的变量来说，load、store、read和write操作在某些平台上允许例外）**
* lock（锁定）：作用主内存的变量，把一个变量标识为线程独占状态
* unlock（解锁）：作用于主内存的变量，它把一个处于锁定状态的变量释放出来，释放后的变量才可以被其他线程锁定
* read（读取）：作用于主内存变量，它把一个变量的值从主内存传输到线程的工作内存中，以便随后的load动作使用
* load（载入）：作用于工作内存的变量，它把read操作从主内存中变量放入工作内存中，以便随后的load动作使用
* use（使用）：用于工作内存中的变量，它把工作内存中的变量传输给执行引擎，每当虚拟机遇到一个需要使用到变量的值，就会使用到这个指令
* assign（赋能）：作用于工作内存中的变量，它把一个从执行引擎中接收到的值放入工作内存的变量副本中
* store（存储）：作用于主内存中的变量，它把一个从工作内存中一个变量的值传送到主内存中，以便后续的write使用
* write（写入）：作用于主内存中的变量，它把store操作从工作内存中得到的变量的值放入主内存的变量中
### JMM对这八种指令的使用，制定了如下规则：
* 不允许read和load、store和write操作之一单独出现。即使使用了read必须load，使用了store必须write
* 不允许线程丢弃他最近的assign操作，即工作变量的数据改变了之后，必须告知主存
* 不允许一个线程将没有assign的数据从工作内存同步回主内存
* 一个新的变量必须在主内存中的诞生，不允许工作内存直接使用一个未被初始化的变量，就是怼变量实施use、store操作之前，必须经过assign和load操作
* 一个变量同一时间只有一个线程能对其进行lock。多次lock后，必须执行相同次数的unlock才能解锁
* 如果对一个变量进行lock操作，会清空所有工作内存中此变量的值，在执行引擎使用这个变量前，必须重新load或assign操作初始化变量的值
* 如果一个变量没有被lock操作，会清空所有工作内存中此变量的值，在执行引擎使用这个变量前，必须重新lacd或assign操作初始化变量的值
* 如何一个变量没有被lock，就不能对其进行unlock操作。也不能unlock一个被其他线程锁住的变量
* 对一个变量进行unlock变量操作之前，必须把此变量同步回主内存

问题：程序不知道主内存的值已经被修改过了

![](../images/Pasted%20image%2020231118144028.png)


## 17、Volatile
>1、保证可见性

```java
public class JMMDemo {  
  
    // 不加 volatile 程序就会死循环  
    // 加 volatile 可以保证可见性  
    private volatile static int num = 0;  
  
    public static void main(String[] args) {  
        new Thread(() -> {  
            while (num == 0) {  
  
            }  
        }).start();  
  
        try {  
            TimeUnit.SECONDS.sleep(1);  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }  
  
        num = 1;  
        System.out.println(num);  
  
    }  
  
}
```

>2、不保证原子性

原子性：不可分割
线程A在执行 任务的时候、不能被打搅的，也不能被分割。要么同时成功啊，要么同时失败。
```java
public class VDemo02 {  
  
    private volatile static int num = 0;  
  
    public static void add() {  
        num++;  
    }  
  
    public static void main(String[] args) {  
        for (int i = 0; i < 21; i++) {  
            new Thread(() -> {  
                for (int j = 0; j < 101; j++) {  
                    add();  
                }  
            }).start();  
        }  
        while (Thread.activeCount() > 2) {  
            Thread.yield();  
        }  
  
        System.out.println(Thread.currentThread().getName() + " " + num);  
    }  
}
```

**如果不加lock和synchronized，怎么样保证原子性**

![](../images/Pasted%20image%2020231118150516.png)

使用原子类，解决 原子性问题

![](../images/Pasted%20image%2020231118150602.png)

```java
public class VDemo02 {  
    private volatile static int num = 0;  
    public static void add() {  
        num++;  
    }  
    public static void main(String[] args) {  
        for (int i = 0; i < 21; i++) {  
            new Thread(() -> {  
                for (int j = 0; j < 101; j++) {  
                    add();  
                }  
            }).start();  
        }  
        while (Thread.activeCount() > 2) {  
            Thread.yield();  
        }  
        System.out.println(Thread.currentThread().getName() + " " + num);  
    }  
}
```

这些类的底层直接和操作系统挂钩！在内存中修改值！Unsafe类是一个很特殊的存在！

>指令重排

什么是指令重排：**你写的程序，计算机并不是按照你写的那样去执行的。**
源代码-->编译器优化的重排-->指令并行也可能会重排-->内存系统也会重排-->执行

**处理器在进行指令重排的时候，考虑：数据之间的依赖性！**
```java
int x = 1;
int y = 2;
x = x + 5;
y = x * x;

我们所期望的1234 但是可能执行的时候会变成 2134 1324
可不可能是 4123！
```

可能造成影响的结果：a b x y 这四个值默认都是0；
![](../images/Pasted%20image%2020231118151710.png)
正常的结果：x=0;y=0; 但是可能由于指令重排
![](../images/Pasted%20image%2020231118151753.png)
指令重排导致的诡异结果：x=2;y=1;

>非计算机专业

**volatile可以避免指令重排**
内存屏障。cPU指令。作用：
1、保证特定的操作的执行顺序！
2、可以保证某些变量的内存可见性（利用这些特性volatile实现了可见性）
![](../images/Pasted%20image%2020231118152407.png)

**Volatile 是可以保证 可见性。不能保证原子性，由于内存障壁，可以保证指令重排的现象产生！**

## 18、彻底玩转单例模式
饿汉式  DCL懒汉式 ， 深究 ！
>饿汉式

```java
public class Hungry {  
    private final static Hungry HUNGRY = new Hungry();  
    private final byte[] data1 = new byte[1024 * 1024];  
    private final byte[] data2 = new byte[1024 * 1024];  
    private final byte[] data3 = new byte[1024 * 1024];  
    private final byte[] data4 = new byte[1024 * 1024];  
  
    private Hungry() {  
  
    }  
    public static Hungry getInstance() {  
        return HUNGRY;  
    }  
}
```

>DCL懒汉式

```java
public class LazyMan {  
  
    private static boolean liubr = false;  
    private volatile static LazyMan lazyMan;  
  
    private LazyMan() {  
        synchronized (LazyMan.class) {  
            if (!liubr) {  
                liubr = true;  
            } else {  
                throw new RuntimeException("不要试图使用反射破坏异常");  
            }  
        }  
    }  
    // 双重检测锁模式的   懒汉式单例  DCL懒汉式  
    public static LazyMan getInstance() {  
        if (lazyMan == null) {  
            synchronized (LazyMan.class) {  
                if (lazyMan == null) {  
                    lazyMan = new LazyMan(); // 不是一个原子操作  
                }  
            }  
        }  
        return lazyMan;  
    }  
  
    // 反射  
    public static void main(String[] args) throws NoSuchFieldException, NoSuchMethodException, InvocationTargetException, InstantiationException, IllegalAccessException {  
        Field liubr = LazyMan.class.getDeclaredField("liubr");  
  
        liubr.setAccessible(true);  
        Constructor<LazyMan> declared = LazyMan.class.getDeclaredConstructor(null);  
        declared.setAccessible(true);  
        LazyMan instance = declared.newInstance();  
  
        liubr.set(instance, false);  
          
        LazyMan instance1 = declared.newInstance();  
        System.out.println(instance);  
        System.out.println(instance1);  
    }  
  
}
```

>静态内部类

```java
public class Holder {  
      
    private Holder(){  
          
    }  
      
    public static Holder getInstance() {  
        return InnerClass.HOLDER;  
    }  
      
    public static class InnerClass{  
        private static final Holder HOLDER = new Holder();  
    }  
      
}
```
>单例不安全，反射

>枚举

```java
public enum EnumSingle {  
    INSTANCE;  
  
    public EnumSingle getInstance() {  
        return INSTANCE;  
    }  
  
}  
  
class Test {  
    public static void main(String[] args) throws NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {  
        EnumSingle instance1 = EnumSingle.INSTANCE;  
        Constructor<EnumSingle> declaredConstructor = EnumSingle.class.getDeclaredConstructor(String.class, int.class);  
        declaredConstructor.setAccessible(true);  
        EnumSingle instance2 = declaredConstructor.newInstance();  
  
        // NoSuchMethodException: com.kuang.single.EnumSingle.<init>()  
        System.out.println(instance1);  
        System.out.println(instance2);  
    }  
}
```

![](../images/Pasted%20image%2020231118161337.png)

枚举类型的最终反编译源码：

```java
public final class EnumSingle extends Enum {  
    public static EnumSingle[] values() throws CloneNotSupportedException {  
        return (EnumSingle[]) $VALUES.clone();  
    }  
    public static EnumSingle valueOf(String name) {  
        return (EnumSingle) Enum.valueOf(EnumSingle.class, name);  
    }  
    private EnumSingle(String s, int i) {  
        super(s, i);  
    }  
    public EnumSingle getInstance() {  
        return INSTANCE;  
    }  
    public static final EnumSingle INSTANCE;  
    private static final EnumSingle $VALUES[];  
  
    static {  
        INSTANCE = new EnumSingle("INSTANCE", 0);  
        $VALUES = (new EnumSingle[] {  
                INSTANCE  
        });  
    }  
}
```

## 19、深入理解CAS

>什么是CAS

大厂你必须要深入研究底层！有所突破！**修内功，操作系统，计算机网络原理**

```java
public class CASDemo {  
    public static void main(String[] args) {  
        AtomicInteger atomicInteger = new AtomicInteger(2020);  
        // 期望、更新  
        // public final boolean compareAndSet(int expect, int update)  
        // 如果我期望的值达到了，那么就更新，否则，就不更新。CAS 是CPU的并发原语  
        System.out.println(atomicInteger.compareAndSet(2020, 2021));  
        System.out.println(atomicInteger.get());  
  
        atomicInteger.getAndIncrement();  
        System.out.println(atomicInteger.compareAndSet(2020, 2021));  
        System.out.println(atomicInteger.get());  
    }  
}
```

>UnSafe类

![](../images/Pasted%20image%2020231118170235.png)
![](../images/Pasted%20image%2020231118172654.png)

![](../images/Pasted%20image%2020231118172706.png)

CAS：比较当前工作内存中的值和主内存中的值，如果这个值是期望的，那么则执行操作！如果不是就一直循环！
**缺点：**
1、循环会耗时
2、一次性只能保证一个共享变量的原子性
3、ABA问题

>CAS：ABA问题（狸猫换太子）

![](../images/Pasted%20image%2020231118172939.png)

```java
public class CASDemo {  
    public static void main(String[] args) {  
        AtomicInteger atomicInteger = new AtomicInteger(2020);  
        // 期望、更新  
        // public final boolean compareAndSet(int expect, int update)  
        // 如果我期望的值达到了，那么就更新，否则，就不更新。CAS 是CPU的并发原语  
  
        // =============捣乱线程  
        System.out.println(atomicInteger.compareAndSet(2020, 2021));  
        System.out.println(atomicInteger.get());  
  
        atomicInteger.getAndIncrement();  
        System.out.println(atomicInteger.compareAndSet(2021, 2020));  
        System.out.println(atomicInteger.get());  
  
  
        // =========期望线程  
        System.out.println(atomicInteger.compareAndSet(2020, 6666));  
        System.out.println(atomicInteger.get());  
    }  
  
}
```

## 20、原子引用
>解决ABA问题，引用原子引用！对应的思想：乐观锁！

带版本号的原子操作！

```java
public class CASDemo01 {  
  
    static AtomicStampedReference<Integer> atomicStampedReference = new AtomicStampedReference<>(1, 1);  
  
    public static void main(String[] args) {  
  
        new Thread(() -> {  
            int stamp = atomicStampedReference.getStamp();  
            System.out.println("al => " + stamp);  
  
            try {  
                TimeUnit.SECONDS.sleep(1);  
            } catch (InterruptedException e) {  
                throw new RuntimeException(e);  
            }  
  
            atomicStampedReference.compareAndSet(1, 2,  
                    atomicStampedReference.getStamp(),  
                    atomicStampedReference.getStamp() + 1);  
            System.out.println("a2 => " + atomicStampedReference.getStamp());  
  
            System.out.println(atomicStampedReference.compareAndSet(2, 1,  
                    atomicStampedReference.getStamp(),  
                    atomicStampedReference.getStamp() + 1));  
            System.out.println("a3 => " + atomicStampedReference.getStamp());  
        }, "a").start();  
  
  
        // 乐观锁原理  
        new Thread(() -> {  
            int stamp = atomicStampedReference.getStamp();// 获得版本号  
            System.out.println("b1 => " + stamp);  
  
            try {  
                TimeUnit.SECONDS.sleep(2);  
            } catch (InterruptedException e) {  
                throw new RuntimeException(e);  
            }  
  
            System.out.println(atomicStampedReference.compareAndSet(1, 6, stamp, stamp + 1));  
            System.out.println("b2 => " + atomicStampedReference.getStamp());  
  
        }, "b").start();  
    }  
}
```
**注意：**
**Integer 使用了对象缓存机制，默认范围是 128-127 , 推荐使用静态工厂方法 valueOf 获取对象实例，而不是 new，因为 valueOf 使用缓存，而 new 一定会创建新的对象分配新的内存空间；**
![](../images/Pasted%20image%2020231118175049.png)

## 21、各种锁的理解
### 1、公平锁、非公平锁
公平锁： 非常公平， 不能够插队，必须先来后到！ 
非公平锁：非常不公平，可以插队 （默认都是非公平）
```java
/**  
 * Creates an instance of {@code ReentrantLock}.  
 * This is equivalent to using {@code ReentrantLock(false)}.  
 */public ReentrantLock() {  
    sync = new NonfairSync();  
}  
  
/**  
 * Creates an instance of {@code ReentrantLock} with the  
 * given fairness policy. * * @param fair {@code true} if this lock should use a fair ordering policy  
 */public ReentrantLock(boolean fair) {  
    sync = fair ? new FairSync() : new NonfairSync();  
}
```

### 2、可重入锁
可重入锁（递归锁）
![](../images/Pasted%20image%2020231118175434.png)

>Synchronized

```java
public class Demo01 {  
    public static void main(String[] args) {  
        Phone phone = new Phone();  
        new Thread(() -> {  
            phone.sms();  
        }, "A").start();  
        new Thread(() -> {  
            phone.sms();  
        }, "B").start();  
    }  
    private static class Phone {  
        public synchronized void sms() {  
            System.out.println(Thread.currentThread().getName() + "SMS");  
            call(); // 这里也有锁  
        }  
        public synchronized void call() {  
            System.out.println(Thread.currentThread().getName() + "Call");  
        }  
    }  
}
```

>Lock版

```java
public class Demo02 {  
  
    public static void main(String[] args) {  
        Phone2 phone = new Phone2();  
        new Thread(() -> {  
            phone.sms();  
        }, "A").start();  
        new Thread(() -> {  
            phone.sms();  
        }, "B").start();  
    }  
  
    private static class Phone2 {  
        Lock lock = new ReentrantLock();  
  
        public synchronized void sms() {  
            lock.lock();// 细节问题：lock.lock(); lock.unlock(); // lock 锁必须配对，否则就会死在里面  
            lock.lock();  
            System.out.println(Thread.currentThread().getName() + "SMS");  
            call(); // 这里也有锁  
  
            lock.unlock();  
            lock.unlock();  
        }  
  
        public synchronized void call() {  
            lock.lock();  
            System.out.println(Thread.currentThread().getName() + "Call");  
            lock.unlock();  
  
        }  
    }  
  
}
```

### 3、自旋锁

spinlock
![](../images/Pasted%20image%2020231118181020.png)
我们来定义一个锁测试
```kava
public class SpinlockDemo {  
  
    AtomicReference<Thread> atom = new AtomicReference<>();  
  
    //  加锁  
    public void myLock() {  
        Thread thread = Thread.currentThread();  
        System.out.println(Thread.currentThread().getName() + "==> mylock");  
  
        // 自旋锁  
        while (!atom.compareAndSet(null, thread)) {  
  
        }  
  
    }  
  
    // 解锁  
    public void myUnLock() {  
        Thread thread = Thread.currentThread();  
        System.out.println(Thread.currentThread().getName() + "==> myUnLock");  
        atom.compareAndSet(thread, null);  
    }  
}
```

>测试

```java
public class TestSpinLock {  
    public static void main(String[] args) {  
        SpinlockDemo lock = new SpinlockDemo();  
        new Thread(() -> {  
            lock.myLock();  
            try {  
                TimeUnit.SECONDS.sleep(5);  
            } catch (InterruptedException e) {  
                throw new RuntimeException(e);  
            } finally {  
                lock.myUnLock();  
            }  
        }, "T1").start();  
  
        new Thread(() -> {  
            lock.myLock();  
            try {  
                TimeUnit.SECONDS.sleep(1);  
            } catch (InterruptedException e) {  
                throw new RuntimeException(e);  
            } finally {  
                lock.myUnLock();  
            }  
        }, "T2").start();  
    }  
}
```

![](../images/Pasted%20image%2020231118182121.png)

## 4、死锁
>死锁是什么

![](../images/Pasted%20image%2020231118182200.png)

死锁测试，怎么排除死锁：
```java
public class DeadLockDemo {  
    public static void main(String[] args) {  
  
        String lockA = "lockA";  
        String lockB = "lockB";  
        new Thread(new MyThread(lockA, lockB), "T1").start();  
        new Thread(new MyThread(lockA, lockB), "T2").start();  
  
    }  
  
    private static class MyThread implements Runnable {  
        String lockA;  
        String lockB;  
  
        public MyThread(String lockA, String lockB) {  
            this.lockA = lockA;  
            this.lockB = lockB;  
        }  
  
        @Override  
        public void run() {  
            synchronized (lockA) {  
                System.out.println(Thread.currentThread().getName() + "lock:" + lockA + "=>get" + lockB);  
            }  
  
            try {  
                TimeUnit.SECONDS.sleep(2);  
            } catch (InterruptedException e) {  
                throw new RuntimeException(e);  
            }  
  
            synchronized (lockB) {  
                System.out.println(Thread.currentThread().getName() + "lock:" + lockB + "=>get" + lockA);  
            }  
        }  
    }  
}
```

>解决问题

1、使用`jps -l`定位进程号
![](../images/Pasted%20image%2020231118182818.png)

2、使用`jstack 进程`找到死锁问题

![](../images/Pasted%20image%2020231118182921.png)
面试，工作中！ 排查问题： 
1、日志  9
2、堆栈  1