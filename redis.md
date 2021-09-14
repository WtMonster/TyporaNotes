### Redis的安装

**默认端口号6379**

**安装gcc编译redis：**yum install gcc

**开放端口**

firewall-cmd --zone=public --add-port=6379/tcp --permanent

重启防火墙

firewall-cmd --reload

查看设置是否生效

firewall-cmd --zone=public --query-port=6379/tcp

查看系统所有开放的端口

firewall-cmd --zone=public --list-ports


#### **目录**

**默认安装路径为**/usr/local/bin

**默认安装目录：**

**redis-benchmark：**性能测试工具，可以在自己电脑上运行，看电脑性能如何

**redis-check-aof：**修复有问题的AOF文件

**redis-sentinel：**redis集群使用

**redis-server：**redis服务器启动命令

**redis-cli：**客户端，操作入口



### Redis启动

**前台启动：**redis-server

**后台启动：**

先修改redis.conf文件中daemonize为yes（守护进程）

redis-server /etc/redis.conf

**单实例关闭：**redis-cli shutdown

**进入终端后关闭：**shutdown

**多实例关闭，指定端口关闭：**redis-cli -p 6379 shutdown



### **相关知识**

redis：单线程+多路IO复用

redis默认使用16个数据库，类似数组下标，初始默认使用0号库

**连接到数据库：**redis-cli

**切换库：**select 1

**查看当前数据库key的数量：**dbsize

**清空当前库：**flushdb

**通杀全部库：**flushall



### 数据类型

#### key键

**常见操作**

**set <key><value>** 添加键值对

**keys ***查看当前库所有key（匹配：keys\*1）

**exists key** 判断某个key是否存在

**type key**查看key的类型

**del key** 删除指定的key数据

**unlike key** 根据value选择非阻塞删除

仅将keys从keyspace元数据中删除，真正的删除会在后续异步操作

**expire key 10 **为给定的key设置过期时间（10秒钟）

**ttl key** 查看还有多少秒过期，-1表示永不过期，-2表示已经过期

select 命令切换数据库

**dbsize** 查看当前数据库的key数量



#### **String**

**介绍**

String类型是二进制安全的，所以可以包含任何数，包括jpg图片或者序列化对象

String类型是redis最基本的数据类型，一个redis中字符串value最多可以是512M

数据结构为简单动态字符串，是可以修改的字符串，与java中的ArrayList类似，采取预分配冗余空间的方式来减少内存的频繁分配

**常见操作**

**set <key><value>** 添加键值对

***NX：**当数据库中key不存在时，可以将key-value添加数据库

***XX：**当数据库中key存在时，可以将key-value添加数据库，与NX参数互斥

***EX：**key的超时秒数

***PX：**key的超时毫秒数，与EX互斥

**get** <key> 查询对应键值

**append** <key><value> 将给定字符串追加到原字符串末尾，并输出追加后字符串长度

**strlen** <key>获得值的长度

**setnx** <key><value>只有在key不存在时 设置key的值

**incr** <key> 将key中储存的数字增1 

**decr** <key> 将key中储存的数字减1 

**incrby/decrby** <key><步长> 将key中储存的数字按步长增减



**以下操作具有原子性**

同时设置一个或多个键值对

 **mset**  <key1><value1><key2><value2>......<keyn><valuen>

同时获取一个或多个value

**mget** <key><key>......<key>

同时设置一个或多个键值对，当且仅当所有key都不存在

**msetnx** <key1><value1><key2><value2>......<keyn><valuen>



**getrange** <key><起始位置><>结束位置>

获得指定范围的值

**setrange** <key><起始位置><value>

用<value>覆写<key>所储存的字符串值，从起始位置开始



**setex** <key><过期时间><value>

设置键值的同时，设置过期时间，单位秒

**getset** <key><value>

设置新值同时获得旧值



#### List

**介绍**

Redis中List是字符串有序列表，按照插入顺序排序。你可以添加任何一个元素到列表的头部或者尾部

底层是双向链表，两端操作性能高，索引下表操作性能很差

List的数据结构为快速链表quickList。

首先在列表元素较少的情况下会使用一块连续的内存存储，这个结构是ziplist，也即是压缩列表。

它将所有的元素紧挨着一起存储，分配的是一块连续的内存。

当数据量比较多的时候才会改成quicklist。

因为普通的链表需要的附加指针空间太大，会比较浪费空间。比如这个列表里存的只是int类型的数据，结构上还需要两个额外的指针prev和next。                              

Redis将链表和ziplist结合起来组成了quicklist。也就是将多个ziplist使用双向指针串起来使用。这样既满足了快速的插入删除性能，又不会出现太大的空间冗余。

**常见操作**

**lpush/rpush** <key><value1><value2>......<valuen>

从左边/右边插入一个或多个值

**lpop/rpop** <key>

从列表左边/右边吐出一个值

**rpoplpush** <key><start><stop>

从<key1>列表右边吐出一个值，插到<key2>列表左边

**lrange** <key><start><stop>

按照索引下标获得元素(从左到右，0 -1表示取出所有)

**lindex** <key><index>

按照索引下标获得元素

**llen** <key>

获得列表长度



**linsert** <key> before <value><newvalue>在<value>的后面插入<newvalue>插入值

**lrem** <key><n><value> 从左边删除n个<value>

**lset** <key><index><value>将列表key下标为index的值替换成value



#### Set

**介绍**

Redis的Set是String类型的无序集合，自动排重

它底层是一个value为null的hash表，所以添加、删除、查找的复杂度都是O（1）

**常见操作**

**sadd** <key><value1><value2> ..... 

将一个或多个 member 元素加入到集合 key 中，已经存在的 member 元素将被忽略

**smembers** <key>取出该集合的所有值。

**sismember** <key><value>判断集合<key>是否为含有该<value>值，有1，没有0

**scard**<key>返回该集合的元素个数。

**srem** <key><value1><value2> .... 删除集合中的某个元素。

**spop** <key>随机从该集合中吐出一个值。

**srandmember** <key><n>随机从该集合中取出n个值。不会从集合中删除 。

**smove** <source><destination>value把集合中一个值从一个集合移动到另一个集合

**sinter** <key1><key2>返回两个集合的交集元素。

**sunion** <key1><key2>返回两个集合的并集元素。

**sdiff** <key1><key2>返回两个集合的差集元素(key1中的，不包含key2中的)



#### Hash

**介绍**

redis的hash是一个string类型的field和value的映射表，hash特别适合用于存储对象，类似java中的Map类 

Hash类型对应的数据结构是两种：ziplist（压缩列表），hashtable（哈希表）。当field-value长度较短且个数较少时，使用ziplist，否则使用hashtable

<key> <field><value><field><value>......<field><value>

**常用操作**

**hset** <key><field><value>给<key>集合中的 <field>键赋值<value>

**hget** <key1><field>从<key1>集合<field>取出 value 

**hmset** <key1><field1><value1><field2><value2>... 批量设置hash的值

**hexists**<key1><field>查看哈希表 key 中，给定域 field 是否存在。 

**hkeys** <key>列出该hash集合的所有field

**hvals** <key>列出该hash集合的所有value

