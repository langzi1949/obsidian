#前端 #JavaScript #jQuery 

# 1.  获取属性

`attr(attrName)`   获取一个匹配的属性信息，返回值是string类型

特别注意：在`input`的`checkbox`的情况下，应该如何获取checked的属性值呢

```html
<input id="check1" type="checkbox" checked="checked">
```

```js
console.log($('check1').attr('checked')); // checked
console.log($('check1').prop('checked'))  // true
console.log($('check1').is(':checked'))   // true 
```

`prop(attrName)`  获取一个属性名称，可以返回字符串、boolean、数字等类型


# 2. 设置属性

## 2.1 传统方式  attr(name, value)

```js
$('div').attr('title','这个一个值');  // 会覆盖原有title
```

## 2.2 对象方式   attr(obj)

```js
$('div').attr({'title':'这个一个值','alt':"value"});
```

## 2.3  回调函数方式 attr(name, function)

前提，这个属性值一定已经存在，要不然回调函数中的参数就是undefined，这个方法的作用只是对原来的title进行加工
```html
<div id="div1" title="first sec">这个是第一个div</div>
```
```js
$('#div1').attr('title', function (i, val) {
      return val + 'hells';
    });
/*最终div的title 为first sechells*/ 
/*注意，不是对title里面是整体的字符串*/
```

## 2.4 采用prop(name, value)

```js
$('#div1').prop('title', 'kobe');

$('#div1').prop({ title: 'kobes' })

$('#div1').prop('title', (index, item) => {
        return item + 'sssss';
})
```

> [!danger]
> ==那么attr() 和prop()有啥区别呢==
> - 操作的对象不一样，attr() 操作的是HTML Attributes； prop()操作的是DOM
> - 返回值不一样，attr() 总是返回字符串；prop() 可以返回字符串、boolean、数字等
> - 代表的状态不一样， attr() 适合那些不会随着交互而改变的属性； prop() 获取当前的状态，会随交互而改变的
>  
>  ==最佳实践==
>  - attr() 适合那些自定义属性、不变的属性、或者获取元素的原始值
>  - prop()  适合处理表单元素的动态状态，比如`checked`、`selected`、`disabled`等
>
>  **总结： 优先使用`prop()`，只有操作那些纯HTML的属性的时候，才用`attr()`**


# 3. 移除属性

`removeAttr(属性名称)`
`removeProp(属性名称)`

# 4. 添加class name信息

- `addClass(className)`  这个是单值
- `addClass(classNames)` 可以传数组
- `addClass(function)`   对原有的class属性进行补充和加工 
```js
$( "ul li" ).addClass(function( index ) {
  return "item-" + index;
});
```
- `$( "p" ).removeClass( "myClass noClass" ).addClass( "yourClass" );` 可以结合removeClass来操作  

# 5. 移除class name信息

`$( "p" ).removeClass( "myClass yourClass" )`
此方法可以不接受参数
`$( "p" ).eq( 1 ).removeClass();`  移除所有class信息

# 6. 得到元素文本信息

```html
<div id="div1" title="first sec">这个是<span>sdds</span>第一个div</div>
```

```js
console.log($('#div1').html());  // 这个是<span>sdds</span>第一个div   包含html的标签信息
console.log($('#div1').text());   // 这个是sdds第一个div   不包含html信息
```

# 7. 给一个元素设置html文本信息

`$('#div1').html('jiangsu<span>我的</span>')`  html()方法读写是同一个，不传参就是读，传参就是写入

# 8. 查找和过滤方法

jQuery提供了一些使用的DOM筛选的方法，就是通过查找和过滤来实现的

## 8.1 查找方法

### 8.1.1 向下查找

- `.find(selector)` 从当前集合中的每个元素开始，在==**所有后代元素**==中查找匹配的元素
- `.children([selector])`  和find不同，他是==**查找的直接子元素**==
### 8.1.2 向上查找

- `.parent([selector])`  获取每个元素的直接父元素
- `.parents([selector])`  获取每个元素的所有祖先元素，知道html跟标签
- `.closest(selector)`  从当前元素开始向上找，返回与选择器匹配的第一个祖先元素

### 8.1.3 旁边查找

- `.sibings([selector])`  获取匹配的所有同级元素(不包含自身)
- `.next()`   紧邻的下一个同级兄弟元素
- `.prev()`  紧邻的上一个同级兄弟元素
- `.nextAll()`  后面的所有同级兄弟元素
- `.prevAll()`  前面的所有同级兄弟元素

## 8.2 过滤方法

- `.filter(selector)`  从当前集合中，筛选出与给定选择器匹配的元素
- `.not(selector)`   从当前集合中移除与给定选择器匹配的元素
- `.has(selector)`  筛选出那些包含某个特定后代元素的元素
- `.last()、.first()、 .eq(index)`等

>[!danger]
> `find()`和 `filter()`的区别
> 
> - `find()`是向下查询，是从当前集合中的元素开始，在内部向下寻找匹配
> - `filter()` 是从当前集合中的元素里面筛选符合条件的，最终的范围肯定是在这些集合中


# 9. 一个最佳实践

原则：**行为与样式分离**

场景：给一个表格中偶数行设置背景色
```html
<table>
  <tr>
	<td>Row with Index #0</td>
  </tr>
  <tr>
	<td>Row with Index #1</td>
  </tr>
  <tr>
	<td>Row with Index #2</td>
  </tr>
  <tr>
	<td>Row with Index #3</td>
  </tr>
</table>
```

## 9.1 方案1： css()方法

```js
$('table tr:odd').css('background-color', 'yellow');
```

这个有一个缺点：他是修改的style属性，会触发浏览器重绘，每次修改会有一定的性能开销

## 9.2 方案2： addClass() 进行样式与行为分离

1. 先用`css`样式文件中提前定义好样式，一般用`class`选择器

```css
table .odddd {
	background-color: yellow;
}
```

2. 然后通过`addClass`的方式，给对应的元素添加类名

```js
$('table tr:odd').addClass('odddd');  // 这个意思就是给偶数的tr添加类为 odddd
```

##  9.3 哪种更好

结论： ==**方案2**==更加好
- 分离关注点
	- CSS负责样式
	- JavaScript负责行为
	- 这样代码更加容易维护
- 性能优势
	- css()方法会导致重绘页面，有性能开销
	- addClass()方法，通常更加高效
- 可维护性和可扩展性
	- 以后样式调整只需要改CSS文件即可

那么直接使用`css()`的场景是啥呢？
- **动态计算的样式**： 需要一些变量或者计算结果来动态设置样式的，例如鼠标位置的变化影响样式的
- **一次性简单的样式调整**： 如果只是上面那种最简单的样式调整，可以偶尔用用`css()`方法，千万不能到处都在用


