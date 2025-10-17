#前端 #CSS #Sass

一句话来说就是`Sass`是css的预处理文件，最终都会通过构建工具或者编译器将`sass文件`转为`css`
但是他提供的语法能够解决css中很多的问题


# 0. 前期准备工作

安装
`npm install -g sass`

在vscode中安装两个插件:
- `Sass` ：语法提示的，要不然对`.scss` 文件编写的时候没有提示功能
- `Live Sass Compiler`： 对编写好的`.scss`文件进行编译生成对应的`css`文件

安装好了之后，在编写`sass`源码的时候，需要在vscode的底部状态栏下，点击『Wath Sass』按钮，让他处于监听状态，这样才会进行编译生成对应的css文件
可以在工作区的`settings.json`文件中添加
```json
{
  // Live Sass Compiler 配置
  "liveSassCompile.settings.formats": [
    {
      "format": "expanded",
      "extensionName": ".css",
      "savePath": null // 编译到项目根目录下的 /css 文件夹  这个savePath可以有多种逻辑
    }
  ],
  "liveSassCompile.settings.generateMap": true, // 生成 Source Map
  "liveSassCompile.settings.autoprefix": [
    "last 2 versions",
    "> 1%",
    "ie >= 10"
  ],
  "liveSassCompile.settings.excludeList": [
    "**/node_modules/**",
    ".vscode/**",
    "**/_*.scss" // 排除所有以下划线开头的 Sass 文件
  ],
  "liveSassCompile.settings.showOutputWindow": false, // 不显示输出窗口
}
```

> [!tips]
> savePath: 最重要的配置之一，定义了编译后的 CSS 文件保存的路径。
> - /css：会将 src/scss/style.scss 编译到 css/style.css。
> - null：会将 src/scss/style.scss 编译到 src/scss/style.css (与源文件同级)。
> - ./：与 null 类似，也是在源文件同级目录。
> - ~/../css：表示相对于源文件目录的上一级目录下的 css 文件夹。

#  1. 变量

提出变量的意思就是，可以在不同的地方可以用到此变量，尤其是一些背景色或者通用的样式，需要进行变量存储，以后只要改变改一处就可以了

```sas
$font-stack: Helvetica, sans-serif;
$primary-color: #333;

body {
  width: 200px;
  height: 300px;
  font: 100% $font-stack;
  color: $primary-color;
}
```

变量的命名使用`$`符号来定义，使其称为变量，然后后续使用的时候，直接也是`$变量名`

# 2.  嵌套

```css
nav {
  ul {
    margin: 0;
    padding: 0;
    list-style: none;
  }

  li { display: inline-block; }

  a {
    display: block;
    padding: 6px 12px;
    text-decoration: none;
  }
}
```

# 3.  Partials

可以创建部分`sass`文件，这个文件可以包含少量css代码或者定义的变量等信息，这个文件不会被编译器生成`css`文件，所以要告诉编译器这个文件的类型，用下划线`_`开头即可。比如`_partial.scss`文件，就不会生成`css`文件，那么这个文件的作用是啥呢？供其他`scss`文件引入使用。

# 4.  模块

```css
// _base.scss
$font-stack: Helvetica, sans-serif;
$primary-color: #333;

body {
  font: 100% $font-stack;
  color: $primary-color;
}
```

然后另外一个`sass`文件来引用他

```css
@use 'base';

.inverse {
  background-color: base.$primary-color;
  color: white;
}
```

在使用文件的时候，不需要包含拓展名，直接使用下划线后面的名称即可

# 5. Mixin 

主要解决的问题就是原生css中代码冗余的问题，现在可以定一个一个mixin，等到用的时候直接include就行
它允许将重复的样式打包成一个可命名的块，然后在需要的时候调用他

定义方式：
```css
@mixin mixin-name {
  // 样式声明
}

// 或者带参数的 Mixin
@mixin mixin-name($parameter1, $parameter2: default-value) {
  // 样式声明，可以使用参数
}
```

```css
@mixin theme($theme: DarkGray) {
  background: $theme;
  box-shadow: 0 0 1px rgba($theme, .25);
  color: #fff;
}

.info {
  @include theme;
}
.alert {
  @include theme($theme: DarkRed);
}
.success {
  @include theme($theme: DarkGreen);
}
```

# 6.Extend/Inheritance

同样的解决的是重复的样式问题，但是它不支持参数，只能是那种固定的样式

```css
.class_name {
  padding: 10;
  border: 1px solid skyblue;
}

%display_name_cl {
  line-height: 20px;
}

.noke {
  @extend %display_name_cl;
}

.fun {
  @extend .class_name;
  color: blue;
}

.noe {
  @extend .class_name;
}
```

> [!tips]
> 如果占位符以`%`开头， 比如上面的`%display_name_cl` ，那么他们本身不会编译成CSS，但是@extend用他的时候会正常的使用就行了，相当于纯粹的使用继承

