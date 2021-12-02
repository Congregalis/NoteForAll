# 一条SQL是如何从输入到执行的？

> [《详解一条 SQL 的执行过程》](https://mp.weixin.qq.com/s/OnGaqyUpB58pC2rqqzIzgw)的阅读笔记

1. 准备更新一条 SQL 语句

2. MySQL 去缓冲池（Buffer Pool）中**查找**这条数据，如果没有查到就去磁盘中查找并加载到缓冲池中

3. 从磁盘加载到 Buffer Pool 时，会将该数据的原始记录保存到 **undo 日志**中，便于回滚

4. innodb **在 Buffer Pool 中执行更新操作**

5. 将更新后的数据记录到 **redo log buffer** 中

6. MySQL **提交事务**，将做以下三件事，缺少任何一件都不算提交成功
    1. 将 redo log buffer 中的输入**写入 redo 日志**中（redo log 为 innodb 特有），但此时 redo 日志不一定写入了磁盘（刷磁盘可以通过 `innodb_flush_log_at_trx_commit` 参数来设置）

    2. 还会将本次修改的数据记录到 **bin log** （MySQL 级别的日志）中

    3. 将本次修改的 bin log **文件名**和修改的内容在 bin log 中的**位置**记录到 **redo log** 中，同时在 **redo log** 最后写入 **commit 标记**，表示本次**事务被成功提交**


8. MySQL 若在第 **6** 步提交事务是或之前重启会**将 redo 日志恢复到 Buffer Pool 中**
