#前端 #Bootstrap5 

# 1. 文本相关的类

## 1.1 文本对齐

- `.text-{breakpoint}-start`   左对齐
- `.text-{breakpoint}-end`  右对齐
- `.text-{breakpoint}-center`  居中对齐
- `.text-{breakpoint}-justify`  两端对齐

## 1.2 文本颜色

注意： 不支持breakpoint，因为这种有语义化含义，不应该在不同屏幕上有不同的展示

- `text-primary`  主要颜色  蓝色
- `text-secondary`  次要颜色  灰色
- `text-success`   绿色
- `text-danger`     危险，红色
- `text-warning`   黄色
- `text-info`    青色
- `text-light`    浅色
- `text-dark`   深色
- `text-muted`    柔和的灰色   用于次要文本

### 1.2.1 文本颜色的透明度

- 必须和上面的文本颜色配合起来使用
- 格式 `.text-opacity-{value}`
	- value的取值范围只有0，25，50，75 ，100
	- 越大代表的就是不透明度越高，颜色越深

## 1.3  字体大小和粗细

- `.fw-bold` 和 `.fw-bolder` 和 `.fw-semibold`      粗体、 更粗体、中等粗体
- `.fw-medium`  中等
- `.fw-normal`  正常粗细
- `.fw-light`   和 `.fw-lighter` 细体、更细
- `.fst-italic`  斜体
- `.fst-normal`  正常
- `.fs-1` 到`.fs-6`   设置字体大小
- `.text-decoration-underline`  下划线
- `.text-decoration-line-through`   中划线
- `.text-decoration-none`  去除下划线

## 1.4 文本换行和转换类

- `.text-nowrap`  阻止文本换行
- `.text-wrap`   自动换行
- `.text-break`  自动换行，但是他比`.text-wrap` 强大在可以处理一个超长单词的换行，`.text-wrap` 只会在有空格的时候会换行
- `.text-truncate`  超出部分截断并在末尾显示省略号， 这个类需要在块或者行内块中才有效
- `.text-uppercate`  大写
- `.text-lowercase`  小谢
- `.text-capitalize`  首字母大写

## 1.5  行高与间距类

 `lh` 是`line-heigh`的缩写 
- `.lh-1`   行高和字体大小相同，文本行之间几乎没有间距
- `.lh-sm`  行高1.25倍
- `.lh-base`  行高1.5倍
- `.lh-lg`  行高2倍
- `.font-monospace`  使用等宽字体

#  2. 背景色

## 2.1 背景颜色

和上面的`.text-{color}` 一样，只是前缀不一样而已，背景相关的都以`bg`开头

- `.bg-{color}`

还有一些特殊的，主要是适配主题模式的
- `.bg-body`     设置为页面的主要背景色，通常是白色（在浅色模式下）或黑色（在深色模式下）。
- `.bg-body-secondary`   设置为页面的次要背景色。这个颜色比 .bg-body-body 稍浅，常用于与主体内容形成微妙的对比。
- `.bg-body-tertiary`  设置为页面的第三层级背景色，比 secondary 颜色更浅。这个颜色在深色模式下会更深，以提供清晰的层次。  一般用于页脚、边栏、卡片头部等辅助性UI组件

## 2.2 渐变色效果

使用`.bg-gradient` 给背景颜色添加一个柔和的渐变效果

```html
<div class="bg-primary bg-gradient p-3 text-white">
    这是一个带渐变的主色调背景。
</div>
```

## 2.3 透明度

和文本处理一样，采用`.bg-opacity-*`  必须和`color`类集合起来使用

# 3. 给整个元素设置透明度

- `.opacity-{value}`

```html
<div class="opacity-100">...</div>
<div class="opacity-75">...</div>
<div class="opacity-50">...</div>
<div class="opacity-25">...</div>
<div class="opacity-0">...</div>
```

# 4.  边框 border

## 4.1 激活边框或者显示边框

```html
<span class="border"></span>    // 显示所有边框
<span class="border-top"></span>   // 显示上边框
<span class="border-end"></span>  // 显示右边框 
<span class="border-bottom"></span>  // 显示下边框
<span class="border-start"></span>    // 显示左边框

<!-- 还可以用取反的逻辑 -->
<span class="border border-0"></span>   // 隐藏所有的边框
<span class="border border-top-0"></span>   // 隐藏上边框
<span class="border border-end-0"></span>    // 隐藏右边框
<span class="border border-bottom-0"></span>  // 隐藏下边框
<span class="border border-start-0"></span>   // 隐藏左边框
```

## 4.2 边框颜色

同`text`、`background`一样，采用`.border-{color}`

## 4.3  边框颜色透明度

同`text`、`background`一样，采用  `.border-opactity`

## 4.4 边框的宽度

- `.border-{size}`  
	- size 的取值范围为 1到5

