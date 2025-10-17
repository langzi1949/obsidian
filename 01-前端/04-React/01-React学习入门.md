#前端 #react

# 1. 什么是React？

React是由Facebook开发和维护的，用于构建用户界面的JavaScript库，专注UI层的库。

**react的核心**

- **组件化**： 这个是react最核心的优势，组件是独立的、可复用的，这让代码结构更加清晰、更加易于维护和扩展
- **高效的性能**：react使用虚拟DOM的概念，最后通过和真实DOM进行diff，只更新变化的部分，这样大大提升性能
- **声明式编程**：只需要描述UI在给定状态下是什么样子的就行，如何更新由react完成
- **强大的社区生态**


# 2. 前期准备

跟着B站上的尚硅谷视频进行学习

从react的前世今生开始学起来，然后过渡到最新版本的react

依赖四个包，临时都用cdn的形式进行引入，后续还是通过`npm`包管理进行整合
- 两个react的核心包
	- `react.development.js`
	- `react-dom.development.js`
- 一个babel.js的包，用于进行转换jsx
- 一个prop-types 类型校验的包

```html
<!-- react的核心库-->
<script crossorigin src="https://unpkg.com/react@17/umd/react.development.js"></script>
<!-- react-dom 用于支持react操作DOM-->
<script crossorigin src="https://unpkg.com/react-dom@17/umd/react-dom.development.js"></script>
<!-- babel，用于将jsx转为js-->
<script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
<!-- prop-types的库，进行类型校验等-->
<script crossorigin src="https://unpkg.com/prop-types@15/prop-types.js"></script>

```

# 3. 什么是jsx

jsx就是js的一个语法拓展，允许写类似html的标签，然后通过babel进行转换为普通的JavaScript的对象
简单来说就是可以让我们更加直观和更声明式的方式在JS中创建或者描述UI的结构

# 4. jsx语法规则

- 定义虚拟DOM时，不要写引号，直接就类似写HTML就行
- 标签中混入JS表达式时要用`{}`，其实就是表达式处理
- 样式的类名指定不要用`class`，要用`className`
- 内联样式要用`style= {{key:value}}`的形式去写，如果key是类似`font-size` 这种的，需要变成驼峰形式`fontSize`
- 一个虚拟DOM，只能有一个根标签， 好在react推出了 `<></>`空标签的方式，这样最终渲染就剥离这个空标签了
- 标签必须闭合
- 标签首字母
	- （1）若小写字母开头，则将该标签转为HTML中同名元素，若HTML中无该标签对应的同名元素，则报错
	- （2）若大写字母开头，react就去渲染对应的组件，若组件没有定义，则报错

# 5. 面向组件编程

react有两种方式定义组件
- 函数式组件
- 类式组件

## 5.1 函数式组件

```js
// 1. 创建函数式组件
function MyComponent() {
	console.log(this); // 此处的this是undefined，因为babel编译后开启了严格模式，严格模式下方法体内的this不能指向window，所以只能是undefined
	return <h2>我是一个函数式定义的组件【用于简单组件】</h2>;
}
// 2. 渲染组件到页面
ReactDOM.render(<MyComponent />, document.getElementById('test'));
```

什么是简单组件呢？ 一句话解释：没有状态的组件，可以认为是简单组件

## 5.2 类式组件

```js
// 1. 创建类式组件
class MyComponent extends React.Component {
	render() {
	  return <h2>我是一个类式定义的组件【用于简单复杂组件】</h2>;
}
}
// 2. 渲染组件到页面
ReactDOM.render(<MyComponent />, document.getElementById('test'));
```

一句话解释复杂组件：有状态变化的组件，可以认为是复杂组件

# 6. React的三大核心属性

## 6.1  state属性

这个是一个组件类实例的一个属性，可以控制这个属性的变化来对页面进行调整。

