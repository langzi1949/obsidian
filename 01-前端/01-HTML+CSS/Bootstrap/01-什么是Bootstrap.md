#前端 #Bootstrap5

# 1. 什么是Bootstrap

> Build fast, responsive sites with Bootstrap

官网：[[https://getbootstrap.com/]]


Bootstrap的默认设计哲学：**移动优先（Mobile-First）**

# 2. 核心的逻辑

- 预制了一系列丰富的组件样式
- 响应式的布局
- 网格化的布局
- 可以自定义相关布局等

# 3. Breakpoint

![[Pasted image 20250805134113.png]]
为了进行响应式布局，针对不同的屏幕大小做了一个大致的划分。

当后续类中如果没有加上`breakpoint` 那么就代表的是所有屏幕，然后加上一个`breakpoint` 那么就代表  >= 对应的尺寸的屏幕生效的样式， `breakpoint`的覆盖是从大 到小的，`breakpoint`值大的对应的样式会覆盖breakpoint值小的对应的样式。




# 3. 容器 Container

容器是Bootstrap中最重要的布局元素。

- `.container`    一个有居中的有宽度的容器
- `.container-fluid`  一个宽度为`100%`的容器
- `.container-{breakpoint}`   在小于breakpoint的设备上都是100%宽度



# 4. 网格  Grid

```html
<div class="container text-center">
  <div class="row">
    <div class="col">
      Column
    </div>
    <div class="col">
      Column
    </div>
    <div class="col">
      Column
    </div>
  </div>
</div>
```


# 5. 不等宽响应式列  

- **基础网格列** `.col`    同一行中的所有列将自动平分剩余空间、形成等宽列
- **固定宽度列类**  `.col-{breakpoint}-{number}`   Bootstrap网格系统将一行分为**12个单元格**， 用这个可以代表占用几格，number代表的是所占的单元格数
	- `.col-md-4`  表示在中等尺寸或者更大的设备上，该列占用12个中的4个单元格，即1/3的宽度
- **自动宽度列类**  `.col-{breakpoint}-auto`   根据内容来调整列宽

> [!danger]
> 在列中，还可以创建行，这个行就是这个列的嵌套行列

```html
<div class="container">
  <div class="row">
    <div class="col-8">
      <h2>主网格 - 左侧列（8/12）</h2>
      <div class="row">
        <div class="col-6">
          <p>嵌套列 1（相对于父列的 6/12）</p>
        </div>
        <div class="col-6">
          <p>嵌套列 2（相对于父列的 6/12）</p>
        </div>
      </div>
    </div>
    <div class="col-4">
      <h2>主网格 - 右侧列（4/12）</h2>
    </div>
  </div>
</div>
```

# 6. 偏移列 

  偏移列允许将一个列向右移动，只能向右移动，从而在其左侧创建空白空间，一般用于创建中间布局、右对齐布局、元素间距等
- `.offset-{breakpoint}-{number}`    
	- number的范围为`[1,11]`  代表的偏移的份数

```html
<div class="container">
  <div class="row">
    <div class="col-6 offset-3">
      <h3>居中内容</h3>
      <p>这个列占据了6份，并通过offset-3向右移动了3份，使其在12列网格中居中。</p>
    </div>
  </div>
</div>
```

## 6.1 讲一下  margin utilities

从Bootstrap4开始，可以采用`margin utilities` 工具类来实现类似的偏移效果，在某些情况下更加的灵活

margin和padding的逻辑是一致的，所以直接讲一下

命名方式
- `{property}{sides}-{breakpoint}-{size}`
	- `{property}` 
		- `m`  用于margin
		- `p`  用于padding
	- `{side}`
		- `t`  top  顶部
		- `b` bottom  底部
		- `s`  left  左侧  start的缩写
		- `e`  right 右侧   end的缩写
		- `x`  同时设置`left` 和`right`
		- `y` 同时设置`top`和`bottom`
	- `{size}`
		- 一个从0到5的数字，用于控制间距
		- `0`   就是0
		- `1`  0.25rem
		- `2` 0.5rem
		- `3`  1rem
		- `4` 1.5rem
		- `5`  3rem
		- `auto`  就是自动

![[Pasted image 20250805183203.png]]


# 7. 网格列排序

有时候为了在不同的屏幕上显式不同的列，而不改变它们在HTML中的源码，所以需要网格列排序

- 列排序类 `.order-{breakpoint}-{number}`
	- number 范围 `[1, 5]`
	- 默认情况下，没有`order`类的列相当于 `order-0`
	- **数值越小，列越靠前；数值越大，列越靠后**
- 特殊排序类  `.order-first`  和 `.order-last`
	- `.order-first` 强制排在所有其他列之前，相当于`order:-1`
	- `.order-last`  强制排在所有其他列之后，相当于`order:6`

```html
<div class="row">
  <div class="col order-sm-3 order-md-1">列 1</div>
  <div class="col order-sm-1 order-md-3">列 2</div>
  <div class="col order-sm-2 order-md-2">列 3</div>
</div>
```

# 8. 网格row-cols

有时候为了省去在每一列上重复写`.col-*`类，可以使用`.row-cols-*`类，它特别适合创建一系列等宽列的场景

- `.row-cols-{breakpoint}-{number}`
	- number  取值范围`[1,6]` 

这个表达的意思是在一行设置多少列，多余的列自动换行

```html
<div class="container">
  <div class="row row-cols-3">
    <div class="col">
      <div class="p-3 border bg-light">列 1</div>
    </div>
    <div class="col">
      <div class="p-3 border bg-light">列 2</div>
    </div>
    <div class="col">
      <div class="p-3 border bg-light">列 3</div>
    </div>
    <div class="col">
      <div class="p-3 border bg-light">列 4</div>
    </div>
  </div>
</div>
```

> [!danger]
> `.row-cols-*` 和`.col-*`的优先级？？
> 如果某个列单独指定了`.col-*` 类，这个列的宽度由`.col-*`类来决定
> 
> 有时候，`.row`下有超过12个`col`，最后也是在一行展示的，是因为啥？
> 如果没有指定`.col`宽度，导致Flex布局将所有元素平分在一行。

# 9. 排版标题

- 使用标准的HTML标签`<h1>....<h6>`
- 使用`.h1`到`.h6`类
	- 如果想一个非标题的标签，比如`p`或者`div`拥有标题的样式，可以使用`.h1`到`.h6`这些类
- 使用`.dislay`类来创建更加醒目的标题
	- Bootstrap提供了一组`display`类，可以创建比标准标题更大、更轻的标题，适合宣传语
	- `.display-1`到`.display-6`
- 使用`<small>`标签或者用`.small`类 加上`.text-muted`创建副标题
- 使用`.lead` 类来突出段落

```html
<h1 class="display-1">Display 1</h1>
<h1 class="display-2">Display 2</h1>
<h1 class="display-3">Display 3</h1>
<h1 class="display-4">Display 4</h1>
<h1 class="display-5">Display 5</h1>
<h1 class="display-6">Display 6</h1>

<h3>
  主标题
  <small class="text-muted">带有次要文本</small>
</h3>
```

# 10.   列的对齐方式

因为`.row`是flex布局，所以他的子元素`.col`支持对齐方式

## 10.1 垂直对齐

采用`.align-items-*` 

- `.align-items-start` 垂直顶部对齐
- `.align-items-center` 垂直中部对齐
- `.align-items-end`  垂直底部对齐

```html
<div class="container text-center">
  <div class="row align-items-end">
    <div class="col">
      One of three columns
    </div>
    <div class="col">
      One of three columns
    </div>
    <div class="col">
      One of three columns
    </div>
  </div>
</div>
```

Bootstrap5还支持每个列独自设置垂直对齐的方式
- `.align-self-*` 类

```html
<div class="container text-center">
  <div class="row">
    <div class="col align-self-start">
      One of three columns
    </div>
    <div class="col align-self-center">
      One of three columns
    </div>
    <div class="col align-self-end">
      One of three columns
    </div>
  </div>
</div>
```

## 10.2 水平对齐

采用`.justify-content-*` 类，其实和`css`的属性`justify-content`一致

- `.justify-content-start`   左对齐
- `.justify-content-end`  右对齐
- `.justify-content-center`  水平居中
- `.justify-content-around`    均匀分布，两端距离是中间距离的一半
- `.justify-content-between` 均匀分布，两端对齐   -- 最常用
- `.justify-content-evenly`   均匀分布，两端距离和中间距离一样

## 10.3 列的水平分隔

- `<div class="w-100"></div>` 这个可以强制列换行，这个是官方推荐的
- `<div class="clearfix"></div>`  也可以强制换行，不太直观

```html
<div class="container text-center">
  <div class="row">
    <div class="col-6 col-sm-3">.col-6 .col-sm-3</div>
    <div class="col-6 col-sm-3">.col-6 .col-sm-3</div>

    <!-- Force next columns to break to new line -->
    <div class="w-100"></div>
    <!-- <div class="clearfix"></div> -->

    <div class="col-6 col-sm-3">.col-6 .col-sm-3</div>
    <div class="col-6 col-sm-3">.col-6 .col-sm-3</div>
  </div>
</div>
```


# 11. Gutter

Gutter是row上的一个很关键的概念，他用于网格列之间分隔内容的间距

> [!question]
> Q：列之间的分隔间距为啥不用margin？
> A：是因为用margin可能会使得元素超出row的范围，引起样式混乱

gutter工具类可以在行上直接控制水平、垂直或者所有方向的间距

命名方式：
- `{方向}-{breakpoint}-{大小}`
	- `{方向}` 
		- `g`  用于同时控制水平和垂直的间距
		- `gx`  只用于水平间距
		- `gy`  只用于垂直间距
	- `{大小}`
		- 一个从 0 到 5 的数字，对应不同的间距大小（从 0.25rem 到 3rem）。
		- `0`  表示没有间距
		- ~~`auto`  不适用~~
```html
<div class="row gx-5">
  <div class="col">
    <div class="p-3 border bg-light">水平间距为 3rem</div>
  </div>
  <div class="col">
    <div class="p-3 border bg-light">水平间距为 3rem</div>
  </div>
</div>
```


# 12. display

可以设置元素的css属性`display`，Bootstrap5提供了一套相关的工具类

- `d-{breakpoint}-{value}`
	- `{value}`
		- `none`   = `display: none`
		- `block` = `display: block`
		- `inline` = `display: inline` 
		- `inline-block` = `display: inline-block`
		- `flex` = `display: flex`
		- `inline-flex` = `display: inline-flex`
		- `grid` = `display: grid`
		- `table` = `display: table`

```html
<div class="d-inline p-2 text-bg-primary">d-inline</div>
<div class="d-inline p-2 text-bg-dark">d-inline</div>
```

## 12.1 重点讲一下flex

### 12.1.1  调整flex的主轴方向

- `.flex-{breakpoint}-{主轴方向}`
	- `{主轴方向}`
		- `row`  默认的横向， 从左到右
		- `row-reverse`  横向  从右到左
		- `column`  垂直  从上到下
		- `column-reverse` 垂直 从下到上
比如`.flex-row`  `.flex-column-reverse`  `.flex-md-row-reverse`

### 12.1.2 水平对齐

之前已经讲过了，方式是

- `.justify-content-{breakpoint}-{direction}`

### 12.1.3 垂直对齐 

<span class="red-text">注意：这个支持一行的flex子元素；如果多行的话，请看后面的多行垂直对齐</span>

同样在之前已经讲过了，在一行的话，通过`.align-items-*`进行处理

```html
<div class="d-flex align-items-start">...</div>
<div class="d-flex align-items-end">...</div>
<div class="d-flex align-items-center">...</div>
<div class="d-flex align-items-baseline">...</div>
<div class="d-flex align-items-stretch">...</div>
```

而且还支持自身的对齐方式，通过`.align-self-*`的方式

```html
<div class="align-self-start">Aligned flex item</div>
<div class="align-self-end">Aligned flex item</div>
<div class="align-self-center">Aligned flex item</div>
<div class="align-self-baseline">Aligned flex item</div>
<div class="align-self-stretch">Aligned flex item</div>
```

### 12.1.4 Fill 填充

这个用于让Flex项目子元素自动占据所有可用的额外空间，他们会平分剩余的空间
- `.flex-{breakpoint}-fill`

### 12.1.5  伸缩

- `.flex-grow-*`  控制flex项目在有额外空间时如何伸开
- `.flex-shrink-*`  控制flex项目在空间不足时
### 12.1.6  是否换行

- `.flex-nowrap`  是否换行
- `.flex-wrap`  不换行，即使空间不足，所有子元素也会挤在一样

```html
<div class="d-flex flex-nowrap">
  ...
</div>

<div class="d-flex flex-wrap">
  ...
</div>
```

### 12.1.7 多行垂直对齐

- `.align-content-{breakpoint}-{value}`

这个value的值和单行垂直对齐是一致的

```html
<div class="d-flex align-content-between flex-wrap">...</div>
<div class="d-flex align-content-center flex-wrap">...</div>
```

# 13. float 浮动布局

```html
<div class="float-start">Float start on all viewport sizes</div><br>
<div class="float-end">Float end on all viewport sizes</div><br>
<div class="float-none">Don’t float on all viewport sizes</div>
```

<span class="red-text">需要注意的是：需要在父类身上清除浮动，使用clearfix类</span>

# 14. img元素尺寸自适应

有时候，img的信息需要在父容器里面自适应，以此来填充相关内容区域

- `.object-fit-{breakpoint}-{value}`
	- `{value}`
		- `contain`    保留元素的原始宽高比，并缩放以适应容器。元素可能会有空白区域 
		- `cover`       保留元素的原始宽高比，并缩放以此完全覆盖容器。 元素超出的部分会进行裁剪
		- `fill`        填充整个容器，不保持宽高比，图片有可能会被拉伸或者压缩
		- `scale`        效果和`contain`类似，但它会选择`none`和`contain`中尺寸最小的一个
		- `none`         元素不缩放，保持原始尺寸。如果超出容器会被裁剪

```html
<img src="..." class="object-fit-contain border rounded" alt="...">
<img src="..." class="object-fit-cover border rounded" alt="...">
<img src="..." class="object-fit-fill border rounded" alt="...">
<img src="..." class="object-fit-scale border rounded" alt="...">
<img src="..." class="object-fit-none border rounded" alt="...">
```

#









