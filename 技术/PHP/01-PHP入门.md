# 1. 基础语法

## 1.1 脚本规则

1. PHP脚本以`<?php`开头  以`?>` 结尾
注意： 在纯PHP文件中，可以省略`?>`结尾，但是在包含html语句的php文件中必须开闭标签都得有

2. phpinfio 方法-- 会输出当前版本的php的信息，以及一下API的资料等

```php
phpinfo();
```

3. 输出方法：`echo`和`print`
	1. `echo`： 能够输出一个以上的字符串，英文逗号隔开
	2. `print`：只能输出一个字符串，并始终返回1
	3. echo比print更加常见且性能更好，使用更多

```php
echo "Hello, world", 'zmglove';
print("Hello again");
```

4. 注释
	1. 常见的单行注释 用`//`或者`#`
	2. 多行就是`/*   */`
	3. 实际开发的时候，需要写phpdoc类型的注释，和Java很像，就是对方法或者变量都写比较详细的注释

6. 变量
	1. PHP中最让人抓狂😱 😱 😱的就是 变量必须要以`$`符号开头
	2. 变量名必须是字母或者下划线开头，这些都是Java玩剩下来的
	3. 变量名对大小写敏感，建议跟Java保持一致，以小写驼峰式命名

> [!danger] 
> 特别sb的是，虽然变量名是大小写敏感的，但是**类名、函数名、方法名**又变成了大小写不敏感，我无语了😤 😤 😤

7. 空白符

```php
$a = "Hello
world
";
echo $a;
```

以上这一段代码，在命令行中和在浏览器中执行是不一样的，是为啥呢？
- 因为浏览器接收后，解析成HTML，在HTML中换行符以及不管都有多少个空行，最后只有一个空行，所以，它最后在页面上展示的是`Hello world` 在一行显式
- 在命令行中执行的话，就是我们肉眼可见的正常，是有换行符的
>[!tips]
>如果在浏览器上输出字符串的原始数据，保留换行以及空格等信息，应该怎么办？
>- 如果只处理换行符的话，使用`nl2br()`函数，会将换行符转为`<br/>`标签
>- 如果不止换行符还有其他空格，直接使用`<pre>`标签包裹一下`echo "<pre>" . $text . "</pre>";`

7. 常量
在php中定义常量有种方式，一种是`define()`，另外一种是`const`声明

- PHP中的常量不用`$`开头，直接写变量名就行，正常都是大写的，中间可以有下划线
- 使用`define(PI, 3.1415926);`   通过`define()`函数定义的常量将注册到全局的，在任何php的文件中都能使用
- `const PI = 3.1415926;`   通过`const`的方式，跟普通的变量一样，有作用域的

# 2. 数据类型

字符串、整数、浮点数、boolean、数组、对象、NULL、资源类型
用`var_dump()`会返回变量的数据类型和值，一般用于开发debug使用

## 2.1  字符串

用双引号或者单引号都可以表示字符串，但是它们有一个本质上的区别，和Groovy上的使用有点像
- **双引号**
	- 会解析变量和转义字符，类似字符串模板值，会计算里面的变量的名称
	- `echo "Hello $name"`   这个就会求值
	- `echo "这是第一行。\n这是第二行。"; `  这个就会进行转义
- **单引号**
	- 不会解析，就是原值输出
	- 所以，一般情况下来说，我们优先使用单引号作为字符串

> [!danger]
> 🅾️🅾️🅾️🅾️🅾️
> 注意： php中的字符串和golang中一样，是区分多字节字符和单字节字符的，所以需要额外注意
> 通常`str`开头的函数，对应的都有一个`mb_str`开头的函数，没有没有对应的，那肯定是没有必要的，说明`str`的函数能够解决

### 2.1.1 获取长度

这个跟跟Golang有点类似，原生的`strlen()`方法获取的字符串的字节数，而不是字符数，这个对于英文字母没问题，但是对于中文就不对了，在utf-8下 一个中文可能占用3个字节

```php
$str = "hello";
echo strlen($str); // 这个只能针对英文字符，因为它返回的是字节数

$str = "中国";
echo mb_strlen($str); // 2  ✅ ✅ ✅ 这个就是处理中文字符的
```

### 2.1.2 取单个字符以及遍历

- 取单个字符
	- 同样和Golang类似，直接`[index]`的话，针对英文字符串没问题
	- 中文字符的话，采用`mb_substr(str, index, length)`

```php
$name = 'hello';
echo $name[2]; // l 

$name = '中国万岁';
echo mb_substr($name, 0, 1); // 取第一个字符，其中第三参数表达式，取的长度
```

- 遍历字符串

```php
$str = 'hello world';
// 传统方式
for ($i = 0; $i < strlen($str); $i++) {
    echo "$str[$i] ";
}
// 先转为字符数组 然后 foreach
$strArr = str_split($str);
foreach ($strArr as $item) {
    echo  $item;
}

//////// 以上只针对英文字符
// 同样先转为字符数组
$str2 = "中国万岁";
$str2Arr = mb_str_split($str2);
foreach ($str2Arr as $item) {
    echo  $item;
}
```

