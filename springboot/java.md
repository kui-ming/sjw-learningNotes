

# 线程与进程

- 一个程序一个进程，与操作系统对接，具有独立内存空间

- 进程是线程的集合，一个进程包括至少一个或多个线程，线程是进程中的各个分支，共享进程内存空间



##### java默认包含几个线程？

2个，一个Main线程一个GC线程



##### java真的能开启线程吗？

不能！Java代码是由虚拟机执行，无法直接操作硬件，只能通过调用本地方法，通过调用C++开启

```java
/* 线程的启动方法 */
public synchronized void start() {
        /**
         * This method is not invoked for the main method thread or "system"
         * group threads created/set up by the VM. Any new functionality added
         * to this method in the future may have to also be added to the VM.
         *
         * A zero status value corresponds to state "NEW".
         */
        if (threadStatus != 0)
            throw new IllegalThreadStateException();

        /* Notify the group that this thread is about to be started
         * so that it can be added to the group's list of threads
         * and the group's unstarted count can be decremented. */
        group.add(this);

        boolean started = false;
        try {
            start0(); // 调用本地方法
            started = true;
        } finally {
            try {
                if (!started) {
                    group.threadStartFailed(this);
                }
            } catch (Throwable ignore) {
                /* do nothing. If start0 threw a Throwable then
                  it will be passed up the call stack */
            }
        }
    }
	
	/* native : 本地方法 */
    private native void start0();
```



##### 并发、并行区别？

并发

- CPU单核下，多条线程在很短的间内交替占用CPU，使看上去多个线程是一起执行的

并行

- CPU多核下，可同时执行多个线程



##### 并发编程的本质？

可充分利用CPU资源



##### 线程有几种状态？

从JAVA层面：**6种**

```java
public enum State {

    	// 新建状态 ：线程对象被创建但还未调用start()
        NEW,

		// 可运行状态 ：调用了start()方法等待CPU
        RUNNABLE,

		// 阻塞状态 ：获取锁失败，进入Monitor的阻塞队列，不占用CPU
        BLOCKED,

		// 等待状态 ：调用wait()方法进入Monitor的等待队列，释放锁，不占用CPU
        WAITING,

		// 有时限等待 ：调用wait(long)进入等待队列等待，超过指定时间自动进入可运行
    	// sleep(long)也是有时限等待，但不会进入等待队列，会一直占用CPU持锁等待
        TIMED_WAITING,

    	// 终止状态 ：线程代码执行完毕
        TERMINATED;
    }
```



从JVM操作系统层面：**5种**

- 新建态 ：对象被创建还未调用start()时
- 就绪态 ：调用start()方法，等待CPU
- 运行态 ：占用CPU，执行代码
- 阻塞态 ：存在阻塞队列中，无法获取CPU
- 终止态 ：线程执行完毕



##### wait/sleep区别？

- 1、**属于不同类**。wait为Object类的方法，sleep是Thread类的静态方法 (JUC：TimeUnit.DAYS.sleep(1)休眠一天)
- 2、**锁的释放**。wait释放锁不占用CPU等待，sleep持锁占用CPU等待
- 3、**作用范围不同**。sleep可在任何地方休眠，wait需要在同步代码块或方法中休眠



##### lock锁的使用？

```java
// 利用可重入锁实现同步状态下两线程交替输出值
    static class LockRunnable implements Runnable {

        static int num = 0;
        static Lock lock = new ReentrantLock(); // 创建一个可重入锁
        Condition condition = lock.newCondition(); // 取代对象监视器

        @Override
        public void run() {
            while (true){
                lock.lock(); // 手动上锁
                try {
                    // 业务
                    System.out.println(Thread.currentThread().getName() + " : " + (++num));
                    condition.signal(); // 唤醒
                    if (num >= 100) break;
                    else {
                        try {
                            condition.await(); // 等待
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                } catch (Exception e){
                    e.printStackTrace();
                } finally { // 防止抛出异常后无法解除锁
                    lock.unlock(); // 手动解除锁
                }

            }
        }
    }
```





##### 公平锁和非公平锁的区别？

公平锁：先到先执行，后到后执行，不可插队

非公平锁：可以插队（当3s排在3h线程后面时，可以插队先执行，为默认锁）



##### synchronized与Lock的区别？

- 1、synchronized是关键字；Lock是java类

- 2、synchronized无法获取锁状态；Lock可以判断是否取得锁

- 3、synchronized自动释放锁；Lock需手动释放锁