**hincrby** <key><field><increment>为哈希表 key 中的域 field 的值加上增量 1  -1

**hsetnx** <key><field><value>将哈希表 key 中的域 field 的值设置为 value ，当且仅当域 field 不存在 



**Zset(有序集合)**

**介绍**

Zset每个成员都关联了一个评分（score），被用来按照从最低分到最高分的方式排序集合中的成员，集合的成员是唯一的，但评分可以重复。

由于元素的有序性，可以根据评分或者次序快速查找元素

Zset底层使用了两个数据结构

(1)hash，hash的作业就是关联元素和权重，保障元素的唯一性，可以通过元素找到相应score值

(2)跳跃表，跳跃表的目的在于给元素value排序，根据score的范围获取元素列表

**常用操作**

**zadd** <key><score1><value1><score2><value2>…

将一个或多个 member 元素及其 score 值加入到有序集 key 当中。

**zrange** <key><start><stop> [WITHSCORES]  

返回有序集 key 中，下标在<start><stop>之间的元素

带WITHSCORES，可以让分数一起和值返回到结果集。

**zrangebyscore key minmax** [withscores] [limit offset count]

返回有序集 key 中，所有 score 值介于 min 和 max 之间(包括等于 min 或 max )的成员。有序集成员按 score 值递增(从小到大)次序排列。 

**zrevrangebyscore key maxmin** [withscores] [limit offset count]        

同上，改为从大到小排列， [limit offset count] 是分页，offset设置起始位置，count设置结果数量。 

**zincrby** <key><increment><value>   为元素的score加上增量

**zrem** <key><value>删除该集合下，指定值的元素

**zcount** <key><min><max>统计该集合，分数区间内的元素个数 

**zrank** <key><value>返回该值在集合中的排名，从0开始。



### **Redis配置文件redis.conf**

redis只支持bytes，不支持bit，大小写不敏感

**INCLUDES部分**

类似jsp中的include，多实例的情况可以把公用的配置文件提取出来

**bind 127.0.0.1**

默认只能通过本机访问，注释掉即可外部连接

**protected-mode yes**

表示开启保护模式，只能本机访问，改成no

**port 6379**

默认端口号

**tcp-backlog**

设置tcp的backlog，backlog其实是一个连接队列，backlog队列总和=未完成三次握手队列 + 已经完成三次握手队列。

在高并发环境下你需要一个高backlog值来避免慢客户端连接问题。

注意Linux内核会将这个值减小到/proc/sys/net/core/somaxconn的值（128），所以需要确认增大/proc/sys/net/core/somaxconn和/proc/sys/net/ipv4/tcp_max_syn_backlog（128）两个值来达到想要的效果

**timeout 0**

以秒为单位，0表示永不超时

 **tcp-keepalive 300**

300秒检测一次连接决定是否释放

**loglevel notice**

日志级别

**logfile **

设置日志文件输出路径，默认为空

**database 16**

数据库数量，默认16个

**maxclients**

redis同时连接客户端的最大数量，默认为10000

**maxmemory**

设置redis可以使用的内存量，一单达到内存使用上限，redis将会试图移除内部数据，移除规则可以通过maxmemory-policy来指定

建议一定要设置，否则将内存沾满会造成服务器宕机

如果redis无法根据移除规则来移除内存中的数据，或者设置了“不允许移除”，那么redis则会针对那些需要申请内存的指令返回错误信息，比如SET、LPUSH等。

但是对于无内存申请的指令，仍然会正常响应，比如GET等。如果你的redis是主redis（说明你的redis有从redis），那么在设置内存使用上限时，需要在系统中留出一些内存空间给同步队列缓存，只有在你设置的是“不移除”的情况下，才不用考虑这个因素。

**maxmemory-policy**

volatile-lru：使用LRU算法移除key，只对设置了过期时间的键；（最近最少使用）

allkeys-lru：在所有集合key中，使用LRU算法移除key

volatile-random：在过期集合中移除随机的key，只对设置了过期时间的键

allkeys-random：在所有集合key中，移除随机的key

volatile-ttl：移除那些TTL值最小的key，即那些最近要过期的key

noeviction：不进行移除。针对写操作，只是返回错误信息

**maxmemory-samples**

设置样本数量，LRU算法和最小TTL算法都并非是精确的算法，而是估算值，所以你可以设置样本的大小，redis默认会检查这么多个key并选择其中LRU的那个。

一般设置3到7的数字，数值越小样本越不准确，但性能消耗越小



**发布和订阅**

1、 打开一个客户端订阅channel1

**SUBSCRIBE channel1**                               

2、打开另一个客户端，给channel1发布消息hello

**publish channel1 hello**

返回的1是订阅者数量

3、打开第一个客户端可以看到发送的消息

注：发布的消息没有持久化，如果在订阅的客户端收不到hello，只能收到订阅后发布的消息



### **新数据类型**

#### **Bitmaps**

**介绍**

一个以位为单元的数组表示字符串，下标被称为偏移量

**常见操作**

**setbit **<key><offset><value>设置Bitmaps中某个偏移量的值(0或1)

在第一次初始化Bitmaps时， 假如偏移量非常大， 那么整个初始化过程执行会比较慢， 可能会造成Redis的阻塞。

**getbit** <key><offset>获取Bitmaps中某个偏移量的值

**bitcount**<key>[start end] 统计字符串从start字节到end字节比特值为1的数量，注意！单位是字节

start 和 end 参数的设置，都可以使用负数值：比如 -1 表示最后一个位，而 -2 表示倒数第二个位，start、end 是指bit组的字节的下标数，二者皆包含。

**bitop and(or/not/xor) <destkey> [key…]**       

已有bit1和bit2 

例：**bitop and bit3 bit2 bit1**                        

bitop是个复合操作， 它可以做多个Bitmaps的and（交集） 、 or（并集） 、 not（非） 、 xor（异或） 操作并将结果保存在destkey中。



#### **HyperLogLog**

**介绍**

快速计算基数的数据类型（近似）

**常用操作**

**pfadd <key>< element> [element ...]**  添加指定元素到 HyperLogLog 中

**pfcount<key> [key ...]** 计算HLL的近似基数

**pfmerge<destkey><sourcekey> [sourcekey ...]** 将一个或多个HLL合并后的结果存储在另一个HLL中



#### **Geospatial**

geographic地理信息数据类型，是元素的二维坐标，对应地图上经纬度，提供了查询，范围查询，距离查询，经纬度Hash等常见操作

**geoadd<key>< longitude><latitude><member> [longitude latitude member...]**  添加地理位置（经度，纬度，名称）

两极无法直接添加，一般会下载城市数据，直接通过 Java 程序一次性导入。

有效的经度从 -180 度到 180 度。有效的纬度从 -85.05112878 度到 85.05112878 度。

当坐标位置超出指定范围时，该命令将会返回一个错误。

已经添加的数据，是无法再次往里面添加的。

**geopos <key><member> [member...]** 获得指定地区的坐标值

**geodist<key><member1><member2> [m|km|ft|mi ]** 获取两个位置之间的直线距离

