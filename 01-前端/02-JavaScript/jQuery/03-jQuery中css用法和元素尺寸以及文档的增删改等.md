#前端 #JavaScript #jQuery 

# 1. `css()`方法使用

## 2.1 获取元素的css属性值

```js
$('selector').css('background-color') // 获取background-color的属性值
```

## 2.2 设置元素的css属性值

```js
/*k-v形式*/
$('selector').css('background-color','red')

/*接收对象*/
$('table tr:odd').first().css({
        background: 'red',
        width: '1000px',
        height: '400px',
})  
```


# 2. offset()和position()的用法

- `offset()` 获取相对页面左上角的位置，返回一个对象，此对象有两个属性：top和left

```js
/*读取*/
const $offset = $('selector').offset();
console.log($offset.top, $offset.left);

/*设置*/
$('selector').offset({
   top:50,
   left:50
})
```

- `position()` 获取相对父元素的左上角的位置   返回一个对象，此对象有两个属性：top和left

```js
/*读取*/
const $position = $('selector').position();
console.log($position.top, $position.left);

/*设置*/
$('selector').position({
   top:50,
   left:50
})
```

# 3. scrollTop() 和 scrollLeft()  使用

在有滚动条的元素中，获取滚动条的位置

- `scrollTop()` 垂直滚动条的位置
- `scrollLeft()` 水平滚动条的位置
这个方法同样的是读写一致的，想设置的话，直接传参数


## 3.1 有一个小需求： 实现回到顶部的逻辑

### 方案1： 直接设置 $('html').scrollTop(0)

`$('html').scrollTop(0)`  这种方式不够平滑，他是瞬间到顶部

### 方案2： 通过循环定时器的方式，实现平滑

#循环定时器  

```js
// 总共高度
let distance = $('html').scrollTop() + $('body').scrollTop();
// 总时间
let time = 500;
// 间隔时间
const intervalTime = 50;
// 每次移动的距离
const itemDistance = distance / (time / intervalTime);
// 使用循环定时器
const intervalId = setInterval(() => {
	distance -= itemDistance;
	// 到达顶部，停止定时器
	if (distance <= 0) {
	    distance = 0;
		clearInterval(intervalId);
	}
	$('html').scrollTop(distance);
}, intervalTime);
```

### 方案3：采用jQuery动画效果(==推荐使用==)

```js
$('#backTop').click(() => {
  $('html').animate(
	{
	  scrollTop: 0,
	},
	500
  );
});
```


# 4. 元素尺寸

```js
$('#myElement').width(); // 获取内容区的宽度 ，不包含 padding 和border 以及margin
$('#myElement').innerWidth();  // 获取内容区+padding的宽度  不包含 border和margin
$('#myElement').outerWidth();  // 获取内容区+padding+border的宽度  不包含 margin
$('#myElement').outerWidth(true);  // 获取内容区+padding+border+margin的宽度 
```

# 5. 文档的操作

## 5.1 添加元素信息

- `append(content)`  在匹配的元素内部末尾插入内容
- `$(content).appendTo([selector/jquery元素对象])`  将内容插入到某个匹配的元素内
- `prepend(content)` 在匹配的元素内部开头插入内容
- `$(content).prependTo([selector/jquery元素对象])`  将内容插入到某个匹配的元素内的开头
- `after(content)`  在匹配的元素的外面后面插入内容
- `before(content)` 在匹配的元素的外面前面插入内容
- `$(content).insertAfter([selector/jquery元素对象])`  和之前的`appendTo`类似，是一个方向性问题，是将内容插入到选中元素的后面
- `$(content).insertBefore([selector/jquery元素对象])`  和之前的`prependTo`类似，是一个方向性问题，是将内容插入到选中元素的前面

## 5.2  包裹元素  wrap等方法