- 4、synchronized线程阻塞其他线程一直等待；Lock会通过tryLock()尝试获取锁

- 5、synchronized可重入锁，不可中断，为不公平锁；Lock可重入锁，可判断锁类型，可设置锁类型

- 6、synchronized适合少量代码同步；Lock适合大量代码同步



##### 什么是虚假唤醒？

此问题只发生在多线程上，当等待同一条件的多个线程被同时唤醒时，只有一个线程抢到锁继续执行，并在执行过程中又改变了条件，此时其他线程应该继续睡眠却在抢到锁后因为执行过判断条件而继续执行的问题。此问题多是用if循环控制条件造成的，应该使用while循环条件，不满足就会一直睡眠。



##### HashSet的底层实现？

使用HashMap实现，本质使用HashMap的key不可重复的原理，存储Set的数据。

```java
public HashSet() {
    map = new HashMap<>();
}

// add ：使用HashMap的key存储

// Dummy value to associate with an Object in the backing Map
private static final Object PRESENT = new Object(); // 不可变

public boolean add(E e) {
    return map.put(e, PRESENT)==null;
}
```



##### 线程不安全的集合与线程安全的集合？



List

线程不安全：**`ArrayList`**

```java
/**
* 使用不安全的List测试fail-fast机制
  * 有几率抛出java.util.ConcurrentModificationException异常
*/
@Test
void arrayListFailFast(){
    List<Integer> list = new ArrayList<>();
    for (int i = 0; i < 10; i++) {
        new Thread(()->{
            for (int j = 0; j < 10; j++) {
                list.add(j);
            }
        }).start();
    }
}
```



线程安全：**`Collections.synchronizedList()`**，**`CopyOnWriteArrayList`**，**`Vector`**

```java
/**
     * 使用Collections的静态方法synchronizedList加工ArrayList，使得ArrayList变为线程安全的
     */
    @Test
    void collectionsSyncToArrayList(){
        List<Integer> list = Collections.synchronizedList(new ArrayList<>());
        for (int i = 0; i < 10; i++) {
            new Thread(()->{
                for (int j = 0; j < 10; j++) {
                    list.add(j);
                }
            }).start();
        }
    }

    /**
     * Vector是线程安全的，不会存在fail-fast
     * Vector的整个方法使用synchronized同步锁，太过于臃肿，不建议使用
     */
    @Test
    void vector() {
        List<Integer> list = new Vector<>();
        for (int i = 0; i < 10; i++) {
            new Thread(() -> {
                for (int j = 0; j < 10; j++) {
                    list.add(j);
                }
                System.out.println(Thread.currentThread().getName() + " : " + list);
            },"Vector线程"+i).start();
        }
    }


    /**
     * CopyOnWriteArrayList是线程安全的，不会存在fail-fast
     * CopyOnWrite 写入时复制（COW），是计算机设计领域的一种优化策略
     * 防止多线程写入时造成的数据覆盖
     * 与Vector的区别，使用Lock锁代替synchronized，但效率似乎没有多大影响
     */
    @Test
    void copyOnWriteArrayList() {
        List<Integer> list = new CopyOnWriteArrayList<>();
        for (int i = 0; i < 10; i++) {
            new Thread(() -> {
                for (int j = 0; j < 10; j++) {
                    list.add(j);
                }
                System.out.println(Thread.currentThread().getName() + " : " + list);
            },"CopyOnWriteArrayList线程"+i).start();
        }
    }
```



Set

线程不安全：**`HashSet`**

```java
/**
     * HashSet也是线程不安全的，具备fail-fast
     * 当多条线程修改HashSet时，它会尽快抛出ConcurrentModificationException异常
     */
    @Test
    void setFailFast() {
        Set<String> set = new HashSet<>();
        for (int i = 0; i < 100; i++) {
            new Thread(()->{
                set.add(UUID.randomUUID().toString().substring(0,5));
                System.out.println(Thread.currentThread().getName() + " : " + set);
            },"HashSet线程"+i).start();
        }
    }
```



线程安全：**`Collections.synchronizedSet()`**，**`CopyOnWriteArraySet`**，**`concurrentSkipListSet`**

