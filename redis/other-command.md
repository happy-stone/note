<h1 id='Pipeline'><a href='Pipeline'>Redis Pipeline</a></h1>

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

<h1 id='Transaction'><a href='Transaction'>事务</a></h1>

Reids 的事务可以保证一些操作要么完全执行，如果遇到错误，可以回滚到初始状态。使用 `MULTI` 命令之后，服务器会把接受的命令放进事务队列中，当遇到 `EXEC` 命令才开始执行。或者遇到  `DISCARD` 放弃事务。


<h1 id='lock-transaction'><a href='lock-transaction'>带有乐观锁的事务</a></h1>

当事务在开始到执行期间，相关的数据可能会被修改。比如 事务需要使用 key a，在 执行了 multi 之后，命令不断的进入事务队列，此时 key a 的值，被另一个客户端修改了，那么事务的执行可能就会出错。 此时需要对 a 进行 “加锁”，以确保事务执行的正确性。
通过 `WATCH` 命令可以监控多个键，如果在执行事务期间，键值发生改变服务器就会拒绝执行事务。 `UBWATCH` 可以取消监控。
