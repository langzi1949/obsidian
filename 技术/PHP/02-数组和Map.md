#PHP #数组 #map

# 1. 数组

在php中，数组和Java等语言中的数组完全不是一回事，它的底层是一个有序的HashMap，它保留了元素的插入顺序
你把它当成map就行了，普通数组的话，类似 index:value  这种格式的map

## 1.1 声明和初始化

```php
// 两种方式， 但是现在基本都是[]表达就行
$arr = array("zmg","kobe","jiangsu");
$arr = ["zmg","kobe","jiangsu"];
$arr = []; // 声明一个空数组
```

- 特殊初始化，比如我们初始化一个1-100的数组

```php
$arr = range(1, 100);  // 
$arr = range('a', 'f') // [a, b, c, d, e, f]
```

- 创建填充值指定的数组

```php
$arr = array_fill(0, 3, 4); //  创建了index从0到3 ，默认值为4 
```

##  1.2 赋值

```php
// ❗️❗️❗️ 
$arr = [];
$arr[3] = 10;  // 现在给key = 3赋值了10
```

以上的代码，在Java理解下，就是索引3的值是10，那么前面index 0 到2值不应该是默认值吗？
但是在PHP中神奇的地方是，它只有index=3的值，其他的值都是NULL，没有对应的key
比如：
```php
echo $arr[1];
// Warning: Undefined array key 1  但是会有警告
var_dump($a);  // NULL
```

## 1.3 追加 append

```php
$arr[]  = 90; // 这个就是向最后一个元素后面添加value
```


## 1.4 访问元素和修改元素 

数组的长度  
```php
$length = count($arr);
```

判断键是否存在（key不为null）
```php
isset($arr['name'])
```

判断值是否存在
```php
in_array($value, $arr);
```

删除元素

```php
unset($arr[1]);  // 这个删除，原索引不变
array_splice($arr, 1, 1); // 和上面一样删除索引为1的数据，但是最终索引会重新编号
```

##  1.5 for循环

```php
for ($i = 0; $i < count($arr); $i++) {
    echo $arr[$i];
}

foreach ($arr as $item) {
    echo $item;
}
```

其他遍历函数

```php
// 可以有一个回调函数，进行处理
array_walk($arr, fn($value, $key) => print "key = $key, value = $value \n");
```

## 1.6 数组过滤和转换

1. 过滤元素
类似Java中的stream中filter

```php
$arr = [9, 8, 5, 7, 3, 2, 9];
$newArr = array_filter($arr, function($value) {
    return $value > 6;
});
// 过滤出 >6的元素 [9,8,7,9]
```

2. 转换元素

```php
$newArr = array_map(function($value) {
    return $value * $value;
}, $arr);
```

让人抓狂的是，两个函数的参数顺序完全胡来。

## 1.7 数组分割与合并

```php
$slice = array_slice($arr, 1, 4);  // 截取切片

$a = [1, 2];
$b = [3, 4];
// 合并
$merged = array_merge($a, $b); // [1, 2, 3, 4]

// 一个简单的写法
$merged = $a + $b;  // 也是合并逻辑
```

## 1.8 数组排序

```php
sort($arr); 
rsort($arr);  // 降序
asort($arr);  // 关联数组按值升序排序（保留键名）
ksort($arr);  //关联数组按键名升序排序
```

# 2. map

php最坑爹的map终于来了，在php中map也同样是用数组来表达的，称之为『关联数组』。

## 2.1  声明和初始化

```php
$arr = ["name" => "zmg", "age" => 28];
$arr = array("name" => "zmg", "age" => 28);
```

## 2.2 赋值和添加k-v

```php
$arr['address'] = "Shanghai";
print_r($arr);
```

## 2.3 访问元素和修改元素

pass

## 2.4 for循环

```php
foreach ($arr as $key=>$value) {
    echo "key = $key, value = $value\n";
}
```

## 2.5 常用函数

1. 将两个长度相同的普通数组，合并成一个map，其中一个是key，另一个是value

```php
$a = [1, 3, 4];
$b = ["kobe", "ai", "jordon"];
$com = array_combine($a, $b);  // [1=>"kobe",3=>"ai",4=>"jordon"]
```

2.  用指定key数组来初始化数据

```php
$keys = ['x', 'y', 'z'];
$map = array_fill_keys($keys, 0); 
// 结果：['x' => 0, 'y' => 0, 'z' => 0]
```

3. 检查key是否存在

```php
// isset函数是判断key是否存在或者值为null，如果value=NULL,也会返回false
isset($arr['name']) // true

// 这个是专业的判断key是否在
echo array_key_exists("name",$arr);
```

4. 值是否存在

```php
is_array("zmg", $arr);
```

5. 删除
通过key来删除
```php
unset($arr['name'])
```

6. 获取所有的key

```php
$map = ['a' => 1, 'b' => 2];
print_r(array_keys($map)); // 输出：Array ( [0] => a [1] => b )
```

7.  获取所有的值

```php
print_r(array_values($map)); // 输出：Array ( [0] => 1 [1] => 2 )
```

8. 合并两个map

```php
$map1 = ['a' => 1, 'b' => 2];
$map2 = ['b' => 3, 'c' => 4];
$merged = array_merge($map1, $map2);
// 结果：['a' => 1, 'b' => 3, 'c' => 4]（重复键后者覆盖前者）
```

9. 过滤

```php
$map = ['a' => 1, 'b' => 2, 'c' => 3, 'd' => 4];
// 保留值为偶数的元素
$filtered = array_filter($map, fn($value) => $value % 2 == 0);
// 结果：['b' => 2, 'd' => 4]
```

10. key和value 反转

```php
$original = [
    'name' => '张三',
    'age' => 25,
    'city' => '北京'
];

// 键值互换
$flipped = array_flip($original);

print_r($flipped);
// 输出：
// Array (
//   [张三] => name
//   [25] => age
//   [北京] => city
// )
```