```java
/**
     * 使用Collections.synchronizedSet方法加工HashSet，使得HashSet变为线程安全的
     */
    @Test
    void collectionSyncSet() {
        Set<String> set = Collections.synchronizedSet(new HashSet<>());
        for (int i = 0; i < 100; i++) {
            new Thread(()->{
                set.add(UUID.randomUUID().toString().substring(0,5));
                System.out.println(Thread.currentThread().getName() + " : " + set);
            },"collectionSyncSet线程"+i).start();
        }
    }

    /**
     * 与CopyOnWriteArrayList基本一致，写时复制，解决多线程下的数据覆盖问题，是线程安全的
     */
    @Test
    void copyOnWriteToArraySet() {
        Set<String> set = new CopyOnWriteArraySet<>();
        for (int i = 0; i < 100; i++) {
            new Thread(()->{
                set.add(UUID.randomUUID().toString().substring(0,5));
                System.out.println(Thread.currentThread().getName() + " : " + set);
            },"copyOnWriteToArraySet线程"+i).start();
        }
    }
	

    @Test
    void concurrentSkipListSet() {
        Set<String> set = new ConcurrentSkipListSet<>();
        for (int i = 0; i < 100; i++) {
            new Thread(()->{
                set.add(UUID.randomUUID().toString().substring(0,5));
                System.out.println(Thread.currentThread().getName() + " : " + set);
            },"concurrentSkipListSet线程"+i).start();
        }
    }
```



Map

线程不安全：**`HashMap`**

```java
 /**
     * 使用不安全的HashMap测试fail-fast
     * 在多线程环境下，修改HashMap会尽快抛出ConcurrentModificationException 并发修改异常
     */
    @Test
    void hashMapFailFast(){
        Map<Integer,String> map = new HashMap<>();
        for (int i = 0; i < 30; i++) {
            int temp = i;
            new Thread(()->{
                map.put(temp, UUID.randomUUID().toString().substring(0,5));
                System.out.println(map);
            }).start();
        }
    }
```



线程安全：**`Collections.synchronizedMap()`**，**`ConcurrentHashMap`**

```java
/**
     * 使用Collections的synchronizedMap方法包裹HashMap，实现线程安全访问
     */
    @Test
    void CollectionsSyncMap(){
        Map<Integer, String> map = Collections.synchronizedMap(new HashMap<>());
        for (int i = 0; i < 30; i++) {
            int temp = i;
            new Thread(() -> {
                map.put(temp, UUID.randomUUID().toString().substring(0,5));
                System.out.printf("%s -> %s \n",Thread.currentThread().getName(),map);
            },"CollectionsSyncMap" + i).start();
        }
    }

    /**
     * 使用JUC的线程安全类： concurrentHashMap代替HashMap
     */
    @Test
    void concurrentHashMap(){
        ConcurrentHashMap<Integer, String> map = new ConcurrentHashMap<>();
        for (int i = 0; i < 30; i++) {
            int temp = i;
            new Thread(() -> {
                map.put(temp, UUID.randomUUID().toString().substring(0,5));
                System.out.printf("%s -> %s \n",Thread.currentThread().getName(),map);
            },"concurrentHashMap" + i).start();
        }
    }
```



##### Callable与Runnable的区别？

- 1、callable有返回值，runnable没有
- 2、callable可以抛出异常，runnable不能
- 3、callable回调方法使用call()，runnable使用run()



##### Callable如何使用？

因为Thread类只回调run()方法，所以需要使用继承了RunnableTask接口的FutureTask类包装Callable实现类，然后将FutureTask对象放入Thread类中。

```java
public class CallableTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        FutureTask<Integer> futureTask = new FutureTask<>(new CallableThread());
        new Thread(futureTask, "A").start();
        new Thread(futureTask, "B").start(); // 结果会被缓存，新建两个线程但只执行一次，第二次直接return
        Integer num = (Integer) futureTask.get(); // 阻塞，直到获取call()方法的返回值，可以把它放在最后或通过异步通信调用
        System.out.println(num);
        /* 结果：等待3秒 输出：
         A调用CALL方法完成！
         1024
        */
    }
}

class CallableThread implements Callable<Integer> {

    @Override
    public Integer call() throws Exception {
        TimeUnit.SECONDS.sleep(3);

        if ("A".equals(Thread.currentThread().getName())) {
            System.out.println("A调用CALL方法完成！");
            return 1024;
        } else {
            System.out.println("B调用CALL方法完成！");
            return 2048;
        }
    }
}
```





##### 使用FutureTask的get()方法接收Callable返回值是否会阻塞线程？

会阻塞等待，同时多条线程使用同一个FutureTask对象会进行资源优化缓存，只有第一个线程会执行，其他直接返回第一个线程的返回值。



##### 常用的同步辅助类？



**`CountDownLatch`**类：允许一个或多个线程等待直到在其他线程中执行的一组操作完成的同步辅助类

