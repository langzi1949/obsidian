
# 1. Express框架初体验

```shell
# 先初始化npm
npm init -y
# 安装express
npm install express
```

```js title=入口文件(比如app.js)
// 1. 导入模块
const express = require('express');
// 2. 初始化Express
const app = express();
const port = 3000;
// 3. 添加路由
app.get('/home', (req, res) => {
	res.send('Hello Express')
});
// 4. 启动服务，进行监听
app.listen(port, () => {
  console.log(`Example app listening at http://localhost:${port}`);
});
```

# 2. request的相关信息

```js
app.get('/home', (req, res) => {
  // 原生的 Node.js 提供的 req 对象
  // console.log(req.method);
  // console.log(req.url);
  // console.log(req.headers);

  // express 提供的 res 对象
  console.log(req.path);
  console.log(req.query);
  console.log(req.ip);
  // 获取header中的相关属性信息
  console.log(req.get('User-Agent'));

  res.send('Hello World!');
});

app.get('/about/:id', (req, res) => {
  // 路由参数
  console.log(req.params.id);
  res.end('About Page');
});
```

## 2.1 获取request的body的数据

```js
const express = require('express');
const app = express();

// Express 5.x body parser配置
app.use(express.json({ 
  limit: '50mb',
  verify: (req, res, buf, encoding) => {
    // 验证原始数据
    console.log('Raw body length:', buf.length);
  }
}));

app.use(express.urlencoded({ 
  extended: true, 
  limit: '50mb' 
}));

app.use(express.text({ type: 'text/*' }));
app.use(express.raw({ type: 'application/octet-stream' }));

// 通用的body日志中间件
app.use((req, res, next) => {
  if (req.body) {
    console.log(`${req.method} ${req.path} - Body type:`, typeof req.body);
    console.log('Body content:', req.body);
  }
  next();
});

// JSON API示例
app.post('/api/data', (req, res) => {
  const { name, email, metadata } = req.body;
  
  // Express 5.x 改进的错误处理
  if (!name || !email) {
    const error = new Error('Name and email are required');
    error.status = 400;
    throw error;
  }
  
  res.json({
    success: true,
    received: { name, email, metadata },
    timestamp: new Date().toISOString()
  });
});

// Form数据示例
app.post('/form', (req, res) => {
  const formData = req.body;
  
  res.json({
    message: 'Form data received',
    data: formData,
    fields: Object.keys(formData)
  });
});

// 错误处理（Express 5.x改进）
app.use((err, req, res, next) => {
  if (err.type === 'entity.parse.failed') {
    return res.status(400).json({ 
      error: 'Invalid JSON in request body' 
    });
  }
  
  if (err.type === 'entity.too.large') {
    return res.status(413).json({ 
      error: 'Request body too large' 
    });
  }
  
  res.status(err.status || 500).json({
    error: err.message || 'Internal Server Error'
  });
});

app.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

```js title=简化版
// 1. 解析JSON格式的请求
app.use(express.json());
// 2. 解析URL-encoded 格式的请求体，比如用于HTML表单提交的数据
app.use(express.urlencoded({ extended: true })); // extended: true 允许解析更复杂的对象和数组

app.post('/api/users', (req, res) => {
  console.log(req.body);
  const { id, name } = req.body;

  console.log(`${id} ---- ${name}`);

  res.send('User created');
});
```
## 2.2 404路由如何解决

在Express的 5.x版本中，和4.x版本不一样了，我这边直接说5.x版本的知识，当所有的没有匹配上之后，就会出现匹配这个最终的兜底路由

```js
// 这个是express 5.x中的使用方式来处理404
app.use((req, res) => {
  res.status(404).send('404 Not Found1111111');
});
```

## 2.3 express的响应设置

就是给response设置一些数据，比如header中添加k-v等

```js
app.get('/response', (req, res) => {
  // 设置响应状态码(原生的方式)
  // res.statusCode = 201;
  // res.statusMessage = 'Created';
  // res.setHeader('k-name', 'k-value');
  // res.write('Hello Response');

  // express 提供的方式
  // res.status(202);
  // res.set('k-name-2', 'k-value-2');
  // res.send('Hello Response 2 你好');

  // 通过链式
  res.status(203).set('k-name-3', 'k-value-3').send('Hello Response 3');

  res.end();
});
```

