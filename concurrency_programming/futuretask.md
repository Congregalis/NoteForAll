# FutureTask

一句话介绍：FutureTask 代表可取消的异步计算
> A cancellable asynchronous computation. This class provides a base implementation of **Future**, with methods to **start** and **cancel** a computation, **query** to see if the computation is complete, and **retrieve the result** of the computation. The result can only be retrieved when the computation has completed; the get methods will block if the computation has not yet completed. **Once the computation has completed, the computation cannot be restarted or cancelled** (unless the computation is invoked using runAndReset()). (from [doc](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/FutureTask.html))

其可以用来包裹 Callable 和 Runnable，因为他实现了这两个接口。同时他还可以被提交到 Executor (线程池) 中执行。

## 使用

新建 FutureTask
```java
FutureTask<String> future = new FutureTask<>(new Callable<String>() {
    @Override
    public String call() throws Exception {
        System.out.println(Thread.currentThread().getName() + ": I'm running...");
        return "task done!";
    }
});
```

1. 如果**直接调用 run**，任务会在**当前线程**运行，不会开启新的线程

```java
future.run();
System.out.println("main end, get message: " + future.get());
```
可以得到结果：
```
main: I'm running...
main end, get message: task done!
```
发现任务是在当前线程——主线程中运行的。

2. 托管给单独的线程处理

```java
new Thread(future).start();
System.out.println("main end, get message: " + future.get());
```
可以得到结果：
```
Thread-0: I'm running...
main end, get message: task done!
```
发现任务在 **Thread-0** 这个线程中被执行。

3. 托管给线程池处理

```java
ExecutorService es = Executors.newSingleThreadExecutor();
es.submit(future);
es.shutdown();

System.out.println("main end, get message: " + future.get());
```
可以得到结果：
```
pool-1-thread-1: I'm running...
main end, get message: task done!
```
发现任务在 **pool-1-thread-1** 这个线程中被执行。

4. 其他使用示例：
- 如果先取消，再开线程执行该任务，会发现控制台不打印 `I'm running...` 了。
```java
future.cancel(true);
new Thread(future).start();
```
这说明当 FutureTask 处于未启动状态时一旦被取消则导致该任务永远不会再被执行。

- 如果先执行任务再取消，会根据 cancel(...) 中的 `mayInterruptIfRunning` 参数决定是否中断当前任务的执行，若是 `false` 则不会有什么影响
```java
FutureTask<String> future = new FutureTask<>(new Callable<String>() {
    @Override
    public String call() throws Exception {
        Thread.sleep(100); // 延迟 0.1 秒，目的是让主线程先执行 cancel()
        System.out.println(Thread.currentThread().getName() + ": I'm running...");
        return "task done!";
    }
});

//  先执行，再以中断方式取消，则会直接中断
new Thread(future).start();
Thread.sleep(10); // 防止 cancel 先于上面的 start 执行，导致任务还没开始的时候就取消了
future.cancel(true);
```
按上面执行则会发现成功取消了任务的执行。

按下面这样执行则会发现任务照常执行了下去。
```java
//  先执行，再以非中断方式取消，则任务继续执行
new Thread(future).start();
Thread.sleep(10); // 防止 cancel 先于上面的 start 执行，导致任务还没开始的时候就取消了
future.cancel(false); // 不中断
```