- **countDown()**：数量-1
- **await()**：等待计算器归零，然后向下执行
- 原理：每次线程调用countDown()数量-1，当计数器为0时，所有用countDownLatch.await()方法等待的线程开始运行

```java
public class CountDownLatchTest {
    /**
     * CountDownLatch : 允许一个或多个线程等待直到在其他线程中执行的一组操作完成的同步辅助类
     */
    public static void main(String[] args) throws InterruptedException {
        CountDownLatch latch = new CountDownLatch(6); // 减数器，初始计数6
        for (int i = 0; i < 6; i++) {
            new Thread(()->{
                System.out.println(Thread.currentThread().getName() + "执行完毕！");
                latch.countDown();
            }).start();
        }
        new Thread(()->{
            try {
                latch.await();
                System.out.println("计数器归零，本线程开始执行！");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

        }).start();
        latch.await(); // 将该线程等待，直到计数器归零
        System.out.println("latch归零，main线程继续执行！");
        
        /*
        Thread-0执行完毕！
        Thread-4执行完毕！
        Thread-2执行完毕！
        Thread-3执行完毕！
        Thread-1执行完毕！
        Thread-5执行完毕！
        计数器归零，本线程开始执行！
        latch归零，main线程继续执行！
        */
    }
}
```



**`CyclicBarrier `**：允许一组线程全部等待彼此达到共同屏障点的同步辅助

- **await()** ：该线程等待，直到等待队列中的线程达到指定数量后全部继续执行
- 原理：先使一组线程等待，当最后一个线程开始等待时，这一组线程全部继续执行

```java
public class CyclicBarrierTest {
    /**
     * CyclicBarrier : 允许一组线程全部等待彼此达到共同屏障点的同步辅助
     * 先使一组线程等待，当最后一个线程开始等待时，这一组线程全部继续执行
     */
    public static void main(String[] args) throws BrokenBarrierException, InterruptedException {
        // 收集7龙珠
        CyclicBarrier barrier = new CyclicBarrier(7, new Runnable() {
            @Override
            public void run() {
                // 当线程等待数量为7后，马上执行该线程
                System.out.println("召唤神龙！");
            }
        });
        for (int i = 0; i < 7; i++) {
            final int temp = i;
            new Thread(()->{
                System.out.printf("%s 收集到 %d号龙珠 \n",Thread.currentThread().getName(),temp+1);
                try {
                    barrier.await(); // 等待线程达到一定数量，7
                } catch (Exception e) {
                    e.printStackTrace();
                }
                // 等待线程达到7，开始破障，全部线程继续执行
                System.out.println("破障！线程继续执行");
            }).start();
        }
    }
}

```



**`Semaphore`** ：一个计数信号量。在概念上，维持指定数量许可证，获得许可证的继续执行，没有的等待。

- **acquire()** ：把持一个信号量
- **release()** ：释放一个信号量
- 原理：多个资源的互斥使用，限流控制最大线程数。简单的说就是   一组线程抢指定数量的信号量，抢到信号量的线程继续执行，没抢到的休眠到其他线程释放信号量后继续抢。

```java
ublic class SemaphoreTest {

    public static void main(String[] args) {
        // 抢车位
        Semaphore semaphore = new Semaphore(3); // 3个信号量/许可证/车位
        // 申请到信号量的线程可继续执行，申请不到的继续等待
        for (int i = 0; i < 5; i++) {
            new Thread(()->{
                try {
                    // 该线程请求一个信号量，没有则休眠等待，直到其他线程interrupted中断或release()释放信号量
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName() + "抢到车位！");
                    // 睡眠两秒
                    TimeUnit.SECONDS.sleep(2);
                    System.out.println(Thread.currentThread().getName() + "离开车位！");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    // 该线程释放信号量
                    semaphore.release();
/*
Thread-0抢到车位！
Thread-2抢到车位！
Thread-1抢到车位！
Thread-2离开车位！
Thread-0离开车位！
Thread-3抢到车位！
Thread-1离开车位！
Thread-4抢到车位！
Thread-3离开车位！
Thread-4离开车位！
*/
                }
            }).start();
        }
    }
}
```



##### 输出HashMap对象为什么能打印键值对？