## 2.4 其他的response响应设置

```js
app.get('/index', (req, res) => {
	// 重定向
  res.redirect('/home');
});

// 下载
app.get('/download', (req, res) => {
  // 设置响应头 Content-Disposition
  // 方式1
  // res.set(
  //   'Content-Disposition',
  //   'attachment; filename="hello.txt"; filename*=utf-8\'\'hello%E4%B8%96%E7%95%8C.txt'
  // );
  // res.send('Hello Download!');

  // 方式2
  res.download(__dirname + '/jiangsu.txt');
});

// 传输json数据
app.get('/json', (req, res) => {
  const data = {
    name: '张三',
    age: 20,
  };
  res.json(data);
});
```

# 3. 中间件  middleware

这个跟PHP有点像，在Laravel中，middleware就是针对路由进行一些加强操作，比如auth或者格式化包装结果等

## 3.1 什么是middleware

本质上是一个回调函数，middleware函数可以访问`request`和`response`， 用于封装公共操作

## 3.2 middleware 类型

- 全局middleware
- 路由middleware

## 3.3 全局middleware

```js
// 一个全局的middleware
function logger(req, res, next) {
  console.log(`${req.method} ${req.url} - ${new Date().toISOString()}`);
  next();
}
// 使用这个middleware
app.use(logger);
```

## 3.4 路由middleware

路由middleware就是针对单独的route进行设置middleware，不影响其他的route

```js
const router = express.Router();

const checkAuth = (req, res, next) => {
  console.log('相关的验证' + req.url);
  next();
};

// 这个就是当前这个路由，都会走到checkAuth
// router.use(checkAuth);

// 只有/create 会走到checkAuth的逻辑
router.get('/create', checkAuth, (req, res) => {
  res.send('创建成功');
});

// /list不会走到checkAuth的逻辑
router.get('/list', (req, res) => {
  res.send('获取列表成功');
});

// 最后需要将router应用到/post路径下
app.use('/post', router);
```

正常情况下，会将一个路由单独一个文件，比如`/post` 单独一个文件，最后导出这个`router`

```js
module.exports = router
```

## 3.5  忽略某个middleware

一般都是通过条件判断来做
### 3.5.1 在路由层面使用数组跳过

1. 不要使用`app.use()`进行全局绑定， 先定义一个middleware

```js
// 定义middleware
const globalMidware = (req, res, next) => {
	console.log('全局中间件运行了');
	next();
};
```

2. 创建路由组并应用中间件
```js
const express = require('express');
const app = express();

// 1. 需要全局中间件的路由（将中间件作为参数传入）
app.get('/protected/data', [globalMiddleware], (req, res) => {
    res.send('这是受保护的数据');
});

// 2. 豁免全局中间件的路由（不传入中间件）
app.get('/public/api', (req, res) => {
    // 这个路由会被豁免，globalMiddleware 不会运行
    res.send('这是公共API');
});

// 3. 将中间件应用于一个 Router 实例（模块化）
const protectedRouter = express.Router();
protectedRouter.use(globalMiddleware); // 只对 protectedRouter 内的路由生效

protectedRouter.get('/profile', (req, res) => {
    res.send('用户档案');
});

app.use('/v1', protectedRouter); // 只有 /v1/profile 会运行 globalMiddleware
```

### 3.5.2 使用条件判断中间件，针对app.use()的变通

1. 先定义排除列表

```js
const EXCLUDED_ROUTES = ['/public/api', '/health/check'];

const excludeMiddleware = (req, res, next) => {
    // 检查当前请求的 URL 是否在排除列表中
    if (EXCLUDED_ROUTES.includes(req.path)) {
        // 如果在排除列表中，直接跳到下一个中间件或路由（即跳过 globalMiddleware）
        return next('route'); 
    }
    // 如果不在排除列表中，继续执行正常的中间件流程
    next(); 
};
```

2. 在`app.use()`中使用

```js
const express = require('express');
const app = express();
// 1. 设置条件判断中间件
app.use(excludeMiddleware);
// 2. 设置需要被豁免的全局中间件
app.use(globalMiddleware); 
// 3. 定义路由
app.get('/public/api', (req, res) => { /* ... */ });
app.get('/protected/data', (req, res) => { /* ... */ });
// ...
```

