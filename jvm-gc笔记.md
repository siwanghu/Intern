# Jvm GC笔记（siwang.hu&nbsp;&nbsp;V1.0）  
> ## 内存申请过程  
> + JVM会试图判别对象大小，如果是大对象则会优先在老年代中分配，如果这个时候老年代的空间不够，则会触发full GC；如果是小对象，则会在Eden空间中分配内存  
>  
> + 当Edge空间足够时，内存申请结束  
>  
> + JVM试图释放在Eden中所有不活跃的对象（触发minor GC），释放后若Eden空间仍然不足以放入新对象，则试图将部分Eden中活跃对象放入Survivor区  
>  
> + Survivor区被用来作为Eden及old的中间交换区域，当old区空间足够时，Survivor区的对象会被移到Old区，否则会被保留在Survivor区  
>  
> + 当old区空间不够时，JVM会触发full GC  
>  
> + 完全垃圾收集后，若Survivor及old区仍然无法存放从Eden复制过来的部分对象，导致JVM无法在Eden区为新对象创建内存区域，则出现Out of memory错误  
>  
> ## 对象衰老过程  
> 新创建的对象的内存分配自eden区。Minor GC的过程就是将eden和survivor中的活对象copy到空闲survivor 空间中。对象在young generation里经历了一定次数(可以通过参数配置)的minor GC后，就会被移到old generation中，称为tenuring  
>  
> ## JVM参数  
> [官方参数说明](https://www.oracle.com/technetwork/java/javase/tech/vmoptions-jsp-140102.html)  
>  
> **-Xms**	初始堆大小  
> ***  
> **-Xmx**	最大堆大小  
> ***  
> **-Xmn**	年轻代大小  
> ***  
> **-XX:NewSize** 设置年轻代初始值  
> ***  
> **-XX:MaxNewSize** 年轻代最大值  
> ***  
> **-XX:PermSize**	设置持久代初始值  
> ***  
> **-XX:MaxPermSize**  设置持久代最大值  
> ***  
> **-Xss** 每个线程的堆栈大小  
> ***  
> **-XX:NewRatio**	年轻代(包括Eden和两个Survivor区)与年老代的比值(除去持久代)  
> ***  
> **-XX:SurvivorRatio**	Eden区与Survivor区的大小比值  
> ***  
> **-XX:+DisableExplicitGC** 关闭System.gc()  
> ***  
> **-XX:MaxTenuringThreshold**	垃圾最大年龄
> *如果设置为0的话,则年轻代对象不经过Survivor区,直接进入年老代. 对于年老代比较多的应用,可以提高效率.如果将此值设置为一个较大值,则年轻代对象会在Survivor区进行多次复制,这样可以增加对象再年轻代的存活 时间,增加在年轻代即被回收的概率该参数只有在串行GC时才有效.*  
> ***  
> **-XX:PretenureSizeThreshold** 对象超过多大是直接在老年代中分配  
> *单位字节 新生代采用Parallel Scavenge GC时无效
另一种直接在旧生代分配的情况是大的数组对象,且数组中无外部引用对象.*   
> ***  
> **-XX:+CollectGen0First**	FullGC时是否先YGC  
> ***  
> **吞吐量throughput（工作时间不算gc的时间占总的时间比）和暂停pause（gc发生时app对外显示的无法响应）**  
> ## 年轻代大小选择  
> + 响应时间优先的应用:尽可能设大,直到接近系统的最低响应时间限制(根据实际情况选择).在此种情况下,年轻代收集发生的频率也是最小的.同时,减少到达年老代的对象  
>  
> + 吞吐量优先的应用:尽可能的设置大,可能到达Gbit的程度.因为对响应时间没有要求,垃圾收集可以并行进行,一般适合8CPU以上的应用  
>  
> + 避免设置过小.当新生代设置过小时会导致:1.YGC次数更加频繁 2.可能导致YGC对象直接进入老年代,如果此时老年代满了,会触发FGC  
>  
> ## 年老代大小选择  
> + 响应时间优先的应用:年老代使用并发收集器,所以其大小需要小心设置,一般要考虑并发会话率和会话持续时间等一些参数.如果堆设置小了,可以会造成内存碎 片,高回收频率以及应用暂停而使用传统的标记清除方式;如果堆大了,则需要较长的收集时间.最优化的方案,一般需要参考以下数据获得:
并发垃圾收集信息、持久代并发收集次数、传统GC信息、花在年轻代和年老代回收上的时间比例  
>  
> + 吞吐量优先的应用:一般吞吐量优先的应用都有一个很大的年轻代和一个较小的年老代.原因是,这样可以尽可能回收掉大部分短期对象,减少中期的对象,而年老代尽存放长期存活对象.  
> 
> ## 大型网站服务器JVM参数配置举例  
> **-server -Xmx3550m -Xms3550m -Xmn1256m -Xss128k -XX:SurvivorRatio=6 -XX:MaxPermSize=256m -XX:ParallelGCThreads=8 -XX:MaxTenuringThreshold=0 -XX:+UseConcMarkSweepGC**  
> + -Xmx 与 -Xms 相同以避免JVM反复重新申请内存。-Xmx 的大小约等于系统内存大小的一半，即充分利用系统资源，又给予系统安全运行的空间  
>  
> + -Xmn1256m 设置年轻代大小为1256MB。此值对系统性能影响较大，Sun官方推荐配置年轻代大小为整个堆的3/8,但要按照实际应用需要配置  
>  
> + -Xss128k 设置较小的线程栈以支持创建更多的线程，支持海量访问，并提升系统性能  
>  
> + -XX:SurvivorRatio=6 设置年轻代中Eden区与Survivor区的比值。系统默认是8，根据经验设置为6，则2个Survivor区与1个Eden区的比值为2:6，一个Survivor区占整个年轻代的1/8  
>  
> + -XX:MaxTenuringThreshold=0 设置垃圾最大年龄（在年轻代的存活次数）。如果设置为0的话，则年轻代对象不经过Survivor区直接进入年老代。对于年老代比较多的应用，可以提高效率；如果将此值设置为一个较大值，则年轻代对象会在Survivor区进行多次复制，这样可以增加对象再年轻代的存活时间，增加在年轻代即被回收的概率。根据被海量访问的动态Web应用之特点，其内存要么被缓存起来以减少直接访问DB，要么被快速回收以支持高并发海量请求，因此其内存对象在年轻代存活多次意义不大，可以直接进入年老代，根据实际应用效果，在这里设置此值为0  
>  
> + -XX:+UseConcMarkSweepGC 设置年老代为并发收集。CMS（ConcMarkSweepGC）收集的目标是尽量减少应用的暂停时间，减少Full GC发生的几率，利用和应用程序线程并发的垃圾回收线程来标记清除年老代内存，适用于应用中存在比较多的长生命周期对象的情况  
>  
> ## 命令  
> 查看heap使用情况与jvm采用的gc策略  
> **jmap -heap pid**  
> ***  
> 查看jvm程序工具  
> jconsole或jvisualvm或jprofiler  
> 
