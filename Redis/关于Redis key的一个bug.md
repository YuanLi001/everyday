后端生成的一个Redis key名字为`redisson_delay_queue:{redis_delay_queue: other}` 

数据类型为zset，当时想在客户端查看一下这个zset里的内容

结果：

```sh
localredis:0>zrange  redisson_delay_queue:{redis_delay_queue: other}  0 5
"ERR syntax error"
```

经过反复测试，发现问题是other单词前面有个空格。

问题来了，为什么这个key可以生成并且正常存在呢。

原因：

Redis的key允许是一切二进制数据，甚至可以用一个JPEG图片来当key。

参考：https://stackoverflow.com/questions/30271808/naming-convention-and-valid-characters-for-a-redis-key