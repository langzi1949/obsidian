#go #Golang  
# 1.  命名规范

1. 以   任何Unicode认为是字母的字符 或者 下划线`_` 开头
2. 只能是字母、下划线、数字 这些组成，==和Java以及JavaScript不一样的是，Go不能有`$`，但是Java和JS是允许的==
3. 不能是保留关键字

> [!tips]
> Go语言中，总共有25个关键字，在编程语言中是很少的，Java有50个左右，Python有35个，JS最多65个。
> 但是有一个比较诟病的是，Go的命名可以是 `true`、`int8`、`string`  这些，其实会有很多歧义的，但是这种就靠规范了

# 2. 变量
## 2.1 变量的声明方式

声明一个变量的方式，标准的写法是
` var name type = expression`

但是`type`可以省略，go编译器会进行类型推理；`expression` 也可以省略，这样就是初始化一个默认值，number类型的就是0，boolean类型的就是false，数组的元素都是0，字符串就是空字符串； 但是需要注意的是`type`和`expression`不能同时省略，这样就没有办法类型推断了，那就无从谈起初始化

```go
var i, j k = true, 2.3, "kobe"   // 一行语句对多个变量进行赋值
// 所以可以进行变量的交换
var i, j = 1,2
i, j = j ,i   // 这样就把变量交换了，比Java简洁
```

在函数体内，可以将变量的声明简化一下，通过`name := expression`   不需要上面的写法，相对比较简单，这个在代码中使用是最常见的了。

## 2.2 特别情况说明

在Go语言中，有个特殊的规定，在`:=` 使用中，允许它在同时声明新变量的时候，对已存在的变量进行重新赋值。直接看代码

<span class='rt-b'>注意： 必须是至少有一个新的变量，才可以对已存在的变量进行赋值</span>

```go
func main() {
	i := 34
	i := 23  // ❌ ❌ ❌ 这个直接报错，不能重新声明并赋值
	// 但是下面的方式是正确的
	a, b := 23, 45
	c, b := 33, 55  // ✅ ✅ ✅ 这个就是正确的，b会被重新赋值，因为这一行里面有一个新变量c,所以go允许这样操作

	// 这个很常见
	in ,err := dosomesthing()
	// .... 代码
	out, err := dosomethingElse()  //✅ ✅ ✅ go允许这样，就是防止定义太多的err， 但是err类型不能变化了

}
```

## 2.3  指针

golang中有指针的概念，它的概念和C语言几乎一致，但是它更加安全，同时不允许对指针本身进行一些运算操作等

```go
i := 10
p := &i  // p 指向了i的地址
*p = 20   // 将p指向的地址内的数据改成20
fmt.Print(i)  // 20  
```
以上的缺点就是，创建一个指针变量p的时候，必须要指向一个已经明确的变量的地址，如果我直接这样做就会报错

```go
var p *int  // ❗️❗️ p指向一个空值int，它并没有分配任何地址，所以*p赋值的时候，会报错
*p = 2  // ❌ ❌ ❌ 报错，因为p指向的是一个空地址nil，这样会出错的，没有地址就不能修改里面的值
```

## 2.4 new函数

go内置的`new`函数，就可以创建一个匿名的变量，并给这个变量进行初始化，同时返回这个变量的指针
即：==**为给定的类型分配内存，并返回指向该内存的指针**==

```go
p := new(int) //  p就是*int 指针，指向一个匿名的int变量
*p = 20  // ✅ ✅ ✅
```

以下两端代码是一样的逻辑，
```go
func get() *int {
	return new(int)
}

func get() *int {
	var dummy int
	return &dummy
}
```

 🆘 🆘 🆘   <span class='rt-b'>实际上，在实际开发中很少用到new函数</span>
1. 因为可以通过`type`结构体的方式去解决，会很方便，因为在结构体初始化的时候，可以直接赋值了

