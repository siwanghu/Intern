# Java容器（siwang.hu&nbsp;&nbsp;V1.0）  
> Java中常用的存储容器是数组与容器，但是两者有重大区别：  
> + 数组是固定长度的，容器的长度是可变的  
> + 数组可以存储基本数据类型，以可以存储引用数据类型；容器只能存储引用数据类型，基本数据类型要转成对应的包装类中才能放入容器  
>  ## **1.容器框架**
> ![图片](./data/container-structure.png)  
>  
> Java 容器框架主要分为Collection和Map两种。其中，Collection又分为List、Set 、Queue    
> + Collection：一个独立元素的序列，这些元素都服从一条或者多条规则  
> + List：必须按照插入的顺序保存元素  
> + Set：不能有重复的元素  
> + Map：一组成对的“键值对”对象，允许你使用键来查找值  
# 同步容器  
> + Vector 实现了 List 接口，Vector 实际上就是一个数组，和 ArrayList 类似，但是 Vector 中的方法都是 synchronized 方法，即进行了同步措施  
>  
> + Stack 也是一个同步容器，它的方法也用 synchronized 进行了同步，它实际上是继承于 Vector 类  
>  
> + HashTable 实现了 Map 接口，它和 HashMap 很相似，但是 HashTable 进行了同步处理，而 HashMap 没有  
>  
> + Collections 类中提供的静态工厂方法创建的类（由 Collections.synchronizedXxxx 等方法）  
>  
> + 字符串处理上，StringBuilder是线程不安全的，而StringBuffer是线程安全的  
> *未必。同步容器未必真的安全。在做复合操作时，仍然需要加锁来保护*  
# 并发容器  
> JDK 的 java.util.concurrent 包（即 juc）中提供了几个非常有用的并发容器  
> + CopyOnWriteArrayList - 线程安全的 ArrayList  
> + CopyOnWriteArraySet - 线程安全的 Set，它内部包含了一个 CopyOnWriteArrayList，因此本质上是由 CopyOnWriteArrayList 实现的  
> + ConcurrentSkipListSet - 相当于线程安全的 TreeSet。它是有序的 Set。它由 ConcurrentSkipListMap 实现  
> + ConcurrentHashMap - 线程安全的 HashMap。采用分段锁实现高效并发  
> + ConcurrentSkipListMap - 线程安全的有序 Map。使用跳表实现高效并发  
> + ConcurrentLinkedQueue - 线程安全的无界队列。底层采用单链表。支持 FIFO  
> + ConcurrentLinkedDeque - 线程安全的无界双端队列。底层采用双向链表。支持 FIFO 和 FILO  
> + ArrayBlockingQueue - 数组实现的阻塞队列  
> + LinkedBlockingQueue - 链表实现的阻塞队列  
> + LinkedBlockingDeque - 双向链表实现的双端阻塞队列  