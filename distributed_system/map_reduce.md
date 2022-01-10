

核心思想：让程序员只需在该分布式计算系统下编写自己的应用程序，而无需知道分布式的任何知识。只要写出一个 map 函数和 reduce 函数即可，由 MapReduce 来完成其余的所用工作。

## 使用

以统计词频的任务举例，可以这么写 map 和 reduce 函数：
```
// key: file name  value: file contents
map(String key, String value):
    for each word w in value:
        EmitIntermediate(w,"1");


// key: word  value: list of word counts
reduce(String key, Iterator values):
    Emit(len(result));
```

## 实现

数据本地化 trick：在 `Input File -> Map函数` 这一步，MapReduce 会使用某种调度程序保证 Map Task 在 Input File 所在磁盘的计算机上执行，这样就可以大大节省网络通信时间。同时，Map Task 的输出也同样保存在该本地磁盘上。
（但由于现在的数据中心的网络速度远比 04 年出那篇 mapreduce 论文那时快，现在的 mapreduce 就已经不需要这个数据本地化 trick 了）