### 5.2.1 `wrap()` 和 `wrapAll()` 的区别
这两个方法都用于为元素添加一个包装器，但它们的区别在于**包装的方式**。

 - `wrap()`：为每个元素分别添加一个包装器,**每个元素都独立地添加一个相同的父级容器**。 
    你有一个 HTML 结构：
    ```html
    <p>段落 1</p>
    <p>段落 2</p>
    ```
    如果你运行以下代码：
    ```javascript
    $('p').wrap('<div></div>');
    ```
    最终的 HTML 结构会变成：
    ```html
    <div>
      <p>段落 1</p>
    </div>
    <div>
      <p>段落 2</p>
    </div>
    ```
    它为每个 `<p>` 都创建了一个独立的 `<div>` 容器。

- `wrapAll()`：为所有元素添加一个共同的包装器，选中的**所有元素作为一个整体**，并用一个**共同的父级容器**来包裹它们。
    假设你有一个 HTML 结构：
    ```html
    <p>段落 1</p>
    <p>段落 2</p>
    ```
    如果你运行以下代码：
    ```javascript
    $('p').wrapAll('<div></div>');
    ```
    最终的 HTML 结构会变成：
    ```html
    <div>
      <p>段落 1</p>
      <p>段落 2</p>
    </div>
    ```
    它只创建了一个 `<div>`，并把所有 `<p>` 都放了进去。

### 5.2.2 `wrapInner()`：为元素的内部内容添加包装器

`wrapInner()` 方法不是包裹元素本身，而是为**每个元素内部的所有子元素**添加一个包装器。
```html
<div class="box">
  <span>子元素 1</span>
  <span>子元素 2</span>
</div>
```
如果你运行以下代码：
```javascript
$('.box').wrapInner('<div></div>');
```
最终的 HTML 结构会变成：
```html
<div class="box">
  <div>
	<span>子元素 1</span>
	<span>子元素 2</span>
  </div>
</div>
```
它为 `.box` 里面的内容添加了一个新的 `<div>`。

### 5.2.3  `unwrap()`：移除元素的直接父级包装器

`unwrap()` 方法是 `wrap()` 系列方法的反向操作，用于移除元素的**直接父级元素**。
假设你有一个 HTML 结构：
```html
<div>
  <p>段落 1</p>
</div>
```
如果你运行以下代码：
```javascript
$('p').unwrap();
```
最终的 HTML 结构会变回：
```html
<p>段落 1</p>
```
它会删除 `<p>` 的直接父级 `<div>`。

## 5.3 复制替换元素

- `clone([boolean])`  创建匹配元素的深层副本，传入`true` 作为参数时，还会复制其事件处理器和数据

```js
$('li').first().clone().appendTo('#id')   // 复制第一个li元素添加到id下
```

- `.replaceWith(content)`  用新内容替换匹配的元素

```js
$('p').replaceWith('<div>这是新的段落</div>');  //  会用新的<div>标签替换所有的<p>标签
```

- `.replaceAll(content)`  正好是`.replaceWith`的反方向， 用一个元素替换所有匹配的选择器

```js
$('<div>这是新的段落</div>').replaceAll('p');    // 和上面的效果是一样的
```

## 5.4 移除元素

- `.remove()`  删除匹配的元素及其所有后代，同时删除与元素绑定的所有事件处理器和数据。当一个元素确定不要了，可以用这个删除
- `.detach()`  和`remove`类似， 但是会保留数据和事件，  一般用于临时移除一个元素，然后在某个时间点又需要这个元素的时候，可以用他作为临时删除
- `.empty()`  清空匹配元素内部的所有后代元素，但保留元素本身。

## 5.5 获取和设置内容

- `.html([content])`   不传参的时候获取第一个匹配元素的HTML内容；传参的时候，设置所有匹配的元素的HTML内容
- `.text([contnet])`  不传参的时候获取所有匹配的纯文本内容；传参的时候，设置所有匹配元素的纯文本内容
- `.val([value])`   表单中需要用到，比如获取`<input>`的值，传参就是设置值


