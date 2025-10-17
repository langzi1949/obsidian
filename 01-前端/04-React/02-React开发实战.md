#react #redux #react-router

# 1.  创建一个React项目

之前都是`create-react-app`这个脚手架，但是这个脚手架官方已经不维护了，现在都推荐`vite`进行项目初始化的搭建

```shell
npm create vite
```

后面跟着询问回答就OK了

# 2. 调整初始化的目录结构

vite搭建的毕竟是一个很宽泛的几乎空的脚手架，有时候和实际的开发稍微有点差距，我们要做一点微调然后实现

- 在`src`下面创建`components`目录，未来所有的componet都写在这个目录下面，推荐使用`component.jsx`的方式来创建组件，而不是用每个目录下创建`index.jsx`的方式来，那样会很麻烦
- 根目录下的`index.css`换一个名字`global.css` 未来全局的样式都放在此处
- 每个组件的样式文件直接放在`components`下， 然后命名为`component.module.css`，这样就OK了
- 下载引入两个常用的模块`npm install nanoid axios`

# 2.1 解决跨域问题

一般来说，跨域问题都是后端来解决或者将前后端都放在同一个域名下。

在前端开发模式下

- 修改`vite.config.js`

```js hl:7-17
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

// https://vite.dev/config/
export default defineConfig({
  plugins: [react()],
  server: {
    // 配置代理
    proxy: {
      '/api': {
        // 请求以 /api 开头时，代理到后端
        target: 'http://localhost:3001',
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/api/, ''), // 重写路径，去掉 /api
      },
    },
  },
});
```

