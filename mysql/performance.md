<h1 id='explain'><a href="#explain"> explain 计划 </a></h1>

explain 命令可以验证 MySQL 的执行计划，查看查询优化器如何执行查询计划。

可以为正在执行的会话执行 explain 计划，需要制定 connection id（`select connection_id()` 查看当前 ID）。

<h1 id='index'><a href="#index"> 索引 </a></h1>

索引可以在执行查询操作时，快速找到数据，而不必扫描整个表。