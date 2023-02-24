## 简单介绍

`CompletableFuture` 同时实现了 `Future` 和 `CompletionStage` 接口。

```java
public class CompletableFuture<T> implements Future<T>, CompletionStage<T> {
}
```

`CompletableFuture` 除了提供了更为好用和强大的 `Future` 特性之外，还提供了函数式编程的能力。

![img](asserts/image-20210902092441434.png)

`Future` 接口有 5 个方法：

- `boolean cancel(boolean mayInterruptIfRunning)` ：尝试取消执行任务。
- `boolean isCancelled()` ：判断任务是否被取消。
- `boolean isDone()` ： 判断任务是否已经被执行完成。
- `get()` ：等待任务执行完成并获取运算结果。
- `get(long timeout, TimeUnit unit)` ：多了一个超时时间。

![img](asserts/image-20210902093026059.png)

`CompletionStage<T>` 接口中的方法比较多，`CompletableFuture` 的函数式能力就是这个接口赋予的。从这个接口的方法参数你就可以发现其大量使用了 Java8 引入的函数式编程。

## 常见操作

### 创建 CompletableFuture