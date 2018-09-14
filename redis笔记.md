# Redis远程字典服务器  
> 启动Redis&nbsp;&nbsp;**redis-server.exe redis.windows.conf**  
> 远程登陆&nbsp;&nbsp; **redis-cli.exe -h 127.0.0.1 -p 6379**  
> 停止Redis&nbsp;&nbsp; **redis-cli.exe shutdown**  
> Redis默认启动端口为:&nbsp;&nbsp;**6379**  
# 数据类型  
> + 字符串  
> + 散列(哈希表)  
> + 列表  
> + 集合  
> + 有序集合  
# 有关键的命令  
> **keys pattern**  
> 获得符合pattern规则的键名列表  
> + ?&nbsp;&nbsp;匹配一个字符  
> + *&nbsp;&nbsp;匹配任意个字符  
> + [&nbsp;]&nbsp;&nbsp;匹配括号间的任一个字符  
> + \x&nbsp;&nbsp;匹配转义字符  
> ***  
> **exists key**  
> 判断一个键名是否存在  
> *exists name&nbsp;&nbsp;判断键name是否存在*  
> ***  
> **del key**  
> 删除名字为key的键  
> ***  
> **type key**  
> 查看key键的数据类型  
# 字符串类型命令  
> string是Redis最基本的数据类型，可以存储任何形式的字符串，包括二进制数据，例如：图片，序列化的对象  
> ***  
> **set key value**  
> 设置键为key值为value  
> ***  
> **get key**  
> 获取键为key的value  
> ***  
> **incr key**  
> 如果key对应的值是整数形式,其value加1  
> ***  
> **incrby key increment**  
> key对应的值是整数形式,其value加increment数  
> ***  
> **decr key**  
> **decr key increment**  
> 与incr功能相反  
> ***  
> **incrbyfloat key increment**  
> 增加指定的increment浮点数  
> ***  
> **append key value**  
> 向key对应的字符串连接上value  
> ***  
> **strlen key**  
> 获取key的value值字符串的长度  
> ***  
> **mget key1 key2 key3**  
> **mset key1 v1 key2 v2**  
> 同时设置多个键值对  
# 位操作命令  
> **getbit key offset**  
> 获取key对应的value值的二进制位的第offset位  
> ***  
> **setbit key offset value**  
> 将key对应的value值的二进制位的第offset位值为value(0|1)  
> ***  
> **bitcount key**  
> 获取key对应的value值的二进制中1的个数  
> **bitcount key begOffset endOffset**  
> ***  
> **bitop or|and|xor|not**  
> bitop提供位运算  
> bitop or res key1 key2  
> ***  
# 散列类型命令  
> Redis中散列类型适合存储对象。一般我们使用对象类型与ID构成键名object:id  
> ***  
> **hset key field value**  
> 存储键名位key属性为field值为value  
> hset ustc:sa17225123 name siwanghu  
> ***  
> **hget key field**  
> 获取对象键名为key属性field的值  
> hget ustc:sa17225123 name  
> ***  
> **hmset key field1 value1 field2 value2**  
> **hmget key field1 field2**  
> 同时设置与获取对象键名为key的多个属性  
> ***  
> **hgetall key**  
> 获取对象键名为key的所有属性与属性对应的值  
> hgetall ustc:sa17225123  
> ***  
> **hexists key field**  
> 判断对象键名为key的属性field是否存在  
> ***  
> **hsetnx key field value**  
> 给对象键名为key赋值属性field对应值为value，如果属性不存在；如果属性field存在，则什么也不做  
> ***  
> **hincrby key field increment**  
> 属性field增加数字值increment  
> ***  
> **hdel key field**    
> 删除对象键名为key的属性field  
# 列表类型命令  
> **lpush key value**  
> 向列表key左边增加元素value  
> **rpush key value**  
> 向列表key右边增加元素value  
> ***  
> **lpop key**  
> 从列表key左边弹出元素  
> **rpop key**  
> 从列表key右边弹出元素  
> ***  
> **llen key**  
> 统计列表key的元素个数  
> ***  
> **lrange key start stop**  
> 类似python切片操作，区别是返回包含start与stop为的元素  
> ***  
> **lrem key count value**  
> 从列表key中前count个数中删除值为value的元素  
> count可以指定为正数或负数，区别是正数从左开始，负数从右开始  
# 集合类型命令  
> **sadd key member**  
> 向集合key中添加member元素  
> ***  
> **smembers key**  
> 获取集合key中的所有元素
> ***  
> **sismember key member**  
> 判断集合key中是否存在member元素  
> ***  
> **sdiff key1 key2 key3**  
> 求集合差集key1-key2-key3  
> **sinter key1 key2 key3**  
> 求集合交集  
> **sunion key1 key2 key3**  
> 求集合并集  
# 有序集合类型命令  
> 一个元素与该元素的分数  
> 如果元素的分数相同，Redis会按照元素字典顺序排序
> **zadd key score member**  
> 向有序集合key中增加成员member分数是score  
> ***  
> **zscore key member**  
> 获取有序集合key成员member的分数  
> *** 
> **zrange key start stop**  
> 获取有序集合key中排名在start与stop的元素,有序集合按照分数排名  
> **zrange key start stop withscore**  
> ***  
> **zrangescore key min max**  
>  获取有序集合key中指定分数范围的元素使用(表示不包含，+inf表示无上限
> ***  
> **zincrby key increment member**  
> 增加某个元素的分数  
> ***  
> **zrevrangescore key stop start limit offsetr count**  
> **zrangescore key stop start limit offsetr count**  
> 两者命令效果相反
> ***  
# 事务  
> ## 命令规范  
> **multi**&nbsp;&nbsp;开启事务  
> **.......**&nbsp;&nbsp;发送Redis命令  
> **exec**&nbsp;&nbsp;执行事务  
> ## 错误处理  
> + 如果是命令语法错误，执行exec命令后，Redis会直接返回错误，事务中的一条命令都不执行  
> + 如果是运行时错误，Redis会忽略运行时错误的命令，其余无错误的命令都会执行  
> + Redis不支持事务回滚功能  
# watch命令  
> watch命令可以监控一个或多个键，一旦其中一个键在事务开始之前被修改，那么之后的事务就不会执行  
# 过期时间命令  
> **expire key seconds**  
> 期望key键在seconds秒后过期(无效)  
> ***  
> **ttl key**  
> 查看key键还有多少时间过期  
> ***  
> **persist key**  
> 取消key键的过期时间，注意set命令为键赋值也会清除键的过期时间  
# 排序命令  
> **sort key**  
> 对列表类型，集合类型和有序集合进行排序  
> 默认是从小到大排序,sort key desc可以从大到小  
> **sort tag:ruby:posts by post:*->time desc**  
> 根据对象post:*(通配符)的time属性以降序的方式对列表tag:ruby:posts排序  
> + >lpush sortlist 1 2 3  
> + >set item:1 50  
> + >set item:2 100  
> + >set item:3 -10  
> + > sort sortlist by item:* desc  
> + >结果&nbsp;&nbsp;2 &nbsp;&nbsp;1&nbsp;&nbsp; 3  
> sort tag:ruby:posts by post:*->time desc get post:*->title get post:*->time  
> 根据对象post:*(通配符)的time属性以降序的方式对列表tag:ruby:posts排序,同时获取post:*->title和post:*->time  
# 任务队列  
> + 松耦合  
> + 易于扩展  
> 使用Redis中队列类型,生产者lpush任务进入队列，消费者rpop获取任务  
> **一般消费者使用brpop；brpop当列表中没有元素时会一直阻塞住连接，直到有新元素加入**  
# 优先级队列  
> brpop命令可以接受多个键；同时检测多个键，如果所有键都没有元素则阻塞，如果其中有一个键有元素则会从该键中弹出元素，按照从左到右的顺序取键中的元素  
> + **brpop queue:one queue:two**  
> + 一旦queue:one键中有元素就会去除queue:one键中的元素，直到queue:one键中无元素才会到queue:two键中取元素，也就是说queue:one的优先级比queue:two高  
# 发布/订阅模式  
> Redis提供一组命令可以实现发布/订阅模式  
> **publish channel.1 hi**  
> 向频道channel.1发送hi  
> **subscribe channel.1**  
> 订阅频道channel.1  
> psubscribe命令可以支持正则表达式的形式订阅指定的频道  
# 管道  
>  &nbsp; &nbsp; &nbsp; &nbsp;客户端与Redis使用TCP协议连接的，每次执行多个命令时每条命令必须要等待上一条命令执行完才能执行，这样会造成客户端与Redis执行多条命令时有累加时延。  
> ***  
> &nbsp; &nbsp; &nbsp; &nbsp;为了解决这个问题可以采用管道,一次性发送多条命令并执行完后一次性将每条命令的执行结果返回。  
> &nbsp; &nbsp; &nbsp; &nbsp;管道通过减少客户端与Redis的通信次数来实现降低往返时延累计值的目的。  
# 持久化  
> + 将Redis作为数据库使用时，Redis存放在内存中的数据需要持久化到内存，下次Redis重启时还能保证数据有效  
>  
>+ 将Redis作为缓存服务器使用时，如果缓存被穿透，所有的缓存同时会失效。导致服务无法响应，这是需要将数据持久化到硬盘  
> ***  
> ## 缓存设计常见问题  
> + 缓存穿透   
> **缓存穿透是指查询一个一定不存在的数据，由于缓存是不命中时被动写的，并且出于容错考虑，如果从存储层查不到数据则不写入缓存，这将导致这个不存在的数据每次请求都要到存储层去查询，失去了缓存的意义。在流量大时，可能DB就挂掉了，要是有人利用不存在的key频繁攻击我们的应用，这就是漏洞。**  
>  
> 解决: **采用布隆过滤器，将所有可能存在的数据哈希到一个足够大的bitmap中，一个一定不存在的数据会被 这个bitmap拦截掉，从而避免了对底层存储系统的查询压力。另外也有一个更为简单粗暴的方法（我们采用的就是这种），如果一个查询返回的数据为空（不管是数 据不存在，还是系统故障），我们仍然把这个空结果进行缓存，但它的过期时间会很短，最长不超过五分钟。**  
> ***  
> + 缓存雪崩  
> **缓存雪崩是指在我们设置缓存时采用了相同的过期时间，导致缓存在某一时刻同时失效，请求全部转发到DB，DB瞬时压力过重雪崩。**  
> 解决:**缓存失效时的雪崩效应对底层系统的冲击非常可怕。大多数系统设计者考虑用加锁或者队列的方式保证缓存的单线 程（进程）写，从而避免失效时大量的并发请求落到底层存储系统上。这里分享一个简单方案就时讲缓存失效时间分散开，比如我们可以在原有的失效时间基础上增加一个随机值，比如1-5分钟随机，这样每一个缓存的过期时间的重复率就会降低，就很难引发集体失效的事件。**  
> ***  
> + 缓存击穿  
> **对于一些设置了过期时间的key，如果这些key可能会在某些时间点被超高并发地访问，是一种非常“热点”的数据。这个时候，需要考虑一个问题：缓存被“击穿”的问题，这个和缓存雪崩的区别在于这里针对某一key缓存，前者则是很多key。缓存在某个时间点过期的时候，恰好在这个时间点对这个Key有大量的并发请求过来，这些请求发现缓存过期一般都会从后端DB加载数据并回设到缓存，这个时候大并发的请求可能会瞬间把后端DB压垮。**  
> 解决:**1.使用互斥锁(mutex key)2."提前"使用互斥锁(mutex key)：3. "永远不过期"：**  
> ***    
> Redis持久化方式  
> + RDB方式(快照)  
> **1.根据用户配置2.执行save或bgsave3.执行flushall命令**
> + AOF方式  
> **AOF将Redis执行写命令时将数据写入硬盘，会明显降低Redis性能**  
# Redis集群  
> ## 复制  
> Redis提供复制功能，可以实现当一台数据库中的数据更新后，自动将更新的数据同步到其他数据库上  
> ## Redis配置  
> 从数据库Redis配置文件中加入slaveof 主数据库地址 主数据库端口;主数据库无需配置  
> ## 哨兵  
> + 监控主数据库和从数据库是否正常运行  
>  
> + 主数据库出项故障时自动将从数据库转为主数据库  
>   
> + 哨兵是一个独立的进程  
> ## 集群  
> 使用集群只需要将每个数据库节点的cluster-enabled配置项打开即可  