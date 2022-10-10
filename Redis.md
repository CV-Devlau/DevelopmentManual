# Redis 速记

## NoSql 特点

解耦

1. 方便扩展 （数据之间没有关系）
2. 大数据量高性能 
3. 数据类型多样型（不需要事先设计数据库！随取随用）

## NoSQL的四大分类

**KV键值对：**

- Redis

**文档型数据库（ bson格式 ）**

- MongoDB
  - 基于分布式文件存储的数据库，C++编写
  - 介于关系型数据库和非关系型数据库中间的产品 !   MongoDB是非关系型数据库中最丰富的，最像关系型数据库的，
- ConthDB

**列存储**

- HBase
- 分布式文件系统

**图形关系数据库**

- 不是存图形的，放的是关系，比如：朋友圈社交网络，广告推荐
- Neo4j，infoGrid

## Redis入门

### 概述

> Redis是什么

- Redis （Remote Dictionary Server）,即远程字典服务！

- 是一个开源的使用ANSI C语言编写、支持网路、可基于内存亦可持久化的日志型、Key-Value数据库，并提供了多种语言的API。

- redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且再次基础上实现了master-slave（主从）同步。

- 当下最热门的NoSQL技术之一！也被称为结构化数据库！

> Redis 能干嘛 ?

- 内存存储、持久化，内存是断电即失，所以需要持久化（rdb、aof）
- 效率高，可以用于高速缓存
- 发布订阅系统
- 地图信息分析
- 计时器、计数器（浏览量！）
- 。。。

> 特性

- 多样的数据类型
- 持久化
- 集群
- 事务
- ......

> 资源

官网：https://redis.io/

中文官网：http://www.redis.cn/

Github：http://github.com/redis/redis

下载地址:

