## 基础类型
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
