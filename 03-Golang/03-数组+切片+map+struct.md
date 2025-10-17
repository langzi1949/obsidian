#Go #Golang 

# 1.  数组
数组是一个固定长度特定的类型元素组成的序列，它的长度固定，这个跟Java有点像，一般不用数组，用的最多的其实是ArrayList， go也有对应的，叫`Slice`，翻译中文叫 『切片』 很怪😱😱

## 1.1 定义和初始化数组

```go
var arr [3]int  // 大小为3的数组
arr := [3]int{}   // 和上面一样
// 声明的时候，直接赋值
arr := [3]int{1,3,4} 
arr := [3]int{1,4}  // 也可以初始化部分，后面的都是为0

// 如果数组的长度位置出现...省略号，表示数组的长度由初始化的值的个数来计算
arr := [...]int{1,2,3}  // 最后大小就是3
// 数组的长度是数组类型的一部分，所以[3]int和[4]int是不同的数组类型
q := [3]int{1,2,3}
q = [4]int{4,5,6,7}  // ❌ ❌ ❌ 这个是错误的

// 还有一个很奇葩的初始化方式
arr := [...]int{0:1,1:6,2:8} // 在初始化中通过类似k:v的方式，来指定index下的数据 最终数组是[1,6,8]
arr := [...]int{99: 1}  // 可以初始化一个大小为100的数组，index=99的值为1，其余为0
```

# 2. Slice 切片

切片Slice代表的是变长的序列，序列中每个元素必须相同类型。一个slice类型一般写作`[]T` ，其中T代表slice中的元  素类型，slice的语法和数组很像，只是没有固定长度
切片可以从数组或者另一个slice而来，但更常见的是直接创建一个切片，这个底层是创建了一个匿名数组

一个slice有三个部分组成 ：**指针、长度以及容量**
大致类似这个结构
```go
type IntSlice struct {
ptr *int
len, cap int
}
```

## 2.1 从数组派生出slice

![[Pasted image 20250908153456.png]]

```go
arr := [8]string{"a","b","c","d","e","f","g","h"}
slice := arr[1:4]   // 这个长度就是 4-1 = 3， 容量就是从1 到 len(arr-1) 容量7
endless := slice[:6]  // 这没有问题，返回的就是 切片 [a, b, c, d ,e, f]  // 因为没有超过容量
endless := slice[:8] //❌ ❌ panic: runtime error: slice bounds out of range [:5] with capacity 4
```


>[!danger]
> 需要注意的是，slice唯一合法的比较操作是和`nil`进行比较，其他的比较都是非法的，两个slice之间都不能直接`!=`这种比较

## 2.2 通过make创建

内置的函数`make`可以创建一个指定元素类型、长度和容量的slice。 其中容量部分可以省略，省略的话，就是容量=长度

```go
s := make([]int, 5) // 容量为5
s := make([]int, 5, 20)  // 容量为20
```

底层原理是创建了一个匿名的数组变量，然后返回slice；只有通过返回的slice才能引用底层匿名的数组变量。
第一个语句，相当于slice就是整个大小为5的数组的完全副本。
第二个语句，相当于只引用了前5个元素，但是容量是包含整个大小为20的数组的

## 2.3 通过字面量的方式

注意是 `[]T{}`的方式

```go
// 最常见的方式是
s := []int{3,4,5} 
var s []int  // 这种相对创建了空的slice，len=0, cap也等于0
```

## 2.4 slice的一些用法

1. append函数
内置的append函数用于向slice追加元素， 并返回一个slice，这个slice和原来的slice是公用同一个底层数组，区别于是否扩容和复制数组。这一点和Java中的ArrayList有点类似。

```go
s = append(s, 10, 20) // 追加两个数据
// 删除索引为1的元素
s2 = append(s2[:1], s2[2:]...)
```

> [!danger]
> 上面的`s2[2:]...`  这个写法就跟JavaScript中的`...`一样，解构slice，将slice数据，直接全部解构出来

底层的原理：
- 如果添加后的长度小于等于capacity，那么就直接赋值就行
- 但是大于capacity了，就要进行扩容一倍，得到一个新的slice，将原有的数据copy过去，就OK了

2.  删除元素

