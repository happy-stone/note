### 开始之前，推荐阅读 [An introduction to Redis data types and abstractions](https://redis.io/topics/data-types-intro) 或者 [Redis 数据类型介绍](http://redis.cn/topics/data-types-intro.html) ###

<h1 id='key'><a href='#key'> key </a> </h1>


| 命令      | 格式                                      | 说明                                                         |
| --------- | ----------------------------------------- | ------------------------------------------------------------ |
|   SELECT |  select db | 切换数据库  |
| KEYS | keys pattern |  获取与 pattern 匹配的 keys |
| EXISTS    | EXISTS key                                | key 是否存在                                                 |
| DEL       | DEL k1 k2 k3                              | 删除 key（会产生阻塞）                                       |
| UNLINK    | UNLINK k1 k2 k3                           | 在 keys 里面删除 k1 k2 k3,<br />正真删除有另外的线程执行，不阻塞 |
| DUMP      | DUMP key                                  | 返回 key 序列话的值                                          |
| EXPIRE    | EXPIRE key second                         | 设置过期时间                                                 |
| PEXPIRE    | EXPIRE key milliseconds      | 设置过期时间  （单位是毫秒）                                               |
| EXPIREAT  | EXPIREAT key timestamp                    | 指定时间戳，设置剩余时间                                     |
| KEYS      | KEYS pattern                              | 返回符合条件的 key 列表                                      |
| TTL       | TTL key                                   | 返回剩余时间                                                 |
| PTTL      | PTTL key                                  | 返回 毫秒 的剩余时间                                         |
| TYPE      | TYPE key                                  | 返回数据类型                                                 |
| RENAME    | RENAME key new_key                        | 重命名                                                       |
| PERSIST   | PERSIST key                               | 删除剩余时间，是对象变成 持久、不过期                        |
| TOUCH     | TOUCH k1 k2                               | 访问 k， 修改 k 的最后访问时间                               |
| RANDOMKEY | RANDOMKEY                                 | 随机返回一个 key                                             |
| MOVE      | MOVE key db                               | 数据移到 另一个 db 中                                        |
| WAIT      | WAIT numslaves timeout                    | 阻塞当前客户端，直到指定的 salves 执行完成<br />最多等待 timeout 秒 |
| SCAN      | SCAN cursor [MATCH pattern] [COUNT count] | 扫描 所有的键， 和 keys 一次返回所有 key 不同，<br />scan 能指定返回的值，和游标，多次返回。 |
| DBSIZE |  DBSIZE | 当前数据库包含多少键值对  |
| TYPE |  TYPE key | key 的类型  |
| FLUSHDB |  FLUSHDB [ async ] | 清空当前数据库 <br /> async 异步执行 |
| FLUSHALL |  FLUSHALL [ async ] | 清空所有数据库 <br /> async 异步执行 |
| SWAPDB |  SWAPDB  index index | 交换数据库 |


<h1 id='string-and-num'><a href='#string-and-num'> 字符串和数字 </a> </h1>
​    基本常用操作，其中对 bit 的操作单独放在下一节中介绍。

| 命令        | 格式                                 | 说明                                                         |
| ----------- | ------------------------------------ | ------------------------------------------------------------ |
| SET         | set k v  [ EX 秒 / PX  毫秒] [NX XX] | EX 设置过期时间 单位 秒<br />PX 单位 毫秒， EX、PX 二选一<br />NX 如果 k 不存在执行命令<br />XX 如果 k 存在，执行命令 |
| SETEX       | set k EX v                           | 设置带有过期时间（单位 秒）的命令                            |
| PSETEX      | pset k pX v                          | 设置带有过期时间（单位 毫秒）的命令                          |
| SETNX       | Set k v                              | 如果 k 不存在，执行命令                                      |
| SETRANGE    | setrange k offset v                  | 对于 k 的值，偏移 offset 位之后设置新值，如果不存在 填充 `\x00`。 |
| MSET        | mset k1 v1 k2 v2                     | 同时设置多个值                                               |
| MSETNX      | msetnx k1 v1 k2 v2                   | 如果 多个键 都不存在，则执行操作                             |
| MGET        | mget k1 k2                           | 查询多个 键值                                                |
| GET         | get k                                | 查询 k 的值，如果不存在，返回 nil                            |
| GETRANGE    | getrange k start end                 | 查询 k， 在 [start, end) 的值                                |
| STRLEN      | strlen key                           | 查询长度                                                     |
| APPEND      | append key value                     | 原值添加新值                                                 |
| BITCOUNT    | bitcount k [s e]                     | k 的 bit 数                                                  |
| INCRBY      | INCRBY key incerment                 | 如果原值是 数字，则增加 incerment                            |
| INCR        | incr k                               | INCRBY key  1                                                |
| INCRBYFLOAT | incrbyfloat k incerment              | 增加浮点数（如果减，就执行增加 负数）                        |
| DESCBY      | descby key decrement                 | 如果原值是 数字，则减少 decrement                            |
| DESC        | desc key                             | descby key 1                                                 |