- 首先，`state`是对象，当然你可以是其他类型的数据，但最好是对象
- 针对`state`的所有操作必须调用他的`this.setState`方法，不能直接修改，因为直接修改不符合规范

> [!danger]
> react是如何进行事件绑定的？
> - 整体和原生JS是一致的
> 	- 内联的事件，比如onclick等
> 	- 通过`element.onclick = () => {}` 通过这种方式
> 	- 通过`element.addEventListener('click', () => {})`
> - 那么react的jsx中的写法都支持上面的三种，但是在内联的事件上稍微有点不一样
> 	- 原生的是 `<button onclick="handleClick()">点击我</button>`
> 	- JSX的语法是`<button onClick={handleClick}>点击我</button>`
> 	- 注意： 所有的事件都是驼峰命名的方式，这一点需要特别注意
> 	- 注意： 绑定的值不一样，原生的是字符串，JSX是一个函数引用

```jsx
// 1. 创建类式组件
  class MyComponent extends React.Component {
	// 实例对象上的属性可以直接通过这种方式进行初始化
	state = { isHot: true };
	// render几乎没有办法简化了
	render() {
	  return (
		<h2 onClick={this.changeWeather}>
		  今天天气{this.state.isHot ? '炎热' : '凉爽'}
		</h2>
	  );
	}

	// 使用箭头函数，this就绑定定义此函数的地方，指这个类实例对象本身
	changeWeather = () => {
	  const { isHot } = this.state;
	  this.setState({ isHot: !isHot });
	};
  }
```

在16.8版本后，函数式组件的state属性如何整？

- 使用React的`useState` hook函数

```jsx
import {useState} from 'react';
function App() {
	const [name, setName] = useState('zmg'); // 初始化state，存一个name的属性
	const change = ()=> {
		setName('czh'); // 也可以传入一个函数
		setName(() => 'czh')
	}
return (<div>App: {name}</>)
}

```

## 6.2 props属性

用于给组件传入标签的属性值，同时可以配合`prop-types`对相关的属性值进行校验

```js
// 1. 创建类式组件
  class MyComponent extends React.Component {
	// 对MyComponent中的props进行验证和校验
	static propTypes = {
	  name: PropTypes.string.isRequired,
	  age: PropTypes.number,
	  sex: PropTypes.string,
	};

	// 默认值的设置
	static defaultProps = {
	  age: '100',
	  sex: '未知',
	};
	
	render() {
	  const { name, age, sex } = this.props;
	  return (
		<ul>
		  <li>姓名：{name}</li>
		  <li>年龄：{age}</li>
		  <li>性别：{sex}</li>
		</ul>
	  );
	}
  }

  // 2. 渲染组件到页面
  ReactDOM.render(
	<MyComponent name="zmg" age={'20'} />,
	document.getElementById('test1')
  );
```

针对函数式组件中的props，直接通过函数的参数进行传入即可，但是需要注意，要解构参数。

## 6.3 refs属性

它让你能够直接访问一个 React 元素所对应的底层 DOM 节点或组件实例。
可以将`refs`理解成，在 React 声明式的世界中，通往底层命令式 DOM API 的一道“后门”

### 6.3.1  为啥需要用`refs`呢？

React 的核心理念是声明式地管理 UI，通常情况下，你不应该直接操作 DOM。
你应该通过改变组件的状态（state）来让 React 自动更新 UI，
但是有时候我们在一些特定场景下需要用到操作实际DOM，比如：
- 管理焦点、文本选择或媒体播放（比如在页面加载时自动聚焦一个输入框）
- 触发强制性的动画效果。

### 6.3.2 如何使用？

` <input ref="input1" type="text" placeholder="输入数据" />`

这个里面的`ref`就是指向这个标签元素本身，它都会收集到组件对象的`refs`属性中，这是一个对象

1. `ref` 可以跟上面的处理一样，可以采用字符串，但是目前react已经将其标记为过时了，主要是影响性能