**georadius<key>< longitude><latitude>radius m|km|ft|mi**  以给定的经纬度为中心，找出某一半径内的元素



### **通过Jedis操作Redis**

**Jedis所需要的jar包**

```java
<dependency>
<groupId>redis.clients</groupId>
<artifactId>jedis</artifactId>
<version>3.2.0</version>
</dependency>
```

**创建测试程序**

```java
package com.atguigu.jedis;
import redis.clients.jedis.Jedis;
public class Demo01 {
public static void main(String[] args) {
Jedis jedis = new Jedis("192.168.137.133",6379);
String pong = jedis.ping();
System.out.println("连接成功："+pong);
jedis.close();
}
}
```

**Jedis-API:    Key**

```java
jedis.set("k1", "v1");
jedis.set("k2", "v2");
jedis.set("k3", "v3");
Set<String> keys = jedis.keys("*");
System.out.println(keys.size());
for (String key : keys) {
System.out.println(key);
}
System.out.println(jedis.exists("k1"));
System.out.println(jedis.ttl("k1"));                
System.out.println(jedis.get("k1"));
```

**Jedis-API:    String**

```java
jedis.mset("str1","v1","str2","v2","str3","v3");
System.out.println(jedis.mget("str1","str2","str3"));
```

**Jedis-API:    List**

```java
List<String> list = jedis.lrange("mylist",0,-1);
for (String element : list) {
System.out.println(element);
}
```

**Jedis-API:    set**

```java
jedis.sadd("orders", "order01");
jedis.sadd("orders", "order02");
jedis.sadd("orders", "order03");
jedis.sadd("orders", "order04");
Set<String> smembers = jedis.smembers("orders");
for (String order : smembers) {
System.out.println(order);
}
jedis.srem("orders", "order02");
```

**Jedis-API:    hash**

```java
jedis.hset("hash1","userName","lisi");
System.out.println(jedis.hget("hash1","userName"));
Map<String,String> map = new HashMap<String,String>();
map.put("telphone","13810169999");
map.put("address","atguigu");
map.put("email","abc@163.com");
jedis.hmset("hash2",map);
List<String> result = jedis.hmget("hash2", "telphone","email");
for (String element : result) {
System.out.println(element);
}
```

**Jedis-API:    zset**

```java
jedis.zadd("zset01", 100d, "z3");
jedis.zadd("zset01", 90d, "l4");
jedis.zadd("zset01", 80d, "w5");
jedis.zadd("zset01", 70d, "z6");
 
Set<String> zrange = jedis.zrange("zset01", 0, -1);
for (String e : zrange) {
System.out.println(e);
}
```

#### **Jedis工具类（连接池）**

```java
public class JedisPoolUtil {
	private static volatile JedisPool jedisPool = null;

	private JedisPoolUtil() {
	}

	public static JedisPool getJedisPoolInstance() {
		if (null == jedisPool) {
			synchronized (JedisPoolUtil.class) {
				if (null == jedisPool) {
					JedisPoolConfig poolConfig = new JedisPoolConfig();
					poolConfig.setMaxTotal(200);
					poolConfig.setMaxIdle(32);
					poolConfig.setMaxWaitMillis(100*1000);
					poolConfig.setBlockWhenExhausted(true);
					poolConfig.setTestOnBorrow(true);  // ping  PONG
				 
					jedisPool = new JedisPool(poolConfig, "192.168.44.168", 6379, 60000 );
				}
			}
		}
		return jedisPool;
	}

	public static void release(JedisPool jedisPool, Jedis jedis) {
		if (null != jedis) {
			jedisPool.returnResource(jedis);
		}
	}

}
```



### Redis整合SpringBoot

#### 依赖

```xml
		<!--redis-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
		<!--springboot2.x集成redis所需-->
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-pool2</artifactId>
            <version>2.6.0</version>
        </dependency>
```



#### **properties文件配置**

```properties
#Redis服务器地址
spring.redis.host = 192.168.137.133
#Redis服务器端口号
spring.redis.port = 6379
#Redis数据库索引
spring.redis.database = 0
#连接超时时间(毫秒)
spring.redis.timeout = 1800000
#连接池最大连接数(负数表示没有限制)
spring.redis.lettuce.pool.max-active =20
#最大阻塞等待时间(负数表示没有限制)
spring.redis.lettuce.pool.max-wait = -1
#连接池中的最大空闲连接
spring.redis.lettuce.pool.max-idle = 5
#连接池中的最小空闲连接
spring.redis.lettuce.pool.min-idle = 0
```

#### **Redis配置类**

```java
@EnableCaching
@Configuration
public class RedisConfig extends CachingConfigurerSupport {

    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        RedisSerializer<String> redisSerializer = new StringRedisSerializer();
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
        template.setConnectionFactory(factory);
//key序列化方式
        template.setKeySerializer(redisSerializer);
//value序列化
        template.setValueSerializer(jackson2JsonRedisSerializer);
//value hashmap序列化
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        return template;
    }

    @Bean
    public CacheManager cacheManager(RedisConnectionFactory factory) {
        RedisSerializer<String> redisSerializer = new StringRedisSerializer();
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
//解决查询缓存转换异常的问题
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
// 配置序列化（解决乱码的问题）,过期时间600秒
        RedisCacheConfiguration config = RedisCacheConfiguration.defaultCacheConfig()
                .entryTtl(Duration.ofSeconds(600))
                .serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(redisSerializer))
                .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(jackson2JsonRedisSerializer))
                .disableCachingNullValues();
        RedisCacheManager cacheManager = RedisCacheManager.builder(factory)
                .cacheDefaults(config)
                .build();
        return cacheManager;
    }
}

```



### 事务

Redis事务是一个单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。

Redis事务的主要作用就是串联多个命令防止别的命令插队。

#### **Multi、Exec和discard**

从输入Multi命令开始，输入的命令都会依次进入命令队列中，但不会执行，直到输入Exec后，Redis会将之前的命令队列中的命令依次执行。

组队的过程中可以通过discard来放弃组队。 

![image-20210701224355948](C:\Users\enslaver\AppData\Roaming\Typora\typora-user-images\image-20210701224355948.png)





#### **错误处理**

**组队中出现错误则停止组队**

**执行过程中出现错误则只有错误的不执行**



#### **锁**

##### **悲观锁**

每次拿数据都先上锁，传统数据库中用到了很多悲观锁机制，比如行锁表锁，读锁写锁等

##### **乐观锁**

每次拿数据都不会上锁，但在更新的时候会判断一下别人有没有更改过这个数据，可以使用版本等机制，乐观锁适用于多读的应用类型，并不是绝对安全，具有ABA问题

抢票是乐观锁



#### **监视操作**

##### **watch** **key** **[key ...]**

在执行multi之前，先执行watch key1 [key2],可以监视一个(或多个) key ，如果在事务执行之前这个(或这些) key** 被其他命令所改动，那么事务将被打断。

##### **unwatch**

取消 WATCH 命令对所有 key 的监视。

如果在执行 WATCH 命令之后，EXEC 命令或DISCARD 命令先被执行了的话，那么就不需要再执行UNWATCH 了。



#### Redis事务三特性

**单独的隔离操作**