<h1 id='BitMap'><a href='#BitMap'>BitMap</a></h1>
​    位图 是由二进制位组成的数组，数组中的每个二进制位都有对应的索引（也是 offset），可以通过 offset 对位图中的数据进行操作。

| 命令     | 格式                                                         | 说明                                                         |
| -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| SETBIT   | setbit k offset v                                            | 对 k 的值，偏移 offset 位，设置 bit                          |
| GETBIT   | getbit k offset                                              | 查询 k， 偏移 offset 位的 bit 值                             |
| BITCOUNT | BITCOUNT key [start end]                                     | 统计在 [start, end] 之间有多少 个 1<br />start，end 表示 第几个 字节，不是位。 |
| BITPOS   | BITPOS key bit [start] [end]                                 | 查找 第一个 被设置为 bit （0/1）的字节位置，<br />并返回。   |
| BITOP    | BITOP operation <br />destkey key [key ...]                  | 对 多个 key 的数据进行操作，并存储在 dest 中。<br />operation 可以是 AND、OR、XOR、NOT |
| BITFIELD | BITFIELD key <br />[GET type offset] <br />[SET type offset value]<br /> [INCRBY type offset increment] <br />[OVERFLOW  WRAP / SAT/ FAIL] | 在 位图 存储指定长度的整数，并对这些整数执行加减操作。<br />SET 在 offset 位置设置 type 类型的 value<br />GET 在 offset 位置获取 type 类型的 值<br />INCRBY 在 offset 位置 加减 type 类型的 increment<br />OVERFLOW 溢出处理，<br />WRAP（回绕，达到最大值之后，从最小值重新开始），<br />SAT（饱和，达到最大值就设置为最大值），<br />FAIL（如果一处就返回失败） |



<h1 id='Hash'><a href='#Hash'> 散列 Hash </a> </h1>
​    一个 key 中，存储多个字段。基本操作和 string 差不多，命令前面 加上 `H`

| 命令         | 格式                                           | 说明                                                         |
| ------------ | ---------------------------------------------- | ------------------------------------------------------------ |
| HSET         | HSET key field value                           | 设置 key 指定的 hash 中 字段 的 值                           |
| HSETNX       | HSETNX key field value                         | 如果 key 指定 hash 中 字段 不存在就设置                      |
| HMSET        | HMSET key field value [field value ...]        | 设置 key 指定的 hash 中多个字段的值                          |
| HEXISTS      | HEXISTS key field                              | 判断 key 指定的 hash 中 字段 是否存在                        |
| HKEYS        | HKEYS key                                      | 返回 所有字段的名字                                          |
| HVALS        | HVALS key                                      | 返回 所有字段的值                                            |
| HGET         | HGET key field                                 | 返回 key 指定的 hash 中 字段 的 值                           |
| HMGET        | HMGET key field [field ...]                    | 返回多个 key 指定的 hash 中 字段 的 值                       |
| HGETALL      | HGETALL key                                    | 返回指定哈希集中所有的字段和值                               |
| HSCAN        | HSCAN key cursor [MATCH pattern] [COUNT count] | 和 scan 类似。<br />对 一个 hash 中的 字段名称 进行扫描<br />返回 游标和 名称列表 |
| HLEN         | HLEN key                                       | 返回 哈希集 中 字段 的数量                                   |
| HSTRLEN      | HSTRLEN key field                              | 返回 哈希集 中 字段 的值的长度                               |
| HINCRBY      | HINCRBY key field increment                    | 返回 哈希集 中 字段 的值 增加 increment                      |
| HINCRBYFLOAT | HINCRBYFLOAT key field increment               | 和 HINCRBY 类似，增加浮点数                                  |

