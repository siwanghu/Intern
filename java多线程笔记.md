# Java多线程（siwang.hu&nbsp;&nbsp;V1.0）  
> ## **1.基本概念**  
> + 并发：交替执行多任务，不一定要同时  
>  
> + 并行：同时处理多个任务  
>  
> + 同步：程序发出一个调用，在没有得到结果之前，该调用就不返回，程序阻塞。但是一旦调用返回，就得到返回值  
>  
> + 异步：程序发出一个调用，这个调用就直接返回了，没有返回结果。当一个异步过程调用发出后，调用者不会立刻得到结果。而是在调用发出后，被调用者通过状态、来通知调用者，或通过回调函数处理这个调用  
>  
> + 阻塞：是指调用结果返回之前，当前线程会被挂起。调用线程只有在得到结果之后才会返回  
>  
> + 非阻塞：指在不能立刻得到结果之前，该调用不会阻塞当前线程  
>  
> + 竞态条件：当两个线程竞争同一资源时，如果对资源的访问顺序敏感，就称存在竞态条件  
>  
> + 临界区：导致竞态条件发生的代码区称作临界区  
>  
> + 死锁：多个线程互相等待对方释放锁，从而进入无限期等待状态  
>  
> + 活锁：活锁指的是任务或者执行者没有被阻塞，由于某些条件没有满足，导致一直重复尝试—失败—尝试—失败的过程  
>  
> + 饥饿：线程一直无法获得运行资源，导致一直没有运行  
>  
> + 上下文切换： CPU 从执行一个线程切换到执行另一个线程时，CPU 需要保存当前线程的本地数据，程序指针等状态，并加载下一个要执行的线程的本地数据，程序指针等  
>  
> + 公平锁：多个线程按照申请锁的顺序来获取锁(Lock与Semaphore可以在构造函数中指定为公平锁)  
>  
> + 非公平锁：多个线程获取锁的顺序并不是按照申请锁的顺序，有可能后申请的线程比先申请的线程优先获取锁(synchronized是一种非公平锁)  
>  
> + 可重入锁：在同一个线程在外层方法获取锁的时候，在进入内层方法会自动获取锁(synchronized是一个可重入锁)  
>  
> + 独享锁：指该锁一次只能被一个线程所持有(互斥锁)  
>  
> + 共享锁：指该锁可被多个线程所持有(读写锁)  
>  
> + 互斥锁：独享锁具体实现，在Java中是ReentrantLock类  
>  
> + 读写锁：共享锁具体实现，Java中的具体实现就是ReadWriteLock  
>  
> + 自旋锁：自旋锁是指尝试获取锁的线程不会立即阻塞，而是采用循环的方式去尝试获取锁  
>  
> + 乐观锁：总是认为不会产生并发问题，每次去取数据的时候总认为不会有其他线程对数据进行修改，因此不会上锁，但是在更新时会判断其他线程在这之前有没有对数据进行修改，一般会使用版本号机制或CAS操作实现  
>  
> + 悲观锁： 总是假设最坏的情况，每次取数据时都认为其他线程会修改，所以都会加锁（读锁、写锁、行锁等），当其他线程想要访问数据时，都需要阻塞挂起  
>  
> ## **2.进程与线程的区别**  
>  
> + 进程代表一个正在运行的程序  
>  
> + 进程是操作系统进行资源分配的基本单位  
>  
> + 线程是操作系统进行调度的基本单位  
>  
> + 一个程序至少有一个进程，一个进程至少有一个线程  
>  
> + 线程比进程划分更细，执行开销更小，并发性更高  
>  
> + 进程是一个实体，拥有独立的资源；同一个进程中的多个线程共享进程的资源  
>  
> &nbsp;&nbsp;&nbsp;&nbsp;*JVM在单个进程中运行，JVM中的线程共享属于该进程的堆。这就是为什么几个线程可以访问同一个对象。线程共享堆并拥有自己的堆栈空间。这是为何一个线程如何调用一个方法以及它的局部变量是保持线程安全的。但是堆不是线程安全的并且为了线程安全必须进行同步*  
> ## **3.多线程的优缺点**  
> **优点：**  
> + 更好的资源利用
> + 更简单的编程模型  
> + 程序响应更灵敏  
>  
> **缺点：**  
> + 安全性问题(资源访问)  
> + 活跃性问题(死锁)  
> + 性能问题(上下文切换)  
>  
> ## **4.如何避免死锁与活锁**  
> + 加锁顺序  
> 确保所有的线程都是按照相同的顺序获得锁，那么死锁就不会发生  
>  
> + 加锁时限  
> 获取锁的时候加一个超时时间，这也就意味着在尝试获取锁的过程中若超过了这个时限该线程则放弃对该锁请求,回退并释放所有已经获得的锁  
>  
> + 死锁检测  
>  
> + 没有避免活锁的通用指南  
> 需要开发者自己小心设计代码逻辑  
# Java线程  
> java中定义了6种不同的线程状态，在给定的一个时刻，线程只能处于其中的一个状态：  
> + 开始（New）：还没有调用 start() 方法的线程处于此状态  
>  
> + 可运行（Runnable）：已经调用了start()方法的线程状态。此状态意味着，线程已经准备好了，一旦被线程调度器分配了CPU时间片，就可以运行线程  
>  
> + 阻塞（Blocked）：线程有可能会进入I/O交互，还可能调用sleep()方法，还有可能在当前线程当中有其它线程调用了join()方法，阻塞状态的线程是没有释放对象锁  
>  
> + 等待（Waiting）:一个线程处于等待状态，是由于执行了 3 个方法中的任意方法:Object.wait(),Thread.join()
LockSupport.park()  
>  
> + 定时等待（Timed waiting）：一个线程处于定时等待状态，是由于执行了以下方法中的任意方法：
Thread.sleep(sleeptime)
Object.wait(timeout)
Thread.join(timeout)
LockSupport.parkNanos(timeout)
LockSupport.parkUntil(timeout)  
>  
> + 终止(Terminated)：线程 run() 方法执行结束，或者因异常退出了 run() 方法，则该线程结束生命周期  
>  
> ![图片](./data/thread-state.png)  
>  
> *区别：进入waiting状态是线程主动的, 而进入blocked状态是被动的;相同点:都暂时停止线程的执行, 线程本身不会占用CPU时间片.区别是调用了sleep方法的线程直接受CPU调度, 而wait则是等待另外的java线程在持有同一个对象锁的同步块/方法中进行notify调用*  
# Java线程创建  
> + 继承Thread类  
> + 实现Runable接口  
> + 使用线程池(ThreadPoolExecutor)  
> + 实现callable接口  
```
public class CallableDemo {

    public static void main(String[] args) {
        Callable<Integer> callable = () -> new Random().nextInt(100);
        FutureTask<Integer> future = new FutureTask<>(callable);
        new Thread(future).start();
        try {
            Thread.sleep(1000);//可能做一些事情
            System.out.println(future.get());
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        }
    }
} 
```  
# Thread中的重要方法  
> + run - 线程的执行实体  
>  
> + start - 线程的启动方法  
>  
> + setName、getName - 可以通过 setName()、 getName() 来设置、获取线程名称  
>  
> + setPriority、getPriority - 在 Java 中，所有线程在运行前都会保持在就绪状态，那么此时，哪个线程优先级高，哪个线程就有可能被先执行。可以通过 setPriority、getPriority 来设置、获取线程优先级  
>  
> + setDaemon、isDaemon - 可以使用 setDaemon() 方法设置线程为守护线程；可以使用 isDaemon() 方法判断线程是否为守护线程  
>  
> + isAlive - 可以通过 isAlive 来判断线程是否启动  
>  
> + interrupt - 当一个线程运行时，另一个线程可以直接通过 interrupt() 方法中断其运行状态  
>  
> + join - 使用 join() 方法让一个线程强制运行，线程强制运行期间，其他线程无法运行，必须等待此线程完成之后才可以继续执行  
>  
> + Thread.sleep - 使用 Thread.sleep() 方法即可实现休眠  
>  
> + Thread.yield - 可以使用 Thread.yield() 方法将一个线程的操作暂时让给其他线程执行  
>  
# Java线程间通信  
> ## **Object**
> wait、notify、notifyAll 是 Object 类中的方法  
> + wait - 线程自动释放其占有的对象锁，并等待 notify  
> + notify - 唤醒一个正在 wait 当前对象锁的线程，并让它拿到对象锁  
>  
> + notifyAll - 唤醒所有正在 wait 前对象锁的线程  
>  
> **notify与notifyAll的区别**  
> + 唤醒在此对象监视器上等待的单个线程。如果所有线程都在此对象上等待，则会选择唤醒其中一个线程。选择是任意性的，并在对实现做出决定时发生  
>  
> + 唤醒在此对象监视器上等待的所有线程  
>  
> ## **管道**  
> 主要用于线程之间的数据传输，而传输的媒介为内存. 4 种具体实现：PipedOutputStream、PipedInputStream、PipedReader 和 PipedWriter，前两种面向字节，而后两种面向字符  
>  
> *ThreadLocal线程本地变量，ThreadLocal 为变量在每个线程中都创建了一个副本，那么每个线程可以访问自己内部的副本变量*  
```
public class Piped {

    public static void main(String[] args) throws Exception {
        PipedWriter out = new PipedWriter();
        PipedReader in = new PipedReader();
        // 将输出流和输入流进行连接，否则在使用时会抛出IOException
        out.connect(in);
        Thread printThread = new Thread(new Print(in), "PrintThread");
        printThread.start();
        int receive = 0;
        try {
            while ((receive = System.in.read()) != -1) {
                out.write(receive);
            }
        } finally {
            out.close();
        }
    }

    static class Print implements Runnable {

        private PipedReader in;

        Print(PipedReader in) {
            this.in = in;
        }

        public void run() {
            int receive = 0;
            try {
                while ((receive = in.read()) != -1) {
                    System.out.print((char) receive);
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```  
# Java并发底层实现  
> ## **1.synchronized**  
> 关键字 synchronized 可以保证在同一个时刻，只有一个线程可以执行某个方法或者某个代码块  
> **原理：**  
> + 对于普通同步方法，锁是当前实例对象  
> 造成其他的线程也无法调用当前类的其他同步方法  
>  
> + 对于静态同步方法，锁是当前类的 Class 对象  
>  
> + 对于同步方法块，锁是Synchonized 括号里配置的对象  
> 同步方法块是细粒度的同步，推荐使用这种写法  
> *注意：Java中的synchronized是可重入的，即一个线程得到一个对象锁后再次请求该对象锁，是永远可以拿到锁的*  
> ## **2.volatile**  
> volatile 是轻量级的 synchronized，它在多处理器开发中保证了共享变量的“可见性”  
>  
> 可见性的意思是当一个线程修改一个共享变量时，另外一个线程能读到这个修改的值  
>  
> 一旦一个共享变量（类的成员变量、类的静态成员变量）被 volatile 修饰之后，那么就具备了两层语义：保证了不同线程对这个变量进行操作时的可见性，即一个线程修改了某个变量的值，这新值对其他线程来说是立即可见的。禁止进行指令重排序。如果一个字段被声明成 volatile，Java 线程内存模型确保所有线程看到这个变量的值是一致的  
# Lock锁  
> **synchronized缺陷**  
> + 被synchronized修饰的方法或代码块，只能被一个线程访问。如果这个线程被阻塞，其他线程也只能等待
>  
> + synchronized 不能响应中断  
>  
> + synchronized 没有超时机制  
>  
> + synchronized 只能是非公平锁  
>  
> ![图片](./data/lock.PNG)  
>  
> **Lock接口**  
```
public interface Lock {
    void lock();
    void lockInterruptibly() throws InterruptedException;
    boolean tryLock();
    boolean tryLock(long time, TimeUnit unit) throws InterruptedException;
    void unlock();
    Condition newCondition();
}
```  
> + lock() 方法的作用是获取锁。如果锁已被其他线程获取，则进行等待  
>  
> + tryLock() 方法的作用是尝试获取锁，如果成功，则返回 true；如果失败（即锁已被其他线程获取），则返回 false。也就是说，这个方法无论如何都会立即返回，获取不到锁时不会一直等待  
>  
> + lockInterruptibly() 方法比较特殊，当通过这个方法去获取锁时，如果线程正在等待获取锁，则这个线程能够响应中断，即中断线程的等待状态。也就使说，当两个线程同时通过 lock.lockInterruptibly() 想获取某个锁时，假若此时线程 A 获取到了锁，而线程 B 只有在等待，那么对线程 B 调用 threadB.interrupt() 方法能够中断线程 B 的等待过程。  
>*当一个线程获取了锁之后，是不会被 interrupt() 方法中断的。因为本身在前面的文章中讲过单独调用 interrupt() 方法不能中断正在运行过程中的线程，只能中断阻塞过程中的线程。因此当通过 lockInterruptibly() 方法获取某个锁时，如果不能获取到，只有进行等待的情况下，是可以响应中断的*  
>  
> + unlock() 方法的作用是释放锁  
>  
> ReentrantLock(可重入锁)是唯一实现了 Lock 接口的类  
> **ReadWriteLock 和 ReentrantReadWriteLock**  
> eadWriteLock 允许多个线程同时对其执行读操作，但是只允许一个线程对其执行写操作  
>  
> ReentrantReadWriteLock 实现了 ReadWriteLock 接口，所以它是一个读写锁  
>  
> **ReadWriteLock接口定义**  
```
public interface ReadWriteLock {
    /**
     * 返回用于读操作的锁
     */
    Lock readLock();

    /**
     * 返回用于写操作的锁
     */
    Lock writeLock();
}
```  
> **condition接口**  
> Condition 将 Object 监视器方法（wait、notify 和 notifyAll）分解成截然不同的对象，以便通过将这些对象与任意 Lock 实现组合使用，为每个对象提供多个等待 set（wait-set）。其中，Lock 替代了 synchronized 方法和语句的使用，Condition 替代了 Object 监视器方法的使用  
```
 class BoundedBuffer {
   final Lock lock = new ReentrantLock();
   final Condition notFull  = lock.newCondition(); 
   final Condition notEmpty = lock.newCondition(); 

   final Object[] items = new Object[100];
   int putptr, takeptr, count;

   public void put(Object x) throws InterruptedException {
     lock.lock();
     try {
       while (count == items.length) 
         notFull.await();
       items[putptr] = x; 
       if (++putptr == items.length) putptr = 0;
       ++count;
       notEmpty.signal();
     } finally {
       lock.unlock();
     }
   }

   public Object take() throws InterruptedException {
     lock.lock();
     try {
       while (count == 0) 
         notEmpty.await();
       Object x = items[takeptr]; 
       if (++takeptr == items.length) takeptr = 0;
       --count;
       notFull.signal();
       return x;
     } finally {
       lock.unlock();
     }
   } 
 }
```  
# Semaphore信号量  
> Semaphore用来控制同时访问某个特定资源的操作数量，或者同时执行某个指定操作的数量  
> **原理**：Semaphore管理着一组虚拟的许可（permit），permit的初始数量可通过构造方法来指定。每次执行acquire方法可以获取一个permit，如果没有就等待；而release方法可以释放一个permit  
>  
> + Semaphore 可以用于实现资源池，如数据库连接池  
> + Semaphore 可以用于将任何一种容器变成有界阻塞容器  
> Semaphore构造方法  
```
//初始化固定数量的 permit，并且默认为非公平模式
public Semaphore(int permits) {}
//初始化固定数量的 permit，第二个参数设置是否为公平模式。所谓公平，是指等待久的优先获取许可
public Semaphore(int permits, boolean fair) {}
```  
> Semaphore的重要方法  
```
// 获取 1 个许可
public void acquire() throws InterruptedException {}
//获取 permits 个许可
public void acquire(int permits) throws InterruptedException {}
// 释放 1 个许可
public void release() {}
//释放 permits 个许可
public void release(int permits) {}
```  
> 程序Demo  
```
public class SemaphoreDemo {

    private static final int THREAD_COUNT = 30;

    private static ExecutorService threadPool = Executors.newFixedThreadPool(THREAD_COUNT);

    private static Semaphore s = new Semaphore(10);

    public static void main(String[] args) {
        for (int i = 0; i < THREAD_COUNT; i++) {
            threadPool.execute(() -> {
                try {
                    s.acquire();
                    System.out.println("save data");
                    s.release();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
        }

        threadPool.shutdown();
    }
}
```
# 区别  
>  **Semaphore与Lock的区别**  
> + Semaphore可以同时允许规定数量的线程执行  
> + Lock对于不同的线程是互斥  
> + Semaphore与Lock可以提供公平和不公平锁
>  
> **synchronized与Lock的区别**  
> + synchronized 是Java 语言层面的，是内置的关键字  
> + synchronized 同步的代码块可以由JVM自动释放  
> + lock在性能上较好，支持公平锁和非公平锁  
> + 对于wait()方法，lock提供了condition可以提供更精确的临界区操作  
# Java中的多线程同步手段  
> + 同步方法  
> + 同步代码块  
> + 使用volatile变量  
> + 可重入锁  
> + 尽量使用局部变量  
# 分布式锁  
> *以上所述，只能保证在同一个JVM中的线程安全，在不同JVM（也就是分布式环境中）要保证线程安全，需要使用分布式锁*  
>  
> 在分布式的集群环境中**使用Redis实现分布式锁**  
> Redis分布式锁基本流程：  
> + **1.加锁**  
> 使用setnx命令，其中key是锁的唯一标识（可以按照业务命名），加锁伪代码：setnx(key,1)。当一个线程执行setnx返回1，说明key原本不存在，该线程成功得到了锁；当一个线程执行setnx返回0，说明key已经存在，该线程抢锁失败  
>  
> + **2.解锁**  
> 当得到锁的线程执行完任务，需要释放锁，以便其他线程可以进入，释放锁的最简单方式是执行del命令：del key  
>  
> + **3.锁超时**  
> 如果一个得到锁的线程在执行任务时挂掉，来不及显示释放锁，这块资源将被永远锁住，别的线程再也无法获取锁（Lock，synchronized线程挂掉，锁会自动释放）,所以setnx的key必须设置一个超时时间，以保证即使没有被显示释放，一段时间后自动释放,需要额外命令：expire key 30  
>  
> **重要问题**  
> + 由于setnx与expire是两个命令，必须要保证setnx与expire原子执行，如果setnx命令执行后,线程挂掉，锁会永远无法释放  
>  *Redis2.6.12版本以上增加set(key,1,30,nx取代以上两个命令)*  
>  
> + 锁误删  
> 由于超时时间设置不对，造成锁时间失效，误删除。从而其它线程获取锁，当前线程会删除其他线程的锁（可以通过设置线程ID为key来解决误删其他线程的锁）。同时也可以设置守护线程来为锁续航。增加时间（如果当前线程在锁规定时间内没有结束，则守护线程增加锁的时间）  