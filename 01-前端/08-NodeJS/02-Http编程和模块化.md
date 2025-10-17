
# 1. 创建Http服务端

```js
// 1.导入http模块
const http = require('http');

// 2.创建web服务器实例
const server = http.createServer((request, response) => {
  console.log('收到客户端的请求了,请求路径是' + request.url);

  response.end('hello nodejs');  // 返回给前端
});

// 3.启动服务器
server.listen(3000, () => {
  console.log('服务器启动成功了,可以通过http://localhost:3000/来进行访问');
});
```

# 2. 服务器实例常用方法

### 2.1  server.listen

```js
server.listen(3000, '127.0.0.1', () => {
  console.log('Server listening on port 3000');
});
```

### 2.2 server.close

```js
server.close(() => {
  console.log('Server closed');
});
```

### 2.3 服务器事件
- `request`：收到客户端请求时触发 （和createServer的回调等效）
- `error` : 服务器发生错误的时候触发

```js
server.on('request', (req, res) => {
  console.log('Received request');
});

server.on('error', (err) => {
  console.error('Server error:', err);
});
```

# 3. 客户端实例常用方法

### 3.1 http.request

发送Http请求，options可以配置请求地址、方法、以及头信息等

```js
const http = require('http');

// 配置请求参数
const options = {
  hostname: 'api.example.com',
  port: 80,
  path: '/data',
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Content-Length': Buffer.byteLength(JSON.stringify({ key: 'value' }))
  }
};

// 发送请求
const req = http.request(options, (res) => {
  let data = '';
  // 接收响应数据
  res.on('data', (chunk) => { data += chunk; });
  // 响应结束时处理
  res.on('end', () => {
    console.log('Response:', JSON.parse(data));
  });
});

// 处理请求错误
req.on('error', (err) => {
  console.error('Request error:', err);
});

// 发送请求体（POST 数据）
req.write(JSON.stringify({ key: 'value' }));
// 结束请求
req.end();
```

> [!tips]
> 当然作为客户端，它也有简化的`get`、`post`等方法

# 4. 模块化

在nodejs中，默认是采用`commonjs`的方式进行模块化的管理和使用

### 4.1 导入
就是我们之前一直使用的`require`

```js
const fs = require('fs');
```

### 4.2 导出

使用`module.exports` 将谁导出

- 默认导出， 类似ES module中的  `export default`
- 具名导出  

```js
function kobe() {
  console.log('kobe');
}

// 这种就是具名导出
module.exports = { kobe };
// 默认导出
module.exports =  kobe ;
```

那么如何导入呢？

```js
// 默认导入
const kobe  = require('./03.module');
// 具名导入
const { kobe } = require('./03.module');
```

### 4.3 导入模块的基本流程

1. 将相对路径转为绝对路径，定位到目标文件
2. 缓存检测
3. 读取目标文件代码
4. 包裹成一个函数并执行， 通过`arguments.callee.toString()` 查看自执行函数
5. 缓存模块的值
6. 返回`module.exports`的值