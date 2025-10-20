
# 1. typedef

这个关键字是用于针对现有类型创建一个别名，用于简化表达同时增强可读性。

- 增强可读性和维护性
- 提高可移植性
- 简化复杂类型声明

```c
#include <stdio.h>

typedef unsigned int uint;

int main()
{
    // 这个是原生的
    unsigned int a = 10;
    // 这个用uint别名，它代表unsigned int
    uint b = 10;
    printf("a = %u, b = %u\n", a, b);
    return 0;
}
```

```c
// 1. 基本类型别名
typedef int Integer;
Integer x = 5;

// 2. 指针别名 (常用，避免 int* p1, p2 的歧义)
typedef char* String;
String s1, s2; // s1 和 s2 都是 char* 类型指针

// 3. 数组别名
typedef int Array10[10];
Array10 myArray; // 等价于 int myArray[10];
```

```c
// 定义一个名为 'Person' 的新类型
typedef struct Person {
    char name[50];
    int age;
} Person;

struct Person p2;
Person p1; // 无需写 struct Person p1;


// 定义函数指针
// 声明一个函数指针变量 'op_ptr'，它指向一个接受两个 int 并返回 int 的函数
int (*op_ptr)(int, int);
// 1. 定义函数指针类型别名：IntOperator
typedef int (*IntOperator)(int, int);

// 2. 使用别名声明变量：
IntOperator op_ptr; // 简洁且清晰


// 必须在结构体内部使用完整的 struct Node 名称
// 而在外部使用 typedef 创建的别名 (Node_t)
typedef struct Node {
    int data;
    struct Node *next; // 内部必须是 struct Node *
} Node_t;

Node_t head; // 使用别名声明变量
```

> [!danger]
> `typedef` 和 `#define`的区别？？
> - 工作方式： `typedef`是在编译阶段由编译器处理，`#define`是在预处理阶段进行文本替换
> - 安全性：    `typedef`安全， `#define`在多变量的指针声明会出错
> 	- `typedef int* IntP;   IntP a, b;`  a和b都是指针
> 	- `#define IntP int*    IntP a , b;`   只有a是指针，b还是int。 因为是文本替换，相当于`int *a, b`

# 2. static

## 2.1 修饰局部变量

当用`static`修饰局部变量之后，就会改变变量的声明周期

- 当被static修饰的变量不会存放在栈区，而是放在了静态区中
- 生命周期：变量只有初始化一次，后面不会初始化，直接从静态区中获取即可，直到程序结束
- 作用域： 局部变量的作用域还是函数体内

```c
#include <stdio.h>

void counter() {
    // count 只在第一次调用时初始化为 0
    static int count = 0; 
    count++;
    printf("当前调用次数: %d\n", count);
}

int main() {
    counter(); // 输出: 1
    counter(); // 输出: 2
    return 0;
}
```

## 2.2 修饰全局变量

当`static`用于文件作用域内的全局变量时， 它改变了变量的『链接性』，限制了它的可见范围，只能当前文件可见

```c
// 这是一个静态全局变量，只能在 file1.c 内部使用
static int file_private_data = 100; 

void print_data() {
    printf("Data in file1: %d\n", file_private_data);
}
```

## 2.3 修饰函数

和修饰全局变量类似，控制函数的可见性

```c title=tools.c
// 这是一个静态函数，只能在 tools.c 内部调用
static int private_helper(int a, int b) {
    return a + b;
}

// 这是一个外部函数
int public_api(int x) {
    // 可以在内部调用静态函数
    return private_helper(x, 10); 
}
```

```c title=main.c
// 声明外部函数
extern int public_api(int x);

int main() {
    public_api(5);         // 成功调用 public_api
    // private_helper(1, 2); // 错误：找不到 private_helper 函数
    return 0;
}
```

> [!danger]
> extern使用的时候，最后编译的时候，需要把定义这个变量的文件也要一起编译
> # 确保你的命令包含了所有相关的目标文件
   `gcc main.c vars.c -o my_program`
> 

# 3. extern

`extern`的作用就是声明外部定义的变量以及函数，允许跨文件访问。

# 4. define

