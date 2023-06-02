
## Key的结构

Redis的key允许有多个单词形成层级结构，多个单词之间使用‘:’隔开，格式如下：
	项目名:业务名:类型:id
格式并非固定

eg:
- user 相关的key：heima:user:1
- product相关的key：heima:product:1

如果value是一个Java对象，例如一个Use对象，则可以将对象序列化成为JSON字符串后存储

| key           | value                             |
| ------------- | --------------------------------- |
| heima:user1:1 | '{"id":1,"name":"jack","age":21}' |
| heima:user2:2 | '{"id":2,"name":"pony","age":78}' | 



## 基础类型

### Hash类型
也叫散列，value是一个无序的字典，类似于HashMap的结构

String 结构是将对象序列化成为JSON字符串之后存储，当需要修改对象某个字段的时候很不方便

Hash结构可以将对象中的每个字段独立存储，可以针对单个字段做CRUD

### String类型
redis中最简单的存储类型，其value是字符串，不过根据字符串格式的不同，又可以分为三类：
- string ：普通字符串
- int ：整数类型，可以做自增、自减操作
- float：浮点类型，可以做自增、自减操作
不管是那种格式，底层都是字节数组形式存储，只不过是编码方式不同。字符串类型的最大空间不能超过512m
常见命令：
- SET key val
- GET key 
- MSET key1 val1 key2 val2 key3 val3 ...
- MGET key1 key2 key3
- INCR 让一个整型的key自增一，如果不是整型，比如传入的是字符串 会抛出error:**(error) ERR value is not an integer or out of range**
- INCRBY：让一个整型按照指定的步长增长 eg: INCRBY age 2
- INCRBYFLOAT：让一个浮点类型的数字自增指定的步长
- SETNX ：添加一个String类型的键值对，前提是这个key不存在，否则不执行
- SETEX：添加一个String类型的键值对，并指定有效期 eg: setex name 10 jack
### List类型
	Redis 中 的list与Java 中的linkedList 类似，可以看成一个双向链表结构。既可以支持正向检索也可以支持反向检索
	特征与linkedlist类似
- 有序
- 元素可以重复
- 插入和删除快
- 查询速度一般（链表遍历查询）
List的常见命令：
- LPUSH key element ... : 向列表左侧插入一个或多个元素
- LPOP key: 移除并**返回** 列表左侧的第一个元素
- RPUSH key element ...:  向列表右侧插入一个或多个元素
- RPOP key：移除并返回列表右侧的第一个元素
- LRANGE key star end ：返回一段角标范围内的所有元素
- BLPOP和BRPOP ：与LPOP和RPOP类似，只不过在没有元素时等待指定时间，而不是直接返回nil
### Set类型的常见命令
- SADD key member ： 向set中添加一个或者多个元素
- SREM key member ：移除set中的指定元素
- SCARD key ：返回set中元素的个数
- SMEMBERS：获取set中所有元素
- SISMEMBER key member ：判断一个元素是否存在于set中
- SINTER key1 key2 ：求key1与key2 的交集
- SDIFF key1 key2 ：求key1与key2 的差集
- SUNION key1 key2 ：求key1与key2的并集
### SortedSet类型的常见命令
	类似于java中的TreeSet,但是底层数据结构差别很大。
	SortedSet中的每一个元素都有score属性，可以基于score属性对元素排序,
	底层的是现实 跳表（SkipList）加 Hash表
SortedSet具有以下特性
- 可排序
- 元素不重复
- 查询速度快
根据可排序特性，经常用来实现排行榜这样的功能
常见命令
- ZADD key score member：添加以恶搞或多个元素到SortedSet，如果已经存在则更新Score值
- ZREM key member：删除指定元素
- ZSCORE key member ：获取指定元素的score值
- ZRANK key member ：获取指定元素的排名
- ZCARD key ：获取元素个数
- ZCOUNT key min max ：统计score值在给定范围内的所有元素个数
- ZINCRBY key increment member：让sortedset中的指定元素自增，步长为指定的increment值
- ZRANGE key min max：按照score排序之后，获取指定**排名**范围内的元素
- ZRANGEBYSCORE key min max：按照score排序之后，获取指定score范围内的元素
- ZDIFF、ZINTER、ZUNION：求差交并集合
**注意： 所有的排名默认升序，如果要降序则在命令的Z之后添加REV即可 比如”ZREVRANGE“**


# Jedis

jedis是线程不安全的，频繁地2创建和销毁链接会有性能损，推荐使用Jedis链接池代替直接使用Jedis

# SpringDataRedis
RedisTemplate 会接受任意的Object作为值写入Redis，只不过写入前把Object序列化为字节的形式，默认是采用Jdk序列化。
缺点：
- 内存占用大
- 可读性差
## 序列化
#Json序列化器 #GenericJackson2JsonRedisSerializer
![[Pasted image 20230602233501.png]]
缺点：存入redis的时候会将类的class类型写入json字符串，存入redis中，会带来额外的内存开销
#StringRedisTemplate #String序列化器
为了节省内存开销，我们不会使用json序列化器来处理value，而是统一使用String序列化器，要求只能存储String类型的Key和Value，当要存储Java对象的时候，手动进行序列化与反序列化
![[Pasted image 20230602234020.png]]