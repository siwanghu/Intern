# Java 原子变量类笔记（siwang.hu&nbsp;&nbsp;V1.0）  
## 原子操作硬件的实现原理
> 在intel x86处理器使用基于对缓存加锁或总线加锁的方式来实现多处理器之间的原子操作  
> + 通过总线锁保证原子性，使用处理器提供的一个LOCK＃信号，当一个处理器在总线上输出此信号时，其他处理器的请求将被阻塞住，那么该处理器可以独占共享内存  
>  
> + 通过缓存锁定来保证原子性，在同一时刻，我们只需保证对某个内存地址
的操作是原子性即可，但总线锁定把CPU和内存之间的通信锁住了，这使得锁定期间，其他处理器不能操作其他内存地址的数据，所以总线锁定的开销比较大，目前处理器在某些场合下使用缓存锁定代替总线锁定来进行优化，所谓“缓存锁定”是指内存区域如果被缓存在处理器的缓存行中，并且在Lock操作期间被锁定，那么当它执行锁操作回写到内存时，处理器不在总线上声言LOCK＃信号，而是修改内部的内存地址，并允许它的缓存一致性机制来保证操作的原子性，因为缓存一致性机制会阻止同时修改由两个以上处理器缓存的内存区域数据，当其他处理器回写已被锁定的缓存行的数据时，会使缓存行无效  
>  
## Java CAS
> + 在Java中可以通过锁和循环CAS的方式来实现原子操作  
>  
> + CAS通过调用JNI代码实现。JVM将CAS操作编译为底层提供的最有效方法。在支持CAS的处理器上，JVM 将它们编译为相应的机器指令；在不支持CAS的处理器上，JVM将使用自旋锁  
>  
> + 一般情况下，比锁性能更高。因为 CAS 是一种非阻塞算法，所以其避免了线程被阻塞时的等待时间
## CAS操作  
>  
> ![图片](./data/kk1.PNG)  
> CAS操作属于乐观派，它总认为自己可以成功完成操作，当多个线程同时使用CAS操作一个变量时，只有一个会胜出，并成功更新，其余均会失败，但失败的线程并不会被挂起，仅是被告知失败，并且允许再次尝试，当然也允许失败的线程放弃操作  
## 原子变量类  
> + 原子变量比锁的粒度更细，量级更轻，并且对于在多处理器系统上实现高性能的并发代码来说是非常关键  
>  
> + 原子类在内部使用现代CPU支持的CAS指令(cmpxchg)来实现同步。这些指令通常比锁更快  
>  
> + java.util.concurrent.atomic包下面，采用了CAS机制来实现加锁  
>  
> + atomic不会造成像使用lock、synchronized等线程频繁的上下文切换，造成由于频繁上下文切换带来的资源浪费  
## 原子更新基本类型  
> + AtomicBoolean - 原子更新布尔类型  
> + AtomicInteger - 原子更新整型  
> + AtomicLong - 原子更新长整型  
```
public class AtomicIntegerDemo {

    public static void main(String[] args) throws InterruptedException {
        ExecutorService executorService = Executors.newFixedThreadPool(5);
        AtomicInteger count = new AtomicInteger(0);
        for (int i = 0; i < 1000; i++) {
            executorService.submit((Runnable) () -> {
                System.out.println(Thread.currentThread().getName() + " count=" + count.get());
                count.incrementAndGet();
            });
        }

        executorService.shutdown();
        executorService.awaitTermination(30, TimeUnit.SECONDS);
        System.out.println("Final Count is : " + count.get());
    }
}
```
## 原子更新数组  
> + AtomicIntegerArray - 原子更新整型数组里的元素  
> + AtomicLongArray - 原子更新长整型数组里的元素  
> + AtomicReferenceArray - 原子更新引用类型数组的元素  
> + AtomicBooleanArray - 原子更新布尔类型数组的元素  
```
public class AtomicIntegerArrayDemo {

    private static AtomicIntegerArray atomicIntegerArray = new AtomicIntegerArray(10);

    public static void main(final String[] arguments) throws InterruptedException {

        for (int i = 0; i < atomicIntegerArray.length(); i++) {
            atomicIntegerArray.set(i, i);
        }

        Thread t1 = new Thread(new Increment());
        Thread t2 = new Thread(new Compare());
        t1.start();
        t2.start();

        t1.join();
        t2.join();

        System.out.println("Final Values: ");

        for (int i = 0; i < atomicIntegerArray.length(); i++) {
            System.out.print(atomicIntegerArray.get(i) + " ");
        }
    }

    static class Increment implements Runnable {

        public void run() {

            for (int i = 0; i < atomicIntegerArray.length(); i++) {
                int add = atomicIntegerArray.incrementAndGet(i);
                System.out.println(Thread.currentThread().getName() + ", index " + i + ", value: " + add);

            }
        }
    }

    static class Compare implements Runnable {

        public void run() {

            for (int i = 0; i < atomicIntegerArray.length(); i++) {
                boolean swapped = atomicIntegerArray.compareAndSet(i, 2, 3);

                if (swapped) {
                    System.out.println(Thread.currentThread().getName() + ", index " + i + ", value: 3");
                }
            }
        }
    }
}
```
## 原子更新引用类型
> + AtomicReference - 原子更新引用类型  
> + AtomicReferenceFieldUpdater - 原子更新引用类型里的字段  
> + AtomicMarkableReference - 原子更新带有标记位的引用类型  
```
public class AtomicReferenceDemo {

    private static String message;
    private static Person person;
    private static AtomicReference<String> aRmessage;
    private static AtomicReference<Person> aRperson;

    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(new MyRun1());
        Thread t2 = new Thread(new MyRun2());
        message = "hello";
        person = new Person("Phillip", 23);
        aRmessage = new AtomicReference<String>(message);
        aRperson = new AtomicReference<Person>(person);
        System.out.println("Message is: " + message
            + "\nPerson is " + person.toString());
        System.out.println("Atomic Reference of Message is: " + aRmessage.get()
            + "\nAtomic Reference of Person is " + aRperson.get().toString());
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println("\nNow Message is: " + message
            + "\nPerson is " + person.toString());
        System.out.println("Atomic Reference of Message is: " + aRmessage.get()
            + "\nAtomic Reference of Person is " + aRperson.get().toString());
    }

    static class MyRun1 implements Runnable {

        public void run() {
            aRmessage.compareAndSet(message, "Thread 1");
            message = message.concat("-Thread 1!");
            person.setAge(person.getAge() + 1);
            person.setName("Thread 1");
            aRperson.getAndSet(new Person("Thread 1", 1));
            System.out.println("\n" + Thread.currentThread().getName() + " Values "
                + message + " - " + person.toString());
            System.out.println("\n" + Thread.currentThread().getName() + " Atomic References "
                + message + " - " + person.toString());
        }
    }

    static class MyRun2 implements Runnable {

        public void run() {
            message = message.concat("-Thread 2");
            person.setAge(person.getAge() + 2);
            person.setName("Thread 2");
            aRmessage.lazySet("Thread 2");
            aRperson.set(new Person("Thread 2", 2));
            System.out.println("\n" + Thread.currentThread().getName() + " Values: "
                + message + " - " + person.toString());
            System.out.println("\n" + Thread.currentThread().getName() + " Atomic References: "
                + aRmessage.get() + " - " + aRperson.get().toString());
        }
    }

    static class Person {

        private String name;
        private int age;

        Person(String name, int age) {
            this.name = name;
            this.age = age;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        int getAge() {
            return age;
        }

        void setAge(int age) {
            this.age = age;
        }

        @Override
        public String toString() {
            return "[name " + this.name + ", age " + this.age + "]";
        }
    }
}
```
## 原子更新字段类  
> + AtomicIntegerFieldUpdater - 原子更新整型的字段的更新器  
> + AtomicLongFieldUpdater - 原子更新长整型字段的更新器  
> + AtomicStampedReference - 原子更新带有版本号的引用类型。该类将整型数值与引用关联起来，可用于原子的更新数据和数据的版本号，可以解决使用 CAS 进行原子更新时可能出现的 ABA 问题  
```
public class AtomicStampedReferenceDemo {

    private final static String INIT_REF = "abc";

    public static void main(String[] args) throws InterruptedException {

        AtomicStampedReference<String> asr = new AtomicStampedReference<>(INIT_REF, 0);
        System.out.println("初始对象为：" + asr.getReference());
        final int stamp = asr.getStamp();

        ExecutorService executorService = Executors.newFixedThreadPool(100);
        for (int i = 0; i < 100; i++) {
            executorService.submit(() -> {
                try {
                    Thread.sleep(Math.abs((int) (Math.random() * 100)));
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

                if (asr.compareAndSet(INIT_REF, Thread.currentThread().getName(), stamp, stamp + 1)) {
                    System.out.println(Thread.currentThread().getName() + " 修改了对象！");
                    System.out.println("新的对象为：" + asr.getReference());
                }
            });
        }

        executorService.shutdown();
        executorService.awaitTermination(60, TimeUnit.SECONDS);
    }
}
```
## volatile与Atomic原子操作的区别  
> + volatile关键字是通过本地代码实现的写锁，只保证知有一个线程在写某个数据  
> *JVM为了提高数据存取的速度，允许每个线程在自己独立的数据块，对进程中共享的数据进行私有拷贝。volatile就是保证每次读数据时，读的都是存在共享数据块里的数据，而不是私有拷贝*  
>  
> + volatile仅仅用来保证该变量对所有线程的可见性，但不保证原子性  
> *不能将volatile用在getAndOperate场合（这种场合不原子，需要再加锁），仅仅set或者get的场景是适合volatile的*  
>  
> + Atomic则通过一种线程安全的加减操作接口  
>  
## 性能比较  
> + synchronized：在资源竞争不是很激烈的情况下，偶尔会有同步的情形下，synchronized是合适的,原因在于，编译程序通常会尽可能的进行优化synchronize，另外程序的可读性非常好  
>  
> + ReentrantLock：提供了多样化的同步，比如有时间限制的同步，可以被Interrupt的同步（synchronized的同步是不能Interrupt的）等。在资源竞争不激烈的情形下，性能与synchronized差不多。但是当同步非常激烈的时候，synchronized的性能一下子能下降好几十倍,而ReentrantLock确还能维持常态  
>  
> + Atomic：的性能会优于ReentrantLock一倍左右。但是缺点，就是只能同步一个值，一段代码中只能出现一个Atomic的变量，多于一个同步无效。因为他不能在多个Atomic之间同步。在竞争激烈的时候，同步效果最好  
> 
> + 非常简单的操作且又不想引入锁可以考虑使用 CAS 操作，当想要非阻塞地完成某一操作也可以考虑 CAS。不推荐在复杂操作中引入 CAS，会使程序可读性变差，且难以测试，同时会出现ABA问题  
>  
**ABA问题：** *因为 CAS 需要在操作值的时候检查下值有没有发生变化，如果没有发生变化则更新，但是如果一个值原来是 A，变成了 B，又变成了 A，那么使用 CAS 进行检查时会发现它的值没有发生变化，但是实际上却变化了。ABA 问题的解决思路就是使用版本号。在变量前面追加上版本号，每次变量更新的时候把版本号加一，那么 A－B－A 就会变成 1A-2B－3A。从 Java1.5 开始 JDK 的 atomic 包里提供了一个类 AtomicStampedReference 来解决 ABA 问题。*