有两种方式
1. 去除两个部分，然后拼接起来

```go
s = append(s[:1], s[2:]...)  // 这个就是两段拼起来
```

2.  将后半部分，复制到正确的位置，然后截取

```go
func delete(s []int, index int) []int {
	copy(s[index:], s[index+1:])
	return s[:len(s) - 1]
}
```

## 2.5 关于slice的一些技巧

1. 预分配容量，提高性能
	1. 这个和Java类似，如果明确的知道大致需要多少容量，可以预分配大一点的容量，这样可以减少频繁的分配内存和复制数据，从而大幅提高性能
2. 巧用`copy`函数
	1. `copy()`函数可以将一个切片的元素复制到另一个切片，这比手动循环赋值要高效

```go
source := []int{1, 2, 3}
destination := make([]int, 3)
copy(destination, source) // 将 source 的内容复制到 destination
```

3. 清空切片

清空切片最快的方式是将其长度设为0，从而无需重新分配内存

```go
mySlice := []int{1, 2, 3}
mySlice = mySlice[:0] // 长度变为0，容量不变
```

4. 避免切片泄露

当从一个大切片截取小切片之后，然后大切片不再使用，小切片依然持有对底层大叔组的引用，导致无法GC，可能有内存泄露问题
可以使用`copy`函数将小切片的内容复制到一个新的、小的底层数组中

```go
// 假设 bigSlice 非常大，但我们只需要一个小子集
bigSlice := make([]byte, 10000)
smallSlice := bigSlice[100:200]
// 创建一个新切片，只包含 smallSlice 的内容
// 这样 bigSlice 就可以被垃圾回收了
newSlice := make([]byte, len(smallSlice))
copy(newSlice, smallSlice)
```

# 3. Map 

这个map使用和Java上的使用几乎没有区别，有的只有一些细节上的不同而已

## 3.1 声明和初始化

go中的Map初始化有两种方式
1. 通过`make`函数进行创建
	1. 语法： `make(map[键类型]值类型`
2. 通过字面量的方式创建
	1. `map[键类型]值类型{键1: 值1, 键2: 值2, ...}`

```go
m := make(map[int]int)
m[23] = 100
// 字面量的方式
a := map[string]int{
		"name": 223,
		"age":  22,
	}
```

## 3.2 访问元素

正常情况下，直接通过`map[k]`的方式就能获取到值，但是有一个特别注意的坑，如果值是int类型的， 取值一个不存在的key，那么会返回0，这个跟Java不同，Java是返回一个null， go是返回零值，这就有一个问题，导致恰巧值是0 ，还是因为key不存在而返回的0？

所以，go提供了一种更加安全的处理方式 `v,ok := map[k]` 的方式，它会返回两个值，其中ok就是判断值是否存在的

```go
scores := map[string]int{"Alice": 98, "Bob": 88}

// 安全地获取值
value, ok := scores["Alice"]   // ✅ ✅ ✅
if ok {
	fmt.Println("Alice 的分数是:", value)
} else {
	fmt.Println("Alice 不在 map 中")
}

// 访问一个不存在的键会返回零值（本例中为 0）
value2, ok2 := scores["Charlie"]
fmt.Println("Charlie 的分数是:", value2) // 输出: 0
fmt.Println("Charlie 是否存在:", ok2)   // 输出: false
```

## 3.3 删除元素

通过内置的函数`delete`来解决

```go
scores := map[string]int{"Alice": 98, "Bob": 88}

delete(scores, "Bob") // 删除键 "Bob"
fmt.Println(scores)   // 输出: map[Alice:98]
```

## 3.4 遍历元素


```go
colors := map[string]string{
	"red":   "#ff0000",
	"green": "#00ff00",
	"blue":  "#0000ff",
}

for key, value := range colors {
	fmt.Printf("键: %s, 值: %s\n", key, value)
}
```

## 3.5 nil的处理
map的零值是`nil`， 那么不能直接访问和添加元素，只能=直接赋值，所以显得鸡肋，大家都是直接初始化就用了

```go
var myMap map[string]int // myMap 的零值是 nil

// 这行代码会引发 panic
// myMap["test"] = 1 

// 正确的做法是先用 make() 初始化
myMap = make(map[string]int)
myMap["test"] = 1
```

