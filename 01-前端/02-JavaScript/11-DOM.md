#前端 #JavaScript #JS #DOM

# 1. document

`document`是`HTMLDocument`的实例，表达的是整个HTML的页面。 `document`是`window`对象的属性，因此他是一个全局对象。

```js
let title = document.title; // 读取文档标题
document.title = 'newTitle'; // 修改文档标题
```


## 1.1 定位元素

最开始的版本中提供了两个常见的定位元素的方法：`getElementById()` 和 `getElementsByTagName()`。

## 1.2 获取属性

```js
let div = document.getElementById("myDiv");
alert(div.getAttribute("id")); // "myDiv"
alert(div.getAttribute("class")); // "bd"
alert(div.getAttribute("title")); // "Body text"
alert(div.getAttribute("lang")); // "en"
alert(div.getAttribute("dir")); // "ltr"

// 设置属性
alert(div.getAttribute("dir")); // "ltr"
```

## 1.3 创建元素

最常见且安全的方式，就是`document.createElement()`的方法来创建新元素。他接收一个参数，即要创建的元素标签名。

## 1.4 DocumentFragment类型

这个在页面优化的时候特别重要，正常情况下，为了防止dom操作引起页面不断重绘重排，影响页面的性能，有一个很重要的优化，是先把相关的操作用在`DocumentFragment`这个轻量级的文档上，最后再统一添加到`document`上。

```js
let fragment = document.createDocumentFragment();
let ul = document.getElementById("myList");
for (let i = 0; i < 3; ++i) {
	let li = document.createElement("li");
	li.appendChild(document.createTextNode(`Item ${i + 1}`));
	fragment.appendChild(li);
}
ul.appendChild(fragment);
```

## 1.5 动态创建表格

```js
// 创建表格
let table = document.createElement("table");
table.border = 1;
table.width = "100%";
// 创建表体
let tbody = document.createElement("tbody");
table.appendChild(tbody);
// 创建第一行
tbody.insertRow(0);
tbody.rows[0].insertCell(0);
tbody.rows[0].cells[0].appendChild(document.createTextNode("Cell 1,1"));
tbody.rows[0].insertCell(1);
tbody.rows[0].cells[1].appendChild(document.createTextNode("Cell 2,1"));
// 创建第二行
tbody.insertRow(1);
tbody.rows[1].insertCell(0);
tbody.rows[1].cells[0].appendChild(document.createTextNode("Cell 1,2"));
tbody.rows[1].insertCell(1);
tbody.rows[1].cells[1].appendChild(document.createTextNode("Cell 2,2"));
```

## 1.6 添加元素

- `parent.appendChild(child)`   将一个新元素添加到父元素的末尾
- `parent.insertBefore(newChild, referenceChild)`  将一个新元素添加到父元素中指定子元素的前面

## 1.7 复制元素

- `element.cloneNode(deep)`  克隆一个元素
	- deep =  true： 克隆元素及其所有子元素
	- deep = false：  只克隆元素本身，不克隆其子元素

## 1.8 替换元素

- `parent.replaceChild(newChild, oldChild)` 用一个新元素来替换父元素中的旧元素

## 1.9 移除元素

- `parent.removeChild(child)`  从父元素中移除一个指定的子元素
- `element.remove()`    这个更加现代，元素调用remove()就是删除自身，无需引用父元素



# 2. 最重要的DOM selectors API

在实际的开发中，很少使用`document.getElementById()`和`document.getElementByTagName()`，甚至`JQuery`都没有采用这两个方法，都是以CSS选择符查询DOM来获取元素引用。
目前最核心的两个方法是
- `querySelector()`
- `querySelectorAll()`

## 2.1 querySelector()

`querySelector()`方法接收CSS选择符参数，返回匹配该模式的第一个后代元素，如果没有匹配项则返回null。

```js
const urlElement = document.querySelector('header nav ul');
```

最佳实践就是，浏览器上看F12，检查你要操作的元素的选择器是啥，直接拷贝。

## 2.2 querySelectorAll()

`querySelectorAll()`方法跟 `querySelector()`一样，也接收一个用于查询的参数，但它会返回
所有匹配的节点，而不止一个。

## 2.3 matchs()方法

顾名思义，判断是否匹配当前选择的选择器，如果匹配就返回true，否则会返回false。

## 2.4 元素遍历

```js
let parentElement = document.getElementById('parent');
let currentChildElement = parentElement.firstElementChild;
// 没有子元素，firstElementChild 返回 null，跳过循环
while (currentChildElement) {
	// 这就是元素节点，做相应处理
	processChild(currentChildElement);
	if (currentChildElement === parentElement.lastElementChild) {
		break;
	}
	currentChildElement = currentChildElement.nextElementSibling;
}
```

# 3. HTML5拓展的一些方法

最重要的是`document.getElementByClassName()`

## 3.1 自定义数据属性

HTML5允许给元素指定非标准属性，但是要使用前缀`data-`以便告诉浏览器，这些属性既不包含与渲染有关的信息，也不包含元素的语义信息。

```html
<div id="myDiv" data-appId="12345" data-myname="Nicholas"></div>
```
```js
let div = document.getElementById("myDiv");
// 取得自定义数据属性的值
let appId = div.dataset.appId;
let myName = div.dataset.myname;
// 设置自定义数据属性的值
div.dataset.appId = 23456;
div.dataset.myname = "Michael";
// 有"myname"吗？
if (div.dataset.myname){
console.log(`Hello, ${div.dataset.myname}`);
}
```

## 3.2 插入标记

- innertHtml
- innerText

## 3.3 样式

```js
 const liE = document.querySelector('header nav ul li');
 // 对获取的选择器进行样式调整
 liE.style.backgroundColor = 'red';
```

总之是可以对元素进行样式的修改和设置，需要的时候，请查看相关文档

## 3.4 元素的尺寸

有时候我们需要定位和得到相关元素的尺寸来进行计算其他的元素定位的位置等

### 3.4.1 偏移尺寸

- offsetWidth    包含元素内容+padding+border 的值（还有可能涉及到的滚动条的宽度）
- offsetHeight   包含元素内容+padding+border 的值（还有可能涉及到的水平滚动条的高度）
- offsetTop      元素上边框外侧距离包含元素上边框内侧的像素数
- offsetLeft     元素左边框外侧距离包含元素左边框内侧的像素数。

![[Pasted image 20250728121144.png]]


### 3.4.2 客户端尺寸

- clientWidth
- clientHeight

![[Pasted image 20250728121233.png]]

### 3.4.3 滚动尺寸

- scrollHeight
- scrollWidth
- scrollTop
- scrollLeft

### 3.4.4 确定元素的尺寸和位置

相对于浏览器视口的位置来说，每个元素都暴露了`getBoundingClientRect()`方法，返回一个`DOMRect`对象， 包含6个属性
- ==x 或者left==   元素左侧边界到视口左侧的距离
- ==y  或者top==   元素顶部边界到视口顶部的距离
- ==right==      元素右侧边界到视口左侧的距离  = x + width
- ==bottom==    元素底部边界到视口顶部的距离 = y  + height
- ==height==   元素的总高度 = 内容高度 + padding + 边框
- ==width==    元素的总宽度 = 内容宽度 + padding + 边框

![[Pasted image 20250728123808.png]]