**用回调函数的方式用`ref`**

```js
<input
ref={(current) => (this.input1 = current)}
type="text"
placeholder="输入数据"
/>
```

`ref={(current) => (this.input1 = current)}`   这个是通过一个回调函数的方式使用ref， 表示的意思就是将当前组件对象上添加一个属性`input1`，指向的就是当前的`input`标签元素

>[!danger]
>关于回调函数方式的ref的问题？
> - 使用内联回调的话，会导致这个方法被调用两次，官方的解释就是：`it will get called twice during updates, first with null and then again with the DOM element`
> - 可以不用内联回调，但是也无伤大雅，没有太大问题

**用`createRef`的API的方法**


```js

class MyComponent extends React.Component {
	// 专人专用，给每个ref创建createRef
	input1 = React.createRef();
	// 给input2创建一个
	input2 = React.createRef();

	// 使用的方式
	showInput1 = () => {
		// 这个就输出input1对应的标签元素
		console.log(this.input1.current);
	}
}
```

在函数式组件中如何使用ref呢？
直接使用`useRef` hook函数就行了

# 7. React中的Content使用

Content就是上下文的意思， 它的最主要的功能就是解决跨组件传递数据的问题

使用Content通常分为三个步骤：
1. 创建
2. 提供
3. 消费



# 8. React组件的生命周期

整个React分为两种类型的生命周期
- 类式组件的生命周期
- 函数式组件的生命周期

## 8.1 传统的类式组件的生命周期

1. **挂载阶段**
	1. 这个阶段是组件实例被创建并插入到DOM中
	2. `constructor()`  这个构造函数在创建组件的时候最先被调用，一般可以用于state的初始化或者绑定事件函数的`this`
	3. `render()`  必须要实现的方法，返回JSX的react组件对象，用于组件的渲染
	4. `componentDidMount()`  组件挂载(渲染)到DOM后立即执行，他只会执行有且只有一次，一般这个用于**数据请求、定时器、订阅事件或者直接操作DOM**
	
2. **更新阶段**
	1. 当`props`或者`state`发生变化的时候，组件会重新渲染
	2. `shouldComponentUpdate()`  在 `render() `前调用，用于性能优化。如果此方法返回 false，则组件不会重新渲染， 一般可以作为阀门
	3. `render`  根据新的 `props` 和 `state` 重新渲染 UI。
	4. `getSnapshotBeforeUpdate()`：在 DOM 更新前调用，可以获取 DOM 的一些信息（如滚动位置），通常与 `componentDidUpdate() `配合使用。
	5. `componentDidUpdate()`：组件完成更新并重新渲染到 DOM 后调用。适合在 `props` 或 `state` 变化后执行一些副作用操作。
	
3.  **卸载阶段**
	1. 当组件从 DOM 中被移除时，这个阶段开始。
	2. `componentWillUnmount()`：组件被销毁前调用。在这里进行资源清理，比如取**消网络请求、清除定时器或移除事件监听器，以防止内存泄漏**。

<span class="red-text">注：卸载不会触发更新阶段，不能算作更新</span>

那么如何卸载一个组件呢？

在React 18版本之前，主要用`ReactDOM.unmountComponentAtNode`， 在18之后被废弃了
```js
// 假设你有一个根节点
const container = document.getElementById('root');

// 渲染组件
ReactDOM.render(<App />, container);

// ... 过一段时间后，需要卸载组件
function unmountApp() {
  // 移除 container 上的组件
  ReactDOM.unmountComponentAtNode(container);
}
```


18版本卸载组件的方式为：
```js
import React from 'react';
import { createRoot } from 'react-dom/client';
import App from './App';

// 1. 创建一个新的根
const container = document.getElementById('root');
const root = createRoot(container);

// 2. 渲染组件
root.render(<App />);

// ... 过一段时间后，需要卸载组件
function unmountApp() {
  // 调用 unmount() 方法来卸载组件
  root.unmount();
}
```
## 8.2 函数式组件的生命周期

