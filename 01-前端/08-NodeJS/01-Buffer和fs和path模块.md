
# 1. 什么是Node.js？

首先，node不是一个新的语言或者框架，它只是一个JavaScript的运行时环境。通过Google V8引擎进行驱动处理，提供了异步I/O和文件处理以及网络编程的处理一系列API，它是服务端运行的，所以没有BOM、DOM以及Ajax这些前端的API

![[Pasted image 20250917155238.png\|500]]

# 2.  Buffer

在Node中，Buffer是专门用于处理二进制数据的内置对象，是实现文件传输、网络传输等基础组件，在传统的JS中String只能处理Unicode字符，Buffer的诞生，使得文件操作、网络通信、以及流式处理成为可能

## 2.1 为啥需要Buffer

1. 读取图片文件
2. 网络传输
3. 加密、解密
4. 操作二进制协议

## 2.2 Buffer的核心特性

1. **固定大小**  Buffer一旦创建，长度就不可修改，是一个类似数组的结构，但是大小固定，创建时必须指定大小或者通过数据自动计算大小
2. **每个元素的大小**  是0-255之间的整数，也就是最多是8位，因为一个字节代表8位
3. **基于内存**  Buffer存储在Node的『堆外内存』，由底层处理，无需手动回收
4. **字节级操作** 提供丰富的API用于读写单字节或者批量处理字节
5. **支持与字符串双向转换**  通过指定编码（UTF8  base64）将Buffer转为字符串，也可以将字符串转为Buffer

## 2.3 使用

### 2.3.1 创建

```js
// 方法1： 创建大小为10的Buffer，会清空原有的Buffer空间，避免残留旧数据
let buf = Buffer.alloc(10);
// console.log(buf);
// 方式2： 和方法1类似， 但是它不清空Buffer，导致可能会包含旧有的数据，所以一般不用
let buf_2 = Buffer.allocUnsafe(10);
// console.log(buf_2);
// 方式3：从数据中穿件Buffer
let buf_3 = Buffer.from('Hello World');
console.log(buf_3);
// 从数组中常见
let buf_4 = Buffer.from([0x56, 0x59]);
console.log(buf_4);
// 从另外一个buffer中创建
let buf_5 = Buffer.from(buf_4);
console.log(buf_5);
```

### 2.3.2 和字符串互相转换

```js
const buf = Buffer.from('Hello 你好');
console.log(buf.toString());  // 默认是utf8
```

### 2.3.3 操作buffer

```js
console.log(buf[0].toString(2)); // 01001000
buf[0] = 99;  // 将第一个字节重新赋值
console.log(buf.toString()); // cello 你好 
```

### 2.3.4 溢出

```js
const buf = Buffer.from('Hello World');
buf[0] = 300; // 300对应的二进制为  1 0010 1100 ，Node会将高位舍弃，最终就是 0010 1100  = 44 十进制 = 2c 十六进制
console.log(buf);
// <Buffer 2c 65 6c 6c 6f 20 57 6f 72 6c 64>
```

# 3. fs模块

就是Java中File IO库，专门用于文件的处理的

## 3.1 写文件

```js
const fs = require('fs');
// 这个是异步的
fs.writeFile('./0917.txt', 'Hello 中国', (err) => {
  if (err) {
    console.log(err);
  } else {
    console.log('Success');
  }
});
// 同步
fs.writeFileSync('./0917.txt', 'Hello 中国');
// 异步追加
 fs.appendFile('./kobe.txt', '\n林俊杰', (err) => {});
 // 同步追加
 fs.appendFileSync('./kobe.txt', '\n梁朝伟');
```

## 3.2 流式写入

一般流式写入适合大数据量写入的情况

```js
const ws = fs.createWriteStream('./kobe.txt', { flags: 'a' });
ws.write('刘德华\n');
ws.write('张国荣\n');
ws.close();
```

## 3.3  读取文件

```js
fs.readFile('./kobe.txt', (err, data) => {
  if (err) {
    console.log('Error', err);
  } else {
    console.log(data.toString());
  }
});
```

## 3.4 流式读取

流式读取的时候，都是通过类似监听的方式来进行处理，然后异步进行处理，

```js
const rs = fs.createReadStream('./kobe.txt');

// 读取数据
rs.on('data', (chunk) => {
  console.log(chunk.toString());
});

// 数据读取完毕后的回调
rs.on('end', () => {
  console.log('读取完毕');
});

// 读取失败后的回调
rs.on('error', (err) => {
  console.log('Error', err);
});
```

