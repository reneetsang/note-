##TCP、HTTP和Node.js的那些事

### url

```javascript
let url = require('url');

// pathname代表的是/后面的和?前面的
// query 是查询字符串 a=1&b=2 true {a:1,b:2}
let { pathname, query } = url.parse('http://user:passowrd@www.zf.cn:80/1.html?a=1&b=c#aaa',true);
console.log(pathname, query);
```

### TCP

TCP是用来计算机之间进行通信的，通过编写客户端和服务端聊天的代码，对于服务器与客户端的工作步骤有了深刻的了解。在node中为了实现TCP，提供了一个`net`模块，`net` 模块给你提供了一个异步的网络封装，它包含创建服务器和客户端（称为流）的功能。

#### 创建TCP服务端

- net.createServer(options,connectionListener);

创建一个tcp服务，里面放的是回调函数 监听函数，当连接到来时才会执行。

其中传进去的参数socket是套接字，帮我们实现会话，每一个客户端对应的socket都是不一样的。是一个duplex双工流，可以支持读操作和写操作。

- server.listen()

可以设置服务监听的端口，主机名， backlog服务端处理的最大请求，默认是511，可以不写。还有回调函数，当服务启动成功会调用

```javascript
let net = require('net');
let server = net.createServer(function(socket){
	...
})
server.listen(8080,'localhost',function(){
    console.log(`server start 8080`)
});
```

接下来可以对服务端进行配置

#### 设置一个服务端最大的链接数

```javascript
server.maxConnections = 6;
```

#### 得到连接的时候触发方法

希望每次请求到来时都有一个提示，当前最大连接多少个，现在一共连接多少个

```javascript
server.getConnections(function(err,count){ 
    socket.write(`当前最大容纳${server.maxConnections},现在${count}人`)
});
```

#### 接受客户端数据

- 读取客户端的输入

```javascript
socket.on('data',function(data){
    console.log(data); 
});
```

- 暂停读取

```javascript
socket.pause();
```

- 恢复读取

```javascript
socket.resume();
```

#### pipe&unpipe

监听客户输入时，将客户端输入的内容写入到文件内容。

如果有两个客户端，就有两个socket，其中一个关闭了，另一个就写不进去了

```javascript
let net = require('net');
let path = require('path');
let ws = require('fs').createWriteStream(path.join(__dirname,'./1.txt'));
let server = net.createServer(function(socket){
    socket.pipe(ws,{end:false}); // 所以可以设置这个可写流不关闭
    setTimeout(function(){
        ws.end(); // 关闭可写流
        socket.unpipe(ws); // 取消管道，就不能传输了
    },5000)
});
server.listen(8080);
```

#### 关闭客户端

- socket.end();  除了手动关闭，我们也可以调用此方法让它关闭。

#### 关闭服务端

- server.close(); 

close事件表示服务端不在接收新的请求了，当前的还能继续使用，当客户端全部关闭后会执行close事件。

```javascript
// close事件只有调用close方法才会触发
server.on('close',function(){
    console.log('服务端关闭');
})
```

- server.unref();

unref() 不会触发close事件，只有当所有客户端都关闭了，服务端才关闭，如果有人进来（有新请求）仍然可以。

#### 创建TCP客户端

- net.createConnection 是用来创建于服务端的连接

```javascript
let net = require('net');

let socket = net.createConnection({port:8080},function(){
    socket.write('hello');
    socket.on('data',function(data){
        console.log(data);
    });
});
```

### HTTP

node中为了实现http同样也提供了一个模块，叫http模块，其中封装了高效的http服务器和http客户端 ，与net模块用法相似。

- 请求的一方叫客户端，响应的一方叫服务器端
- 通过请求和响应达成通信
- HTTP是一种不保存状态的协议

#### 创建HTTP服务器

- request

当客户端请求到来的时候，该事件被触发。

我们最常用的还是`request`事件，http也给这个事件提供了一个捷径：`http.createServer([requestListener])` 。

- 参数res req

传入中的参数req是请求（客户端），是一个可读流。res是响应（服务端），是一个可写流。

是通过socket解析出来请求和响应。

创建HTTP服务器有两种方法

1. 第一种

```javascript
let http = require('http'); 
let server = http.createServer();
server.on('request',function(req,res){
    req.on('end',function(){
        res.write('hello');
        res.end('world');
    });
});
// 默认连接成功后 会把socket解析成 req,res，解析后触发一个事件，事件叫request
server.on('connection',function (socket) {
// socket  net中的socket
//   socket.write(`
// HTTP/1.1 200 OK
// Content-Length: 2
// Content-Type: text/html;charset=utf8

// ok
//   `);
//   socket.end();
  console.log('连接成功');
});

server.listen(8080);
```

2. 可以这样简写

```javascript
let http = require('http');
let server = http.createServer(function(req,res){
  res.end('ok');
});
server.listen(8080);
```

#### 关闭HTTP服务器 

```javascript
let http = require('http');
let server = http.createServer(function(req,res){
});
server.on('close',function(){
    console.log('服务器关闭');
});
server.listen(8080,'127.0.0.1',function(){
    console.log('服务器端开始监听!')
    server.close(); // 关闭
});
```

#### 创建HTTP客户端

http 模块提供了两个函数 http.request 和 http.get，功能是作为客户端向 HTTP服务器发起请求。

- http.request(options, callback) 发起 HTTP 请求。接受两个参数，option 是一个类似关联数组的对象，表示请求的参数，callback 是请求的回调函数。

```javascript
let http = require('http');
let options = {
    hostname:'localhost',
    port:4000,
    path: '/',
    method:'get',
    // 告诉服务端我当前要给你发什么样的数据
    headers:{
        'Content-Type':'application/x-www-form-urlencoded',
        'Content-Length':17
    }
}
let req = http.request(options);
// 当客户端收到服务器响应的时候触发
req.on('response',function(res){
    res.on('data',function(chunk){
        console.log(chunk);
    });
});
req.end('name=renee&&age=18');
```

- http.get(options, callback) http 模块还提供了一个更加简便的方法用于处理GET请求：http.get。它是 http.request 的简化版，唯一的区别在于http.get自动将请求方法设为了 GET 请求，同时不需要手动调用 req.end()。

```javascript
var http = require('http');

http.createServer(function(req,res){}).listen(3000);

http.get('http://www.baidu.com/index.html',function(res){
    console.log('get response Code :' + res.statusCode);
}).on('error',function(e){
    console.log("Got error: " + e.message);
})
```