HashMap继承自AbstractMap这个抽象类，它的父类实现了Map接口，这是前提。我们发现HashMap本身并没有toString方法，而print输出对象本质是调用它的toString方法，于是我傻傻的去接口里看发现也不可能存在，于是我知道了，它还有父类！HashMap输出会调用父类的toString方法，而父类的toString方法的确调用了迭代器输出了整个集合的键值对。此时，结合Fail-Fast的机制，在多线程中同时修改HashMap的同时又调用迭代器循环HashMap会报ConcurrentModificationException异常，这就说得通同样是多线程环境，一个输出HashMap对象的本身会抛出异常，而调用get()方法只会产生脏读的问题。





##### ReadWriteLock读写锁？

**`ReadWriteLock`**接口的实现类**`ReentrantReadWriteLock`**，此实现类中有两个用于读写控制的内部类**`ReadLock`**与**`WriteLock`**。



**ReadLock(读锁/共享锁)**：上锁后，该线程只能读不要写，其他线程也可以同时申请读锁，从而进行多个线程同时读的操作。

**WriteLock(写锁/独占锁/排他锁)**：一个线程独占，其他线程等待写锁或读锁，直到此线程释放写锁。



注意：

- 除了读读不互斥，读写、写写都互斥

- 在拿到读锁后再拿写锁会导致死锁，因为写锁排他，一个线程必须释放读锁才能拿到写锁

- 拿到读锁，其线程本身也不能再拿取写锁，否则会死锁，因为写锁会等待读锁释放，而释放语句又在获取写锁后面
- 写锁的优先级比读锁高，其他线程需要等待写锁释放后才能获取读锁或写锁，但本线程可以在获取写锁的情况下再获取读锁
- 读锁线程必须等待前一个写锁线程释放，但不必等待后一个写锁线程被执行，如果此时读后一个写锁写入的数据会出现脏读



```java
public class ReadWriteLockTest {
    /**
     * 测试独占锁(写锁)与共享锁（读锁）
     * 共享锁：上锁后，该线程只能读不能写，其他线程也可以同时申请锁，从而可以进行同时读的操作。
     * 独占锁：一个线程独占，其他线程不可申请锁，只能等到此线程运行完毕。
     */
    public static void main(String[] args) {

        MyCache myCache = new MyCache();
        for (int i = 0; i < 10; i++) {
            final int temp = i;
            new Thread(()->{
                myCache.put(temp+"", temp + "号数据");
            },temp+"号线程").start();
        }
        for (int i = 0; i < 10; i++) {
            final int temp = i;
            new Thread(()->{
                System.out.println(Thread.currentThread().getName()+"读取完毕 ： " + myCache.get(temp+""));

            },temp+"号线程").start();
        }
    }

}

class MyCache {

    private static Map<String,String> cache = new HashMap<>();
    ReadWriteLock  lock = new ReentrantReadWriteLock();
    // 在拿到读锁后再拿写锁会导致死锁，因为写锁排他，一个线程必须释放读锁才能拿到写锁
    // 拿到读锁，其线程本身也不能再拿取写锁，否则会死锁，因为写锁会等待读锁释放，而释放语句又在获取写锁后面
    // 在读锁中不要进行写操作，这是线程不安全的，同时更不能在读锁下获取写锁，这会导致死锁的产生
    // 写锁的优先级比读锁高，其他线程需要等待写锁释放后才能获取读锁，但本线程可以在获取写锁的情况下再获取读锁
    // 读锁线程必须等待前一个写锁释放，但不必等待后一个写锁被执行，如果此时读后一个写锁写入的数据则会出现脏读。
    public void put(String key, String value) {

        lock.writeLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + " 存储 " + key);

            cache.put(key,key);
            System.out.println(Thread.currentThread().getName() + " 存储完成！");
            TimeUnit.SECONDS.sleep(2);
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.writeLock().unlock();
        }

    }

    public String get(String key) {
        lock.readLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + " 读取 " + key);
            return cache.get(key);
        } finally {
            lock.readLock().unlock();
        }
    }

}
```



##### BlockingQueue阻塞队列？

BlockingQueue是Queue的子接口，常用的实现类有`ArrayBlockingQueue`和。其本质是先进先出（FIFO）的队列，可以很好的解决生产者消费者问题，主要用到的方法是添加、移除方法。



四组API

| 方式         | 会抛出异常      | 有返回值，不抛出异常 | 阻塞等待     | 超时等待                        |
| ------------ | --------------- | -------------------- | ------------ | ------------------------------- |
| 添加         | *boolean* add() | *boolean* offer()    | *void* put() | *boolean* offer(e,timeout,unit) |
| 移除         | *E* remove()    | *E* poll()           | *E* take()   | *E* poll(timeout,unit)          |
| 检查队首元素 | *E* element()   | *E* peek()           | -            | -                               |