```go
type Person struct {
Name: string,
Age: int
}
// 方式一：完整初始化，顺序赋值
    p1 := &Person{"Alice", 30}
    
    // 方式二：指定字段名初始化（推荐，因为字段顺序不重要）
    p2 := &Person{Name: "Bob", Age: 25}

    // 方式三：只初始化部分字段，未初始化的字段将是零值
    p3 := &Person{Name: "Charlie"}

```

## 2.5 变量的生命周期

 没啥好说的，和正常的理解一样就行了，唯一的问题就是在function中的变量会发生逃逸。
```go
var p *int

func hello() {
	i := 2
	p = &i     // 由于外部的p指向了临时的变量i，所以i在函数过后不会被回收，生命周期就是p决定的了
}
```

## 2.6 可赋值性

在Go语言在哪个，它的赋值的哲学是『零隐式』（zero  implicit），不会进行任何自动的类型转换
比如, 不同类型的数据，压根就不能进行 == 或者 != 来比较两个不同类型的数据，编译器压根就不会通过，这个和Java和JavaScript的处理是不同的，需要注意！

 
```go
package main

import "fmt"

func main() {
    var a int = 10
    var b float64 = 10.0
    
    //❗️ ❗️ ❗️ 编译时报错：类型不匹配
    // Go 不允许直接比较不同类型的数值
    // fmt.Println(a == b)
    
    // 你需要显式转换才能比较
    fmt.Println(float64(a) == b) // true
}
```

## 2.7 类型

Go中可以创建一个类型，新的类型名称来表达某个底层的类型
语法是`type 类型名称 底层类型`，但是类型声明语句一般出现在包一级，如果新创建的类型的首字母大写，外部也可以使用。

```go
package tempconv
import "fmt"
type Celsius float64 // 摄氏温度
type Fahrenheit float64 // 华氏温度
const (
	AbsoluteZeroC Celsius = -273.15 // 绝对零度
	FreezingC Celsius = 0 // 结冰点温度
	BoilingC Celsius = 100 // 沸水温度
)
func CToF(c Celsius) Fahrenheit { return Fahrenheit(c*9/5 + 32) }
func FToC(f Fahrenheit) Celsius { return Celsius((f - 32) * 5 / 9) }
```

1. 首先，定义两个类型`Celsius`和`Fahrenheit` 两个类型，他们对应的都是float64，但这两个类型是不同的，所以不能互相直接赋值和比较
2. `Fahrenheit(x)` 这个只是将数值转换为类型而已，它并不是一个函数的调用
3. 底层数据类型决定了内部结构和表达方式，也决定了是否可以像操作底层类型一样来操作我们自定义的类型数据
4. 只有相同的变量类型才可以比较或者赋值等一系列操作

命名类型还可以为该类型的值定义一个新的行为，其实就是拓展方法了。

语法:

`func (c Celsius) String() string { return fmt.Sprintf("%g°C", c) }`

1. `(c Celsius)` 接收者， 定义了这个方法属于哪个类型，此时对应的就是属于类型`Celsius`
2. 其余的逻辑就跟正常的方法一样，无需说明了

```go
type Kobe int

func (k Kobe) goat(s string) string {
	return fmt.Sprintf("jiangsu = %s, current k = %d", s, k)
}

func main() {

	var i Kobe = 20
	str := i.goat("yancheng")
	fmt.Println(str)
}
```

> [!danger]
> 和Java有点类似，许多类型会定义一个String方法，这样fmt打印的时候，会优先调用这个类型对应的String方法，有点类似Java中的toString()

# 3. 包和文件

通常情况下，为了社区的统一，一个包所在的目录路径的后缀就是一个包的导入路径，这个有点类似Java了，但是Go没有做强制限制。
Go语言中，如果一个名字是大写字母开头的，那么这个名字(变量或者方法、类型等)是可以导出的，类似public

1. 一个目录下，只能定义一个包名
2. 导入包的时候，可以给包起别名  `import [别名] "包"`
3. 一个特殊的方法  `init()` 
	1. 一个`.go`文件中，可以有一个`init()`，同时一个包内的不同`go`文件，都可以有`init()`，但是他们加载初始化的顺序不确定
	2. 唯一能够确定的就是 `main()`肯定是在所有的`init()`执行完毕后，才执行
	3. `init()`不能有参数和返回值，它一般用于初始化一些复杂的数据
	4. `init()`可以用`匿名函数自调用`的方式来替代，但是唯一的区别就是执行时机的不同，匿名函数自调用在`init()`之前执行

