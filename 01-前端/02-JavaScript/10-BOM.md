#前端 #JavaScript #JS #BOM 


# 1. window对象

BOM的核心就是`window`对象，表示的是浏览器的实例。

因为`window`对象被复用为Global对象，所以通过var声明的所有变量和函数都会变成`window`对象的属性和方法。

## 1.1 窗口大小

所有的现代浏览器都支持4个属性:
- innerWidth
- innerHeigh
- outerWidth
- outerHeigh

## 1.2 视口位置

```js
window.pageXoffset // 或者 window.scrollX
window.pageYoffset  // 或者 window.scrollY

window.scrollBy(0,100) // 相对于当前视口向下滚动100px
```

## 1.3 导航与打开新窗口

`window.open()`

接收4个参数
1. 要加载的URL
2. 目标窗口
3. 特性字符串
4. 表示新窗口在浏览器历史记录中是否替代当前加载页面的boolean

```js
window.open("http://www.wrox.com/",
	"wroxWindow",
	"height=400,width=400,top=10,left=10,resizable=yes");
// 这行代码会打开一个可缩放的新窗口，大小为 400 像素×400 像素，位于离屏幕左边及顶边各 10 像
// 素的位置。
```


## 1.4 定时器

`setTimeout(() => {}, 1000)`  


## 1.5 系统对话框

- alert
- confirm
- prompt

## 1.6 location对象

location对象是BOM中最常用的对象之一，提供了当前窗口中加载的文档信息以及通常的导航功能。
这个对象有个独特的地方，它既是`window`的属性，也是`document`的属性，`window.location === document.location`。

### 1.6.1 常用的属性

- location.hash
- location.host
- location.href
- location.hostname
- location.pathname
- location.port

### 1.6.2 操作地址

可以修改浏览器的访问地址  
- `location.assign('http://www.baidu.com')`
- `location.href = 'http://www.baidu.com'`
- `window.location = 'http://www.baidu.com'`
以上三种效果是一样，但是`location.href`是最常见的

### 1.6.3 navigator对象

有一个很重要的需求就是检查浏览器是否安装了某个插件

```js
for (let plugin of window.navigator.plugins) {
    console.log(plugin);
  }
```

## 1.7 localStorage 和  sessionStorage

存储相关信息保存在浏览器中，比如用户信息，一些字典信息等。。。