<h1 id='List'><a href='#List'>列表 List</a> </h1>
​    一种限行有序结构，按照元素被存储列表的顺序存储元素。`L` 开头表示从坐标操作，`R` 开头表示从右边操作。

| 命令       | 格式                                  | 说明                                                         |
| ---------- | ------------------------------------- | ------------------------------------------------------------ |
| LPUSH      | LPUSH key value  [value ...]          | 在 列表头部 插入数据，<br />如果列表不存在，先创建一个列表，后插入<br />如果 key 对应的不是 list， 返回 错误 |
| LPUSHX     | LPUSHX k v                            | k 存在时，左边插入 v，<br />不存在时不插入                   |
| RPUSH      | RPUSH key value  [value ...]          | 和 LPUSH 差不多，从右边操作。                                |
| RPUSHX     | RPUSHX key value                      | k 存在时，右边插入 v，<br />不存在时不插入                   |
| LSET       | LSET key index value                  | 设置指定位置的 value                                         |
| LTRIM      | LTRIM key start stop                  | 对 列表 进行修剪，只保留 start 到 stop 的数据                |
| LINSERT    | LINSERT key BEFORE/AFTER pivot value  | 在 pivot 的 前面/后面插入 value                              |
| LINDEX     | LINDEX key index                      | 返回第 index 个值                                            |
| LPOP       | LPOP key                              | 返回并删除 最左边的值                                        |
| RPOP       | RPOP key                              | 返回并删除 最右边的值                                        |
| BLPOP      | BLPOP key [key ...] timeout           | LPOP 的阻塞版本                                              |
| BRPOP      | BRPOP key [key ...] timeout           | BRPOP 的阻塞版本                                             |
| LRANGE     | LRANGE key start stop                 | 返回指定范围的值                                             |
| RPOPLPUSH  | RPOPLPUSH source destination          | 把 source 尾部的值移动到 destination 的头部                  |
| BRPOPLPUSH | BRPOPLPUSH source destination timeout | BRPOPLPUSH 的阻塞版本                                        |
| LREM       | LREM key count value                  | 删除 count 个 value<br />c > 0 , 从左向右操作<br />c < 0， 从右向左 操作<br />c = 0 ，删除所有 |
| LLEN       | LLEN key                              | 返回 列表长度                                                |

<h1 id='Set'><a href='#Set'>集合 Set</a> </h1>
​    多个不重复的元素组成的集合，命令前面 加上 `S`。 集合可以执行 集合 运算 交集、并集、差集。（另外还有  Sorted Sets ）

| 命令        | 格式                                                 | 说明                                                    |
| ----------- | ---------------------------------------------------- | ------------------------------------------------------- |
| SMEMBERS    | SMEMBERS key                                         | 返回所有元素                                            |
| SADD        | SADD key member [member ...]                         | 向集合添加元素                                          |
| SPOP        | SPOP key [count]                                     | 随机返回一个或多个元素，并删除                          |
| SRANDMEMBER | SRANDMEMBER key [count]                              | 随机返回 count 个元素，不删除                           |
| SREM        | SREM key member [member ...]                         | 删除指定的元素                                          |
| SINTER      | SINTER key [key ...]                                 | 返回多个集合的交集                                      |
| SINTERSTORE | SINTERSTORE destination key [key ...]                | 计算多个集合的交集<br />并存储到 destination            |
| SUNION      | SUNION key [key ...]                                 | 返回多个集合的并集                                      |
| SUNIONSTORE | SUNIONSTORE destination key [key ...]                | 计算多个集合的并集，并存储到 destination                |
| SDIFF       | SDIFF key [key ...]                                  | 返回第一个集合与剩下集合的差集                          |
| SDIFFSTORE  | SDIFFSTORE destination key [key ...]                 | 计算第一个集合与剩下集合的差集<br /> 存储到 destination |
| SISMEMBER   | SISMEMBER key member                                 | 判断 元素 是否存在                                      |
| SMOVE       | SMOVE source destination member                      | 移动 member                                             |
| SSCAN       | SSCAN key cursor [MATCH pattern] <br />[COUNT count] | 扫描 集合，返回 符合 pattern 的 count个元素             |

<h1 id='Sorted-Set'><a href='#Sorted-Set'>有序集合 Sorted Set</a> </h1>
内部元素按照  `分值` 排序的集合。命令前面 加上 `Z`。 