![image-20210331113058314](https://i.loli.net/2021/03/31/9f6GUqh5cioZ83S.png)

### Window安装

https://github.com/tporadowski/redis/releases

### Linux安装

1. 官网下载安装包
   
   - `redis-6.2.1.tar.gz`

2. 在 opt 目录解压安装包 
- `tar -zxvf redis-6.2.1.tar.gz`
3. 进入加压后的文件，可以看见redis的配置文件

4. 基本环境安装
   
   - `yum install gcc-c++`  安装 gcc
   
   - `make`
   
   - `make install`
     
     ```bash
     [root@localhost redis-6.2.1]# make install
     cd src && make install
     make[1]: 进入目录“/opt/redis-6.2.1/src”
         CC Makefile.dep
     make[1]: 离开目录“/opt/redis-6.2.1/src”
     make[1]: 进入目录“/opt/redis-6.2.1/src”
     
     Hint: It's a good idea to run 'make test' ;)
     
         INSTALL install
         INSTALL install
         INSTALL install
     make[1]: 离开目录“/opt/redis-6.2.1/src”
     ```

5. redis默认安装路径 `/usr/local/bin`

6. 将redis配置文件拷贝过来
   
   ```bash
   [root@localhost bin]# mkdir myconfig
   [root@localhost bin]# cp /opt/redis-6.2.1/redis.conf myconfig
   [root@localhost bin]# ls
   myconfig  redis-benchmark  redis-check-aof  redis-check-rdb  redis-cli  redis-sentinel  redis-server
   [root@localhost bin]# cd myconfig
   [root@localhost myconfig]# ls
   redis.conf
   ```

7. redis 默认不是后台运行，需要修改配置文件
   
   ```bash
   # 将配置文件中的 daemonize 改为yes
   daemonize yes
   ```

#### 启动

- 启动 redis 服务器  `redis-server myconfig/redis.conf `
  
  ```bash
  [root@localhost bin]# redis-server myconfig/redis.conf 
  ```

- 启动客户端  `redis-cli -p 6379`
  
  ```bash
  [root@localhost bin]# redis-cli -p 6379
  ```

- 测试启动是否成功  ` ping`
  
  ```bash
  127.0.0.1:6379> ping
  PONG
  ```

```bash
127.0.0.1:6379> set name lau 
OK
127.0.0.1:6379> get lau
(nil)
127.0.0.1:6379> get name
"lau"
127.0.0.1:6379> keys *
1) "name"
```

#### 查看进程

 查看 redis 进程  `ps -ef|grep redis`

```bash
[root@localhost bin]# ps -ef|grep redis
root      25862      1  0 11:17 ?        00:00:30 redis-server 127.0.0.1:6379
root      28031   5257  0 14:46 pts/0    00:00:00 redis-cli -p 6379
root      28062  28032  0 14:46 pts/1    00:00:00 grep --color=auto redis
```

#### 关闭服务

关闭 redis 服务 `shutdown`

```bash
127.0.0.1:6379> shutdown
not connected> exit
[root@localhost bin]# ps -ef|grep redis
root      28445   5257  0 15:19 pts/0    00:00:00 grep --color=auto redis
```

### 测试性能

redis-benchmark 是一个压力测试工具！

| 序号  | 选项        | 选项                                | 默认值       |
|:--- |:--------- |:--------------------------------- |:--------- |
| 1   | **-h**    | 指定服务器主机名                          | 127.0.0.1 |
| 2   | **-p**    | 指定服务器端口                           | 6379      |
| 3   | **-s**    | 指定服务器 socket                      |           |
| 4   | **-c**    | 指定并发连接数                           | 50        |
| 5   | **-n**    | 指定请求数                             | 10000     |
| 6   | **-d**    | 以字节的形式指定 SET/GET 值的数据大小           | 3         |
| 7   | **-k**    | 1=keep alive 0=reconnect          | 1         |
| 8   | **-r**    | SET/GET/INCR 使用随机 key, SADD 使用随机值 |           |
| 9   | **-P**    | 通过管道传输 <numreq> 请求                | 1         |
| 10  | **-q**    | 强制退出 redis。仅显示 query/sec 值        |           |
| 11  | **--csv** | 以 CSV 格式输出                        |           |
| 12  | **-l**    | 生成循环，永久执行测试                       |           |
| 13  | **-t**    | 仅运行以逗号分隔的测试命令列表。                  |           |
| 14  | **-I**    | Idle 模式。仅打开 N 个 idle 连接并等待。       |           |

```bash
[root@localhost bin]# redis-benchmark -h localhost -c 100 -n 10000
====== PING_INLINE ======                                         
  10000 requests completed in 0.12 seconds  # 10w请求
  100 parallel clients   # 100并发
  3 bytes payload  # 3字节
  keep alive: 1 
  host configuration "save": 3600 1 300 100 60 10000
  host configuration "appendonly": no
  multi-thread: no
```

### 基础知识

- 默认端口号 6379

- redis 默认16个数据库
  
  - ```bash
    databases 16 #配置文件中的数据库数量
    ```

- 默认使用第 0 个

- redis 6 之前是单线程的 6开始支持多线程

#### 切换数据库

`select 3`

```bash
127.0.0.1:6379> select 3 #切换数据库
 OK
```

#### 获取数据库大小

 `dbsize`

```bash
127.0.0.1:6379[3]> dbsize  #查看数据库大小
(integer) 0
127.0.0.1:6379[3]> set name lau
OK
127.0.0.1:6379[3]> dbsize
(integer) 1
```

#### 清理当前数据库

`flushdb`

```bash
127.0.0.1:6379[3]> flushdb
OK
127.0.0.1:6379[3]> dbsize
(integer) 0
```

#### 清理全部数据库

`flushall`

## 五大数据类型

### Redis-Key

#### 添加 key-value

添加key-value `set key value`

```bash
127.0.0.1:6379[3]> set name lau # set key
OK
```

#### 获取值

获取value `get key`

```bash
127.0.0.1:6379[3]> get name
"lau"
```

#### 追加值

追加值 `append key value`

- 如果当前 key 不存在，相当于 set

```bash
127.0.0.1:6379[3]> append name "append"
(integer) 9
127.0.0.1:6379[3]> get name
"lauappend"
```

#### 查看所有key

查看所有key `keys *`

```bash
127.0.0.1:6379[3]> keys *
1) "name"
```

#### 查询key存不存在

查询key存不存在 `exists key`

```bash
127.0.0.1:6379[3]> exists name
(integer) 1
```

#### 移动key到其他数据库

移动key到其他数据库  `move key 1`  

将name移动到数据库 1

#### 过期时间

设置过期时间 `expire key 10`

```bash
127.0.0.1:6379[3]> expire name 10
(integer) 1
```

#### 剩余时间

查看剩余时间 `ttl key `  

- -1 表示没有设置过期时间

- 返回 -2 表示已经过期

```bash
127.0.0.1:6379[3]> ttl name
(integer) 2
127.0.0.1:6379[3]> ttl name
(integer) -2
```

#### 获取类型

查看key的类型 `type key`

```bash
127.0.0.1:6379[3]> type age
string
```

#### 命令大全

命令大全： http://www.redis.cn/commands.html

### String （字符串）

#### 获取字符串长度

`strlen key` 获取字符串长度

```bash
127.0.0.1:6379[3]> strlen name
(integer) 10
```

#### 自增1

`incr key` value 值减一  ++

- 如果 key 不存在 则添加key 执行后值为 1

```bash
127.0.0.1:6379[3]> set num 1
OK
127.0.0.1:6379[3]> incr num
(integer) 2
127.0.0.1:6379[3]> get num
"2"
```

#### 自减1

`decr key` value 值减一  --

- 如果key 不存在则创建key 执行命令后值为 -1

```bash
127.0.0.1:6379[3]> decr num
(integer) 1
127.0.0.1:6379[3]> get num
"1"
```

#### 加等于

加等于 `incrby key num `   i += num

```bash
127.0.0.1:6379[3]> incrby num 10
(integer) 11
127.0.0.1:6379[3]> get num
"11"
```

#### 减等于

`decrby key num ` 减等于  i -= num

```bash
127.0.0.1:6379[3]> decrby num 5
(integer) 6
127.0.0.1:6379[3]> get num
"6"
```

#### 截取字符串

截取字符串 `getrange key start end`   [ start , end ]

```bash
127.0.0.1:6379[3]> get range1
"helloword"
127.0.0.1:6379[3]> getrange range1 0 3  # [ 0 , 3 ]
"hell"
127.0.0.1:6379[3]> getrange range1 0 -1 # 输出全部
"helloword"
```

#### 替换

替换字符串 `setrange key index value `  从 index 开始覆盖为 value

```bash
127.0.0.1:6379[3]> setrange range1 1 xx #将range1 从 第1个字符开始覆盖为 xx
(integer) 9
127.0.0.1:6379[3]> get range1
"hxxloword"
```

#### Set 并设置过期时间

 `setex key seconds value` （ set with expire ） set一个 并设置 过期时间

```bash
127.0.0.1:6379[3]> setex name 10 lau  #设置 name 的值 为lau ，10秒后过期
OK
127.0.0.1:6379[3]> ttl name
(integer) -2
```

#### 没有就创建

`setnx key value`  如果 key 不存在就创建

- 如果存在返回 0 ，即创建失败

```bash
127.0.0.1:6379[3]> setnx name ll
(integer) 1
127.0.0.1:6379[3]> get name
"ll"
127.0.0.1:6379[3]> setnx name 22
(integer) 0
```

#### 批量设置

`mset k1 v1 k2 v2`  同时设置多个值

```bash
127.0.0.1:6379[3]> mset k1 v1 k2 v2 
OK
127.0.0.1:6379[3]> keys *
1) "k2"
2) "k1"
```

#### 批量获取

`mget k1 k2` 同时获取多个值

```bash
127.0.0.1:6379[3]> mget k1 k2  # 同时获取 k1 k2
1) "v1"
2) "v2"
127.0.0.1:6379[3]> 
```

#### 如果不存在批量设置

`mgetnx k1 v1 k2 v2`  ==原子性操作：成功都成功，失败都失败==   都不存在才能创建成功

```bash
127.0.0.1:6379[3]> msetnx k1 v1 k4 v4  # 同时设置k1 k4 ,k1已存在，设置失败
(integer) 0
127.0.0.1:6379[3]> get k4
(nil)
```

#### 对象

```bash
set user:1 {name:lau,age,11} # 设置一个user:1 对象 值为 json字符串来保存一个对象

# key的巧妙设计 user:{id}:{filed}
127.0.0.1:6379[3]> mset user:1name lau user:1age 18
OK
127.0.0.1:6379[3]> mget user:1name user:1age
1) "lau"
2) "18"
```

#### 获取并设置

`getset key value`  先获取当前的value ，然后再设置为新的value

- 如果没有会返回 nil

```bash
127.0.0.1:6379[3]> getset db redis
(nil)
127.0.0.1:6379[3]> get db
"redis"
127.0.0.1:6379[3]> getset db mongodb
"redis"
127.0.0.1:6379[3]> get db
"mongodb"
```

#### 使用场景

value除了是字符串还可以是数字

- 计数器
- 统计多单位的数量
- 粉丝数
- 对象缓存存储

### List

- 双向链表
- 可以实现队列（lpush、rpop）、栈（lpush、lpop）

#### 插入列表头部

`lpush key value`   将一个或多个值插入列表头部（左边）

#### 区间获取值

`lrange key 0 -1`  根据区间获取值 [ ] ，[0,-1] 表示全部值

- 左右闭区间 [ ,]
- 从左往右输出

#### 插入列表尾部

`rpush key value` 将一个或多个值插入列表尾部（右边）

#### 移除 头部(左)值

`lpop key`

#### 移除尾部值(右)

`rpop key`

#### 下标获取值

`lindex key index`  通过下标获取 list中的某一个值

#### 长度

`llen key` 获取长度

#### 移除指定值

`lrem key count value`  移除 list 中指定个数的value，精确匹配

#### 截取

`ltrim key index end`  通过下标截取指定长度

- 会改变原来的 list ，只留下截取的部分

#### 移除并移动

`rpoploush list newlist`  移除列表最后一个元素，将其移动到新的列表

#### 更新值

`lset list index value`   将下标为 index 的值更新为 value

- 下标不存在会报错

#### 插入

`linsert key brfore/after value1 value`  将 value 插入到 value1前面或后面

### Set

- 无序、不重复

#### 添加

`sadd key value` 添加一个值

#### 查看指定Set值

`smembers key`  查看指定Set的所有值

#### 值是否存在

`sismember key value` 判断 Set 中是否有 value

#### 内容个数

`scard key`  获取Set中值的数量

#### 移除元素

`srem key value`  删除指定 Set 中的值

#### 随机元素

`srandmember key`  从指定Set中随机抽取一个元素

`srandmember ket count`  从指定Set中随机抽取指定数量的元素

#### 随机删除

`spop key ` 从指定Set 中随机删除一个元素

#### 移动

`smove key newkey value `  将 key 中的指定值 移动到 newkey 中

#### 差集

`sdiff key1 [key...]`    多个 Set 之间的差集

#### 交集

`sinter key1 [key...]`   多个 Set 之间的交集

#### 并集

``sinter key1 [key...]`   多个 Set 之间的差集

- 共同关注

### Hash

- `key field value（key map( field value )）`
  - field 字段

#### 设置值

`hset key field value`

#### 获取值

`hget key field ` 从hash中获取某个字段的value

#### 获取多个

`hmget key field1 field2`

#### 获取全部

`hgetall key`  获取全部数据

#### 删除

`hdel key field`   删除某个字段

#### 长度

`hlen key`  获取hash的长度

#### 判断是否存在

`hexists key field`

#### 获取所有字段

`hkeys key`

#### 获取所有值

`hvals key`

#### 自增

`hincr key field`

#### 指定增量

`hinceby key field num`

#### 不存在设置

`hsetnx key field value`

### Zset

- 有序的set
- 跳跃链表

#### 添加

`zadd key score value [score2 value2]`  添加一个值，可以添加多个

#### 按索引 显示值

`zrange key min max `  按索引从小到大显示指定区间的值

- `zrange key 0 -1 `  按索引从小到大显示所有值

`zrevrange key min max` 按索引从大到小显示指定区间的值 

#### 从 score 小到大显示

`zrangebyscore key min max [withscores]`  从大到小显示全部数据

`zrangebyscore key min max withscores`从大到小显示全部数据并且显示 score

#### 按score 从大到小

``zrevrangebyscore key min max`   

#### 移除元素

`zrem key value`  移除有序集合中的指定元素

#### 获取元素个数

`zcard key`   获取有序集合的元素个数

#### 区间元素个数

`zcount key min max `  获取指定区间 之间有多少个值

#### 案例思路

- 班级成绩表
- 工资表排序
- 带权重
- 排行榜

## 三种特殊类型

### geospatial 地理位置

#### 添加位置

==geoadd==

`geoadd key 经度 维度 名称`

#### 获取经纬度

`geopos key 名称`

#### 直线距离

==geodist==

`geodist key 名称1 名称2 [单位]`

单位：

- **m** 米
- **km** 千米
- **mi** 英里
- **ft** 英尺

#### 给定经纬度半径内元素

以给定的经纬度为中心，找出指定半径内的元素

==georadiu==

`georadiu key 经度 维度 半径 单位 [withdist] [withcoord] [count num]` 

- `withdist` 参数显示直线距离
- `withcoord` 参数显示经纬度
- `count num`  指定显示数量

#### 指定元素半径内元素

==georadiusbymember==

`georadiusbymember key 名称 半径 单位`   找出指定元素 周围的其他元素

#### 返回元素经纬度一维字符串

==geohash==     将经纬度转换为11位的字符串

`geohash key 名称1 名称2 `

#### 删除元素

==GEO底层使用Zset 可以使用zset命令来操作geo==

`zrem key 名称`

### Hyperloglog

- redis 2.8.9 更新了Hyperloglog 数据结构
- 基数统计的算法
- 优点： 占用内存固定 , 2^64 不同的元素数据，只需要12kb内存
- 使用场景
  - 网页 UA 独立访问数
- 0.81% 的错误率

#### 添加

==pfadd==

`pfadd key element[e2 e3...]`   创建一组元素可以重复

统计

==pfcount==

`pfcount key`         统计key 中基数数量

#### 合并

==pfmerge==

`pfmerge newkey key1 key2`   合并key1 key2 并集操作

### Bitmaps

- 位存储
- 只有0 和 1两个状态
- 使用场景：登陆、未登录！活跃、不活跃！ 打卡

案例：记录周一到周日的打卡

#### 设置

setbit key offset value

### 查看

==getbit key offset==

#### 统计

`bitcount key`

## 事务

==redis 事务的本质： 一组命令的集合！==

- 一个事务中的所有命令都会被串行化，事务执行过程中，会按照顺序执行
- 一次性、顺序性、排他性！
- 没有隔离级别的概念
- 单条命令是原子性，事务不保证原子性
- 命令添加到事务后不会被立即执行，只有发起执行命令时候才会执行 exec
- 开启事务后，命令将会放到队列中，发起执行后，按顺序执行

redis 事务过程：

- 开启事务 （multi）
- 命令入队
- 执行事务（exec）

### 开启事务

==multi==

### 执行事务

==exec==

### 放弃事务

==discard==  放弃事务，队列中所有命令都不会执行

### 事务错误

- 编译时异常 （参数错误、命令错误等 编译时期的错误）
  - 事务不会执行
- 运行时错误 
  - 错误的命令提示错误，不影响其他命令

### 监控

#### 悲观锁

- 很悲观，认为什么时候都会出问题，无论做什么都会加锁

#### 乐观锁

- 很乐观，认为什么时候都不会出问题，所以不会上所！更新数据的时候判断一下，是否有人修改国数据
  - 例如使用 version 字段 ，先获取 version ，更新时比较一下，看看是否被修改

#### 监视

==watch==

`watch key [key ...]`  监视 这个key

- 如果事务执行之前  这个key的值被修改了 ，事务就会执行失败

#### 解除监视

==unwatch==

## 整合springboot

- springboot 2.x 原来的 jedis 被替换为 lettuce
  - 采用 netty ，实例可以在多个线程中进行共享

### 导入依赖

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

### 配置

#### 自动配置类

- springboot 所有的配置类，都有一个自动配置类  `RedisAutoConfiguration`
- 自动配置类中都会绑定一个配置文件 `RedisProperties`

```java
@Configuration(proxyBeanMethods = false)
@ConditionalOnClass(RedisOperations.class)
@EnableConfigurationProperties(RedisProperties.class)  //配置文件
@Import({ LettuceConnectionConfiguration.class, JedisConnectionConfiguration.class })
public class RedisAutoConfiguration {

    @Bean
    @ConditionalOnMissingBean(name = "redisTemplate")  //如果这个Bean不存，这个类就生效
    //可以自定义redisTemplate 替换这个
    @ConditionalOnSingleCandidate(RedisConnectionFactory.class)
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        // 默认的 没有过多的设置
        RedisTemplate<Object, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }

    @Bean
    @ConditionalOnMissingBean
    @ConditionalOnSingleCandidate(RedisConnectionFactory.class)
    public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory) {
        StringRedisTemplate template = new StringRedisTemplate();
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }

}
```

#### 配置

```properties
# 地址
spring.redis.host=127.0.0.1  
# 端口号
spring.redis.port=6379
```

#### 导入bean

```java
@Autowired
private RedisTemplate redisTemplate;
```

#### 使用

需要使用 redisTemplate

##### **数据操作**

redisTemplate.opsFor数据类型.

- `opsForValue` 操作字符串
- `opsForList`   操作list
- `opsForSet`  操作set
- `opsForHash` 操作hash
- ...... 

```java
redisTemplate.opsForValue().set("name","dsd");
```

> 如果直接存储对象，对象需要序列化

##### 获取链接

```java
RedisConnection connection = redisTemplate.getConnectionFactory().getConnection();
connection.flushDb();
```

### 自定义redisTemplate

- 默认的 redisTemplate 使用 jdk 序列化 会转义字符串

![image-20210418210320706](https://i.loli.net/2021/04/18/8VERjPg7NXxD9ac.png)

```java
/**
 * @Author: Lau
 * @Date: 2021/4/18 21:39
 * @Description: Redis配置类
 */
@Configuration
public class RedisConfig {
    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        //<String,Object>
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(redisConnectionFactory);
        //配置序列化
        Jackson2JsonRedisSerializer<Object> jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer<>(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        //设置带有类型的json
        om.activateDefaultTyping(LaissezFaireSubTypeValidator.instance, ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
        //Stirng 序列化
        StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();
        //设置key采用String的序列化方式
        template.setKeySerializer(stringRedisSerializer);
        template.setHashKeySerializer(stringRedisSerializer);
        //value采用jackson序列化
        template.setValueSerializer(jackson2JsonRedisSerializer);
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        template.afterPropertiesSet();
        return template;
    }

}
```

## Redis配置文件

### 单位

![image-20210421201632746](https://i.loli.net/2021/04/21/NB4FItvzrpgqs9E.png)

- 配置文件 unit单位 对大小写不敏感

### 包含 **INCLUDES**

- 可以包含其他配置文件

![image-20210421201648563](https://i.loli.net/2021/04/21/sOoTERIeLArb6PW.png)

### 网络 NETWORK

![image-20210421202132655](https://i.loli.net/2021/04/21/LjV5Aanv8oqcfiI.png)

```bash
bind 127.0.0.1 #绑定的ip
protected-mode yes # 保护模式
port 6379 # 端口号
```

### 通用 GENERAL

![image-20210421202408441](https://i.loli.net/2021/04/21/PdvtGK3rF94786R.png)

```bash
daemonize yes # 守护进程（后台运行）  默认是no
pidfile /var/run/redis_6379.pid  # 如果后台运行 指定的pid文件

# Specify the server verbosity level.
# This can be one of:
# debug (a lot of information, useful for development/testing)
# verbose (many rarely useful info, but not a mess like the debug level)
# notice (moderately verbose, what you want in production probably)  生产
# warning (only very important / critical messages are logged)  警告
loglevel notice  # 日志级别
logfile ""  #日志文件位置
databases 16 # 数据库数量 默认16
always-show-logo yes # 是否总是显示LOGO
```

### 快照 SNAPSHOTTING

![image-20210421202840833](https://i.loli.net/2021/04/21/k5e1VJ7ru24jifq.png)

持久化，在规定的时间内，执行多少次操作 就会持久化到文件 .rdb .aof

```bash
save 3600 1
save 300 100
# 60秒内 有10000个key 进行了修改，就进行持久化
save 60 10000

stop-writes-on-bgsave-error yes # 如果保存出错，停止写入

rdbcompression yes # 是否压缩 rdb 文件 ，会占用cpu资源

rdbchecksum yes #保存edb文件时，进行错误的检查校验

dir ./ # rdb文件保存目录
```

### 复制

### 安全 SECURITY

```bash
requirepass password  #设置密码
```

#### 获取密码

`config get requirepass`

#### 设置密码

`config set requirepass ""`

#### 认证密码

`auth password`

### 限制 CLIENTS

```bash
maxclients 10000 # 链接redis 最大客户端数量

maxmemory <bytes> # redis 最大的内存容量

maxmemory-policy noevction # 内存到达上限后的处理策略
```

| 规则名称            | 规则说明                      |
| --------------- | ------------------------- |
| volatile-lru    | 使用LRU算法删除一个键（只对设置了生存时间的键） |
| allkeys-lru     | 使用LRU算法删除一个键              |
| volatile-random | 随机删除一个键（只对设置了生存时间的键）      |
| allkeys-random  | 随机删除一个键                   |
| volatile-ttl    | 删除生存时间最近的一个键              |
| noeviction      | 不删除键，只返回错误                |

LRU算法，least RecentlyUsed，最近最少使用算法。也就是说默认删除最近最少使用的键。

但是一定要注意一点！redis中并不会准确的删除所有键中最近最少使用的键，而是随机抽取3个键，删除这三个键中最近最少使用的键。

那么3这个数字也是可以设置的，对应位置是配置文件中的maxmeory-samples.

### APPEND ONLY MODE

```bash
appendonly no  # 默认不开启aof 默认使用rdb持久化
appendfilename "appendonly.aof" # 持久化文件

# appendfsync always  # 每次修改都会 sync 消耗性能
appendfsync everysec  # 每秒执行一次 sync 可能会丢失1秒的数据
# appendfsync no      # 不执行 sync
```

## 持久化

如果同时开启两种持久化方式，优先使用AOF方式

### RDB持久化

![image-20210423185725163](https://i.loli.net/2021/04/23/2QZ3qU76HxgBPjV.png)

在指定的时间间隔内将内存中的数据集快速写入磁盘，也就是Snapshot 快照，他恢复时是将快照文件直接读到内存。

Redis会单独创建（fork）一个子进程来进行持久化，会先将数据写入一个临时文件中，待持久化过程结束，再替换上次的持久化文件。

主进程不进行io操作，确保极高的性能。适合大规模的数据恢复，对数据恢复完整性不敏感，rdb 方式比aof方式更高效，但是最后一次持久化可能会丢失数据。默认使用 RDB方式

默认保存文件 ： dump.rdb

```bash
save 3600 1
save 300 100
# 60秒内 有10000个key 进行了修改，就进行持久化
save 60 10000

stop-writes-on-bgsave-error yes # 如果保存出错，停止写入

rdbcompression yes # 是否压缩 rdb 文件 ，会占用cpu资源

rdbchecksum yes #保存edb文件时，进行错误的检查校验

dir ./ # rdb文件保存目录
```

#### 触发规则

1. 满足 save 规则 ，自动触发 rdb规则
2. 退出 redis 会自动产生 rdb文件
3. flushall 命令 自动生成一个空的 rdb文件
4. 使用 bgsave 手动保存

#### 恢复数据

1. 将 rdb 文件放到 redis 启动目录， redis 启动时会自动检查dump.rdb 恢复其中的数据

2. 查看 目录
   
   ```bash
   127.0.0.1:6379> config get dir
   1) "dir"
   2) "/usr/local/bin"   
   ```

#### 优点

1. 适合大规模的数据恢复
2. 对数据的完整性要求不高

#### 缺点

1. 需要一定的时间间隔进行操作，如果意外宕机了，最后一次修改的数据就没有了
2. fork 进程的时候，会占用一定的内存

### AOF持久化

AOF 方式 记录redis 所有写操作

![image-20210423190052835](https://i.loli.net/2021/04/23/pHlfFJ61SbZnTYQ.png)

默认是不开启的  需要将 `appendonly yes` 改为yes

```bash
appendonly no  # 默认不开启aof 默认使用rdb持久化
appendfilename "appendonly.aof" # 持久化文件

# appendfsync always  # 每次修改都会 sync 消耗性能
appendfsync everysec  # 每秒执行一次 sync 可能会丢失1秒的数据
# appendfsync no      # 不执行 sync
```

#### 修复

`redis-check-aof  --fix 文件名.aof`

如果 aof 文件有错误，这时候无法启动 redis，需要使用检测文件修复 aof文件

#### 重写

![image-20210423191752784](https://i.loli.net/2021/04/23/mpkbHSOJ7WyfM6j.png)

如果 aof 文件大于 64m ， redis  会 fork 一个新的进程来将aof文件进行重写

#### 优点

1. 每次修改都同步 ，文件完整性更好
2. 每秒同步，可能会丢失一秒的数据
3. 从不同步，效率最高

#### 缺点

1. aof 文件 远远大于 rdb ，修复速度比 rdb 慢
2. 循行效率比 rdb 慢

## Redis 发布订阅

Redis 发布订阅（pub/sub）是一种**消息通信模式**：发送者发送消息，订阅者接收消息

- 公众号、微博、关注系统

![image-20210423192213384](https://i.loli.net/2021/04/23/lWkKrPtomQxUDYX.png)

### 订阅

`subscribe 频道名 `  订阅一个频道

### 发布

`publish 频道 message`    向频道发布消息

### 使用场景

1. 实时消息
2. 聊天室
3. 订阅、关注系统

## 主从复制