```go
var Age string = func() string {}()

// 上一句就等于
func init() {
	Age = .....
}
```

# 4. 作用域问题

```go
func Hello() {
	x := "hello!"
	for i := 0; i < len(x); i++ {
		x := x[i]
		if x != '!' {
			x := x + 'A' - 'a'
			fmt.Printf("%c", x)
		}
	}
}
```

以上的代码逻辑在Java中是错误的，但是Go中是正确的，因为它的变量的作用域是从最小的代码块里面算起的，这样就不会冲突，就说一个简单的道理，最小的作用域是代码块，如果代码中重新声明了一个外部已经存在的变量，是正确的，在此代码块中可以一直使用。  <span class='rt-b'>PS：if 语句的判断条件  、for循环的初始化、switch等初始化的语句，都是隐式的代码块，他们也是作用域</span>

PS：特别注意：在 短声明中`:=`中的作用域的问题

```go
var cwd string
func init() {
	cwd, err := os.Getwd() // NOTE: wrong!
	if err != nil {
	log.Fatalf("os.Getwd failed: %v", err)
	}
	log.Printf("Working directory = %s", cwd)
}
```

以上的变量`cwd`是一个包级变量，在`init`中 通过`:=`不会正确的初始化`cwd`， 这个是因为cwd的作用域的问题，
cwd 已经在外层作用域声明，而 := 又在内层作用域声明了同名的局部变量，编译器会认为这是一个可疑的“遮盖”（shadowing）行为，并发出警告。

```go
var cwd string
func init() {
	var  err error  // ✅ ✅ ✅ 这样就OK了，先提前将error在同一个作用域中声明了，下面一句话只是类似赋值
	cwd, err := os.Getwd() // NOTE: wrong!
	if err != nil {
	log.Fatalf("os.Getwd failed: %v", err)
	}
	log.Printf("Working directory = %s", cwd)
}
```


# 5. 基本数据类型

## 5.1 整型

1. 有符号
	1. int、int8、int16、int32、int64  - int是最常用的，它是动态判断操作系统的，操作系统是32位，他就是init32，操作系统是64位，它就是int64
2. 无符号
	1. uint 、uint8、uint16、uint32、uint64   同理 unit也是操作系统决定位数
3. 两个别名
	1. rune    相当于int32，一般专门用于表示 Unicode字符
	2. byte    相当于uint8

> [!tips]
> 补充一下，数字范围是如何计算的。 
> 1. 有符号 ，最高一位(其实就是最左) 代表符号，1代表负数，0代表整数
> int8  ->  就是 10000000 ~ 01111111    正的好算  2^7 -1  = 127
>        负数  就要通过补码的方式，将0变为1，1变为0 ，那么就是01111111，再加1，就是10000000   = 2^7 ，最后加上负数符号，就是 -2^7  = -128
>  所以： int8 的范围就是 -128 ~ 127
>  2. 无符号： 最高位不表示符号，而且无符号代表>=0，所以最小值就是0，最大值就是11111111   = 2^8 -1  = 255
>   所以： uint8 的范围就是 0 ~ 255

## 5.2 浮点型

float32 和float64


## 5.3 复数

这个就是我们高中学的  a+bi  这种形式，有复数i
complex64  和 complex128
在初始化的时候，没有办法表达是复数，所以采用内置的`complex`函数进行表达，同时有`real()`和`imag()`函数表示实数和虚数部分

```go
var x complex128 = complex(1, 2) // 1+2i
var y complex128 = complex(3, 4) // 3+4i
fmt.Println(x*y) // "(-5+10i)"
fmt.Println(real(x*y)) // "-5"
fmt.Println(imag(x*y)) // "10"
```

<span class='rt-b'>PS: 几乎用不到，不用考虑</span>

