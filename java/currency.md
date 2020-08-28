# java 并发，多线程 

## 悲观锁，乐观锁 (optimistic locking)
* 悲观锁， jdk1.5 之前靠synchronized关键字保证同步，采用独占的方式来访问这些变量。独占锁其实就是一种悲观锁，所以可以说synchronized是悲观锁。
* 乐观锁 (optimistic locking),是一种思想。相对悲观锁而言，乐观锁假设认为数据一般情况下不会造成冲突，所以在数据进行提交更新的时候，才会正式对数据的冲突与否进行检测，如果发现冲突了，则让返回用户错误的信息，让用户决定如何去做。

## 原子操作
原子操作（atomic operation）意为”不可被中断的一个或一系列操作” 。
原子操作是指一个不受其他操作影响的操作任务单元。原子操作是在多线程环境下避免数据不一致必须的手段。
int++并不是一个原子操作, 到JDK1.5，java.util.concurrent.atomic包提供了int和long类型的原子包装类

## 多线程中的上下文切换
多线程会共同使用一组计算机上的CPU， 为了让各个线程都有执行的机会，就需要轮转使用CPU。不同的线程切换使用CPU发生的切换数据等就是上下文切换。

## 线程与进程
进程是操作系统分配资源的最小单元，线程是操作系统调度的最小单元
一个程序至少有一个进程,一个进程至少有一个线程。

## 在java中守护线程和本地线程区别
java中的线程分为两种：守护线程（Daemon）和用户线程（User）。通过方法Thread.setDaemon(true), Thread.setDaemon()必须在Thread.start()之前调用，否则运行时会抛出异常。Daemon是为其他线程提供服务，如果全部的User Thread已经撤离，Daemon 没有可服务的线程，JVM撤离

## 如何在 Java 中实现线程
继承Thread类或实现Runnable接口

## Java 中 Runnable 和 Callable 有什么不同
Runnable和 Callable 都代表那些要在不同的线程中执行的任务。Runnable 从 JDK1.0 开始就有了，Callable 是在 JDK1.5 增加的。它们的主要区别是 Callable 的 call () 方法可以返回值和抛出异常.java.util.concurrent.Callable ,  V call() throws Exception

## 什么是Executors框架
Executor框架是一个根据一组执行策略调用，调度，执行和控制的异步任务的框架
利用Executors框架可以非常方便的创建一个线程池。

## 阻塞队列
阻塞队列（BlockingQueue）是一个支持两个附加操作的队列。

这两个附加的操作是：在队列为空时，获取元素的线程会等待队列可用。当队列满时，存储元素的线程会等待队列可用。

阻塞队列常用于生产者和消费者的场景，生产者是往队列里添加元素的线程，消费者是从队列里拿元素的线程。阻塞队列就是生产者存放元素的容器，而消费者也只从容器里拿元素。
ArrayBlockingQueue ：一个由数组结构组成的有界阻塞队列。
LinkedBlockingQueue ：一个由链表结构组成的有界阻塞队列。
PriorityBlockingQueue ：一个支持优先级排序的无界阻塞队列。

## 并发容器的实现
可以简单地理解为通过synchronized来实现同步的容器， Vector，Hashtable，以及Collections.synchronizedSet，synchronizedList等方法返回的容器。

## 线程状态
新建状态（New）， 它和其他Java对象一样，仅仅在堆区中被分配了内存
就绪状态（Runnable）， 线程对象创建后，其他线程调用它的start()方法
运行状态（Running）， 处于这个状态的线程占用CPU，执行程序代码
阻塞状态（Blocked）
死亡状态（Dead）

## synchronized和ReentrantLock的区别

synchronized是和if、else、for、while一样的关键字，ReentrantLock是类，这是二者的本质区别。既然ReentrantLock是类，那么它就提供了比synchronized更多更灵活的特性，可以被继承、可以有方法、可以有各种各样的类变量，ReentrantLock比synchronized的扩展性体现在几点上：
（1）ReentrantLock可以对获取锁的等待时间进行设置，这样就避免了死锁
（2）ReentrantLock可以获取各种锁的信息
（3）ReentrantLock可以灵活地实现多路通知
另外，二者的锁机制其实也是不一样的:ReentrantLock底层调用的是Unsafe的park方法加锁，synchronized操作的应该是对象头中mark word.

## ThreadLocal
线程局部变量是局限于线程内部的变量，属于线程自身所有，不在多个线程间共享。Java提供ThreadLocal类来支持线程局部变量
简单说ThreadLocal就是一种以空间换时间的做法在每个Thread里面维护了一个ThreadLocal.ThreadLocalMap把数据进行隔离，数据不共享

## java中用到的线程调度算法是什么
抢占式。一个线程用完CPU之后，操作系统会根据线程优先级、线程饥饿情况等数据算出一个总的优先级并分配下一个时间片给某个线程执行。

## 你有哪些多线程开发良好的实践
* 给线程命名
* 最小化同步范围
* 尽可能使用更高层次的并发工具而非wait和notify()来实现线程通信,如BlockingQueue,Semeaphore
* 优先使用并发容器而非同步容器
* 考虑使用线程池

## volatile
volatile的作用就是当一个线程更新某个volatile声明的变量时，会通知其他的cpu使缓存失效，从而其他cpu想要做更新操作时，需要从内存重新读取数据。
volatile 会禁止指令重排。
（1）volatile是轻量级同步机制。在访问volatile变量时不会执行加锁操作，因此也就不会使执行线程阻塞，是一种比synchronized关键字更轻量级的同步机制。
（2）volatile**无法同时保证内存可见性和原子性。加锁机制既可以确保可见性又可以确保原子性，而volatile变量只能确保可见性**。
（3）volatile不能修饰写入操作依赖当前值的变量。声明为volatile的简单变量如果当前值与该变量以前的值相关，那么volatile关键字不起作用，也就是说如下的表达式都不是原子操作：“count++”、“count = count+1”。
（4）当要访问的变量已在synchronized代码块中，或者为常量时，没必要使用volatile；
（5）volatile屏蔽掉了JVM中必要的代码优化，所以在效率上比较低，因此一定在必要时才使用此关键字。