函数式组件的生命周期主要是靠一系列的`Hooks`来进行管理和处理的

1. **模拟挂载和更新**
	1. `useState()`：用于管理组件的内部状态，它的值变化会触发组件重新渲染。
	2. `useEffect()`：这是函数式组件中最重要的 Hook，用于处理副作用。它可以模拟类组件中的多个生命周期方法。
	3. 挂载时执行：`useEffect(回调函数, [])`，当依赖数组为空时，它只在组件挂载时执行一次。
	4. 每次更新时执行：`useEffect(回调函数)`，不提供依赖数组时，它在每次渲染后都会执行。
	5. 特定状态更新时执行：`useEffect(回调函数, [依赖])`，当依赖数组中的值变化时，它会执行回调。

2. **模拟卸载**
	1. `useEffect()` 中的清理函数：`useEffect` 的回调函数可以**返回一个清理函数**。这个清理函数会在组件卸载前执行，用于清理资源，完美替代了 `componentWillUnmount()`。


# 9. 虚拟DOM中的key的作用

主要是用于DIFF算法来判断是否要更新真实DOM

react在state或者props变化后，会触发渲染，然后根据【新数据】来生成【虚拟DOM】
然后判断这个新DOM的key和旧有的key是否相同，以及内容是否相同，相同就不更新真实DOM；如果不同会替换之前的真实DOM
原有的DOM中没有这个key，那么直接更新渲染到真实DOM

为啥不用数组的index作为key？ 主要是因为有可能数组的数据被破坏了或者倒序遍历等，这些都会有很多的问题，只有明确数组的遍历确实没有问题，可以使用index，但是一般不推荐

# 10.  Context

`Context`就是上下文，一般适用于`祖组件`和`后代组件`之间的传递，不采用`Context`会导致大量的props传递

- 原理
	- 基于一种**发布-订阅**的模式
- 想定义一个`Context`容器
	- 通过`React.createContext()`  方式进行创建

```js
const ThemeContext = React.createContext('light');
```

- `Provider组件`
	- 这是一个发布者。它通过一个特殊的 `value` 属性，将数据提供给它包裹的所有后代组件。
	- 当这个 `value` 发生变化时，所有订阅了该 Context 的组件都会重新渲染。

```js
<ThemeContext.Provider value="dark">
  {/* 这里的组件及其所有后代都可以访问到 'dark' 值 */}
</ThemeContext.Provider>
```

- `Consumer 组件`
	- 这是一个订阅者，用于获取 Context 中的数据。在现代 React 中，主要有两种方式来订阅：
		- `useContext` Hook：最常用的方式，在函数组件中直接使用。它接收 Context 对象作为参数，返回 Context 的当前值。
```js
const theme = useContext(ThemeContext);
// theme 的值为 'dark'
```
		- `<Context.Consumer> `组件：旧版本的方式，适用于类组件。它使用一个渲染 prop（render prop）模式。


有时候为了优化，比如在context中存放一个对象，可能有多个属性，有的组件只用了其中的某些属性，但是以上的方法会导致，任何属性变化都会进行触发渲染，那么有没有办法，属性没有变化不进行渲染呢？

用`useMemo` ：如果你的 value 是一个对象或数组，并且你不希望所有子组件都重新渲染，可以考虑使用 `useMemo` 来缓存 value 对象，确保只有当依赖项真正变化时才创建新的对象。

```js
import { useState, useMemo } from 'react';
import { ThemeContext } from './ThemeContext';

export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');

  const toggleTheme = () => {
    setTheme(currentTheme => (currentTheme === 'light' ? 'dark' : 'light'));
  };

  // ✅ 使用 useMemo 优化性能
  // 只有当 theme 改变时，才重新创建 value 对象，
  // 避免所有子组件因 value 引用变化而无谓地重新渲染。
  const contextValue = useMemo(() => {
    return { theme, toggleTheme };
  }, [theme]);

  return (
    <ThemeContext.Provider value={contextValue}>
      {children}
    </ThemeContext.Provider>
  );
}
```