事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。 

**没有隔离级别的概念** 

队列中的命令没有提交之前都不会实际被执行，因为事务提交前任何指令都不会被实际执行

**不保证原子性** 

事务中如果有一条命令执行失败，其后的命令仍然会被执行，没有回滚 

#### **并发模拟**

工具ab模拟测试

centos6默认安装

centos7手动安装

yum install httpd-tools

##### ab测试工具参数

-n     ----------requests，用于指定压力测试总共的执行次数

-c     ----------concurrency，用于指定的并发数

-t      ----------timelimit，等待响应的最大时间(单位：秒)

-b      ----------windowsize，TCP发送/接收的缓冲大小(单位：字节)

-p      ---------postfile，发送POST请求时需要上传的文件，此外还必须设置-T参数

-u     -----------putfile，发送PUT请求时需要上传的文件，此外还必须设置-T参数

-T     ------------content-type，用于设置Content-Type请求头信息，例如：application/x-www-form-urlencoded，默认值为text/plain。

-v     -----------verbosity，指定打印帮助信息的冗余级别。

-w     -----------以HTML表格形式打印结果。

-i     -----------使用HEAD请求代替GET请求。

-x     -----------插入字符串作为table标签的属性。

-y    ----------- 插入字符串作为tr标签的属性。

-z     -----------插入字符串作为td标签的属性。

-C     -----------添加cookie信息，例如：“Apache=1234”(可以重复该参数选项以添加多个)。

-H     -----------添加任意的请求头，例如：“Accept-Encoding: gzip”，请求头将会添加在现有的多个请求头之后(可以重复该参数选项以添加多个)。

-A     -----------添加一个基本的网络认证信息，用户名和密码之间用英文冒号隔开。

-P     -----------添加一个基本的代理认证信息，用户名和密码之间用英文冒号隔开。

-X     -----------指定使用的和端口号，例如:“126.10.10.3:88”。

-V     -----------打印版本号并退出。

-k     -----------使用HTTP的KeepAlive特性。

-d     -----------不显示百分比。

-S     -----------不显示预估和警告信息。

-g     -----------输出结果信息到gnuplot格式的文件中。

-e     -----------输出结果信息到CSV格式的文件中。

-r     -----------指定接收到错误信息时不退出程序。

-h     -----------显示用法信息，其实就是ab -help。

举例

ab -n 2000 -c 200 -k -p ~/postfile -T application/x-www-form-urlencoded http://192.168.137.133:8080/Seckill/doseckill

-n     ----------requests，用于指定压力测试总共的执行次数

-c     ----------concurrency，用于指定的并发数

-k     -----------使用HTTP的KeepAlive特性。

-p      ---------postfile，发送POST请求时需要上传的文件，此外还必须设置-T参数

##### **常见问题解决**

**超卖问题**

使用乐观锁+事务解决

**超时问题**

长时间获取不到连接而导致连接超时，通过连接池来解决

**可以使用LUA脚本解决库存遗留问题**

将复杂的或者多步的redis操作，写为一个脚本，一次提交给redis执行，减少反复连接redis的次数。提升性能。

LUA脚本是类似redis事务，有一定的原子性，不会被其他命令插队，可以完成一些redis事务性的操作。

但是注意redis的lua脚本功能，只有在Redis 2.6以上的版本才可以使用。

利用lua脚本淘汰用户，解决超卖问题。

redis 2.6版本以后，通过lua脚本解决争抢问题，实际上是redis 利用其单线程的特性，用任务队列的方式解决多任务并发问题。



### Redis持久化

Redis默认启用RDB，启用AOF需要修改配置文件，AOF与RDB持久化文件同时存在时，优先使用AOF恢复数据

官方建议AOF和RDB同时启用

#### RDB

在指定的时间间隔内将内存中的数据集快照写入磁盘，即Snapshot快照，恢复时将快照文件直接读到内存里

Redis会单独创建（fork）一个子进程来进行持久化，会先将数据写入到 一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。 整个过程中，主进程是不进行任何IO操作的，这就确保了**极高的性能** 如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那RDB方式要比AOF方式**更加的高效**。**RDB的缺点是最后一次持久化后的数据可能丢失**。

##### Fork

Fork的作用是复制一个与当前进程一样的进程。新进程的所有数据（变量、环境变量、程序计数器等） 数值都和原进程一致，但是是一个全新的进程，并作为原进程的子进程

在Linux程序中，fork()会产生一个和父进程完全相同的子进程，但子进程在此后多会exec系统调用，出于效率考虑，Linux中引入了"**写时复制技术**"

**一般情况父进程和子进程会共用同一段物理内存**，只有进程空间的各段的内容要发生变化时，才会将父进程的内容复制一份给子进程。

配置文件中可以修改保存触发的条件，以及持久化文件的位置（默认为执行命令当前目录）

文件名默认为dump.rdb

##### saveVS bgsave

**save ：**save时只管保存，其它不管，全部阻塞。手动保存。不建议。

**bgsave**：**Redis**会在后台异步进行快照操作，**快照同时还可以响应客户端请求。**

可以通过lastsave 命令获取最后一次成功执行快照的时间

**flushall命令**

执行flushall命令，也会产生dump.rdb文件，但里面是空的，无意义

**save**

格式：save 秒钟 写操作次数

RDB是整个内存的压缩过的Snapshot，RDB的数据结构，可以配置复合的快照触发条件，

默认是1分钟内改了1万次，或5分钟内改了10次，或15分钟内改了1次。

禁用

不设置save指令，或者给save传入空字符串

**stop-writes-on-bgsave-error**

当Redis无法写入磁盘的话，直接关掉Redis的写操作。推荐yes.

**rdbcompression** **压缩文件**

对于存储到磁盘中的快照，可以设置是否进行压缩存储。如果是的话，redis会采用LZF算法进行压缩。

如果你不想消耗CPU来进行压缩的话，可以设置为关闭此功能。推荐yes.

**rdbchecksum** **检查完整性**

在存储快照后，还可以让redis使用CRC64算法来进行数据校验，

但是这样做会增加大约10%的性能消耗，如果希望获取到最大的性能提升，可以关闭此功能

推荐yes.



#### AOF

以**日志**的形式来记录每个写操作（增量保存），将Redis执行过的所有写指令记录下来(**读操作不记录**)， **只许追加文件但不可以改写文件**，redis启动之初会读取该文件重新构建数据，换言之，redis 重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  

（1）客户端的请求写命令会被append追加到AOF缓冲区内；

（2）AOF缓冲区根据AOF持久化策略[always,everysec,no]将操作sync同步到磁盘的AOF文件中；

（3）AOF文件大小超过重写策略或手动重写时，会对AOF文件rewrite重写，压缩AOF文件容量；

（4）Redis服务重启时，会重新load加载AOF文件中的写操作达到数据恢复的目的；

AOF文件的保存路径，同RDB的路径一致。

##### 异常恢复

修改默认的appendonly no，改为yes

如遇到AOF文件损坏，通过/usr/local/bin/redis-check-aof--fix appendonly.aof进行恢复

备份被写坏的AOF文件

恢复：重启redis，然后重新加载