## 3.6 静态文件middleware

为啥需要用到静态文件middleware？ 有时候后端访问前端的一些静态文件，比如js或者css文件，使用静态文件中间件的话，就不需要设置相关路由，会自动装载路由

```js
// 使用静态中间件
// app.use(express.static(__dirname + '/../public'));
// 同样可以设置/assets 虚拟前缀
app.use('/assets', express.static(__dirname + '/../public'));
```

## 3.7 防盗链如何实现？

主要通过request的header中的属性`Referer` 是否是我们允许的域名或者ip

```js
// allowedHosts.js
const ALLOWED_HOSTS = [
    'yourdomain.com',       // 你的主域名
    'www.yourdomain.com',   // 你的 www 域名
    'localhost:3000',       // 开发环境
    // 可以添加 CDN 域名等
];

const antiHotlinkMiddleware = (req, res, next) => {
    // 获取 Referer 头
    const referer = req.get('Referer'); 

    // 场景 1: 如果 Referer 为空（例如：用户直接在浏览器输入地址，或通过邮件链接访问）
    // 这种情况下通常应该允许访问，但你可以根据需求调整
    if (!referer) {
        return next(); 
    }

    // 解析 Referer，获取主机名
    try {
        const url = new URL(referer);
        const host = url.host;

        // 检查主机名是否在白名单内
        if (ALLOWED_HOSTS.includes(host)) {
            // 在白名单内，放行
            return next(); 
        }

    } catch (e) {
        // 如果 Referer 格式不正确，通常拒绝访问
        console.error('Invalid Referer URL:', referer);
        return res.status(403).send('Forbidden: Invalid Referer');
    }

    // 场景 2: 如果 Referer 不在白名单内，则阻止访问
    // 返回 403 状态码，并发送一张替代图片
    console.log(`Hotlink attempt blocked from: ${referer}`);

    // --- 防盗链处理：返回替代图片或 403 错误 ---
    // 方案 A: 返回 403 错误
    // return res.status(403).send('Forbidden: Hotlinking is not allowed.'); 
    
    // 方案 B: 重定向到替代图片（更友好的处理）
    // 假设你有一个名为 'denied.png' 的图片
    // 注意：这里的 /images/denied.png 必须是静态资源中间件能够访问到的路径
    return res.redirect('/images/denied.png'); 
};

module.exports = antiHotlinkMiddleware;
```

# 4. 路由模块化

1. 在一个`routes`目录下创建一个js文件

```js title=user.js
const express = require('express');
const router = express.Router();

router.get('/admin', (req, res) => {
  res.send('Admin后台管理系统');
});

module.exports = router;
```

2. 使用

```js
const user = require('./routes/user');
app.use(user);
```

# 5. 模板引擎

目前nodejs中常见的模板引擎有两个
1. pug -  语法简单，强调缩进，跟Python有点类似

```js
const express = require('express');
const app = express();

app.set('view engine', 'pug'); // 告诉 Express 使用 Pug 引擎
app.set('views', './views');   // 模板文件放在 /views 目录下
```


2. ejs -  最接近原生的HTML

```js
const express = require('express');
const app = express();

app.set('view engine', 'ejs');
```

相关学习直接参考文档即可 [https://ejs.co/](https://ejs.co/)

# 6. express-generator

这个就是生成express项目的脚手架，但是生成的内容有点多，现代前端开发趋势为前后端分离，不需要用`ejs`或者`pug`这些模板引擎，所以`express-genertor`用的比较少了。
大家都是直接用一个空目录，自行搭建即可；如果是中小项目，不需要前后端分离可以尝试一下`express-generator`

# 7. 关于TypeScript

现代企业级的项目，推荐使用`typescript` ，方便写出可维护的代码；但是个人项目或者小型其实就直接使用Javascript就行

# 8. ORM

目前主流的Node js相关的ORM框架
- Sequelize    
- prisma    这个是搭配Typescript使用

## 8.1 Sequelize

相关的文档请看[https://sequelize.org/](https://sequelize.org/)