# 11. PureComponent

这个是组件优化的惯用伎俩。

## 11.1 背景

有时候，父组件因为state的变化导致子组件压根没有用到这个state但是依然会重新渲染子组件，这样会带来性能的影响

## 11.2 处理方式和原理

这个时候就用上`PureComponent`了，实现了 `shouldComponentUpdate` 这个生命周期方法，并默认进行了浅比较（shallow comparison）。

当创建的组件继承了`PureComponent`，React 会在每次 `props` 或 `state` 更新前，自动为你执行一个浅比较。

- 浅比较就是简单的比较一下值或者对象，如果对象地址没有变化，只是变化了里面的属性，那么他也认为没有变化

## 11.3 类式组件上如何使用

```js
import React, { PureComponent } from 'react';

class MyComponent extends PureComponent {
  render() {
    console.log('组件重新渲染了');
    return (
      <div>
        <p>姓名: {this.props.user.name}</p>
        <p>年龄: {this.props.user.age}</p>
      </div>
    );
  }
}

class App extends React.Component {
  state = {
    user: { name: 'Alice', age: 30 }
  };

  handleClick = () => {
    // ❌ 这样做不会触发 MyComponent 重新渲染，因为对象引用没有变
    const newUser = this.state.user;
    newUser.age = 31;
    this.setState({ user: newUser });
    
    // ✅ 这样做会触发 MyComponent 重新渲染，因为创建了新对象
    // this.setState({ user: { ...this.state.user, age: 31 } });
  }

  render() {
    return (
      <div>
        <MyComponent user={this.state.user} />
        <button onClick={this.handleClick}>更新年龄</button>
      </div>
    );
  }
}
```

## 11.4 函数式组件如何使用

使用`React.memo`这样的高阶组件。使用`React.memo` 包裹一下函数组件就OK了

```js
import React, { useState } from 'react';

// 假设这是一个耗费性能的子组件
const HeavyComponent = React.memo(({ user }) => {
  console.log('HeavyComponent 重新渲染了');
  return (
    <div>
      <p>姓名: {user.name}</p>
      <p>年龄: {user.age}</p>
    </div>
  );
});

function App() {
  const [count, setCount] = useState(0);
  const [user] = useState({ name: 'Alice', age: 30 }); // user 对象只创建一次

  return (
    <div>
      {/* 按钮每次点击都会改变 App 组件的 state，导致 App 重新渲染 */}
      <button onClick={() => setCount(count + 1)}>
        App 计数器: {count}
      </button>

      {/* 尽管 App 重新渲染了，但 HeavyComponent 的 user prop 引用没有变，
          所以 React.memo 会阻止其重新渲染。 */}
      <HeavyComponent user={user} />
    </div>
  );
}

export default App;
```

## 11.5 不生效的情况，比如组件间传递函数

```js
// ❌ 错误做法：每次 App 重新渲染，`onUserClick` 都会是新函数，导致 memo 失效
const HeavyComponent = React.memo(({ onUserClick }) => {
  console.log('HeavyComponent 重新渲染了');
  return <button onClick={onUserClick}>点击我</button>;
});

function App() {
  const [count, setCount] = useState(0);
  const onUserClick = () => console.log('我被点击了'); // ❌ 每次 App 渲染都会是新函数
  
  return (
    <div>
      <button onClick={() => setCount(count + 1)}>App 计数器: {count}</button>
      <HeavyComponent onUserClick={onUserClick} />
    </div>
  );
}
```

## 11.6 使用`useCallback` 解决上述问题