### 2.1.3 查找和替换

1. `strpos() / strrops()`查找子串第一次或者最后一次出现的位置

```php
$haystack = 'hello world';
echo strpos($haystack, 'world'); // 输出: 6 
echo strrpos($haystack, 'world');

echo mb_strops(str, 中文);
echo mb_strrps(str, 中文);
```


2. `str_replace()` 替换所有出现的子串
	1. 这个没有mb版本
```php
$text = 'I love PHP and PHP is awesome.';
echo str_replace('PHP', 'Laravel', $text);
// 输出: I love Laravel and Laravel is awesome.
$count = 0
echo str_replace('PHP','Laravel', $text, $count)  // 第四个参数会修改count的值 = 替换了几次 
```

**那么如果我们要替换第一个出现的子串，怎么办呢？**
php中没有Java中的replace方法，上面那种是相当于replaceAll
可以通过代码解决

```php
$name = 'hello world====world中国';
$needle = 'world';
// 第一步：找到子串的位置
$pos = strpos($name, $needle);
// 如果存在的话，通过位置去替换
if ($pos) {
	// 第二步： 通过substr_replace进行替换
    $finalName = substr_replace($name, 'ZMG', $pos, strlen($needle));
    echo " $name   $finalName";
}
```

### 2.1.4 截取/子串

```php
$str = "hello";
$str2 = "中国万岁";
echo substr($str, 1, 2);  // el   这个是按照单字节
echo mb_substr($str2,  2, 2); // 万岁   这个是按照多字节
```

### 2.1.5 分割或转为数组

```php
// 分割
$str= "hello";
$arr1 = str_split($str);
print_r($arr1);

$str2 = "中国万岁";
$arr1_2 = mb_str_split($str2);
print_r($arr1_2);

// 按照某个特定字符的进行分割
$str = "hello world";
print_r(explode(" ", $str));
$str = "中国 万岁";
print_r(explode(" ", $str));   // 多字节和单字节用的是同一个方法
```

### 2.1.6 字符串拼接

```php
// 最常见的就是.
$str = "hello" . "zmg";
$str .= "  kobe";
echo $str;
// 或者使用双引号的字符串模板
echo "I love $str";
```

### 2.1.7 复制

```php
// 不分单字节和多字节
$str = str_repeat($str, 3);  
echo $str;
```

### 2.1.8 反转

```php
// 单字节字符串反转
echo strrev($str)."\n";

// ✅ ✅ ✅ 多字节字符串的反转
// 1. 先split成字符数组
$arr = mb_str_split($str2);
// 2. 数组反转
$arr = array_reverse($arr);
// 3. 数组拼接成字符串
echo implode($arr);
```

### 2.1.9 trim

```php
//  首尾空白字符
$str = "  hello world     ";
echo trim($str);
echo ltrim($str);
echo rtrim($str);
// 多字节字符串
$str = "    中国万岁    ";
echo mb_trim($str);
```

### 2.1.10 大小写

```php
$str = "hello world 中国万岁";
echo strtoupper($str);
echo strtolower($str);
// 多字节字符串也有对应的，但是一般情况下估计用不到
```

# 2.2 数值类型

这个没啥好说的，和JavaScript上的逻辑基本也都一致。

### 2.2.1 类型转换

```php
echo (int) 3.8; // 3
echo intval(3.99); // 3
echo intval("123abc"); // 123

$a = 3 . "abc"; // 直接就是字符串 3abc
```

### 2.2.2 格式化

```php
echo number_format(1233.35343, 2); // 1,233.35  // 保留2为小数，同时千位分割
echo number_format(80001);  // 80,001
```

### 2.2.3 运算

其他都还好说，但是`/`运算和JavaScript以及python是一致的，结果都是浮点数的值，所以用`/`运算符的时候一定要想清楚，最后进行数据的转换，python中有点好就是`5 // 2` 这种等于2 ，它用`//`就是整数的值

```php
// 用int强制转换
echo (int)(8/3);
echo intdiv(8, 3);
```

```php
// 绝对值
echo abs(-5) ;
echo abs(-4.52);

// 四舍五入
echo round(3.5); // 4
echo round(3.49); // 3
echo round(2.675, 2); // 2.68  保留两位小数

// 向上去整
echo ceil(4.1); // 5
echo ceil(4.0); // 4

// 向下去整
echo floor(23.5); // 23
echo floor(-2.3); // -3

// 计算幂运算
echo pow(2,3); // 8
```

### 2.2.4 检测与判断

```php
var_dump(is_int(11));  // true
var_dump(is_int(23.4)); // false

is_float(234.4); // true

// 判断是否是数字
var_dump(is_numeric('45'));
var_dump(is_numeric('234.4'));
var_dump(is_numeric("asdfm"));
```

### 2.2.5 注意事项