```java
// 会抛出异常的添加、移除方法
    @Test
    void throwException(){
        // 创建一个固定容量为2的阻塞队列
        ArrayBlockingQueue<Integer> queue = new ArrayBlockingQueue<>(2);
        // 添加3个元素，添加成功返回True，失败抛出异常
        System.out.println(queue.add(1)); //true
        System.out.println(queue.add(2)); // true
        // 添加第3个元素时，因为队列空间已满，所以添加失败抛出异常 IllegalStateException: Queue full
        try {
            System.out.println(queue.add(3)); // Queue full
        } catch (Exception e) {
            e.printStackTrace();
        }
        // 移除3个元素，移除成功返回移除元素信息，失败抛出异常
        System.out.println(queue.remove()); // 1
        // queue.element()检查队首元素,如果当前队列为空会抛出异常IllegalStateException: Queue full
        System.out.println(queue.element()); // 2
        System.out.println(queue.remove());  // 2
        // 移除第3个元素时，因为队列为空，移除失败抛出异常IllegalStateException: Queue full
        System.out.println(queue.remove()); // Queue full
    }

    // 有返回值，不会抛出异常的添加、移除方法
    @Test
    void notThrowException(){
        // 创建一个固定容量为2的阻塞队列
        ArrayBlockingQueue<Integer> queue = new ArrayBlockingQueue<>(2);
        // 添加3个元素
        System.out.println(queue.offer(1)); // true
        System.out.println(queue.offer(2)); // true
        System.out.println(queue.offer(3)); // false
        // 移除3个元素
        System.out.println(queue.poll()); // 1
        System.out.println(queue.poll());  // 2
        // queue.peek()检查队首元素，如果队列为空返回null
        System.out.println(queue.peek()); // null
        System.out.println(queue.poll()); // null
    }

    // 当队列满或空时，添加或移除操作会一直阻塞等待
    @Test
    void blockingWaiting() throws InterruptedException {
        // 创建一个固定容量为2的阻塞队列
        ArrayBlockingQueue<Integer> queue = new ArrayBlockingQueue<>(2);
        // 添加3个元素
        System.out.println(queue.offer(1)); // true
        System.out.println(queue.offer(2)); // true

        new Thread(()->{
            try {
                TimeUnit.SECONDS.sleep(2);
                System.out.println(queue.take()); // 1
                System.out.println(queue.take()); // 2
                // 队列空时，会阻塞等待，直到元素入队后移除
                System.out.println(queue.take()); // 3
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();

        // 队列满时一直阻塞等待队列有空闲
        queue.put(3);
        System.out.println("添加第3个元素成功");
    }

    // 限时阻塞等待，等待一定时间后不成功则返回false或null
    @Test
    void timeBlockingWait() throws InterruptedException {
        // 创建一个固定容量为2的阻塞队列
        ArrayBlockingQueue<Integer> queue = new ArrayBlockingQueue<>(2);
        // 添加
        System.out.println(queue.offer(1)); // true
        System.out.println(queue.offer(2)); // true
        new Thread(()->{
            try {
                TimeUnit.SECONDS.sleep(3);
                queue.poll(); // 1
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();
        // 如果队满，一直阻塞等待5秒，在此期间队列空闲则写入数据，否则5秒后返回false
        System.out.println(queue.offer(3, 5, TimeUnit.SECONDS)); // 3秒后写入数据，true
        // 移除
        System.out.println(queue.poll()); // 2
        System.out.println(queue.poll()); // 3
        System.out.println(queue.poll(4, TimeUnit.SECONDS)); // 4秒后返回null
    }
```



##### SynchronousQueue同步队列？

- SynchronousQueue继承自`AbstractQueue`，实现`BlockingQueue`接口。

- 同步队列没有容量，一次只能存入一个元素，必须取出来后才能放下一个元素。

- 使用put()方法存入，使用take()方法取出。

```java
public class SynchronousQueueTest {
    /**
     * SynchronousQueue：同步队列
     * 同步队列没有容量，一次只能存入一个元素，必须取出来后才能放下一个元素
     */
    public static void main(String[] args) {

        SynchronousQueue<Integer> queue = new SynchronousQueue<>();

        new Thread(()->{
            try {
                System.out.println(Thread.currentThread().getName() + " put " + 1);
                queue.put(1);
                System.out.println(Thread.currentThread().getName() + " put " + 2);
                queue.put(2);
                System.out.println(Thread.currentThread().getName() + " put " + 3);
                queue.put(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"生产者线程").start();

        new Thread(()->{
            try {
                TimeUnit.SECONDS.sleep(1);
                System.out.println(Thread.currentThread().getName() + " take " + queue.take());
                TimeUnit.SECONDS.sleep(1);
                System.out.println(Thread.currentThread().getName() + " take " + queue.take());
                TimeUnit.SECONDS.sleep(1);
                System.out.println(Thread.currentThread().getName() + " take " + queue.take());
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "消费者线程").start();

    }
/*生产者线程 put 1
消费者线程 take 1
生产者线程 put 2
消费者线程 take 2
生产者线程 put 3
消费者线程 take 3*/
}
```