```html
<span class="border border-1"></span>
<span class="border border-2"></span>
<span class="border border-3"></span>
<span class="border border-4"></span>
<span class="border border-5"></span>
```

## 4.5 设置圆角

用`.rounded-*` 来表达

- `.rounded`  添加默认的圆角
- `.rounded-0`  移除所有圆角
-  `.rounded-top`   只有顶部有圆角
- `.rounded-end`   只有右侧有圆角
- `.rounded-bottom`  只有底部有圆角
- `.rounded-start`  只有左侧有圆角
- `.rounded-circle`   标准的圆形
- `.rounded-pill`   扁的胶囊形状

另外，设置圆角还有特殊的用法
- `rounded-1`  到`rounded-5`  角度不同的圆角
- 类似`rounded-bottom-1`  只调整一边的角度的大小
- `rounded-start-circle`   左侧是圆形
- `rounded-end-pill`    右侧是胶囊形状

# 5.  overflow的设置

```html
<div class="overflow-auto">...</div>
<div class="overflow-hidden">...</div>
<div class="overflow-visible">...</div>
<div class="overflow-scroll">...</div>
```

# 6. 定位 position

```html
<div class="position-static">...</div>
<div class="position-relative">...</div>
<div class="position-absolute">...</div>
<div class="position-fixed">...</div>
<div class="position-sticky">...</div>
```

同样可以进行定位的偏移
- `{property}-{position}`
	- `{property}` 
		- `top`
		- `start`
		- `bottom`
		- `end`
	- `{position}`
		- `0`  
		- `50`  50%
		- `100`  100%

```html
<div class="position-relative">
  <div class="position-absolute top-0 start-0"></div>
  <div class="position-absolute top-0 end-0"></div>
  <div class="position-absolute top-50 start-50"></div>
  <div class="position-absolute bottom-50 end-50"></div>
  <div class="position-absolute bottom-0 start-0"></div>
  <div class="position-absolute bottom-0 end-0"></div>
</div>
```

居中元素

- `translate-middle`  元素进行控制
- 同样可以在`x`轴和`y`轴上进行控制


```html
<div class="position-relative">
  <div class="position-absolute top-0 start-0 translate-middle"></div>
  <div class="position-absolute top-0 start-50 translate-middle"></div>
  <div class="position-absolute top-50 start-0 translate-middle-y"></div>
  <div class="position-absolute top-50 start-50 translate-middle"></div>
  <div class="position-absolute top-50 end-0 translate-middle-y"></div>
</div>
```

用position经常用于如下的场景
![[Pasted image 20250806110916.png]]

# 7. 阴影 Shadow

- 使用`shadow-none` 没有阴影效果
- `shadow-{breakpoint}`  开启阴影效果
一般这个阴影效果要呈现的漂亮，需要跟背景色配合起来使用

```html
<div class="shadow-none p-3 mb-5 bg-body-tertiary rounded">No shadow</div>
<div class="shadow-sm p-3 mb-5 bg-body-tertiary rounded">Small shadow</div>
<div class="shadow p-3 mb-5 bg-body-tertiary rounded">Regular shadow</div>
<div class="shadow-lg p-3 mb-5 bg-body-tertiary rounded">Larger shadow</div>
```

# 8. 尺寸 大小

## 8.1 相对父元素

- `.w-{value}`
	- `{value}`
		- 可选范围 25,50,75,100,auto   代表的是百分比
高度同样也是这样设置

同样也可以设置 `max-width` 和 `max-height`

- `mx-75`  用mx代表 `max-width`
- `mh-75` 用mh代表 `max-width`

## 8.2 相对视口

```html
<div class="min-vw-100">Min-width 100vw</div>
<div class="min-vh-100">Min-height 100vh</div>
<div class="vw-100">Width 100vw</div>
<div class="vh-100">Height 100vh</div>
```


# 9. 垂直居中

这里讨论的垂直居中只是 行内、行内块、行内表、表格元素的垂直居中

```html
<span class="align-baseline">baseline</span>
<span class="align-top">top</span>
<span class="align-middle">middle</span>
<span class="align-bottom">bottom</span>
<span class="align-text-top">text-top</span>
<span class="align-text-bottom">text-bottom</span>
```

# 10. 控制元素的可视

```html
<div class="visible">...</div>
<div class="invisible">...</div>
```

# 11. Z-index

这个属性只有对position有效，所以元素必须开启定位

- `.z-{value}`
	- `{value}`  **从 -1 到 3   -1用n1表达**

```html
<div class="z-3 position-absolute p-5 rounded-3"><span>z-3</span></div>
<div class="z-2 position-absolute p-5 rounded-3"><span>z-2</span></div>
<div class="z-1 position-absolute p-5 rounded-3"><span>z-1</span></div>
<div class="z-0 position-absolute p-5 rounded-3"><span>z-0</span></div>
<div class="z-n1 position-absolute p-5 rounded-3"><span>z-n1</span></div>
```