```js
// ✅ 正确做法：使用 useCallback 保持函数引用稳定
const HeavyComponent = React.memo(({ onUserClick }) => {
  console.log('HeavyComponent 重新渲染了');
  return <button onClick={onUserClick}>点击我</button>;
});

function App() {
  const [count, setCount] = useState(0);
  
  // ✅ 使用 useCallback 确保函数引用不变，直到 count 改变
  const onUserClick = useCallback(() => {
    console.log('我被点击了');
  }, []); /* 空数组表示函数永远不会变 */

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>App 计数器: {count}</button>
      <HeavyComponent onUserClick={onUserClick} />
    </div>
  );
}
```

# 12. Render props

`Render Props `是一种在 React 中共享组件之间代码和逻辑的技术。它的核心思想是：将一个组件需要渲染的内容，通过一个 props（这个 props 是一个函数）来传递。

## 12. 1 Render Props 的核心原理

你可以把一个组件想象成一个“逻辑黑盒”。

- `没有 Render Prop`：黑盒内部的逻辑和渲染内容是固定的。
- `有了 Render Prop`：你给黑盒一个名为 render 或 children 的函数道具。黑盒内部的逻辑不变，但它不再渲染固定的内容，而是调用你给它的函数，让这个函数来决定最终渲染出什么。

## 12.2  类式组件中使用

- 实现一个 Render Props 组件 (Logic Provider)

```jsx
// 实现一个 Render Props 组件 (Logic Provider)
import React from 'react';

class MouseTracker extends React.Component {
  state = { x: 0, y: 0 };

  handleMouseMove = (event) => {
    this.setState({
      x: event.clientX,
      y: event.clientY,
    });
  };

  componentDidMount() {
    window.addEventListener('mousemove', this.handleMouseMove);
  }

  componentWillUnmount() {
    window.removeEventListener('mousemove', this.handleMouseMove);
  }

  render() {
    // 检查 props.render 是否是函数，并调用它
    if (typeof this.props.render === 'function') {
      // ✅ 将状态作为参数传递给 render prop
      return this.props.render(this.state);
    }
    return null;
  }
}
```

- 使用组件，在父组件中使用

```jsx hl:10
import React from 'react';
import MouseTracker from './MouseTracker';

function MousePositionTracker() {
  return (
    <div style={{ height: '100vh', border: '1px solid #ccc' }}>
      <h1>移动鼠标来追踪位置</h1>
      <MouseTracker
        // ✅ 传递一个函数作为 props
        render={(mouseState) => (
          <div>
            <p>鼠标 X: {mouseState.x}</p>
            <p>鼠标 Y: {mouseState.y}</p>
          </div>
        )}
      />
    </div>
  );
}

export default MousePositionTracker;
```


## 12.3 函数式组件中使用

函数式组件中使用很简单，其他逻辑不写了，直接写最关键的部分

- 实现一个 Render Props 组件 (Logic Provider)

```jsx
import React, { useState, useEffect } from 'react';

function MouseTracker(props) {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  useEffect(() => {
    // 相关逻辑
  }, []);

  // ✅ 渲染 props.render()
  return props.render(position);
}
```

- 使用这个组件 (Consumer)，方式和类组件一样

```jsx
import React from 'react';
import MouseTracker from './MouseTracker';

function MousePositionTracker() {
  return (
    <div style={{ height: '100vh', border: '1px solid #ccc' }}>
      <h1>移动鼠标来追踪位置</h1>
      <MouseTracker
        render={(mouseState) => (
          <div>
            <p>鼠标 X: {mouseState.x}</p>
            <p>鼠标 Y: {mouseState.y}</p>
          </div>
        )}
      />
    </div>
  );
}
```

## 12.4. 函数式组件使用-方式2

使用 `children` 作为` Render Prop`

- 实现一个 props children 组件 (Logic Provider) 

```jsx
import React, { useState, useEffect } from 'react';

function MouseTracker(props) {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  useEffect(() => {
    // 相关逻辑
  }, []);

  // ✅ 渲染 props.render()
  return props.children(position);
}
```

