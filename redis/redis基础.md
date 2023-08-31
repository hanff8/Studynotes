## 1. key的结构

Redis的key允许有多个单词形成层级结构，多个单词之间使用‘:’隔开，格式如下：项目名:业务名:类型:id
格式并非固定
eg:
- user 相关的key：heima:user:1
- product相关的key：heima:product:1

如果value是一个Java对象，例如一个Use对象，则可以将对象序列化成为JSON字符串后存储

| key           | value                             |
| ------------- | --------------------------------- |
| heima:user1:1 | '{"id":1,"name":"jack","age":21}' |
| heima:user2:2 | '{"id":2,"name":"pony","age":78}' | 



## 2. 基础类型

### 2.1. Hash类型
也叫散列，value是一个无序的字典，类似于HashMap的结构

String 结构是将对象序列化成为JSON字符串之后存储，当需要修改对象某个字段的时候很不方便

Hash结构可以将对象中的每个字段独立存储，可以针对单个字段做CRUD

### 2.2. String类型
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
### 2.3. List类型
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
### 2.4. Set类型的常见命令
- SADD key member ： 向set中添加一个或者多个元素
- SREM key member ：移除set中的指定元素
- SCARD key ：返回set中元素的个数
- SMEMBERS：获取set中所有元素
- SISMEMBER key member ：判断一个元素是否存在于set中
- SINTER key1 key2 ：求key1与key2 的交集
- SDIFF key1 key2 ：求key1与key2 的差集
- SUNION key1 key2 ：求key1与key2的并集
### 2.5. SortedSet类型的常见命令
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



## 3. Jedis

	jedis是线程不安全的，频繁地2创建和销毁链接会有性能损，推荐使用Jedis链接池代替直接使用Jedis

## 4. SpringDataRedis
	RedisTemplate 会接受任意的Object作为值写入Redis，只不过写入前把Object序列化为字节的形式，默认是采用Jdk序列化。
	缺点：
	- 内存占用大
	- 可读性差

## 5. 序列化
#Json序列化器 #GenericJackson2JsonRedisSerializer
![[Pasted image 20230602233501.png]]

	缺点：存入redis的时候会将类的class类型写入json字符串，存入redis中，会带来额外的内存开销

#StringRedisTemplate #String序列化器

	为了节省内存开销，我们不会使用json序列化器来处理value，而是统一使用String序列化器，要求只能存储String类型的Key和Value，当要存储Java对象的时候，手动进行序列化与反序列化

![[Pasted image 20230602234020.png]]

## 6. Redis实现共享Session登陆

### 6.1. 缓存更新策略
1. 内存淘汰
2. 过期淘汰
    - 利用expire命令给数据设置过期时间
3. 主动更新
    - 主动完成数据库与缓存的同时更新
#### 6.1.1. 策略选择
低一致性需求：
    内存淘汰或过期淘汰
高一致性需求：
    主动更新为主
    过期淘汰兜底
#### 6.1.2. 主动更新的方案
1. Cache Aside
    缓存调用这在更新数据库的时候同时完成对缓存的更新，一致性好，实现难度一般
2. Read/Wirte Through
    缓存与数据集成为一个服务，服务保证两者的一致性，对外暴露API接口。调用者调用API，无需知道自己操作的是数据库还是缓存
    一致性优秀，实现复杂，性能一般
3. WriteBack
    缓存调用者的CRUD都针对缓存完成，由独立线程异步地将缓存数据写到数据库，实现最终一致
    一致性差，性能好，实现复杂
#### 6.1.3. Cache Aside的模式选择
更新缓存还是删除缓存？
1. 更新缓存会产生无效更新，并且存在较大的线程安全温馨
2. 删除缓存本质是延迟更新，没有无效更新，线程安全问题较低
先操作数据库还是缓存？
1. 先更新数据在删除缓存 ——满足原子性的情况下，安全问题概率较低
2. 先删缓存再更新数据库—— 安全问题概率较高
如何确保数据库与缓存操作原子性？
1. 单体系统——利用事务机制
2. 分布式系统——利用分布式事务机制
#### 6.1.4. 最佳实践
查询数据时：
1. 先查缓存
2. 如果缓存命中，直接返回
3. 如果缓存未命中，则查询数据库
4. 将数据库数据写入缓存
5. 返回结果
修改数据库时：
1. 先修改数据库
2. 然后删除缓存
3. 保证两者的原子性（加锁，demo中利用的redis的setnx命令以及redis的单线程特点）


### 6.2. 缓存穿透
![[Pasted image 20230606211050.png]]