##### **AOF同步频率设置**

**appendfsync always**

始终同步，每次Redis的写入都会立刻记入日志；性能较差但数据完整性比较好

**appendfsync everysec**

每秒同步，每秒记入日志一次，如果宕机，本秒的数据可能丢失。

**appendfsync no**

redis不主动进行同步，把同步时机交给操作系统。



##### **Rewrite压缩**

**是什么：**

AOF采用文件追加方式，文件会越来越大为避免出现此种情况，新增了重写机制, 当AOF文件的大小超过所设定的阈值时，Redis就会启动AOF文件的内容压缩， 只保留可以恢复数据的最小指令集.可以使用命令bgrewriteaof

**重写原理，如何实现重写**

AOF文件持续增长而过大时，会fork出一条新进程来将文件重写(也是先写临时文件最后再rename)，redis4.0版本后的重写，是指上就是把rdb 的快照，以二级制的形式附在新的aof头部，作为已有的历史数据，替换掉原来的流水账操作。

no-appendfsync-on-rewrite：

如果 no-appendfsync-on-rewrite=yes ,不写入aof文件只写入缓存，用户请求不会阻塞，但是在这段时间如果宕机会丢失这段时间的缓存数据。（降低数据安全性，提高性能）

如果 no-appendfsync-on-rewrite=no, 还是会把数据往磁盘里刷，但是遇到重写操作，可能会发生阻塞。（数据安全，但是性能降低）

**触发机制，何时重写**

Redis会记录上次重写时的AOF大小，默认配置是当AOF文件大小是上次rewrite后大小的一倍且文件大于64M时触发

重写虽然可以节约大量磁盘空间，减少恢复时间。但是每次重写还是有一定的负担的，因此设定Redis要满足一定条件才会进行重写。 

auto-aof-rewrite-percentage：设置重写的基准值，文件达到100%时开始重写（文件是原来重写后文件的2倍时触发）

auto-aof-rewrite-min-size：设置重写的基准值，最小文件64MB。达到这个值开始重写。

例如：文件达到70MB开始重写，降到50MB，下次什么时候开始重写？100MB

系统载入时或者上次重写完毕时，Redis会记录此时AOF大小，设为base_size,

如果Redis的AOF当前大小>= base_size +base_size*100% (默认)且当前大小>=64mb(默认)的情况下，Redis会对AOF进行重写。 

**重写流程**

（1）bgrewriteaof触发重写，判断是否当前有bgsave或bgrewriteaof在运行，如果有，则等待该命令结束后再继续执行。

（2）主进程fork出子进程执行重写操作，保证主进程不会阻塞。

（3）子进程遍历redis内存中数据到临时文件，客户端的写请求同时写入aof_buf缓冲区和aof_rewrite_buf重写缓冲区保证原AOF文件完整以及新AOF文件生成期间的新的数据修改动作不会丢失。

（4）子进程写完新的AOF文件后，向主进程发信号，父进程更新统计信息。2).主进程把aof_rewrite_buf中的数据写入到新的AOF文件。

（5）使用新的AOF文件覆盖旧的AOF文件，完成AOF重写。

#### 对比

**RDB**

**优势**

适合大规模的数据恢复

对数据完整性和一致性要求不高更适合使用

节省磁盘空间

恢复速度快                      

**劣势**

Fork的时候，内存中的数据被克隆了一份，大致2倍的膨胀性需要考虑

虽然Redis在fork时使用了**写时拷贝技术**,但是如果数据庞大时还是比较消耗性能。

在备份周期在一定间隔时间做一次备份，所以如果Redis意外down掉的话，就会丢失最后一次快照后的所有修改。

![image-20210703204005919](C:\Users\enslaver\AppData\Roaming\Typora\typora-user-images\image-20210703204005919.png)





**AOF**

**优势**       

备份机制更稳健，丢失数据概率更低。

可读的日志文本，通过操作AOF稳健，可以处理误操作。

**劣势**

比起RDB占用更多的磁盘空间。

恢复备份速度要慢。

每次读写都同步的话，有一定的性能压力。

存在个别Bug，造成不能恢复。

![image-20210703204526019](C:\Users\enslaver\AppData\Roaming\Typora\typora-user-images\image-20210703204526019.png)

**最后**

官方推荐两个都启用。

如果对数据不敏感，可以选单独用RDB。

不建议单独用 AOF，因为可能会出现Bug。

如果只是做纯内存缓存，可以都不用。

**官方建议**

RDB持久化方式能够在指定的时间间隔能对你的数据进行快照存储

AOF持久化方式记录每次对服务器写的操作,当服务器重启的时候会重新执行这些命令来恢复原始的数据,AOF命令以redis协议追加保存每次写的操作到文件末尾. 

Redis还能对AOF文件进行后台重写,使得AOF文件的体积不至于过大

只做缓存：如果你只希望你的数据在服务器运行的时候存在,你也可以不使用任何持久化方式.

同时开启两种持久化方式

在这种情况下,当redis重启的时候会优先载入AOF文件来恢复原始的数据, 因为在通常情况下AOF文件保存的数据集要比RDB文件保存的数据集要完整.

RDB的数据不实时，同时使用两者时服务器重启也只会找AOF文件。那要不要只使用AOF呢？ 

建议不要，因为RDB更适合用于备份数据库(AOF在不断变化不好备份)， 快速重启，而且不会有AOF可能潜在的bug，留着作为一个万一的手段。

**性能建议**

因为RDB文件只用作后备用途，建议只在Slave上持久化RDB文件，而且只要15分钟备份一次就够了，只保留save 900 1这条规则。   

如果使用AOF，好处是在最恶劣情况下也只会丢失不超过两秒数据，启动脚本较简单只load自己的AOF文件就可以了。  

代价,一是带来了持续的IO，二是AOF rewrite的最后将rewrite过程中产生的新数据写到新文件造成的阻塞几乎是不可避免的。

只要硬盘许可，应该尽量减少AOF  rewrite的频率，AOF重写的基础大小默认值64M太小了，可以设到5G以上。 默认超过原大小100%大小时重写可以改到适当的数值。  



### 主从复制

#### 介绍

主机数据更新后根据配置和策略， 自动同步到备机的master/slaver机制，Master以写为主，Slave以读为主

**作用**

读写分离，性能扩展

容灾快速恢复



#### 如何开启

开启daemonize yes

Pid文件名字pidfile

指定端口port

Log文件名字

dump.rdb名字dbfilename

Appendonly 关掉或者换名字



**也可以通过include来配置**

先确保redis.conf基本文件配置好

然后只对端口号相关做改动

include /myredis/redis.conf

pidfile /var/run/redis_6379.pid

port 6379

dbfilename dump6379.rdb



#### 主从复制原理

**1.从服务器连接上主服务器后，向主服务器发出数据同步消息**

**2.主服务器接到从服务器发过来同步消息，把主服务器数据持久化的rdb文件发送到从服务器，从服务器接收并读取**

**3.每次主服务器进行写操作之后，和从服务器进行数据同步**

**Slave启动成功连接到master后会发送一个sync命令**

