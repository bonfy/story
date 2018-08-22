# IO串行 与 并行

## 概念

串行与并行 I/O 间的明显区别。串行很简单，即先进行一个 I/O 操 作，完成以后，再做下一个;并行实现起来比较复杂，但也不难理解，即同时进行 两个 I/O 操作。重点是，在串行任务中通常完成顺序是确定的，而在并行任务中任 何一个操作先完成返回都是有可能的。

串行和并行任务混合在一起也是可以的。例如，两组并行请求可以串行执行，即先 同时执行两个任务，然后再同时执行另外两个任务。


### 无序并行 I/O

我们先从无序的并行 I/O 开始(例 3-3)，因为这是在 Node 中最容易实现的。事实 上，Node 中所有的 I/O 操作默认都是无序并行的，因为 Node 的所有 I/O 都是异步 非阻塞的。我们操作 I/O 时，只要扔出请求然后等待结果就行了。所有请求可能按我们操作的顺序执行，也可能不是。我们指的无序，并不是指乱序，而是指顺序没 有保证。

```js
    fs.readFile('foo.txt', 'utf8', function(err, data) {
      console.log(data);
    };
    fs.readFile('bar.txt', 'utf8', function(err, data) {
      console.log(data);
    };

```

### 顺序串行 I/O

在这个模式里，我们希望按顺序执行一些 I/O(无限延时)任务。每一个任务都必 须在上一个任务完成后才能开始。在 Node 里，这意味着使用嵌套回调，这样可以 在每个任务的回调函数里发起下一个任务

```js
server.on('request', function(req, res) {
// 从 memcached 里获取 session 信息 memcached.getSession(req, function(session) {
// 从 db 获取信息
db.get(session.user, function(userData) {
// 其他 Web 服务调用
ws.get(req, function(wsData) {
// 渲染页面
page = pageRender(req, session, userData, wsData); // 输出响应内容
res.write(page);
}); });
}); });
```