#### 6.2.1. 解决方案：
##### 6.2.1.1. 缓存null值
思路：
对于不存在的数据也在redis中建立缓存，但是值为空，并且设置一个较短的TTL值
优点：
实现简单，维护方便
缺点：
额外的内存消耗
短期的数据不一致问题
#### 6.2.2. 布隆过滤
思路：
利用布隆过滤算法，在请求进入Redis之前先判断是否存在，如果不存在则直接拒绝请求
优点：
内存占用少
缺点：
实现复杂
存在误判可能
##### 6.2.2.1. 其他 
- 增强id的复杂度，避免被猜测id规律
- 做好数据的基础格式校验
- 加强用户权限校验
- 做好热点参数的限流
### 6.3. 缓存雪崩
    指的是同一时段大量的缓存key同时失效或者Redis服务宕机，导致大量请求到达数据库带来巨大压力
解决方案：
- 给不同的key的TTL添加随机值
- 利用redis集群提高服务的可用性
- 给缓存业务添加降级限流策略
- 给业务添加多级缓存（nginx缓存，jvm缓存，redis缓存）
### 6.4. 缓存击穿
    也叫做热点key问题，
- 短时间内被高并发访问
- 缓存重建耗时较长
缓存重建业务较复杂的Key突然失效了，无数的请求访问在瞬间给数据库带来巨大的冲击

解决办法
- 互斥锁
    - 对请求的key添加互斥锁，可以借助redis的setnx(key不存在的时候才执行语句)实现 
- 逻辑过期
    - 存储数据到redis的时候添加一个过期时间
    - ![[Pasted image 20230608203921.png]]
## 7. 订单
### 7.1. 全局ID生成
全局ID生成器，是一种在分布式系统下用来生成全局唯一ID的工具，一般要满足下列特性
- 高可用
- 唯一性
- 递增性
- 安全性
- 高性能
#### 7.1.1. 生成策略
- UUID
    - 生成16进制字符串
- Redis自增
    - 支持递增，数字类型，占用小
- snowFlake算法 （雪花算法）
    - 不依赖redis，对于时钟依赖比较高
- 数据库自增
    - 单独一张表用来存储自增ID
#### 7.1.2. Redis自增策略
- 每天一个key，方便统计订单量
- ID构造是 时间戳+计数器
### 7.2. 超卖问题
#### 7.2.1. 悲观锁与乐观锁
##### 7.2.1.1. 悲观锁：
    认为线程安全问题一定会繁盛，因此在操作数据之前先获取锁，确保线程串行执行，例如Synchronized，Locked都属于悲观锁
##### 7.2.1.2. 乐观锁：
    认为线程安全问题不一定会发生，因此不加锁，只是在更新数据时，去判断有没有其他线程对数据做了修改
    - 如果没有修改则认为是安全的，自己才更新数据
    - 如果被其他线程修改说明发生了安全问题，因此可以重试或者异常
判断之前查询得到的数据是否有被修改过，常见的方式有两种：
- 版本号法
    - 在更新时，判断当前的库存的版本是否和自己查询的版本号是否一致
    - 优点：简单粗暴
    - 缺点：性能一般
- CAS法（Compare And Set）
    - 在更新时，判断当前库存是否和自己查询到的库存是否一致
    - 优点：性能好
    - 缺点：存在成功率低的问题
### 7.3. 利用Redis实现分布式锁
    满足分布式系统或者集群模式下多进程可见并且互斥的锁
- 多进程可见
- 互斥
- 高可用
- 高性能
- 安全性

常见的有三种分布式锁的实现方法

|        | MySQL                     | Redis                      | Zookeeper                        |
| ------ | ------------------------- | -------------------------- | -------------------------------- |
| 互斥   | 利用MySQL本身的互斥锁机制 | 利用setnx这样的互斥命令    | 利用节点的唯一性和有序性实现互斥 |
| 高可用 | 好                        | 好                         | 好                               | 
| 高性能 | 一般                      | 好                         |         一般                         |
| 安全性 | 断开链接，自动释放锁      | 利用锁的超时时间，到期释放 |  临时节点，断开连接自动释放                                |
获取分布式锁时需要实现的两个基本方法
- 获取锁
    - 互斥：确保只能有一个线程获取锁
    - 非阻塞：尝试一次，成功返回true，失败返回false
    - `set lock thread1 EX 10 NX`    nx是互斥，ex是过期时间
- 释放锁
    - 手动释放
    - 超时释放：获取锁的时候添加一个超时时间
    - `del key`  释放锁，删除即可