**Master接到命令启动后台的存盘进程，同时收集所有接收到的用于修改数据集命令， 在后台进程执行完毕之后，master将传送整个数据文件到slave,以完成一次完全同步**

**全量复制：而slave服务在接收到数据库文件数据后，将其存盘并加载到内存中。**

**增量复制：Master继续将新的所有收集到的修改命令依次传给slave,完成同步**

**但是只要是重新连接master,一次完全同步（全量复制)将被自动执行**



#### 基本指令

**info replication**	查看主从复制的相关信息

**slaveof <ip> <port>**	选取一个master

**slaveof no one**	取消所有master(反客为主)



#### 哨兵模式

**反客为主的自动版**，能够后台监控主机是否故障，如果故障了根据投票数自动将从库转换为主库

![image-20210707001746024](C:\Users\enslaver\AppData\Roaming\Typora\typora-user-images\image-20210707001746024.png)



##### 尝试

1.调整为一主二仆模式，6379带着6380、6381

2.自定义的/myredis目录下新建sentinel.conf文件，名字绝不能错

3.sentinel monitor mymaster 127.0.0.1 6379 1

其中mymaster为监控对象起的服务器名称， 1 为至少有多少个哨兵同意迁移的数量。 

4./usr/local/bin

redis做压测可以用自带的redis-benchmark工具

执行redis-sentinel /myredis/sentinel.conf 



哪个从机会被选举为主机呢？根据优先级别：slave-priority 

原主机重启后会变为从机。

#### 优先级

![image-20210707010933449](C:\Users\enslaver\AppData\Roaming\Typora\typora-user-images\image-20210707010933449.png)

slave-priority 100	(旧版本写法)

replica-priority 100	(现版本写法)

数字越小优先级越高

偏移量是指获得原主机数据最全的

每个redis实例启动后都会随机生成一个40位的runid

#### **复制延时**

由于所有的写操作都是先在Master上操作，然后同步更新到Slave上，所以从Master同步到Slave机器有一定的延迟，当系统很繁忙的时候，延迟问题会更加严重，Slave机器数量的增加也会使这个问题更加严重

#### Java代码

```java
private static JedisSentinelPool jedisSentinelPool=null;

public static  Jedis getJedisFromSentinel(){
if(jedisSentinelPool==null){
            Set<String> sentinelSet=new HashSet<>();
            sentinelSet.add("192.168.11.103:26379");

            JedisPoolConfig jedisPoolConfig =new JedisPoolConfig();
            jedisPoolConfig.setMaxTotal(10); //最大可用连接数
			jedisPoolConfig.setMaxIdle(5); //最大闲置连接数
			jedisPoolConfig.setMinIdle(5); //最小闲置连接数
			jedisPoolConfig.setBlockWhenExhausted(true); //连接耗尽是否等待
			jedisPoolConfig.setMaxWaitMillis(2000); //等待时间
			jedisPoolConfig.setTestOnBorrow(true); //取连接的时候进行一下测试 ping pong

			jedisSentinelPool=new JedisSentinelPool("mymaster",sentinelSet,jedisPoolConfig);
			return jedisSentinelPool.getResource();
        }else{
			return jedisSentinelPool.getResource();
        }
}

```

### Redis集群

#### 为什么需要集群

主从模式，薪火相传模式，主机宕机，导致ip地址发生变化，应用程序中配置需要修改对应的主机地址、端口等信息。

之前通过代理主机来解决，但是redis3.0中提供了解决方案。就是无中心化集群配置。

#### 什么是集群

Redis 集群实现了对Redis的水平扩容，即启动N个redis节点，将整个数据库分布存储在这N个节点中，每个节点存储总数据的1/N。

Redis 集群通过分区（partition）来提供一定程度的可用性（availability）： 即使集群中有一部分节点失效或者无法进行通讯， 集群也可以继续处理命令请求。

#### 配置流程

先删除rdb、aof持久化文件

cluster-enabled yes  打开集群模式

cluster-config-file nodes-6379.conf 设定节点配置文件名

cluster-node-timeout 15000  设定节点失联时间，超过该时间（毫秒），集群自动进行主从切换。

include /myredis/redis.conf

实例

```conf
port 6379

pidfile "/var/run/redis_6379.pid"

dbfilename "dump6379.rdb"

dir "/home/bigdata/redis_cluster"

logfile "/home/bigdata/redis_cluster/redis_err_6379.log"

cluster-enabled yes

cluster-config-file nodes-6379.conf

cluster-node-timeout 15000
```

**dir**配置持久化文件目录，默认持久化到当前命令文件下  The DB will be written inside this directory, with the filename specified

cp命令复制文件，并使用:%s/6379/6380查找并替换修改配置

#### 使用流程

**依次打开每个redis服务**

**检查服务是否正常开启**

ps -ef|grep redis

**进入src目录**

cd /opt/redis-6.2.1/src

redis集群命令必须在src目录下执行

**启动集群自动配置**

redis-cli --cluster create --cluster-replicas 1 192.168.11.101:6379 192.168.11.101:6380 192.168.11.101:6381 192.168.11.101:6389 192.168.11.101:6390 192.168.11.101:6391

replicas表示主节点数/从节点数的比例

**tips**

redis5之后就集成了ruby语言的环境，如果redis文件下没有redis-trib.rb则需要配置ruby环境

**集群方式连接主机**

redis-cli -c -p 6379

-c	集群方式连接

普通方式登录存储数据会出现moved重定向操作

#### **slots插槽**

slots  插槽，集群中每台主机负责一定范围内的插槽，每个插槽储存一定的数据

槽（slots）数据结构实际上是一个二进制数组，数组长度为2048个字节，16384个二进制位。Redis集群通过分片的方式来保存数据库中的键值对。集群的整个数据库被分为16384个槽，即数据库中的所有键都属于这16384个槽中的一个。集群中每个节点可以处理0~16384个槽

Redis将每个键键名的有效部分使用CRC16算法计算出散列值，然后对16384取余，保证每个值都可以分配到16384个槽中，进而分配到指定的节点中处理。槽分配（即迁移），使用redis-cli进行操作。操作过程中会用到槽的索引（槽1~16384对应索引分别为0~16383）

#### 常用命令

cluster nodes

不在一个slot下的键值，不能使用mget,mset等多键操作。

可以通过{}来定义组的概念，从而使key中{}内相同内容的键值对放到一个slot中去。

![image-20210714172205490](C:\Users\enslaver\AppData\Roaming\Typora\typora-user-images\image-20210714172205490.png)

**cluster keyslot k1**	查询k1所在插槽值

**cluster countkeysinslot <slot>**	获取slot槽里键的数量

**cluster getkeysinslot <slot><count>**	返回count个slot槽中的键

**注意！**只能查看本端口redis管理的插槽情况，但是！数据是可以查询到的，只是不能通过插槽查看

![image-20210714203736595](C:\Users\enslaver\AppData\Roaming\Typora\typora-user-images\image-20210714203736595.png)

![image-20210714203717767](C:\Users\enslaver\AppData\Roaming\Typora\typora-user-images\image-20210714203717767.png)

#### **故障恢复**

**如果主节点下线？从节点能否自动升为主节点？**