#### 线程池

​		线程池就是一种线程管理机制，它可以管理一组线程并重复利用它们，避免频繁创建和销毁线程导致的资源开销。线程池通常由线程管理器、工作线程组和等待任务队列三部分组成。线程池接收客户端的请求，并将请求封装成一个任务，然后将此任务添加到任务队列来管理这些任务。当工作线程空闲时，线程池管理器从任务队列中取出一个任务，交给空闲的工作线程运行。工作线程运行完毕后并不被销毁，而是继续等待执行下一个任务。



##### 什么是池化技术？

- 程序的运行，本质是占用系统的资源！池化技术就是优化资源的使用。

- 如线程池、连接池、内存池、常量池、对象池等

- 池化技术：事先准备一些资源，有人要用就在池子里拿，用完后丢入池子供下一个人使用。



##### 池化技术的好处？

- 重复利用线程，不必频繁的创建、销毁线程，降低资源消耗
- 控制并发度，避免系统因过多的线程而导致的性能下降和资源浪费
- 管理线程，方便维护
- 线程可复用，可控制最大并发数，管理线程



##### 三种方法



```java
public class ExecutorsTest {
    // Executors 创建线程池的工具类，具有三大创建线程池的方法
    public static void main(String[] args) {
        // ExecutorService threadPool = Executors.newSingleThreadExecutor(); // 1、创建一个单线程的线程池
        ExecutorService threadPool = Executors.newFixedThreadPool(5); // 2、创建一个线程数量固定为5的线程池
        ///3、创建一个线程数量不固定的线程池，随并发强度增加线程，如果在需要执行线程时池中没有空闲线程就会创建新的线程
        //ExecutorService threadPool = Executors.newCachedThreadPool();

        try {
            for (int i = 0; i < 100; i++) {
                threadPool.execute(()->{
                    System.out.println(Thread.currentThread().getName() + " OK!");
                });
            }
        }finally {
            // 关闭线程池，不关闭的话线程池中的线程一直阻塞等待，程序不会结束
            threadPool.shutdown();
        }
    }
}
```



##### 七大参数



源码分析

```java
// Executors的三大方法
public static ExecutorService newSingleThreadExecutor() {
        return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1,
                                    0L, TimeUnit.MILLISECONDS,
                                    new LinkedBlockingQueue<Runnable>())); // 无界队列，等待队列无限长，此时maximumPoolSize将无用
    }

public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>()); // 无界队列，等待队列无限长，此时maximumPoolSize将无用
    }

public static ExecutorService newCachedThreadPool() {
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE, // 最多创建约等于21亿个线程，不加限制可能会出现OOM
                                      60L, TimeUnit.SECONDS,
                                      new SynchronousQueue<Runnable>()); // 同步队列，此时任务无法在队列中等待，新任务来时便会创建线程执行
    }

// 本质：调用ThreadPoolExecutor()

public ThreadPoolExecutor(int corePoolSize, // 核心线程池大小
                              int maximumPoolSize, // 最大核心线程池大小
                              long keepAliveTime, // 超过指定时间的空闲线程会被释放
                              TimeUnit unit, // 超时单位
                              BlockingQueue<Runnable> workQueue, // 阻塞队列
                              ThreadFactory threadFactory, // 线程工厂，创建线程的
                              RejectedExecutionHandler handler) { // 拒绝策略
        if (corePoolSize < 0 ||
            maximumPoolSize <= 0 ||
            maximumPoolSize < corePoolSize ||
            keepAliveTime < 0)
            throw new IllegalArgumentException();
        if (workQueue == null || threadFactory == null || handler == null)
            throw new NullPointerException();
        this.corePoolSize = corePoolSize;
        this.maximumPoolSize = maximumPoolSize;
        this.workQueue = workQueue;
        this.keepAliveTime = unit.toNanos(keepAliveTime);
        this.threadFactory = threadFactory;
        this.handler = handler;
    }
```