## 5.4 Bool

没啥好讲的，类型就是bool，值只有两个 true  false
go中，if条件必须是一个bool值，不像Js或者python中那样，会进行隐式转换

## 5.5 字符串

字符串类型 string ，  用上引号表示值，而且字符串是不可变的，说的是字符串本身不可变

```go
s := "hello"
f := s   // f保持不变，还是hello

s += ', world'   // 此时s变成了  hello,world。这是一个新的字符串，原来的字符串hello保持不变
```

注意： go中没有类型JavaScript或者groovy 的模板字符串，go中使用`fmt.Sprintf()`函数进行格式化字符串，类似Java中的`format()`
同时，go提供了一个字面量字符串，相当于原值输出，不用转义，就是用
```go
s := `这个是原值输出`
```

### 5.5.1 字符串的操作

在go中，处理字符串一般用到4个很重要的包 `bytes`、`strings`、`strconv` 、`unicode`  这四个包

<span class='r'> 🆘 🆘 需要注意：Go语言中的字符串，和Java的字符串完全不是同一回事，需要额外注意</span>

字符串和字节slice 之间可以互相转换
```go
s := "abc"
b := []byte(s)
s2 := string(b)

c : =[]rune(s)
s3 := string(c)
```

### 5.5.2 基础操作

1. **获取长度**
	1. 需要注意，go语言中获取字符串的长度，在我们惯性思维中用`len(str)`就能获取到，那么这个就是大错特错
	2. `len(str)` 返回的是字符串的字节数，‼️ ‼️ ‼️ 是字节数，不是Java或者Python中的字符数，一定要注意

```go
str := "hello,中国"
len(str)  // ‼️ ‼️ ‼️ 这个返回的不是8,而是12，因为汉字表达式一个3个字节数
```

那么，对于多字节的字符组成的字符串怎么获取长度呢？
 - 使用`unicode/utf8`包，通过`utf8.RuneCountInString()`函数来获取字符数
 - 将str转为`[]rune`切片， 然后获取切片的长度    `len([]rune(str))`

2. **访问单个字符**
	1. 不能直接通过`str[index]`的方式获取，直接使用索引访问到的可能是一个多字节字符的一部分，而不是完整的字符，不能这么做
	2. 如果需要处理所有的字符，用`for...range` 这样就是类似Java中处理字符串的逻辑
	3. 如果要获取某个特殊位置的字符，同样可以用上面的`for...raneg`，判断某个index进而得到字符
	4. 如果要获取某个特殊位置的字符，也可以，现将字符串转为`[]rune`数组，然后再使用索引index获取就行了

```go
	s := "Hello, 世界"
    // 使用 for...range 遍历并获取每个字符
    for _, char := range s {
        fmt.Printf("%c ", char)
    }

// 方式2
	s := "你好Go"
    
    // ❌ ❌ ❌ 错误的做法：直接索引会返回字节
    fmt.Printf("%v\n", s[0]) // 输出: 228 (字符 '你' 的第一个字节的十进制值)
    
    // ✅ ✅ ✅正确的做法：先转换为 []rune 切片
    r := []rune(s)
    // 获取第二个字符（'好'）
    fmt.Printf("%c\n", r[1]) // 输出: 好
```

3. **字符串拼接
	1. 使用`+`或者 `fmt.Sprinf()`

4. **生成重复字符串
	1. 将`abc`重复两次，得到`abcabc`

```go
str := "江苏"
ns := strings.Repeat(str, 3)  // 使用`string.Repeat()`方法
```

5. **空值检查
	1. 直接通过`len(str)`来检查

需要注意的是，因为go中的字符串是基础类型，它肯定明确了类型，所以不能`str == nil`，这个编译不会通过，nil只能用于引用类型，比如指针、函数、接口、slice等
所以，空值判断只需要 `str == ""` 即可

> [!danger]
> 需要注意的是，如果是字符串指针 （`var s *string`） ，字符串指针是可以为nil，所以需要优先判断字符串指针是否为nil  
> （`p == nil`），然后解值 `*p == ""` 才可以