| 命令             | 格式                                                         | 说明                                                         |
| ---------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ZADD             | ZADD key [NX/XX] [CH] [INCR] <br />score member [score member ...] | 添加 分值为 score 的 mem，<br />可以设置  NX（如果不存在）和 XX 如果存在 |
| ZSCORE           | ZSCORE key member                                            | 返回 mem 分值                                                |
| ZRANK            | ZRANK key member                                             | 返回 mem 排名                                                |
| ZREVRANK         | ZREVRANK key member                                          | 返回 从大到小 排序的排名                                     |
| ZREM             | ZREM key member [member ...]                                 | 删除 mem                                                     |
| ZPOPMAX          | ZPOPMAX key [count]                                          | 删除并返回有序集合中的最多 count 个分值最高的成员。          |
| ZPOPMIN          | ZPOPMIN key [count]                                          | 删除并返回有序集合中的最多 count 个分值最低的成员。          |
| ZCARD            | ZCARD key                                                    | 返回有序集元素个数                                           |
| ZCOUNT           | ZCOUNT key min max                                           | 返回 分值 在 [min, max] 的 有序集元素个数                    |
| ZLEXCOUNT        | ZLEXCOUNT key min max                                        | 计算有序集合中 在 [min, max] ,的成员数量。<br />注意和 ZCOUNT 的区别<br />min 和 max 可以是 分值 ，也可以是 集合中的元素 |
| ZINCRBY          | ZINCRBY key increment member                                 | mem 的 分值 增加 increment                                   |
| ZRANGE           | ZRANGE key start stop [WITHSCORES]                           | 按照位置排序，返回 指定范围的元素，<br />start 和 stop 都是基于零的索引<br />WITHSCORES 同时返回元素分值 |
| ZRANGEBYSCORE    | ZRANGEBYSCORE key min max<br /> [WITHSCORES] [LIMIT offset count] | 按照分值排序，返回在 [min, max] 的元素                       |
| ZRANGEBYLEX      | ZRANGEBYLEX key min max <br />[LIMIT offset count]           | 按照字典排序，返回 [min, max] 的元素，<br />WITHSCORES 同时返回元素分值 |
| ZREMRANGEBYLEX   | ZREMRANGEBYLEX key min max                                   | 按字典排序，删除 在 [min, max] 的元素                        |
| ZREMRANGEBYRANK  | ZREMRANGEBYRANK key start stop                               | 按位置排序， 删除 在 [min, max] 的元素                       |
| ZREMRANGEBYSCORE | ZREMRANGEBYSCORE key min max                                 | 按照分值排序，删除 在 [min, max] 的元素                      |
| ZUNIONSTORE      | ZUNIONSTORE dest num key <br />[key ...] [WEIGHTS weight] [SUM/MIN/MAX] | 计算的num个有序集合的并，并且把结果放到 dest 中。<br />默认 新有序列表中元素的分值是该元素的 分值 之和。<br />WEIGHTS 指定 乘法因子。所有的 分值在传递给 聚合函数之前都要 乘以 乘法因子。默认是 1.<br />AGGREGATE 指定 分值的计算选项，默认是 SUM。<br />如果key destination存在，就被覆盖。 |
| ZINTERSTORE      | ZINTERSTORE destination num key <br />[key ...] [WEIGHTS weight] [SUM/MIN/MAX] | 计算的num个有序集合的交集，并且把结果放到destination中<br />其他参数参考 `ZUNIONSTORE` |
| ZREMRANGEBYSCORE | ZREMRANGEBYSCORE key min max                                 | 移除有序集中，所有score值 在 [min, max] 的成员               |
| ZREVRANGE        | ZREVRANGE key start stop [WITHSCORES]                        | 按 分值排序（从大到小），符合条件的元素                      |
| ZREVRANGEBYLEX   | ZREVRANGEBYLEX key max min [LIMIT offset count]              | 字典倒序排序， 符合条件的元素                                |
| ZREVRANGEBYSCORE | ZREVRANGEBYSCORE key max min<br /> [WITHSCORES] [LIMIT offset count] | 返回有序集合中分值 在 [min, max]的成员，分数由高到低排序。<br />WITHSCORES 将成员分数一并返回<br />LIMIT 限定 偏移和数量 |
| ZSCAN            | ZSCAN key cursor [MATCH pattern] [COUNT count]               | 扫描，返回符合条件的 count 个元素，<br />游标起始是 0， 后面使用返回的第一个值 |