现在网络ajax请求用`axios`[https://axios-http.com/]

# 3. 一般的开发要求

- React的组件推荐使用函数式组件
- 在函数式组件中需要用到`useState`或者`useEffect`等hook函数
- 可以不要写原生的`css`文件，如果要写`css`推荐使用`tailwind css`和`sass`


# 4. SPA中的路由

## 4.1 SPA的理解

1. single page application
2. 整个应用只有一个完整的页面
3. 点击页面中的链接**不会刷新**页面，只做页面的局部刷新
4. 数据都是通过Ajax方式进行请求

## 4.2 路由的理解

1. 什么是路由？
	1. 一个路由就是一个映射关系  (key: value)
	2. key为路径path， value可能是`function`或者`component`
2. 路由分类
	1. 后端路由
		1. 理解： value是function，用来处理客户端请求
		2. 注册路由   router.get(path, function() {})
		3. 工作过程：当node接收到一个请求的时候，根据请求路径找到正确的路由，然后调用路由中的回调函数
	2. 前端路由
		1. 浏览器路由：value就是`component`  用于展示页面内容
		2. 注册路由: `<Route path='/test' component={Test}>`
		3. 工作过程： 当浏览器的path变成`/test`时，当前路由组件就变成了`Test`

前端路由依赖于浏览器的BOM里面的history，这个是H5推出的一个特性，不会触发浏览器的重新加载页面


## 4.3 react-router-dom的理解

这个是react的一个插件库，专门用来实现一个SPA应用，基于react的项目基本上都会用到此库

- 安装此库

```shell
npm install react-router-dom
```

> [!danger]
> 需要注意的是react-router v5 以及v6 之间是一次大版本的更新，两者有很大的区别，需要注意


- 最佳实践

### 4.3.1  在`index.jsx` `root`根节点要开启路由

```jsx hl:9
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import { BrowserRouter } from 'react-router-dom';
import './index.css';
import App from './components/App';

createRoot(document.getElementById('root')).render(
  <StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </StrictMode>
);
```

### 4.3.2  定义路由规则

```jsx
<Routes>
	<Route path="/home" element={<Home />} />
	<Route path="/about" element={<About />} />
</Routes>
```

### 4.3.3 添加`Link`

```jsx
<Link to="/about">关于我</Link>
```


这样的话，就可以点击link中的关于我，就根据路由规则找到`Home`组件


### 4.3.4 可以使用`NavLink`

`NavLink`和`Link` 最大的区别就是`NavLink` 在点击激活后，在`className` 中添加一个类名 `active`  代表激活

```ts

// 这个是之前react-router v5版本的，但是可以说明一个问题
<NavLink activeClassName='active' className = 'li-style'  to="/about">关于我</NavLink>
<NavLink activeClassName='active' className = 'li-style'  to="/home">首页/NavLink>
<NavLink activeClassName='active' className = 'li-style'  to="/brand">品牌页</NavLink>

// 以上代码不太优雅，因为相同的部分太多了
// 可以封装一下， 会将所有的props回调到组件内
function MyLink({...props}) {

return 
<NavLink activeClassName='active' className = 'li-style' {...props}></NavLink>
}
```

### 4.3.5 重定向

有时候需要将一个地址重定向到指定的地方，可以采用

```ts
import { Routes, Route, Navigate } from 'react-router-dom';

function App() {
  return (
    <Routes>
      <Route path="/" element={<h2>首页</h2>} />
      
      {/* ✅ 将旧的 /profile 路由重定向到 /dashboard */}
      <Route path="/profile" element={<Navigate to="/dashboard" />} />
      
      <Route path="/dashboard" element={<h2>仪表盘</h2>} />
    </Routes>
  );
}
```

> [!question]
> 设置二级路由后，刷新后，css样式文件找不到了，如何解决？
> 1. 在 index.html 中使用绝对路径 (推荐)，如果是`vite`构建的话，它会自动转为绝对路径，可以不用处理
> 2. 在`vite-config.js`中设置base路径  比如  `base:/my-app/`

### 4.3.6 嵌套路由，实现多级路由

在v6版本中，实现嵌套路由会变得非常优雅，它的核心思想是：**父路由组件负责渲染公共的布局，并使用一个特殊的组件` <Outlet /> `作为子路由内容的占位符。**

`<Outlet/>`是一个特殊的组件，它是一个占位符，当父路由匹配成功时，它的子路由组件就会被渲染到 <Outlet /> 的位置。

1. 定义路由结构

```js
// src/App.jsx
import { Routes, Route, Link } from 'react-router-dom';
import Home from './pages/Home';
import Dashboard from './pages/Dashboard';
import Profile from './pages/dashboard/Profile';
import Settings from './pages/dashboard/Settings';

function App() {
  return (
    <div>
      <nav>
        <Link to="/">首页</Link>
        <Link to="/dashboard">仪表盘</Link>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        
        {/* ✅ 一级路由：仪表盘 */}
        <Route path="/dashboard" element={<Dashboard />}>
          {/* ✅ 二级路由：个人资料，注意这里是相对路径 */}
          <Route path="profile" element={<Profile />} />
          
          {/* ✅ 二级路由：设置 */}
          <Route path="settings" element={<Settings />} />

          {/* ✅ 默认子路由，当访问 /dashboard 时显示 */}
          <Route path="" element={<h3>请选择一个子菜单</h3>} />
        </Route>
      </Routes>
    </div>
  );
}

export default App;
```

2. 在父组件中渲染 `<Outlet />`

```js
// src/pages/Dashboard.jsx
import { Link, Outlet } from 'react-router-dom';

function Dashboard() {
  return (
    <div>
      <h2>仪表盘主页</h2>
      <nav>
        <ul>
          {/* ✅ Link 也是相对路径 */}
          <li><Link to="profile">个人资料</Link></li>
          <li><Link to="settings">设置</Link></li>
        </ul>
      </nav>
      <hr />
      
      {/* ✅ Outlet 占位符：子路由的内容将在这里渲染 */}
      <Outlet />
      
    </div>
  );
}

export default Dashboard;
```

其余的子组件就执行创建就OK了

### 4.3.7 向路由组件传递参数

一般来说，这个是最常用的情况了，点击某个信息，查看详情信息 ，但是详情信息需要知道相关的属性才可以，那么就涉及到向路由组件传递参数了

#### 4.3.7.1  URL参数

类似  `/app/note/32423sdfsadd`  查看note的详情

如何实现？
- 定义路由：在 `<Route>` 的 `path` 属性中，使用`冒号 : `来定义动态参数。
- 获取参数：在组件中，使用 useParams Hook 来获取 URL 中的参数。

```js
// src/App.jsx - 定义路由
import { BrowserRouter, Routes, Route, Link } from 'react-router-dom';
import UserProfile from './UserProfile';

function App() {
  return (
    <BrowserRouter>
      <Routes>
        {/* ✅ 定义 URL 参数 :userId */}
        <Route path="/user/:userId" element={<UserProfile />} />
      </Routes>
      <Link to="/user/123">查看用户 123</Link>
    </BrowserRouter>
  );
}

// src/UserProfile.jsx - 获取参数
import React from 'react';
import { useParams } from 'react-router-dom';

function UserProfile() {
  // ✅ 使用 useParams() Hook 获取参数
  const { userId } = useParams();

  return (
    <div>
      <h2>用户详情</h2>
      <p>用户 ID：{userId}</p>
    </div>
  );
}
```


####  4.3.7.2  查询参数

类似  `/app/note?search=zmg&pageIndex=1`   一般这些都是可选参数

如何实现：

- 设置参数：使用 `<Link to="/path?key=value">`或` useSearchParams` Hook。

- 获取参数：使用 `useSearchParams` Hook。

```js
// src/App.jsx - 定义路由
import { BrowserRouter, Routes, Route, Link } from 'react-router-dom';
import SearchResults from './SearchResults';

function App() {
  return (
    <BrowserRouter>
      <Routes>
        {/* ✅ 定义路由，查询参数不需要在 path 中声明 */}
        <Route path="/search" element={<SearchResults />} />
      </Routes>
      <Link to="/search?q=react&page=1">搜索 React</Link>
    </BrowserRouter>
  );
}

// src/SearchResults.jsx - 获取参数
import React from 'react';
import { useSearchParams } from 'react-router-dom';

function SearchResults() {
  // ✅ 使用 useSearchParams Hook 获取参数
  const [searchParams, setSearchParams] = useSearchParams();

  // 获取 'q' 和 'page' 参数
  const query = searchParams.get('q');
  const page = searchParams.get('page');

  return (
    <div>
      <h2>搜索结果</h2>
      <p>查询词：{query}</p>
      <p>页码：{page}</p>
      <button onClick={() => setSearchParams({ q: query, page: parseInt(page) + 1 })}>
        下一页
      </button>
    </div>
  );
}
```

#### 4.3.7.3 State（用于不可见的、复杂的数据）

> [!danger]
> 注意这个`state`和 组件三大属性`state`不是同一个概念


State 是一种在导航时传递数据的隐式方式，数据不显示在 URL 中。

特点：数据不暴露在 URL 中，可以传递对象等复杂数据类型，但刷新页面后数据会丢失。适用于表单提交后传递临时数据。

如何实现：
- 传递：在 `<Link>的 state prop` 中传递数据，或在 `useNavigate` 的第二个参数中传递。
- 获取：使用 `useLocation` Hook 获取。

```ts
// src/components/FormPage.jsx - 传递 state
import React from 'react';
import { useNavigate } from 'react-router-dom';

function FormPage() {
  const navigate = useNavigate();

  const handleSubmit = () => {
    const user = { name: 'Alice', role: 'admin' };
    // ✅ 在 navigate 中传递 state
    navigate('/success', { state: { user: user } });
  };

  return (
    <div>
      <button onClick={handleSubmit}>提交表单</button>
    </div>
  );
}

// src/components/SuccessPage.jsx - 获取 state
import React from 'react';
import { useLocation } from 'react-router-dom';

function SuccessPage() {
  // ✅ 使用 useLocation Hook 获取 state
  const location = useLocation();
  const user = location.state?.user;

  if (!user) {
    return <h2>未找到用户信息</h2>;
  }

  return (
    <div>
      <h2>提交成功！</h2>
      <p>用户名: {user.name}</p>
      <p>角色: {user.role}</p>
    </div>
  );
}
```

如果`state`方式用声明式处理，使用 `<Link>`组件的 `state` 属性

```js
// src/components/FormPage.jsx
import React from 'react';
import { Link } from 'react-router-dom';

function FormPage() {
  const user = { name: 'Alice', role: 'admin' };

  return (
    <div>
      <h2>表单页面</h2>
      <p>准备通过声明式方式传递数据。</p>
      
      {/* ✅ 在 Link 组件的 state 属性中传递数据 */}
      <Link 
        to="/success" 
        state={{ user: user }} // 传递一个包含 user 对象的 state
      >
        提交表单
      </Link>
    </div>
  );
}

export default FormPage;
```

### 4.4 编程式路由

使用`useNavigate`  钩子函数来实现

```js
// 登录成功后，传递用户信息到首页
navigate('/dashboard', { 
  state: {
    fromLogin: true, 
    user: { name: 'Alice', role: 'admin' }
  } 
});

// 在 dashboard 组件中，通过 useLocation 获取状态
import { useLocation } from 'react-router-dom';

function DashboardPage() {
  const location = useLocation();
  const user = location.state?.user;

  return (
    <div>
      <h1>欢迎, {user?.name}!</h1>
    </div>
  );
}
```

# 5. Redux状态管理库


> [!tips]
> redux是一个专门用于做状态管理的JS库，并不只是服务React，在Angular和Vue中都能使用
> 它的作用： ==**集中式管理react应用中多个组件共享的状态**==

## 5.1 什么时候用redux

- 组件间状态共享
- 一个组件需要改变另一个组件的状态
- 总体原则： 能用就不用，如果不用共享就比较吃力

## 5.2 Redux的原理

![[Pasted image 20250822100325.png]]


**Redux的三大核心原则**

1. 单一数据源
	1. 整个应用的状态都存储在一个**唯一的、集中的、不可变**的JavaScript对象中，这个对象被称为`Store`
	2. 这种集中化的管理，使得应用的状态变得可预测和可追踪
2. 状态是只读的
	1. 改变状态的唯一方式是触发(dispatch)一个Action
	2. 任何组件都不能直接修改状态
3. 使用纯函数来执行修改
	1. 负责根据Action来更新状态的函数称为 `Reducer`
	2. `Reducer`必须是一个纯函数，它只接收旧的状态(previousState)和一个Action，然后返回一个全新的状态(newState)。它不会修改旧状态，也不会有任何的副作用


**Redux的处理流程**

1. 视图： 用户在页面上进行交互，比如点击一个按钮
2. 派发Action  (Dispatch Action) ：组件通过调用`store.dispatch(action)`的方法，派发一个Action对象
	1. Action就是一个普通的JS的对象，它必须包含`type`属性，用于描述『发生了什么』比如 `{type:'ADD', text='学习Redux'}`
3. Reducer计算   ：被派发的Action到达唯一的Reducer函数
	1. Reducer函数接收当前的`state`和刚刚派发过来的`action`函数
	2. Reducer根据`action.type`，计算出下一个状态，并返回这个新的状态对象
4. Store更新   ： Store接收到Reducer返回的新状态，用这个新状态来替换掉旧状态
5. 视图更新：Store通知所有订阅了状态变化的组件，这个组件就会重新渲染以反映最新的状态

简化来说：
 ==**视图  --> Dispatch Action --> reducer --> Store更新 --> 视图更新**==

## 5.3 使用

- 安装库

`npm install @reduxjs/toolkit react-redux`

后续的看代码或者查看官方文档即可


# 6. 组件间通信方式汇总

父子组件： 直接采用props
兄弟组件：pubs-sub的消息订阅方式、集中式管理redux 或者zustand
祖孙组件：pubs-sub的消息订阅方式、集中式管理redux 或者zustand  + content