# 4. struct 自定义结构体

go中没有Java中的类的概念，所以只能用`struct`来代替， 跟C语言很像

```go
type Person struct {
	Name         string    // 注意，没有冒号 ，同样结尾没有逗号或者分号
	Age          uint8
	Tel, Address string   // 相同类型的可以一起声明
} 
```

> [!danger]
> 注意，每行最后没有分号，属性和类型之间也没有冒号； 相同类型的可以一起声明

## 4.1 初始化

```go
// 方式1： 按照字段顺序初始化，不推荐
var p = Person{"zmg", 22, "12323", "Shanghai"}  // 相当于一一对应，这种方式不好，只适合属性很少的
// 方式2： 通过kv方式初始化，最推荐
p := Person{Name: "zmg", Age: 22}  //通过这个k-v的方式，才是正确的
// 方式3：零值初始化，后续在进行赋值操作
var p Person    // 零值初始化
```

> [!danger]
> 注意，一个P类型中的不能包含属性为P类型的， 只能用P类型的指针，当然可以包含其他类型，这个就是组合的形式，类似Java中的继承

```go
type Person struct {
	Name         string
	Age          uint8
	Tel, Address string
	Superior     *Person    // 包含一个Person类型的指针
}
```

## 4.2 结构体比较

如果结构体中的属性都是可比较的，那么这个结构体就是可比较的
可比较的结构体，可以作为map的key来使用；要不然不能作为key来使用

## 4.3 访问字段

和Java的访问一样，通过`.`进行访问和修改结构的字段

```go
user := User{Name: "Charlie", Age: 40}
fmt.Println(user.Name) // 输出: Charlie

user.Age = 41          // 修改字段值
fmt.Println(user.Age)  // 输出: 41
```

## 4.4 使用结构体指针

正常情况下，在方法的参数中都是接收结构体指针，避免在函数调用的时候对整个结构体进行值拷贝，从而提升性能

```go
func increaseAge(u *User) {
    (*u).Age++ // 两种语法都可以
    u.Age++    // 更简洁的语法，Go会自动解引用
}

func main() {
    user := User{Name: "David", Age: 35}
    increaseAge(&user) // 传递结构体的地址
    fmt.Println(user.Age) // 输出: 36
}
```

## 4.5 嵌套结构体和匿名成员

背景： 正常情况下，我两个结构体，包含别人应该怎么做呢？

```go
type A struct {
	Name string
}

type B stuct {
	Age int8
	Ainfo A
}

// 然后怎么访问Ainfo中的Name呢？
var b B
b.Ainfo.Name = "zmg"  // ❗️ ❗️ ❗️这样就显得有点啰嗦，不够简洁
```

所以，go提供了一个特性让我们只声明一个成员对应的数据类型而不指定成员的名称： 这个就是匿名成员
- 匿名成员的数据类型必须是一个命名的类型或者指向一个命令的类型的指针
- 这个其实就是Go提供的继承的逻辑，在go中成为`Composition - 组合`，这是Go实现面向对象设计的主要方式

```go
// 上面的就可以写成
type A struct {
	Name string
}

type B stuct {
	Age int8
	A
}
// 使用的时候直接用
var b B
b.Name = "zmg"
// 当然之前的方式也可以
b.A.Name = "zmg" //  这个方式也是可以的，但是更多的是使用上面的写法
```

<span class="r">值得注意的是：</span>
在结构体字面量中不能简短表示匿名成员的语法
```go
var b B = B{Name:"zmg"}  // ❌ ❌ ❌ compile error: unknown fields
// 如果要使用字面量的方式的话
var b = B{A: A {Name:"zmg"}, Age:19}  // 用这种方式
```

## 4.6 结构体标签

可以在结构体字段后添加标签，通常用于元数据，例如 JSON、数据库字段映射等

```go
type Product struct {
    ID    int    `json:"id"`
    Name  string `json:"product_name"`
    Price float64 `json:"price"`
}
```

## 4.7 struct作为方法的接收这

go可以在结构体上定义方法，这使得结构体能够用于行为

