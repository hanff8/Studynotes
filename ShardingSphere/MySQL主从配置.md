命令行进入docker 容器 terminal
```shell
docker exec -it  mysql_master env LANG=C.UTF-8 /bin/bash
```


主机中创建Slave用户：
```mysql
-- 创建slave用户
CREATE USER 'slave'@'%';
-- 设置密码
ALTER USER 'slave'@'%' IDENTIFIED WITH mysql_native_password BY '123456';
-- 授予复制权限
GRANT REPLICATION SLAVE ON *.* TO 'slave'@'%';
-- 刷新权限
FLUSH PRIVILEGES; 

-- 获取binlog的状态
show master status;
```

从机配置主从关系：

```mysql
CHANGE MASTER TO MASTER_HOST='192.168.0.110',
MASTER_USER='slave',MASTER_PASSWORD='123456',MASTER_PORT=3306,
MASTER_LOG_FILE='binlog.000003',MASTER_LOG_POS=1051;
```


`binLog格式说明`:
- binlog-format = STATEMENT：日志激励的是主机数据库的`写指令`，性能高，但是now()之类的函数以及获取系统参数的操作会出现主从数据不同步的问题；
- binlog-format = ROW（默认）：日志记录的是主从数据库的`写后的数据`，批量操作时性能较差，解决`now()`或者`user()`或者`@@hostname`等操作在主从机器上不一致的问题；
- biglog_format = MIXED：是以上两种level的混合使用，有函数用ROW，没有函数用STATEMENT，但是无法识别系统变量


## 1. 事务
为了保证主从库的事务一致性，避免跨服务的分布式事务，ShardingSphere-JDBC的`主从模型中，事务的数据读写均用主库`
`注意`，在JUnit环境下的 `@Transactional`注解，默认情况下就会对事务进行回滚（即使在没加注解`@Rollback`，也会对事务回滚

```

```

## 2. 负载均衡

### 2.1. for、foreach、while的效率问题

看集合的上限，
foreach 是遍历集合中的每一个元素

while 需要判断条件，这里存在性能损耗

for 如果知道操作次数，可能是更好的选择

  
ShardingSphere是一个开源的分布式数据库中间件，它提供了一系列数据库分片（Sharding）和读写分离（Replication）的解决方案，以帮助应用程序实现高可用性和扩展性。在ShardingSphere中，你可以使用不同的方式来执行读写分离，如foreach、while、for等，它们的效率取决于具体的使用情况和编程语言。

1. **foreach循环**：
    
    - foreach循环通常用于迭代集合或数组中的元素。在读写分离的场景中，你可以使用foreach来遍历一组数据库连接或数据源，然后根据业务逻辑选择合适的连接执行读操作或写操作。
    - 效率：foreach循环的效率取决于集合或数组的大小，对于小型集合，foreach通常是一个简单且可读性良好的选择。然而，在处理大型数据集时，foreach可能会导致性能下降，因为它需要逐个遍历元素。
2. **while循环**：
    
    - while循环通常用于在满足某个条件时重复执行代码块。在读写分离的场景中，你可以使用while循环来不断尝试从一组数据库连接或数据源中选择合适的连接，并执行读写操作，直到满足特定条件。
    - 效率：while循环的效率取决于循环条件和循环体中的代码。如果循环条件频繁评估为true，可能会导致性能开销。在某些情况下，使用while循环可以实现更灵活的逻辑控制，但需要谨慎设计循环条件，以避免无限循环。
3. **for循环**：
    
    - for循环通常用于已知循环次数的情况，它可以指定初始条件、终止条件和每次迭代的步骤。在读写分离的场景中，你可以使用for循环来执行固定次数的读写操作。
    - 效率：for循环的效率通常比while循环更高，因为它的循环次数是固定的，不需要频繁评估条件。如果你知道要执行的读写操作次数，使用for循环可能是一个更好的选择。

总之，选择使用哪种循环方式取决于具体的需求和场景。在读写分离中，循环的效率通常不会成为主要的性能瓶颈，更重要的是如何有效地管理数据库连接、选择合适的数据源和优化SQL查询。在实际编码中，可以根据代码的逻辑和需求来选择最合适的循环方式。