6. **遍历
	1. 如果是遍历字节的话，直接`for i:0; i < len(str); i++ {}`
	2. 如果是遍历字符的话，用`for index,item := range str {}`

### 5.5.3 `string`包
这个包是处理字符串的核心工具，提供了大量的函数，用于处理字符串

#### 5.5.3.1 查找

1. 是否包含某个子字符串

```go
str := "江苏"
flag := strings.Contains(str, "江")
```

2. 是否以`prefix`开头

```go
strings.HasPrefix(str, "江")
```

3. 是否以`suffix`结尾

```go
strings.HasSuffix(str, "苏")
```

4. 子字符串第一次出现的索引

```go
strings.Index(str, "江")
```

5. 子字符串最后一次出现的索引 （从尾部查找）

```go
strings.LastIndex(str, "江")
```

6. 查找所有匹配字符串位置
go中没有类似`strings.IndexAll`的方法，所以只能手动处理了

```go
var indices []int
offset :=0
for {
	index := strings.Index(str[offset:], sub)
	if index == -1 {
		break;
	}
	append(indices, offset + index)
	offset += index + len(sub)
}
```


7. 统计子字符串的数量

go中提供了原生的`strings.Count(str, substr)`， 但是这个有个缺点就是，统计非重叠的子串的出现次，
比如`banana`中`ana`出现的次数，使用`string.Count()`统计出来是1，其实应该是2，这是因为`ana`有重复的部分，但是这个方法只能处理非重叠的子串
如果要处理重叠子串的话，自己手动写一个方法

> [!note]
> 补充几个很使用的方法
> 1. `strings.IndexAny(str, chars)` 
> 	1. 在字符串str中查找chars中的任意一个字符出现的第一个位置
> 	2. 场景： 比如chars是`.!`， 那么就在str中查询`.`或者`!` 这两个字符出现的第一个位置
> 2.  `strings.IndexFunc(str, func)`
> 	1. 满足条件是由func决定， 遍历str每个字符，然后把这个字符作为参数传给func，func返回true，那么就相当于找到了
> 3. `strings.IndexRune(str, char)`
> 	1. 查找特定的一个字符，而不是子串了，其实这个没啥必要，直接用Index也可以

#### 5.5.3.2 修改

1. 替换单个子串

```go
strings.Replace(str, old, new ,n) // 替换子串，n为替换次数，如果是-1的话，就是全部替换
strings.Replace(nStr, "Hello", "hello", -1)
```

2. 全局替换子串

```go
// 
strings.Replace(nStr, "Hello", "hello", -1)  // -1就代表是全部替换
// 也可以使用ReplaceAll
strings.ReplaceAll(nStr, "Hello","hello")
```

3. 插入字符串

如果是指定位置的话，只能手写代码逻辑了，没有现成的方法来做，需要通过字符数组来处理
```go
package main

import "fmt"

func main() {
	s := "世界 Go"
	insertStr := "你好，"
	index := 0 // 插入到字符串开头

	// 将字符串转换为 []rune，方便按字符操作
	runes := []rune(s)
	insertRunes := []rune(insertStr)

	// 使用切片操作插入
	newRunes := append(runes[:index], append(insertRunes, runes[index:]...)...)

	// 将新切片转换回字符串
	newStr := string(newRunes)
	fmt.Println(newStr)
	// 输出: 你好，世界 Go
}
```

4. 删除子串
同样，没有原生的方法，但是可以巧妙的解决，通过replace 替换成空字符串

5. 截取子串
	1. 直接通过`str[0:3]`  这种是按照字节截取，并不是我们想要的
	2. 安全地按照字符截取的话，需要将str转为`[]rune`数组，然后，通过数组截取的方式去获取即可

```go
package main
import "fmt"
func main() {
	s := "你好，世界"
	runes := []rune(s)
	// 截取前两个字符
	substring := string(runes[0:2])
	fmt.Println(substring) // 输出: 你好
}
```

6. 反转字符串
同样，通过`[]rune`的方式进行处理，最后倒序循环处理即可

7. 大小写转换