```go
// 使用值接收者，不会修改原始结构体
func (u User) GetAge() int {
    return u.Age
}

// 使用指针接收者，可以修改原始结构体
func (u *User) SetAge(newAge int) {
    u.Age = newAge
}
```

# 5. JSON

在标准库中，有一个`encoding/json` 这个是对JSON操作的核心包

## 5.1 编码（Encoding） Go Stuct -> JSON

```go
package main

import (
	"encoding/json"
	"fmt"
)

// 定义一个结构体，字段首字母必须大写才能被json包访问
type User struct {
	Name string
	Age  int
	// 使用json tag来定义JSON字段名，并忽略空值
	Email string `json:"email,omitempty"`
}

func main() {
	user := User{
		Name:  "Alice",
		Age:   30,
		Email: "alice@example.com",
	}

	// 编码为 JSON 格式的字节切片
	jsonData, err := json.Marshal(user)
	if err != nil {
		fmt.Println("Error:", err)
		return
	}

	fmt.Println(string(jsonData))
	// 输出: {"Name":"Alice","Age":30,"email":"alice@example.com"}
}
```

- 只有字段可见，才会被JSON包编码

>[!danger]
>如果需要对JSON格式format， 采用`json.MarshalIndent(movies,""," ")`   参数可以参考文档

## 5.2 解码 （Decoding）JSON -> Go Struct

```go
package main

import (
	"encoding/json"
	"fmt"
)

type User struct {
	Name string `json:"name"`
	Age  int    `json:"age"`
}

func main() {
	jsonData := []byte(`{"name":"Bob", "age":25}`)

	var user User

	// 解码到 user 变量中
	err := json.Unmarshal(jsonData, &user)  // 重点
	if err != nil {
		fmt.Println("Error:", err)
		return
	}

	fmt.Printf("Name: %s, Age: %d\n", user.Name, user.Age)
	// 输出: Name: Bob, Age: 25
}
```


# 6. 文本和HTML模板

简单的格式化，用`fmt`下的`Print`基本也够了，但是一些复杂的逻辑，可以使用`text/template`或者`html/template`等模板包才处理
PS: `html/template`包含`text/template`，并且额外提供了HTML前端的跨站脚本攻击（XSS）
## 6.1 通用文本模板
`text/template`主要用于生成任何类型的纯文本输出，比如配置文件、email、或者Java文件等

使用流程
- 创建模板 ： 使用`template.New()`创建一个模板实例
- 解析模板：使用`template.ParseFiles()`或者`template.ParseGlob()`解析模板文件，或者使用`template.Parse()`解析字符串
- 执行模板：使用`template.Execute()`方法将数据应用到模板上，并写入`io.Writer`

```go
package main

import (
	"log"
	"os"
	"text/template"
)

func main() {
	// 定义一个数据结构
	type Config struct {
		Host string
		Port int
	}

	// 准备模板字符串
	const configTmpl = `
Host: {{.Host}}
Port: {{.Port}}
`

	// 创建一个模板实例
	tmpl, err := template.New("config").Parse(configTmpl)
	if err != nil {
		log.Fatal(err)
	}

	// 准备数据
	data := Config{"localhost", 8080}

	// 创建配置文件
	file, err := os.Create("config.txt")
	if err != nil {
		log.Fatal(err)
	}
	defer file.Close() // 确保文件在函数结束时关闭

	// 执行模板并将结果写入文件
	err = tmpl.Execute(file, data)
	if err != nil {
		log.Fatal(err)
	}

	log.Println("配置文件已生成：config.txt")
}
```

## 安全HTML模板

`html/template`专门为Web开发设计了，后续如果用到了，再继续学习

```go
package main

import (
	"html/template"
	"log"
	"os"
)

func main() {
	// 假设用户输入了恶意数据
	type User struct {
		Name string
	}
	// 这里的 Name 字段包含一个脚本标签
	userData := User{"<script>alert('XSS')</script>"}

	const htmlTmpl = `
<h1>Hello, {{.Name}}!</h1>
`

	tmpl, err := template.New("webpage").Parse(htmlTmpl)
	if err != nil {
		log.Fatal(err)
	}

	// 执行模板
	tmpl.Execute(os.Stdout, userData)
}
```