define是用于定义常量和宏的。

 1. 定义常量

```c
#define PI 3.14159
#define MAX_SIZE 100
```

2. 定义宏 （宏可以理解成一个函数）

```c
#define ADD(x, y) ((x) * (y))
```

一般来说宏函数都是用大写，里面的参数不用类型， 最后直接给出结果，当然，逻辑可以很复杂
为啥用括号包裹x和y呢？ 
那是因为处理宏的时候，是进行文本替换，比如
ADD(1+3， 2 +3);   最后替换的结果  `((1+3) * (2+3))` = 4 * 5 = 20
如果不加上括号 ，就是  `1+3 * 2 +3` = 1 + 6 + 3 = 10。
==所以一定要加上括号。==

## 4.1 可见性

如果不是在头文件中的话，普通的c文件定义的宏，只能当前文件可见，其他的文件无法访问。

## 4.2  为啥需要宏？ 全局函数不行吗？

实际项目中，宏用的非常多，多到可怕。为啥呢？ 为啥不能直接用函数呢？
因为，宏有函数没有的超级优势

1. 零运行时开销（性能拉满）
	1. `#define` 定义的宏，在预处理阶段就进行了文本替换，最后相当于没有函数的调用了，被替换了，性能极高
	2. 函数 需要在运行时进行调用，有栈帧的开销，影响性能
2. 类型无关
	1. 宏函数的参数不用定义类型，处理起来就非常方便
	2. 用函数的话，可能需要每个参数类型都来一个函数，非常麻烦
3. 条件编译
	1. 这个是函数无法实现的

```c
#ifdef DEBUG
    #define LOG(...) printf(__VA_ARGS__) // 只有在 DEBUG 模式下，LOG 才有效
#else
    #define LOG(...)                   // 生产模式下，LOG 宏展开为空，零开销
#endif
```

所以，在逻辑不复杂的函数，将其改成宏，会提升性能。
但是逻辑很复杂的，千万别整成宏，这样排查问题将会变成黑洞。
副作用
```c
#define MAX(a, b) ((a) > (b) ? (a) : (b))

int x = 5;
int y = MAX(x++, 10); // 错误！

// 宏展开后：
// int y = ((x++) > (10) ? (x++) : (10));
// x++ 可能被执行一次或两次，导致 x 的值和 y 的结果都不可预测。
```

如果有一些复杂的宏，可以使用`inline`函数，`inline`函数拥有函数的类型安全，同时也有可调试性。它会告诉编译器按照宏的方式进行代码内联，从而避免函数调用开销

# 5. `#`和`##`


# 6. 几个面试题

> 写一个宏，计算结构体中某变量相对于首地址的偏移量，并给出说明

```c
#define OFFSET_OF(struct_type, member) (size_t)&(((struct_type *)0)->member)
```

解释一下上面的宏

- `(struct_type*)0`  ： 将地址`0`强制转换为结构体指针
- `->member`  访问该指针指向的结构体的`member`成员
- `&(((struct_type *)0)->member)`  取该成员的地址，由于结构体首地址被假设为`0`，此时成员的地址就是相对于首地址的偏移
- `size_t`  将地址值转为`size_t`类型，得到偏移量的数值


```c
#include <stdio.h>
#include <stddef.h>

struct Stu
{
    char name[17];
    int age;
    float score;
};
#define OFFSET_OF(struct_type, member) (size_t)&(((struct_type *)0)->member)

int main(void)
{ 
    printf("name偏移量：%zu\n", OFFSET_OF(struct Stu, name));   // 输出 0
    printf("age偏移量：%zu\n", OFFSET_OF(struct Stu, age));     // 输出 20
    printf("score偏移量：%zu\n", OFFSET_OF(struct Stu, score)); // 输出 24
    return 0;
}

```

> [!danger]
> 在C语言中的标准库中已经有`offsetof`这个宏了，实现的逻辑就是上面的逻辑
> 使用`#include <stddef.h>`，然后直接使用即可
> 还有一个问题？ 为啥上面的`age`相对于首地址是20呢？ 这个就涉及到`struct`的内存分配问题了，详细的请参考[[06-struct结构体和union共同体]]