- 使用组件，直接把函数作为子元素传过去就行

```jsx
import React from 'react';
import MouseTracker from './MouseTracker';

function MousePositionTracker() {
  return (
    <div style={{ height: '100vh', border: '1px solid #ccc' }}>
      <h1>移动鼠标来追踪位置</h1>
      <MouseTracker>
      {/* ✅ 将函数作为子元素传递 */}
       {(mouseState) => (
          <div>
            <p>鼠标 X: {mouseState.x}</p>
            <p>鼠标 Y: {mouseState.y}</p>
          </div>
        )}
      <MouseTracker/>
    </div>
  );
}
```

## 12.5 更加优雅的方案

在现代 React 开发中，自定义 Hooks 已经成为更主流的替代方案

```jsx
// 使用自定义 Hook 的现代方式
import React, { useState, useEffect } from 'react';

function useMousePosition() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  // ... 逻辑与上面相同
  return position;
}

function MousePositionTracker() {
  const mouseState = useMousePosition();
  return (
    <div>
      <p>鼠标 X: {mouseState.x}</p>
      <p>鼠标 Y: {mouseState.y}</p>
    </div>
  );
}
```

# 13. React中的错误边界（ErrorBoundary）

在 React 中，`ErrorBoundary`（错误边界是一种特殊的 React 组件，它能捕获子组件树中发生的 JavaScript 错误，并渲染一个备用的 UI，而不是导致整个应用崩溃。

**核心原理与要求**
一个组件要成为错误边界，必须满足两个条件：
- 它必须是类组件。
- 它必须定义以下两个生命周期方法中的一个或两个：
	- static getDerivedStateFromError(error)：在后代组件抛出错误后被调用，用于更新 state，以便在下一次渲染时显示备用 UI。
	- componentDidCatch(error, errorInfo)：在后代组件抛出错误后被调用，用于执行副作用，比如将错误日志发送到外部服务。


```js
import React from 'react';

class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  // ✅ 1. getDerivedStateFromError 用于渲染备用 UI
  static getDerivedStateFromError(error) {
    // 更新 state，以便下一次渲染可以显示降级 UI
    return { hasError: true, error: error };
  }

  // ✅ 2. componentDidCatch 用于执行副作用
  componentDidCatch(error, errorInfo) {
    // 你可以在这里将错误日志发送到外部服务，例如 Sentry 或 Bugsnag
    console.log('--- 错误信息已捕获 ---');
    console.error('组件堆栈:', errorInfo.componentStack);
    console.error('错误:', error);
    // 假设你有一个日志服务
    // logService.logErrorToMyService(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      // 你可以渲染任何你想要的备用 UI
      return (
        <div style={{ padding: '20px', border: '1px solid red' }}>
          <h2>出错了!</h2>
          <p>很抱歉，当前组件无法正常显示。</p>
          {/* 可选：在开发模式下显示错误详情 */}
          {this.state.error && <p>错误详情: {this.state.error.toString()}</p>}
        </div>
      );
    }

    // 如果没有错误，渲染其子组件
    return this.props.children;
  }
}

export default ErrorBoundary;
```

- 使用

针对有可能出错的组件进行包裹即可

```js
import React from 'react';
import ErrorBoundary from './ErrorBoundary';
import BuggyCounter from './BuggyCounter';

function App() {
  return (
    <div style={{ textAlign: 'center', margin: '50px' }}>
      <h1>使用 ErrorBoundary</h1>
      
      {/* 使用错误边界包裹住可能会出错的组件 */}
      <ErrorBoundary>
        <BuggyCounter />
      </ErrorBoundary>
      
      <hr style={{ margin: '30px 0'}} />
      
      {/* 这个组件不受上面错误的影响，将正常工作 */}
      <p>这个组件在错误边界之外，它将正常显示。</p>
    </div>
  );
}

export default App;
```