## 3.5 文件copy

文件copy的最佳实践方案是采用`pipe()`方法

- 如果使用先把所有的数据读出来，在写入到新文件中，导致可能内存泄露，如果文件很大就没有办法了
- 采用`readStream`和`writeStream` 通过流式方式来处理，这个也是一个办法，但是它有一个问题
	- 产生『背压』问题，读写不平衡，写的速度跟不上读的速度，最后可能导致内存泄露
- 解决『背压』最好的方法就是`pipe()`方法

```js
const readStream = fs.createReadStream('./kobe.txt');
const writeStream = fs.createWriteStream('./data_copy2.txt');

// 采用这种stream的方法，可能会导致读写不平衡问题，产生『背压』
readStream.on('data', (chunk) => {
  // 每次读取一部分数据
  // console.log(chunk.toString());
  writeStream.write(chunk);
});

// 方式3 - pipe  -- ✅ ✅ ✅ 最佳实践
// 管道流
// readStream.pipe(writeStream);
```

## 3.6 文件的重命名和移动

```js
// 异步方式
fs.rename('./kobe.txt', './james.txt', (err) => {});
// 同步方式
fs.renameSync('./james.txt', './kobe.txt');
fs.renameSync('./james.txt', './jiangsu/kobe.txt');
```

## 3.7 文件删除

在原来大家都是使用`fs.unlink()`方法来删除文件，现在nodejs推荐si使用
`fs.rm()`方法

```js
fs.rm('./archements/kobe.txt', { recursive: false }, (err) => {});

fs.rmSync('./archements/kobe.txt');
```

## 3.8 创建目录

```js
fs.mkdir('./0920/01/02', (err) => {
  if (err) throw err;

  fs.mkdir('./0920/07', (err) => {
    if (err) throw err;
    console.log('Directories created successfully!');
  });
});

//通过递归选项创建多级目录
fs.mkdir('./0920/01/02', { recursive: true }, (err) => {
  if (err) throw err;
  console.log('Directories created successfully with recursive option!');
});
```

## 3.9 读取目录

```js
fs.readdir('./', (err, files) => {
  if (err) throw err;
  console.log(files);
});
```

## 4.0 删除目录

```js
fs.rmdir('./0920/01/02', { recursive: true }, (err) => {
  if (err) throw err;
  console.log('Directories removed successfully!');
});
```

## 4.1 资源的状态

```js
fs.stat('002/08.stat.js', (err, stats) => {
  if (err) {
    console.error('Error retrieving file stats:', err);
    return;
  }
  console.log('File Stats:', stats);
  console.log('Is File:', stats.isFile());
  console.log('Is Directory:', stats.isDirectory());
  console.log('File Size (bytes):', stats.size);
  console.log('Created At:', stats.birthtime);
  console.log('Last Modified At:', stats.mtime);
});
```

## 4.2 文件当前的目录

```js
// 当前目录
console.log(__dirname);
// 当前的文件
console.log(__filename);
```

# 4. path模块

主要都是文件路径相关的处理

```js
// 拼接绝对路径
const absolutePath = path.resolve(__dirname, 'file.txt'); 
console.log('Absolute Path:', absolutePath);
// 获取操作系统的路径分隔符
console.log(path.sep);

// 获取文件的基础名称
console.log(path.basename(absolutePath));

// 获取文件的扩展名
console.log(path.extname(absolutePath));

// 解析路径为对象
const parsedPath = path.parse(absolutePath);
console.log('Parsed Path:', parsedPath);

// 格式化路径对象为字符串
const formattedPath = path.format(parsedPath);
console.log('Formatted Path:', formattedPath);

// 判断路径是否为绝对路径
console.log('Is Absolute:', path.isAbsolute(absolutePath));

// 获取两个路径之间的相对路径
const relativePath = path.relative(__dirname, absolutePath);
console.log('Relative Path:', relativePath);

// 拼接多个路径片段
const joinedPath = path.join(__dirname, 'subdir', 'file.txt');
console.log('Joined Path:', joinedPath);

// 标准化路径，解决多余的分隔符和相对路径
const normalizedPath = path.normalize(joinedPath + '/../file.txt');
console.log('Normalized Path:', normalizedPath);
// 获取路径的目录名
console.log('Directory Name:', path.dirname(absolutePath));
```

