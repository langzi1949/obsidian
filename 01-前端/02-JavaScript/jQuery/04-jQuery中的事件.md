#前端 #JavaScript  #jQuery #事件 

整体而言，jQuery中的事件和JavaScript的原生事件一样，用到的话直接查询API文档。
# 1. 点击事件

```js
$(selector).click(() => {})
$(selector).on('click', () => {})
```

# 2. 鼠标移入移出事件

```js
/*方式1*/
$(selector).mouseenter(() => {}).mouseleave(() => {})
/*方式2*/
$(selector).on('mouseenter', () => {}).on('mouseleave', () => {})
/*方式3*/
$(selector).hover(() => {}, () => {})  // 第一个函数是移入的回调，第二个是移出的回调
```

# 3. 删除事件

```js
$(selector).off(); //  删除所有事件
$(selector).off('click') // 删除点击事件
```


# 4. 获取事件信息

```js
$(selector).click((event) => {})  // 接收参数event
```

# 5. 停止事件冒泡

```js
$('.inner').click((event) => {
	event.stopPropagation();   // 停止事件冒泡
})
```

# 6. 阻止事件默认行为

```js
$('.inner').click((event) => {
	event.preventDefault();   // 阻止事件默认行为
})
```

# 7. 事件委托

参考[[12-事件|JS原生事件委托]]

但是jQuery提供了完美的封装和简化，让实现事件委托变得非常容易

核心语法
`$(parentSelector).on(eventType, childSelector, callback)`

- `parentSelector`   事件监听器绑定的父元素
- `eventType`    事件类型，比如：`click`等
- `childSelector`   一个字符串，用于指定父元素内的哪个子元素触发事件时才执行回调函数
- `callback`  事件出发时执行的回调函数

```js
// 1. 实现事件委托
// 将点击事件绑定到父元素 ul 上
$('#myList').on('click', 'li', function() {
  // 2. jQuery 会自动处理 event.target
  // 在这里，this 关键字指向被点击的那个 li 元素
  console.log('你点击了:', $(this).text());
  $(this).css('background-color', 'yellow');
});

// 动态添加新元素
$('#addBtn').on('click', function() {
  const newItemCount = $('#myList li').length + 1;
  const newLi = `<li>动态项目 ${newItemCount}</li>`;
  $('#myList').append(newLi);
});
```

在jQuery中，如果要处理动态添加的元素或者大量同类型的元素的事件，始终推荐使用`.on()`的事件委托形式。