七大核心参数解释：

- **`corePoolSize`**：线程池**核心(最小)**线程数量(>=1)，线程池会始终保持这个数量的线程不会销毁，除非设置了`allowCoreThreadTimeOut`参数。

- **`maximumPoolSize`**：线程池**最大**线程数量(>=1)，如果**核心线程**和**等待队列**已满，将启动新的临时线程，但线程池中的线程数不会超过最大线程数量。

- **`keepAliveTime`**：临时线程存活时间，临时线程空闲时间超过此时间时将被销毁。

- **`TimeUnit`**：存活时间单位，通过`TimeUnit`类指定`keepAliveTime`的时间单位，是毫秒还是秒还是其他。

- **`BlockingQueue`**：通过传递一个阻塞队列来当做线程池中的等待队列，不同的阻塞队列对线程池的影响也不同。

- **`ThreadFactory`**：传递一个创建线程的线程工厂，可以设置线程的名称，是否是守护线程等，一般默认。

- **`RejectedExecutionHandler`**：拒绝策略，当工作线程大于等于线程池最大线程同时等待队列已满时，后进的线程会被线程池拒绝，一共有四种拒绝策略。



##### 四种拒绝策略？

- **AbortPolicy**：丢弃任务并抛出 `RejectedExecutionException` 异常。（默认策略）
- **DiscardPolicy**：丢弃任务，但是不抛出异常。
- **DiscardOldestPolicy**：抛弃等待队列中的等待时间最久的任务，然后重新提交任务。
- **CallerRunsPolicy**：将任务返回给调用者（提交任务给线程池的）线程，让调用者线程执行此任务。

```java
	@Test
    void abortPolicy(){
        ThreadPoolExecutor pool = new ThreadPoolExecutor(
                2,
                5,
                2,
                TimeUnit.SECONDS,
                new LinkedBlockingDeque<>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy()); // AbortPolicy 池子满了，还有线程进来，不处理直接抛异常
        try {
            for (int i = 0; i < 9; i++) { // 3+5=8,还有一个线程会被丢弃
                final int temp = i;
                pool.execute(()->{
                    System.out.println(Thread.currentThread().getName() + " OK!" + temp);
                });
            }
        } finally {
            pool.shutdown();
        }
    }

    @Test
    void discardPolicy(){
        ThreadPoolExecutor pool = new ThreadPoolExecutor(
                2,
                5,
                2,
                TimeUnit.SECONDS,
                new LinkedBlockingDeque<>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.DiscardPolicy()); // DiscardPolicy 池子满了，还有线程进来，不处理但也不抛异常
        try {
            for (int i = 0; i < 9; i++) { // 3+5=8,还有一个线程会被丢弃
                final int temp = i;
                pool.execute(()->{
                    System.out.println(Thread.currentThread().getName() + " OK!" + temp);
                });
            }
        } finally {
            pool.shutdown();
        }
    }

    @Test
    void discardOldestPolicy(){
        ThreadPoolExecutor pool = new ThreadPoolExecutor(
                2,
                5,
                2,
                TimeUnit.SECONDS,
                new LinkedBlockingDeque<>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.DiscardOldestPolicy()); // DiscardOldestPolicy 池子满了，还有线程进来，抛弃等待队列中最老的那个线程
        try {
            for (int i = 0; i < 9; i++) { // 3+5=8,还有一个线程会被丢弃
                final int temp = i;
                pool.execute(()->{
                    System.out.println(Thread.currentThread().getName() + " OK!" + temp);
                });
            }
        } finally {
            pool.shutdown();
        }
    }

    @Test
    void callerRunsPolicy(){
        ThreadPoolExecutor pool = new ThreadPoolExecutor(
                2,
                5,
                2,
                TimeUnit.SECONDS,
                new LinkedBlockingDeque<>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.CallerRunsPolicy()); //  池子满了，从哪来回哪去，让调用者线程执行
        try {
            for (int i = 0; i < 9; i++) { // 最后一个线程会被main线程执行
                final int temp = i;
                pool.execute(()->{
                    System.out.println(Thread.currentThread().getName() + " OK!" + temp);
                });
            }
        } finally {
            System.out.println("由main线程调用");
            pool.shutdown();
        }
    }
```



##### IO密集型与CPU密集型？

- CPU 密集型 逻辑处理器有多少个，就设置最大线程为多少，可以保持CPU的效率最高
- IO 密集型 判断程序中的大型IO任务个数，最大线程数大于这个个数，可以是两倍