```go
strings.ToUpper(str)
strings.ToLower(str)
```

8. 去除空白
```go
strings.TrimSpace(str) // 去除两端的空格
```

9. 去除字符串满足特定子串中任意字符的字符

```go
strings.Trim(str, cutset)  // 去除字符串两端满足 cutset 中任意字符的字符。
```

#### 5.5.3.3 分割与组合

1. 按照分隔符分割

```go
strings.Split(str, sep)  
```

2. 合并数组为字符串

```go
strings.Join(a []string, sep string)  // 用sep连接数组，最后生成字符串
```

3. 按照任意空白符进行分割
上面的`strings.Split`只能传入特定数量的空白符，但是原生的方法提供一个方法

```go
strings.Fields(str)  // 最后返回按照任意个数空白符进行分割的数组
```

4. 按照正则表达式的方式进行分割

不用`strings`包，而是`regexp`包，通过正则的包去进行处理

### 5.5.4 strconv包

这个包用于字符串和基本数据类型之间的转换

#### 5.5.4.1 字符串转为数字

`Atoi 其实就是 ASCII  to  Int`

```go
strconv.Atoi(str)  // 将字符串转为int
strconv.ParseInt(str, base, bitSize)   将字符串转为指定进制和位数的int64
strconv.ParseFloat(s, bitSize)  // 转为浮点数
```

#### 5.5.4.2 数字转为字符串

```go
strconv.Itoa(i)  将int转为字符串
strconv.FormatInt(i, base)  将int转为指定进制的字符串
strconv.FormatFloat(f, fmt, prec, bitSize)  将浮点数转为字符串
```

### 5.5.5 bytes包
这个包用于处理大量字符串或者处理二进制数据，`bytes`会更加高效，后续再进行补充

```go
// 将一个int类型的数组，最后组成字符串格式  "[1, 3, 4, 5]"  这种格式
func intsToString(values []int) string {
	var buf bytes.Buffer
	buf.WriteByte('[')
	for i, v := range values {
		fmt.Println(i, v)
		if i > 0 {
			buf.WriteString(", ")
		}
		fmt.Fprintf(&buf, "%d", v)
	}
	buf.WriteByte(']')
	return buf.String() 
}
```


## 5.6 常量

常量表达式的值在编译期间就完成，而不是在运行时期。每种常量的潜在类型必须是基础类型：boolean、string或者数字

```go
const PI = 3.1415926

const (
	e = 2.718
	pi = 3.1415926
)
```
还有一个奇葩的语法，如果是批量声明的常量，除了第一个外，其他的常量右边的初始化表达式都可以省略，如果省略初始化表达式则使用前面常量的初始化表达式写法，对应的常量类型也一样

```go
const (
   a = 1
   b
   c = 3
   d
)

fmt.Println(a, b, c, d) // 最终输出 1 1 3 3
```

### 5.6.1 iota常量生成器

常量声明可以使用`iota`常量生成器初始化。用于生成一组相似规则的常量

```go
type Weeekday int
const (
	Sunday Weekday = iota
	Monday
	Tuesday
	Wednesday
	Thursday
	Friday
	Saturday
)
```

这个有点类似Java中枚举。`iota`是一个预声明的常量生成器，它只能用在`const`声明中。它的作用是简化连续、递增的常量声量，`iota`基本原理
- `iota`的初始化值是0
- 在每一个`const`声明块中，`iota`的值都是从`0`开始递增
- 在声明每个常量的时候，`iota`的值都会自动增加`1`
- 当`const`块结束的时候，`iota`会被重置为0

```go
const (
	D = iota // D = 0
	E        // E = 1
	_        // iota = 2, 但这个值被丢弃
	F        // F = 3
)
func main() {
	fmt.Println(D, E, F)
	// 输出: 0 1 3
}

const (
    Flag_A = 1 << iota // 1 << 0 = 1
    Flag_B             // 1 << 1 = 2
    Flag_C             // 1 << 2 = 4
    Flag_D             // 1 << 3 = 8
)
```

# 6. if  for  switch