### 7.4. Redisson
解决分布式锁的成熟框架
### 7.5. 分布式锁NPC问题
## 8. 消息队列
Redis 提供了三种不同的方式来实现消息队列
- List结构：基于List结构模拟消息队列
- PubSub：基本的点对点消息模型
- stream：比较完善的消息队列模型
### 8.1. List结构的消息队列
Redis的list数据结构是一个双向链表，很容易模拟出队列效果
可以利用**LPUSH** 结合**RPOP**、或者**RPUSH**结合**LPOP**来实现
不过要注意的是，当队列中没有消息时**RPOP**或**LPOP**操作会返回null，并不像JVM的阻塞队列那样会阻塞并等待消息。因此这里应该使用**BRPOP或者BLPOP**来实现阻塞效果
优点
- 利用Redis存储，不受限与JVM内存上限
- 基于Redis的持久化机制，数据安全有保证
- 可以满足消息的有序性
缺点
- 无法避免消息丢失：pop后队列中会被移除，但是如果消费者pop之后没有处理，这样就会出现消息丢失的问题
- 只支持单消费者
### 8.2. PubSub实现的消息队列
    Pubsub(发布订阅) 是Redis2.0版本引入的消息传递模型。消费者可以订阅一个或者多个channel，生产者向对应channel发送信息后，所有订阅者都能收到相关消息
- SUBSCRIBE channel [channel] ：订阅一个频道
- PUBLISH channel msg：向一个频道发送消息
- PSUBSCRIBE patternp[pattern]：订阅与pattern格式匹配的所有频道
#### 8.2.1. 优缺点：
优点
- 基于发布订阅模型，支持多生产、多消费
缺点：
 - 不支持数据持久化
 - 无法避免消息丢失
 - 消息堆积存在上限，超出时数据丢失
### 8.3. 基于Stream的消息队列
Stream是Redis5.0引入的一种新的数据类型，可以实现一个功能非常完善的消息队列
stream类型消息队列的Xread的命令特点：
- 消息可回溯
- 一个消息可以被多个消费者堆区
- 可以阻塞读取
- 有消息漏读的风险
#### 8.3.1. 消费者组
    将多个消费这划分到一个组，监听同一个队列。具备下列特点
- 消息分流：队列中的消息会分流给组内的不同消费者，而不是重复的消费，从而加快消息处理的速度
- 消息标识：消费者组会维护一个标识，记录最后一个被处理的消息，哪怕消费者宕机重启，还会从标识之后读取消息，确保每一个消费者都会被消费
- 消息确认：消费者获取消息之后，消费处于pending状态，并存入一个pending-list。当处理完成之后需要通过XACK来确认消息，标记消息为已处理，才会从pending-list移除
```shell
XGROUP CREATE key groupname ID [MKSTREAM]
```
- key 队列名称
- groupname 消费者组名称
- ID：启始ID标识，$代表队列中最后一个消息，0表示对类中第一个消息
- MKSTREAM：队列不存在时自动创建队列
其他命令：
```shell
# 删除指定的消费者组
XGROUP DESTROY key groupname

# 给指定的消费者组添加消费者
XGROUP CREATECONSUMER key groupname consumername

# 删除消费者组中指定的消费者
XGROUP DELCONSUMER key groupname consumername
```
##### 8.3.1.1. 从消费者组读取消息
```shell
XREADGROUP GROUP group consumer [COUNT count] [BLOCK milliseconds] [NOACK] STREAMS key [key...] ID [ID...]
```
- group ：消费者组名称
- consumer：消费者名称，如果消费者不存在，会自动创建一个消费者
- count： 本次查询的最大数量
- BLOCK milliseconds：当没有消息时的最长等待时间
- NOACK：无需手动ACK，获取到消息之后自动确认
- STREAM key：指定队列名称
- ID：获取消息的起始ID：
    - ">"：从下一个未消费的消息开始
    - 其他：根据指定id从pending-list中获取已消费但未确认的消息，例如0，是从pending-list中的第一个消息开始
#### 8.3.2. 优缺点
优点：
- 支持多消费者消费，加快消费时间
- 支持阻塞读取
- 支持消息回溯
- 没有漏读风险，使用pending-list解决
- 有消息确认机制，确保每条消息都被读取至少一次
缺点：
- 只支持消费者确认，不支持生产者确认
- 没有事务机制
- 无法确保多消费者下的事务的有序性
- 能持久化但是依赖的是redis的持久化，redis的持久化是有丢失风险的