> [!danger]
>  @mixin  和 @extend的比较
>  - @mixin： 复制重复样式，尤其**支持接收参数**，所以更加的灵活，可以生成那种大同小异的样式
>  - @extend：其实就相当于共享一组同样的样式而已，==**不接收参数**==
>  - 由于@extend生成的时候，会合并一些选择器，所有可能会出现意想不到的结果，一般大家还是优先考虑使用@mixin

# 7. 运算

在sass中支持一些丰富的运算，这样的运算会更加的方便，比如：+、-、`*`、math.div() 和 %

```css
@use "sass:math";

.container {
  display: flex;
}

article[role="main"] {
  width: math.div(600px, 960px) * 100%;
}

aside[role="complementary"] {
  width: math.div(300px, 960px) * 100%;
  margin-left: auto;
}
```

# 8. 函数

Sass中提供了很多内置的函数来处理颜色、数字、字符串等，我们也可以自己定义函数
具体用的话，直接查看相关的文档
```css
$base-color: #3498db; // 蓝色

.header {
  background-color: $base-color;
  color: lighten($base-color, 20%); // 将蓝色提亮20%
}

.footer {
  background-color: darken($base-color, 15%); // 将蓝色调暗15%
  color: mix(white, $base-color, 80%); // 80%白色 + 20%蓝色
}
```

## 8.1 自定义函数

使用`@function`关键字定义自己的函数，必须用`@return`语句返回值

```css
@function pixels-to-em($pixels, $base-font-size: 16px) {
  @return ($pixels / $base-font-size) * 1em;
}

.title {
  font-size: pixels-to-em(32px); // 编译为 2em
  line-height: pixels-to-em(40px); // 编译为 2.5em
}

.paragraph {
  font-size: pixels-to-em(18px, 18px); // 编译为 1em (自定义基准字号)
}
```


# 9. 数组和map

在Sass中列表和map使用的比较多

## 9.1 List

```css
// 使用空格分隔 (最常见用于单一属性值)
$font-stack: Arial sans-serif Helvetica;

// 使用逗号分隔 (最常见用于多值，如边距、字体堆栈等)
$border-styles: solid, dashed, dotted;

// 嵌套列表 (列表的列表)
$grid-dimensions: (100px 200px), (300px 400px);

// 空列表
$empty-list: ();
$empty-list-alt: []; // Sass 3.3+ 支持方括号
```

访问列表中的值：
- `nth($list, $index)`: 获取列表中指定索引的值（索引从 1 开始）
```css
$colors: red, green, blue;
.first-color {
  color: nth($colors, 1); // red
}
```
- `length($list)`: 获取列表的长度。
```css
$items: one, two, three;
.item-count::before {
  content: "Total items: #{length($items)}"; // Total items: 3
}
```

循环列表，使用`@each`循环来批量处理

```css
$themes: dark, light, compact;

@each $theme-name in $themes {
  .theme-#{$theme-name} {
    // 根据主题名称生成样式
  }
}
```

## 9.2 Maps

映射使用括号 `()` 定义，键值对之间用冒号 `: `分隔，不同键值对之间用逗号 `, `分隔
```css
// 简单的颜色映射
$colors: (
  "primary": #007bff,
  "secondary": #6c757d,
  "success": #28a745
);

// 字体映射
$font-sizes: (
  "small": 12px,
  "medium": 16px,
  "large": 20px
);

// 嵌套映射 (映射的映射，常用于主题)
$theme-settings: (
  "light": (
    "bg-color": white,
    "text-color": #333
  ),
  "dark": (
    "bg-color": #333,
    "text-color": white
  )
);
```

- ` map-get($map, $key)`: 根据键获取映射中的值。
- `map-has-key($map, $key)`: 检查映射是否包含指定的键。


# 10. 条件判断、循环遍历

## 10.1 条件判断

```css
@if condition {
  // 样式声明
} @else if another-condition {
  // 样式声明
} @else {
  // 样式声明
}
```

## 10.2 循环（@for、 @each、 @while）

### 10.2.1 @for

```css
@for $var from <start> through <end> { // 包含结束值
  // 样式声明
}

@for $var from <start> to <end> { // 不包含结束值
  // 样式声明
}

@for $i from 1 through 6 {
  h#{$i} { // 使用插值 #{}
    font-size: 30px - ($i * 2px); // 字体大小递减
    margin-bottom: 10px + ($i * 5px); // 外边距递增
  }
}

// 编译结果大致为：
/*
h1 { font-size: 28px; margin-bottom: 15px; }
h2 { font-size: 26px; margin-bottom: 20px; }
...
h6 { font-size: 18px; margin-bottom: 40px; }
*/
```

### 10.2.2 @each循环

```css
@each $var in <list-or-map> {
  // 样式声明
}

// 遍历 map 时，可以同时获取 key 和 value
@each $key, $value in <map> {
  // 样式声明
}
```


### 10.2.3 @while

一般来说，while循环不太常用
```css
$i: 1;
@while $i < 5 {
  .item-#{$i} {
    width: $i * 20%;
  }
  $i: $i + 1; // 必须手动递增变量，否则会无限循环
}
```



