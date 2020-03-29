<h1 id='Pipeline'><a href='#Pipeline'>Redis Pipeline</a></h1>

执行 Reids 操作的大部分时间都花在网络上。Piplining 允许客户端把多个命令打包在一起，一次性发送给服务器。

这个命令需要客户端支持。比如 Python 的 redis 包

```python
from redis import Redis

client = Redis(decode_responses=True)
pipe = client.pipeline(transaction=False)
pipe.set("msg", "hello world")
pipe.incrby("a:b", 100)
pipe.sadd("s", 'a', 'b', 'c')
pipe.execute()
```

<h1 id='Transaction'><a href='#Transaction'>事务</a></h1>

Reids 的事务可以保证一些操作要么完全执行，如果遇到错误，可以回滚到初始状态。使用 `MULTI` 命令之后，服务器会把接受的命令放进事务队列中，当遇到 `EXEC` 命令才开始执行。或者遇到  `DISCARD` 放弃事务。


<h1 id='lock-transaction'><a href='#lock-transaction'>带有乐观锁的事务</a></h1>

当事务在开始到执行期间，相关的数据可能会被修改。比如 事务需要使用 key a，在 执行了 multi 之后，命令不断的进入事务队列，此时 key a 的值，被另一个客户端修改了，那么事务的执行可能就会出错。 此时需要对 a 进行 “加锁”，以确保事务执行的正确性。
通过 `WATCH` 命令可以监控多个键，如果在执行事务期间，键值发生改变服务器就会拒绝执行事务。 `UBWATCH` 可以取消监控。


<h1 id='pubsub'><a href='#pubsub'>订阅与发布</a></h1>

订阅发布功能涉及到 （1）频道（channel），（2）发布者（publisher），（3）订阅者（subscriber）。

发布者 把消息发送到 特定频道，订阅者通过订阅频道获得消息。一个频道可以有多个订阅者，一个订阅者也可以订阅多个频道。

| 命令 | 格式 | 说明 |
| ---- | ---- | ---- |
| PUBLISH | PUBLISH channel message | 发送 message 到 channel |
|   SUBSCRIBE  | SUBSCRIBE channel [channel ...] | 订阅 channel |
| UNSUBSCRIBE | UNSUBSCRIBE [channel [channel ...]] | 取消订阅 |
| PSUBSCRIBE | PSUBSCRIBE pattern [pattern ...] | 订阅符合 pattern 的 channel |
| PUNSUBSCRIBE | PUNSUBSCRIBE <br/> [pattern [pattern ...]] | 取消订阅符合 pattern 的 channel |
| PUBSUB | PUBSUB subcommand <br/> [argument [argument ...]] | 查看订阅发布相关的信息。 <br/> PUBSUB CHANNELS [pattern] 列出被订阅的（符合 pattern）的 channel <br/> PUBSUB NUMSUB [channel-1 ... channel-N] 查看订阅 channel 的数量。<br/> PUBSUB NUMPAT 查看订阅模式的数量 |

<h1 id='module'><a href='#module'>模块功能</a></h1>

Redis 的模块功能允许开发者通过 Redis 开放的 API，将 Redis 作为网络和数据存储的平台，在 Redis 之上构建新的数据结构和功能。[https://redis.io/modules](https://redis.io/modules) 展示了可以使用的模块。常见的模块有：

| 模块 | 说明 |
| --- | ---- |
| RediSearch | 全文搜索 |
| rediSQL | 通过集成 SQLite 实现 SQL 查询 |
| RedisJSON | 支持 Json 类型 |
| RedisGraph | 图数据库支持 |
| RedisBloom | 可扩展布隆过滤器（判断元素是否存在） |
| neural-redis | 使用 Redis 数据类型在线训练神经网络 |
