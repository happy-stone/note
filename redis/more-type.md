<h1 id='hpyer-log-log'><a href='#hpyer-log-log'>HpyerLogLog</a></h1>

​    HpyerLogLog 是专门为了计算集合的基数而创建的概率算法。命令以 `PF` 开头。

​    对于给定的集合，HpyerLogLog 可以计算出这个集合的近似基数（并非实际基数， 误差为 0.81%）。优点是，每个 HyperLogLog 只需要 12kb 内存，就可以对 2^64 个元素进行基数。

| 命令    | 格式                                      | 说明                                                         |
| ------- | ----------------------------------------- | ------------------------------------------------------------ |
| PFADD   | PFADD key element [element ...]           | 向 key 中添加多个 ele，<br />如果 key 不存在，就新建一个 HyperLogLog 结构。 |
| PFCOUNT | PFCOUNT key [key ...]                     | 返回存储在 HyperLogLog 中 key 的基数，<br />如果有多个 key 则，把多个结构合并到一个临时表之后，计算基数，<br />如果 key 不存在,则返回0。 |
| PFMERGE | PFMERGE destkey sourcekey [sourcekey ...] | 把多个结构合并到一个表之中                                   |

<h1 id='geo'><a href='#geo'>地理坐标 GEO</a></h1>

​    在 Redis 中存储 地理坐标，并且执行一些地理位置计算。能够很方便的计算两个坐标之间的距离，计算一个坐标附近的坐标，计算坐标的 Geohash 值等操作。其命令以 `geo` 开头。

| 命令              | 格式                                                         | 说明                                                         |
| ----------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| GEOADD            | GEOADD key <br />longitude latitude member<br />[longitude latitude member ...] | 设置多个经度、纬度和名称                                     |
| GEOPOS            | GEOPOS key member [member ...]                               | 获取 mem 的坐标                                              |
| GEODIST           | GEODIST key member1 member2 [unit]                           | 计算两点之间的距离，<br />单位可选 m/km/mi（英里）/ft（英尺） |
| GEORADIUS         | GEORADIUS key longitude latitude radius m/km/ft/mi <br />[WITHCOORD]<br /> [WITHDIST]<br /> [WITHHASH] <br />[COUNT count] | 查找给定的坐标以 redius 为半径的元素。<br />WITHCOORD 同时返回坐标<br />WITHDIST 同时返回距离<br />WITHHASH 同时返回 geohash<br />COUNT 返回前 count 个元素，可以通过 ASC、DESC 指定排序方式 |
| GEORADIUSBYMEMBER | GEORADIUSBYMEMBER key member <br />radius m/km/ft/mi <br />[WITHCOORD]<br /> [WITHDIST]<br /> [WITHHASH]<br /> [COUNT count] | 同 GEORADIUS 相似，输入值改成 元素，而不是 坐标              |
| GEOHASH           | GEOHASH key member [member ...]                              | 返回一个或者多个元素的 geohash                               |

<h1 id='Stream'><a href='#Stream'>流 Stream</a></h1>

​    流是 Redis 5 中的新功能，是使用 Redis 实现消息队列的最佳选择。推荐阅读 [Introduction to Redis Streams](https://redis.io/topics/streams-intro) 或者 [Redis Streams 介绍](http://redis.cn/topics/streams-intro.html) 。
​    流是包含一个或者多个元素的有序队列，每个元素都包含一个 ID 和任意多个键值对，这些元素根据 ID 大小在流中有序排列。新的 ID 值必须必旧的要大，元素设置之后，不能修改。
​    相比较传统的发布与订阅，流不需要将数据 JSON 话，也避免了由于客户端离线导致消息丢失的问题。

​    其命令行以 `x` 开头。

| 命令       | 格式                                                         | 说明                                                         |
| ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| XADD       | XADD key ID field string [field string ...]                  | 设置流 key 中  ID 的 field 为 value<br />如果 ID 设置为 *，则会自动生成一个。<br />如果 key 为空，就新创建一个 key。 |
| XTRIM      | XTRIM key MAXLEN [~] count                                   | 限制流中的元素个数                                           |
| XDEL       | XDEL key ID [ID ...]                                         | 删除指定元素                                                 |
| XLEN       | XLEN key                                                     | 返回流中元素个数                                             |
| XRANGE     | XRANGE key start end [COUNT count]                           | 返回 id 在 [start, end] 之间的元素。                         |
| XREVRANGE  | XREVRANGE key end start [COUNT count]                        | 类似 XRANGE ，由大到小排序。                                 |
| XREAD      | XREAD [COUNT count] <br />[BLOCK milliseconds]<br />STREAMS key [key ...] <br />ID [ID ...] | 从多个 流 中读取 id 大于 ID 的数据，<br />阻塞 n 毫秒，<br />返回 流 中的 count 个元素<br />设置 ID 为 0-0 可以从头开始读取，<br />设置 $ 可以阻塞直到用户 add 新元素。 |
| XGROUP     | XGROUP<br /> [CREATE key groupname id-or-\$  ] <br /> [SETID key id-or-$]<br /> [DESTROY key groupname] <br />[DELCONSUMER key groupname consumername] | 管理流 key 关联的消费者组，一个组里面可以有多个消费者。<br />CREATE 创建 key 关联的 groupname 组，获取大于 id 的元素，$ 表示 key 流中的最后一项 id <br />SETID  更改 groupname 的 id<br />DESTROY 删除 消费者组<br />DELCONSUMER 删除消费者 |
| XREADGROUP | XREADGROUP GROUP group consumer<br /> [COUNT count]<br /> [BLOCK milliseconds]<br /> STREAMS key [key ...]<br /> ID [ID ...] | 和 XREAD 相比，新增了 GROUP group consumer 指定被读取的消费者组和处理消息的消费者。<br />如果 consumer 不在 group 中，那么会先加入 group 后读取数据。 |
| XACK       | XACK key group ID [ID ...]                                   | 删除消费者组的待处理列表中的一条或者多条数据。<br />表示当前 ID 的数据已经处理完成。 |
| XINFO      | XINFO [CONSUMERS key groupname] <br />[GROUPS key]<br />[STREAM key] <br />[HELP] | 查询相关信息。<br />CONSUMERS 查询 流 key 中  groupname 组中每一个消费者的信息 <br />GROUPS 查询 group 组 的信息<br />STREAM 查询 流 key 的信息<br /> |
| XPENDING   | XPENDING key group [start end count] [consumer]              | 获取指定流 key 的消费值组 group 的待处理的信息<br />start end count  id 范围 和 数量<br />consumer 指定 group 中的 consumer |
| XCLAIM     | XCLAIM key group consumer min-idle-time ID [ID ...]<br />[IDLE ms]<br /> [TIME ms-unix-time] <br />[RETRYCOUNT count]<br />[FORCE] [JUSTID] | 改变待处理消息的所有权。<br />如果一条消息被消费者1读取，但是消费者1 出现问题，没能及时处理掉这条消息，那么，就应该转移给别的消费者。<br />对于消息 ID ，等待 min-idle-time ，如果没有被标记处理完成，那么就转移给 consumer。<br />IDLE 消息的空闲时间<br />TIME 将空闲时间设置为指定的 Unix 时间<br />RETRYCOUNT 将重试计数器设置为指定的值，默认每次调用 XCLAIM 都会将 重试计数器 加一。<br />FORCE 即使存在某些 ID 不在其他消费者的待处理列表中 ，也要将在 consumer 的 PEL 中新增这些消息   <br />JUSTID 只返回 ID |

