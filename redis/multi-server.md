<h1 id="master-salve"><a href="#master-salve">主从复制</a></h1>

Redis 服务器允许用户创建多个拥有相同数据库副本的服务器。这些服务器成为从服务器（salve/replica），源服务器成为主服务器（master）。从服务器也可以作为主服务器拥有多个从服务器。但是一个从服务器只能有一个主服务器。一般默认主服务器执行写操作，从服务器执行读操作。

这种方法的好处是，（1）大大提高读操作的效率，（2）降低数据丢失的可能。

#### 配置文件 ####

| 配置项                    | 参数 | 说明                                       |
| ------------------------- | ------ | ------------------------------------------ |
| slaveof /  replicaof    |   replicaof no one     | 本机是 slave/replicaof 时，指定 master 的 ip 和 port <br/> 5.0 之后的版本使用 replicaof |
| masterauth                |        | 本机是 slave 时，指定 master 的密码        |
| replica-announce-ip   |        | 本机是 slave 时，申明自己的 ip             |
| replica-announce-port     |        | 本机是 slave 时，申明自己的 port           |
| replica-read-only         | yes    | 设置 slave 只读                            |
| replica-serve-stale-data  |  yes |  |
| repl-diskless-sync  |  no | 不创建 RDB 文件直接复制 |
| repl-diskless-sync-delay  |  no |  |
| repl-disable-tcp-nodelay  |  no |  |
| repl-backlog-size  |  no |  |
| repl-backlog-ttl  |  no |  |
| replica-priority | 100 | 优先级 |
| min-replicas-max-lag |  | 从服务器与主服务器最后一个通信间隔不超过 多少秒，才执行写操作  |
| min-replicas-to-write | | 至少有多少台从服务器，才执行写命令  |

#### 命令行操作 ####

| 命令     | 参数 | 说明                |
| --------------- | ------ | -------------------- |
| slaveof / replicaof  | replicaof host port | 同配置文件 |
| ROLE  | ROLE | 查询当前服务器角色 |

<h1 id="Sentinel"><a href="#Sentinel">哨兵 Sentinel</a></h1>

具有主从模式的 Redis 服务器理论上数据是一致的，也就是可以相互替换的。如果主服务器故障是，可以将某个从服务器设置为主服务器。

理论上，有主服务器 Matser 和两个从服务器 Repl-1 和 Repl-2 ，如果 Master 现在故障了，可以通过在 Repl-1 执行 `replicaof no one`，将 Repl-1 设置成主服务器，在 Repl-2 上执行 `replicaof Repl-1.ip Repl-1.port`,把 Repl-2 设置成 Repl-1 的从服务器。但是这么做比较麻烦。

Redis 提供了 RedisSentinel 工具。Redis Sentinel 可以监视多个服务器，当主服务器下线时，选择一个从服务器当作主服务器，并将另外的服务器转为新主服务器的从服务器。

设置 sentinel.conf，并启动 Redis Sentinel

```bash
$ redis-sentinel /etc/sentinel.conf
```

可以设置多个 Sentinel，启动 Sentinel 网络。

<h1 id="cluster"><a href="#cluster">集群 cluster</a></h1>

Redis 集群 [https://redis.io/topics/cluster-tutorial](https://redis.io/topics/cluster-tutorial) 提供了主从复制，哨兵功能。集群中的服务器称为节点（node），主节点（master node）负责处理客户端发送的读写请求，从节点负责对主节点复制，主节点和从节点都可以有多个，但是一个从节点只能有一个主节点。Redis 集群将数据分散存储到多个节点来平衡各个节点的负载压力，而主从复制则是每台机器都保存完整的数据。

Redis 集群会把数据库空间划分成 16384 个槽（slot）来实现数据分片（sharding）。当用户新增一个键（key）时，客户端会先计算键所属的槽，然后在记录集群节点的槽分布状态的映射表中找出处理该槽的节点，最后将该键存储到相应的节点中。如果新加入节点，集群会自动处理槽的分配和数据迁移，且数据库不会停机。

操作

```bash
$ // 创建集群文件夹
$ mkdir redis-culster
$ cd redis-culster
$ // 每个节点一个文件夹
$ mkdir node1 node2 node3 node4 node5 node6 
$ // 每个文件夹下创建一个 redis.conf, 端口号不同 30001 -30006
$ echo -e "cluster-enabled yes\nport 30001\ncluster-config-file nodes.conf\ncluster-node-timeout 5000\ndaemonize yes" > node1/redis.conf
$ ...
$ // 每个节点
$ cd node1; redis-server redis.conf
$ ...
$ // 创建集群，并设置每个主节点设置一个从节点
$ redis-cli --culster create 127.0.0.1:30001 127.0.0.1:30002 ... 127.0.0.1:30006 --cluster-replicas 1
```


<br/><br/><br/><br/><br/>
