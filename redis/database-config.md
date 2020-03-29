​    默认配置文件 [http://download.redis.io/redis-stable/redis.conf](http://download.redis.io/redis-stable/redis.conf)，启动 Redis server 时，`./redis-server /etc/redis/6379.conf` 设置配置。 在 redis client 使用 `CONFIG GET *` 查看当前配置。

<h1 id='config'><a href='#config'>本机配置</a></h1>

| 配置项     | 默认值              | 说明                      |
| --------- | ------------------ | ------------------------ |
| daemonize | no                 | 是否以守护进程的方式运行 |
| pidfile   | /var/run/redis.pid | pid 文件                 |
| port      | 6379               | 端口                     |
| bind      | 127.0.0.1          | 主机地址                  |
| databases | 16                 | 指定创建数据库数量。        |
| timeout   | 300                | 闲置连接多长时间后关闭       |
| loglevel  | notice             | 日志记录级别                |
| logfile   | stdout             | 日志输出文件                |
| requirepass  |        | Redis 连接密码，默认不设置密码         |
| maxclients |         | 同一时间最大客户端连接数，默认无限制      |




<h1 id="menory"><a href="#memory">内存</a></h1>


| 配置项                    | 默认值                                         | 说明                                                         |
| ------------------------- | ---------------------------------------------- | ------------------------------------------------------------ |
| maxmemory                 |                                                | 最大内存                                                     |
| vm-enabled                |                                                |                                                              |
| vm-swap-file              |                                                |                                                              |
| vm-max-memory             |                                                |                                                              |
| vm-page-size              |                                                |                                                              |
| vm-pages                  |                                                | swap 文件中的 page 数量                                      |
| vm-max-threads            | 4                                              | 访问swap文件的线程数                                         |
| glueoutputbuf             | yes                                            | 在向客户端应答时，是否把较小的包合并为一个包发送             |
| hash-max-zipmap-entries   |                                                |                                                              |
| hash-max-zipmap-value     |                                                |                                                              |
| activerehashing           | yes                                            | 指定是否激活重置哈希                                         |
| include                   |                                                | 包含其它的配置文件位置                                       |