1. 浮点数精度的问题
	1. 这个是老生常谈的问题了，0.1 + 0.2 == 0.3   这个判断不是true，而是false
	2. 所以两个字符串比较的话，应该将数据取相同的精度后进行比较 `round($a, 2) == round($b, 2)`
2. 类型自动转换的坑， 这个好像是弱类型语言的通病
	1. `echo "10" + 4`  // 最后为15
	2. `echo "10abc" + 5` //  15   虽然可以得出数据，但是会有警告
	3. `echo "abc" + 5` // 5  无法转换的字符串就是0
3. 数值和字符串的比较
	1. `$a = 123 == '123'` // 这个返回false
	2. `$a = 123 === '123'` // 这个返回true
	3. 所以，和JavaScript一样，在所有的判断中必须是用三个等号

> [!danger]
> var_dump(2.35); 和 echo gettype(23.5);  结果是不同的，前者是`float` ，后者是`double` 。这个就让人很困惑了，但是它们是指的同一个，gettype更加偏向于底层，底层是C语言写的，所以它是double类型的
# 3. NULL类型和boolean类型以及资源类型

## 3.1 NULL类型
和Java类似

```php
$k;
echo is_null($k);

if ($k == NULL) {}
```

## 3.2  boolean

在PHP中只有那些是『空』或者『零』的值是false，其余都是true
- false本身
- 整数0
- 浮点数0.0
- 空字符串""
- NULL
- 字符串"0"   🅾️ 🅾️ 🅾️这个是特殊的，因为会进行转为数字0   

## 3.3 资源类型
在PHP中，资源是一个特殊的数据类型，它表示的是一个外部资源的引用，它并不是PHP内部的数据
- 一个打开的文件句柄
- 一个数据库连接
- 一个网络流等等

![[Pasted image 20250913161332.png]]

```php
<?php
// 1. 创建资源：打开一个文件
$fileHandle = fopen('test.txt', 'r');

// 2. 检查变量类型
if (is_resource($fileHandle)) {
    echo "这是一个有效的资源，类型是: " . get_resource_type($fileHandle) . "\n";
    // 输出: 这是一个有效的资源，类型是: stream
}

// 3. 使用资源：读取文件内容
$content = fread($fileHandle, 1024);
echo "文件内容: " . $content . "\n";

// 4. 关闭资源：释放文件句柄
fclose($fileHandle);

// 5. 检查关闭后的变量
if (is_resource($fileHandle)) {
    // 这段代码不会执行
    echo "资源仍然有效";
} else {
    echo "资源已被关闭，不再是有效的句柄\n";
}
?>
```

# 4. 条件分支以及循环

和Java一样，有
- if-else
- if-else if - else
- switch - case
- for
- while
- do-while

以上的逻辑都和Java一样

**php中有一个Java没有的特性 在跳出循环`break`关键字后面，可以加上跳出几层循环**

```php
<?php
for ($i = 0; $i < 3; $i++) {
    for ($j = 0; $j < 3; $j++) {
        if ($i == 1 && $j == 1) {
            echo "正在跳出两层循环。\n";
            break 2; // 同时跳出内层和外层循环
        }
        echo "i: $i, j: $j\n";
    }
}
echo "循环结束。\n";
?>
```

# 5. 常用的运算符

## 5.1 三元运算符

```php
$age > 80 ? "江苏" :"曼谷";
```

## 5.2 空值合并运算符

类似Groovy中的`?:`， 在php中是用`??`
```php
$name = $username ?? "zmg"; // 如果$username 不为就用username，否则用zmg
```

## 5.3 组合比较符

```php
$a = 90;
$b = 90;
$c = $a <=> $b;  // 0
```

- 如果值相等，则返回0
- 如果前值大，则返回1
- 如果前值小，则返回-1


# 6. 常用的全局常量

有很多，基本都是`__`两个下划线结尾的，大致能够猜出作用

```php
echo __FILE__;  // 当前php文件的绝对路径
echo __DIR__;   // 当前php文件的目录
```

- 异常相关的都是`E_`开头
- PHP环境相关的都是`PHP_`开头
- DIRECTORY_SEPARATOR  ： 用于跨平台的目录的分隔符，比如微软是`\` linux是`/` 
- 

# 7. 引入另外的php文件

有时候我们在一个php文件中需要用到另外一个php、html等文件，那么应该怎么办呢？
就需要用到`include`和`require`，他们唯一的区别就是对找不到对应的文件，是否警告还是直接错误不执行后续的代码。
他们就相当于直接将多个php文件合并了而已

- `include`   引入文件，如果找不到文件，给出警告继续执行后续代码
- `include_once`  只引入一次就行，如果不小心写了多条语句，`include_once`也只是引入一次
- `require`  引入文件  ==如果找不到文件，给出Error不会执行后续代码==
- `require_once`  只引入一次就行


有时候，引入一个php后，发现有相同的function，程序会报错的，不能重复定义函数。最好的解决办法就是`namespace`