注意：15秒超时

**主节点恢复后，主从关系会如何？**

主节点回来变成从机。

**如果所有某一段插槽的主从节点都宕掉，redis服务是否还能继续?**

如果某一段插槽的主从都挂掉，而cluster-require-full-coverage 为yes ，那么 ，整个集群都挂掉

如果某一段插槽的主从都挂掉，而cluster-require-full-coverage 为no ，那么，该插槽数据全都不能使用，也无法存储。

redis.conf中的参数 cluster-require-full-coverage

**Java使用Jedis操作集群**

```java

public class JedisClusterTest {
  public static void main(String[] args) { 
     Set<HostAndPort>set =new HashSet<HostAndPort>();
     set.add(new HostAndPort("192.168.31.211",6379));
     JedisCluster jedisCluster=new JedisCluster(set);
     jedisCluster.set("k1", "v1");
     System.out.println(jedisCluster.get("k1"));
  }
}
```

### **Redis应用问题**

#### 缓存穿透

**问题描述**

key对应的数据在数据源并不存在，每次针对此key的请求从缓存获取不到，请求都会压到数据源，从而可能压垮数据源。比如用一个不存在的用户id获取用户信息，不论缓存还是数据库都没有，若黑客利用此漏洞进行攻击可能压垮数据库。

**解决方案**

一个一定不存在缓存及查询不到的数据，由于缓存是不命中时被动写的，并且出于容错考虑，如果从存储层查不到数据则不写入缓存，这将导致这个不存在的数据每次请求都要到存储层去查询，失去了缓存的意义。

**对空值缓存：**如果一个查询返回的数据为空（不管是数据是否不存在），我们仍然把这个空结果（null）进行缓存，设置空结果的过期时间会很短，最长不超过五分钟

**设置可访问的名单（白名单）：**

使用bitmaps类型定义一个可以访问的名单，名单id作为bitmaps的偏移量，每次访问和bitmap里面的id进行比较，如果访问id不在bitmaps里面，进行拦截，不允许访问。

**采用布隆过滤器**：(布隆过滤器（Bloom Filter）是1970年由布隆提出的。它实际上是一个很长的二进制向量(位图)和一系列随机映射函数（哈希函数）。

布隆过滤器可以用于检索一个元素是否在一个集合中。它的优点是空间效率和查询时间都远远超过一般的算法，缺点是有一定的误识别率和删除困难。)

将所有可能存在的数据哈希到一个足够大的bitmaps中，一个一定不存在的数据会被 这个bitmaps拦截掉，从而避免了对底层存储系统的查询压力。

**进行实时监控：**当发现Redis的命中率开始急速降低，需要排查访问对象和访问的数据，和运维人员配合，可以设置黑名单限制服务



#### **缓存击穿**

**问题描述**

key对应的数据存在，但在redis中过期，此时若有大量并发请求过来，这些请求发现缓存过期一般都会从后端DB加载数据并回设到缓存，这个时候大并发的请求可能会瞬间把后端DB压垮。               

key可能会在某些时间点被超高并发地访问，是一种非常“热点”的数据。这个时候，需要考虑一个问题：缓存被“击穿”的问题。          

**解决方案**

预先设置热门数据：在redis高峰访问之前，把一些热门数据提前存入到redis里面，加大这些热门数据key的时长

实时调整：现场监控哪些数据热门，实时调整key的过期时长

使用锁：

（1）  就是在缓存失效的时候（判断拿出来的值为空），不是立即去load db。

（2）  先使用缓存工具的某些带成功操作返回值的操作（比如Redis的SETNX）去set一个mutex key

（3）  当操作返回成功时，再进行load db的操作，并回设缓存,最后删除mutex key；

（4）  当操作返回失败，证明有线程在load db，当前线程睡眠一段时间再重试整个get缓存的方法。



#### **缓存雪崩**

 **问题描述**

key对应的数据存在，但在redis中过期，此时若有大量并发请求过来，这些请求发现缓存过期一般都会从后端DB加载数据并回设到缓存，这个时候大并发的请求可能会瞬间把后端DB压垮。

缓存雪崩与缓存击穿的区别在于这里针对很多key缓存，前者则是某一个key

缓存失效时的雪崩效应对底层系统的冲击非常可怕！

**解决方案**

**构建多级缓存架构：**nginx缓存 + redis缓存 +其他缓存（ehcache等）

**使用锁或队列：**

用加锁或者队列的方式保证来保证不会有大量的线程对数据库一次性进行读写，从而避免失效时大量的并发请求落到底层存储系统上。不适用高并发情况

**设置过期标志更新缓存：**

记录缓存数据是否过期（设置提前量），如果过期会触发通知另外的线程在后台去更新实际key的缓存。

**将缓存失效时间分散开：**

比如我们可以在原有的失效时间基础上增加一个随机值，比如1-5分钟随机，这样每一个缓存的过期时间的重复率就会降低，就很难引发集体失效的事件。



### **Redis分布式锁**

 随着业务发展的需要，原单体单机部署的系统被演化成分布式集群系统后，由于分布式系统多线程、多进程并且分布在不同机器上，这将使原单机部署情况下的并发控制锁策略失效，单纯的Java API并不能提供分布式锁的能力。为了解决这个问题就需要一种跨JVM的互斥机制来控制共享资源的访问，这就是分布式锁要解决的问题！

分布式锁主流的实现方案：

基于数据库实现分布式锁

 基于缓存（Redis等）

基于Zookeeper

每一种分布式锁解决方案都有各自的优缺点：

性能：redis最高

可靠性：zookeeper最高

#### **使用初步举例**

**redis命令**

set lock ok NX PX 10000

EX second ：设置键的过期时间为 second 秒。 SET key value EX second 效果等同于 SETEX key second value 。

PX millisecond ：设置键的过期时间为 millisecond 毫秒。 SET key value PX millisecond 效果等同于 PSETEX key millisecond value 。

NX ：只在键不存在时，才对键进行设置操作。 SET key value NX 效果等同于 SETNX key value 。

XX ：只在键已经存在时，才对键进行设置操作。

多个客户端同时获取锁（setnx）

获取成功，执行业务逻辑{从db获取数据，放入缓存}，执行完成释放锁（del）

其他客户端等待重试

**Java实现**

```java
@GetMapping("testLock")
public void testLock(){
    //1获取锁，setne
    Boolean lock = redisTemplate.opsForValue().setIfAbsent("lock", "111");
    //2获取锁成功、查询num的值
    if(lock){
        Object value = redisTemplate.opsForValue().get("num");
        //2.1判断num为空return
        if(StringUtils.isEmpty(value)){
            return;
        }
        //2.2有值就转成成int
        int num = Integer.parseInt(value+"");v
        //2.3把redis的num加1
        redisTemplate.opsForValue().set("num", ++num);
        //2.4释放锁，del
        redisTemplate.delete("lock");

    }else{
        //3获取锁失败、每隔0.1秒再获取
        try {
            Thread.sleep(100);
            testLock();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

```

**ab测试举例**	ab -n 1000 -c 100 http://192.168.140.1:8080/test/testLock

ab测试具体使用可以参见事务模块

