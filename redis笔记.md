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
#集合类型命令  
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
>