#### **优化之设置锁的过期时间**

首先想到通过expire设置过期时间缺乏原子性：如果在setnx和expire之间出现异常，锁也无法释放

所以在set时指定过期时间（推荐）

```Java
Boolean lock = redisTemplate.opsForValue().setIfAbsent("lock", "111"，100，TimeUnit.SECONDS);
```

#### **优化之UUID防误删**

问题：删除操作缺乏原子性。

场景：

**1.**index1执行删除时，查询到的lock值确实和uuid相等

uuid=v1

set(lock,uuid)；

**2.**index1执行删除前，lock刚好过期时间已到，被redis自动释放

在redis中没有了lock，没有了锁。

**3.**index2获取了lock

index2线程获取到了cpu的资源，开始执行方法

uuid=v2

set(lock,uuid)；

**4.**index1执行删除，此时会把index2的lock删除

index1 因为已经在方法中了，所以不需要重新上锁。index1有执行的权限。index1已经比较完成了，这个时候，开始执行

删除的index2的锁！

**使用LUA脚本保证删除的原子性**

```java
//加锁

// 1. 从redis中获取锁,set k1 v1 px 20000 nx
String uuid = UUID.randomUUID().toString();
Boolean lock = this.redisTemplate.opsForValue()
      .setIfAbsent("lock", uuid, 2, TimeUnit.SECONDS);

//使用lua释放锁

// 释放锁 del
String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";
// 设置lua脚本返回的数据类型
DefaultRedisScript<Long> redisScript = new DefaultRedisScript<>();
// 设置lua脚本返回类型为Long
redisScript.setResultType(Long.class);
redisScript.setScriptText(script);
redisTemplate.execute(redisScript, Arrays.asList("lock"),uuid);

//重试

Thread.sleep(500);
testLock();

```

#### 分布式锁满足条件

为了确保分布式锁可用，我们至少要确保锁的实现同时**满足以下四个条件**：

\- 互斥性。在任意时刻，只有一个客户端能持有锁。

\- 不会发生死锁。即使有一个客户端在持有锁的期间崩溃而没有主动解锁，也能保证后续其他客户端能加锁。

\- 解铃还须系铃人。加锁和解锁必须是同一个客户端，客户端自己不能把别人加的锁给解了。

\- 加锁和解锁必须具有原子性。

### Redis6.0新功能

#### **ACL**

##### **简介**

Redis ACL是Access Control List（访问控制列表）的缩写，该功能允许根据可以执行的命令和可以访问的键来限制某些连接。

在Redis 5版本之前，Redis 安全规则只有密码控制 还有通过rename 来调整高危命令比如 flushdb ， KEYS* ， shutdown 等。Redis 6 则提供ACL的功能对用户进行更细粒度的权限控制 ：

（1）接入权限:用户名和密码 

（2）可以执行的命令 

（3）可以操作的 KEY

参考官网：https://redis.io/topics/acl

##### **命令**

1、使用acl list命令展现用户权限列表

（1）数据说明

 ![image-20210715211136272](C:\Users\enslaver\AppData\Roaming\Typora\typora-user-images\image-20210715211136272.png)       

2、使用acl cat命令

（1）查看添加权限指令类别

 ![image-20210715211149965](C:\Users\enslaver\AppData\Roaming\Typora\typora-user-images\image-20210715211149965.png)

（2）加参数类型名可以查看类型下具体命令

 

 ![image-20210715211159263](C:\Users\enslaver\AppData\Roaming\Typora\typora-user-images\image-20210715211159263.png)

3、使用acl whoami命令查看当前用户

 ![image-20210715211209943](C:\Users\enslaver\AppData\Roaming\Typora\typora-user-images\image-20210715211209943.png)

 

4、使用aclsetuser命令创建和编辑用户ACL

（1）ACL规则

下面是有效ACL规则的列表。某些规则只是用于激活或删除标志，或对用户ACL执行给定更改的单个单词。其他规则是字符前缀，它们与命令或类别名称、键模式等连接在一起。

| ACL规则              |                                                              |                                                    |
| -------------------- | ------------------------------------------------------------ | -------------------------------------------------- |
| 类型                 | 参数                                                         | 说明                                               |
| 启动和禁用用户       | **on**                                                       | 激活某用户账号                                     |
| **off**              | 禁用某用户账号。注意，已验证的连接仍然可以工作。如果默认用户被标记为off，则新连接将在未进行身份验证的情况下启动，并要求用户使用AUTH选项发送AUTH或HELLO，以便以某种方式进行身份验证。 |                                                    |
| 权限的添加删除       | **+<command>**                                               | 将指令添加到用户可以调用的指令列表中               |
| **-<command>**       | 从用户可执行指令列表移除指令                                 |                                                    |
| **+@<category>**     | 添加该类别中用户要调用的所有指令，有效类别为@admin、@set、@sortedset…等，通过调用ACL CAT命令查看完整列表。特殊类别@all表示所有命令，包括当前存在于服务器中的命令，以及将来将通过模块加载的命令。 |                                                    |
| -@<actegory>         | 从用户可调用指令中移除类别                                   |                                                    |
| **allcommands**      | +@all的别名                                                  |                                                    |
| **nocommand**        | -@all的别名                                                  |                                                    |
| 可操作键的添加或删除 | **~<pattern>**                                               | 添加可作为用户可操作的键的模式。例如~*允许所有的键 |

 

（2）通过命令创建新用户默认权限

acl setuser user1

 ![image-20210715211225620](C:\Users\enslaver\AppData\Roaming\Typora\typora-user-images\image-20210715211225620.png)

在上面的示例中，我根本没有指定任何规则。如果用户不存在，这将使用just created的默认属性来创建用户。如果用户已经存在，则上面的命令将不执行任何操作。

 

（3）设置有用户名、密码、ACL权限、并启用的用户

acl setuser user2 on >password ~cached:* +get

 ![image-20210715211232050](C:\Users\enslaver\AppData\Roaming\Typora\typora-user-images\image-20210715211232050.png)

 

(4)切换用户，验证权限

![image-20210715211238347](C:\Users\enslaver\AppData\Roaming\Typora\typora-user-images\image-20210715211238347.png)

#### **IO**多线程

##### **简介**

Redis6终于支撑多线程了，告别单线程了吗？

IO多线程其实指**客户端交互部分**的**网络IO**交互处理模块**多线程**，而非**执行命令多线程**。Redis6执行命令依然是单线程。

##### **原理架构**

Redis 6 加入多线程,但跟 Memcached 这种从 IO处理到数据访问多线程的实现模式有些差异。Redis 的多线程部分只是用来处理网络数据的读写和协议解析，执行命令仍然是单线程。之所以这么设计是不想因为多线程而变得复杂，需要去控制 key、lua、事务，LPUSH/LPOP 等等的并发问题。整体的设计大体如下:

![image-20210715211351299](C:\Users\enslaver\AppData\Roaming\Typora\typora-user-images\image-20210715211351299.png)        

##### 使用

另外，多线程IO默认也是不开启的，需要再配置文件中配置

io-threads-do-reads yes 

io-threads 4





#### 支持cluster的使用

