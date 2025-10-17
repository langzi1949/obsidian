
# 1. 定义struct

```c
struct Student
{
    int id;
    char name[20];
    enum GENDER gender;
};

// 同时还有一个很诡异的功能，创建结构体的是，同时创建两个全局变量
struct Student
{
    int id;
    char name[20];
    enum GENDER gender;
} stu1, stu2;

// 匿名结构体，但是同时创建全局变量
struct
{
	int id;
	char name[20];
} stu1;   // 定义了一个全局变量stu1
struct
{
	int id;
	char name[20];
}arr[20], *p;    //一个是数组，一个是指针
```

使用：
```c
int main()
{
	// 通过顺序进行初始化
    struct Student stu = {100, "kobe", MALE};
    printf("%d %s %d\n", stu.id, stu.name, stu.gender);
	// 通过属性名进行创建
	struct Student stu2 = {.gender = MALE, .id = 90, .name = "zmg"};
    printf("%d %s %d\n", stu2.id, stu2.name, stu2.gender);
    return 0;
}
```

> [!danger]
> 注意事项：
> 1. 通过属性名进行初始化的时候，一定是`.属性名`
> 2. 匿名结构体使用很少

# 2. 函数参数作为struct指针

```c
void printStu(struct Student *s)
{
    printf("%d %s %d\n", (*s).id, (*s).name, (*s).gender);
    // 这个是上面(*s).id的简写
    printf("%d %s %d\n", s->id, s->name, s->gender);
}
```

如果是结构体指针，那么获取成员变量就用`->`操作符。
一般结构体作为函数的参数的时候，都是用指针表示，如果不用指针表示就会进行参数的复制，会带来性能的影响。

# 3. 给结构体的属性进行赋值

正常的基本的类型的赋值直接用 等号就可以， 但是数组名是一个常量指针，不能被整体赋值，可以用数组的copy来实现

```c

size_t max_len = sizeof(stu1.name);
strncpy(stu1.name, "kobe", max_len - 1);
stu1.name[max_len - 1] = '\0';
```

# 4. 补充一个小知识点 n&(n-1)
<span class="big b">n & (n - 1)</sapn>
这个有啥作用？
它的作用就是会将一个数据转为2进制后，最右边的一个1会变成0。

```c title=计算二进制数字中有多少个1
int count = 0;
while (num > 0)
{
	num = num & (num - 1);
	count++;
}
```

```c title=判断一个数是否是2的n次方
if (n > 0 && (n & (n - 1)) == 0) 
{
	return true; // 说明是2的n次方
}
```

# 5. typeof

上面我们用`struct`的变量声明的时候，都是`struct Student stu` 这种方式，显得有点啰嗦，我们可以给`struct Student`起一个别名。

```c
typedef struct 
{
    char name[20];
    int age;
    enum GENDER gender;
} Stu;


Stu s = {.....}
```

同时，我们需要注意的是，如果结构体里面有嵌套，比如Node链表，这样不能进行`typedef  匿名结构体`

```c
typedef struct
{
	int val;
	Node *next;
} Node;  //  ❌❌❌❌  这个是错误的方法，是鸡生蛋还是蛋生鸡的问题

// 但是在结构体的typedef可以重名
typedef struct Node 
{
	int val;
	Node *next;
}  Node;   // 依然还是用Node，这个会更加直观 ✅ ✅ ✅
```

# 6. sizeof of struct

在C语言中，计算struct的sizeof，并不能简单的将所有成员变量的大小加起来。
struct的大小由两个核心的机制决定：
- **Data Alignment  数据对齐**
- **Structure Padding  结构体填充**

核心的逻辑：
CPU通常不是按照字节访问内存的，而是按照其字长（比如int 4个字节）访问的，如果数据没有对齐到其自身大小的倍数地址上，CPU就要多次访问内存，才能找到，效率会大大降低。 

>现代CPU读取内存肯定不是一个字节一个字节的读取，是按照字长（4个字节或者8个字节）或者缓存行（64字节）批量读取的。

1. **成员变量对齐**
	1. 每个成员变量都有一个对齐要求，通常就是其自身的大小
	2. 编译器会确保每个成员变量都从一个**可被其自身大小整除的内存地址**开始存储
	3. 编译器在编译的阶段根据对齐规则和填充规则，就得到每个成员的边界，并在生成机器码中固定下来，方便寻址读取数据。
2. **结构体填充**
	1. 因为要满足上面的对齐，可能会导致两个成员之间会有空闲的字节，这个过程就是填充。
3. 等上面都填充完毕，还需要满足 **结构体的总大小 = 最大成员大小的N倍才可以**。所以最后才需要进行一些填充。

```c
struct Student
{
    char id;
    int age;
    char c2;
};

int main(void)
{
    printf("struct student大小：%ld\n", sizeof(struct Student));  // 答案是12  并不是 1 + 4 + 1
    return 0;
}
```

## 6.1 如何消除结构体填充

1. 优化成员排序  (推荐)
	1. 优化的原则： 将成员按照大小降序排列

```c
// 重新排列：按大小降序排列
struct BetterExample {
    int b;   // 4 字节
    char a;  // 1 字节
    char c;  // 1 字节
};
// 结果：int b (0-3), char a (4), char c (5)。总大小 6 字节。
// 整体对齐到 4 的倍数：8 字节。

// sizeof(BetterExample) = 8 字节
```

2. 使用非标准编译指令来修改对齐参数 （非跨平台）
	1. 可以让编译器在牺牲一点性能的情况下，根据某个规则自动进行消除结构体填充
	2. 使用`#pragma pack(n)`的指令告诉编译器，结构体中的成员应该以n字节进行对齐， n一般是2的n次方

```c
#include <stdio.h>

// 告诉编译器，接下来的结构体都以 1 字节为单位对齐
#pragma pack(push, 1) 或者用 #pragma pack(1)

struct PackedExample {
    char a;  // 1 字节
    double b; // 8 字节，但现在只按 1 字节对齐
    char c;  // 1 字节
};

// 恢复编译器默认的对齐设置
#pragma pack(pop)   或者使用#pragma pack()

int main() {
    // 成员总大小: 1 + 8 + 1 = 10 字节
    // 由于 #pragma pack(1)，sizeof 结果将是 10 字节 (消除了所有填充)
    printf("Size: %zu\n", sizeof(struct PackedExample));
    return 0;
}
```

3. 使用`__attribute__((packed))`  (适合GCC/Clang)
	1. 这个是GCC/Clang特有的拓展，效果与`#pragma pack(n)`是类似的
```c
struct PackedExample {
    char a;  
    double b;
    char c;  
} __attribute__((packed)); // 直接作用于结构体定义
```

> [!danger]
> 一定要注意，虽然`#pragma pack` 可以有效节省内存，但是在某些场景下性能会下降


# 7. 结构体嵌套

```c
struct A
{
    int value;
    struct B b;
};


struct A
{
    int value;
    struct B *b;
};
```

一般来说我们建议嵌套的使用指针来表示，如果用第一种方式的话，最终的内存会很大，而且B的变量没法进行复用。

同时如果是是自嵌套，必须要用指针，因为不用指针的话，最终导致无限递归，计算sizeof就崩溃了

```c
struct Node
{
    int value;
    struct Node next; // ⚠️ 错误！尝试包含自身的完整副本 ❌ ❌ ❌  编译就报错了
};
```

一定要用指针，这样才能确定下来结构体的大小。

```c
struct Node
{
    int value;
    struct Node *next; // ✅ 正确！包含指向自身类型的指针
};
```

# 8. 位段

位段（Bit Fields）是C语言中一种特殊的结构体（struct）成员，
- 首先，它还是struct的成员变量
- 允许用bit为单位来定义和使用此结构体成员，最大限度地节省内存空间

> <span class="b"> 所以说： 位段就是允许结构体成员所占用确切的位数。编译器会将其紧密的打包到更小的存储单元，以减少内存浪费。</span>

## 8.1 位段使用

位段的使用非常简单，只需要在结构体的成员类型后面加上`:`和一个整数，表示该成员所占用的位数。

```c
struct s {
    type member1 : width1;
    type member2 : width2;
    // ...
};
```

- `type`：必须是整数类型，虽然可以是long，但是一般都是`int`、`char`, `_Bool`、`unsigned int`等
- `width`： 表示位数，不能超过`type`的大小。

位段的内存分配： 如果是int类型，按照4个字节来分配，总共32位，让变量进行填充，不够了会再开辟4个字节，总之是按照int类型大小开辟空间；如果是char类型，就按照1个字节来分配空间。

## 8.2 例子

```c
// 假设 int 是 4 字节（32位）
struct Date {
    unsigned int day : 5;    // 日期 1-31, 需要 5 位 (2^5 = 32)
    unsigned int month : 4;  // 月份 1-12, 需要 4 位 (2^4 = 16)
    unsigned int year : 11;  // 年份, 假设需要 11 位 (2^11 = 2048)
    // 总共占用 5 + 4 + 11 = 20 位。
    // 编译器会将其打包进一个 32 位的 unsigned int 中。
};

// 实际使用
struct Date today = {10, 10, 2024};
printf("日期：%d/%d/%d\n", today.year, today.month, today.day);

// 访问方式与普通结构体成员完全相同
today.day = 15;
```

## 8.3 使用场景

- 单片机或者嵌入式中，内存紧张，需要对这些数据进行压缩
- 硬件寄存器的映射（一脸懵逼。。。。）

但是使用位段有一些注意事项和限制
- 不可跨平台移植，需要适配，因为编译器和硬件架构不同，同一段代码处理结果不一致
- 不能取地址 （&）
- 类型限制： 必须是整数类型
# 9. union联合体或者共用体

## 9.1 定义

union也是一种特殊的自定义类型。
这种类型定义的所有成员变量，**共用同一块空间，也就是说同一时间只有一个成员变量可以使用这块共用的空间。**

```c
#include <stdio.h>
#include <string.h>

union Stu
{
    int id;
    char name[20];
};

int main(void)
{
    union Stu stu = {};
    printf("%ld\n", sizeof(union Stu));
    stu.id = 20;
    printf("id = %d\n", stu.id);
    strcpy(stu.name, "kobe");
    printf("id = %d\n", stu.id);  // 这个就不是20了
    printf("name = %s\n", stu.name);
    return 0;
}
```

## 9.2 设计哲学
设计哲学： 为了内存复用 + 数据类型转换

##  9.3 sizeof

union的sizeof就是其中最大类型所占用的空间的最小倍数，同时需要满足整体都要装下，它和struct很像，也要考虑对齐和填充

```c
union Uk
{
    char arr[5];
    int i;
};
```

这个`sizeof(union Uk)`  就是8   ，为啥呢？
首先`arr`需要5个字节，同时i需要4个字节， 所以需要8个字节（要比5大，且是4的倍数）


# 10. 柔性数组

柔性数组 （flexible Array Member）是C99标准引入的一种特殊的数组结构。
它允许在一个结构体的最后一个成员定义成一个不指定长度的数组，目的是为了解决**变长数据结构**的内存分配问题，让结构体所需的内存能够一次性分配在连续的内存中，从而提升存储效率并减少内存碎片。

## 10.1 柔性数组的定义

柔性数组必须满足以下三个条件
- 它必须是结构体的最后一个成员
- 结构体中不能仅有柔性数组
- 它的定义中不能指定数组的长度（用空方括号`[]`）

```c
typedef struct {
    int count;        // 存储实际的数据数量
    double total;     // 其他固定长度的成员
    
    // 柔性数组：不指定长度，且必须是最后一个成员
    int data[];       
} FlexibleArrayStruct;
```

## 10.2 柔性数组的特点

|    特点    |                  描述                   |
| :------: | :-----------------------------------: |
|   尺寸为0   | 柔性数组本身不占用结构体的大小 sizeof()计算的时候，不包含柔性数组 |
|   内存连续   |  柔性数组所占用的实际空间在**运行时**紧接着结构体**连续分配的**  |
|   动态分配   |    必须使用`malloc`函数来为结构体和柔性数组一起分配内存     |
| 必须有其他的成员 |  结构体中只有需要一个**固定**大小的成员，柔性数组不能是唯一的成员   |
|          |                                       |
## 10.3 如何使用内存分配

由于柔性数组本身不占用空间，我们必须要计算出整个结构体所需的总内存，然后通过`malloc`一次性申请。

假设我们想分配一个包含N个`int`元素的柔性数组
1. 计算固定部分大小   $sizeof(struct)$
2. 计算变长部分大小  $sizeof(int) * N$
3. 计算总大小：  $sizeof(struct) + sizeof(int) * N$

```c
#include <stdio.h>
#include <stdlib.h>

#define N 5 // 我们需要的数组长度

int main() {
    // 1. 计算总的内存需求
    size_t total_size = sizeof(FlexibleArrayStruct) + N * sizeof(int);

    // 2. 一次性分配内存
    FlexibleArrayStruct *p = (FlexibleArrayStruct *)malloc(total_size);

    if (p == NULL) {
        perror("malloc failed");
        return 1;
    }

    // 3. 初始化固定成员
    p->count = N;

    // 4. 使用柔性数组（可以直接像普通数组一样使用）
    for (int i = 0; i < p->count; i++) {
        p->data[i] = i * 10;
        printf("data[%d] = %d\n", i, p->data[i]);
    }
    
    // 5. 释放内存
    free(p);
    return 0;
}
```

## 10.4 柔性数组的优势

1. **减少内存碎片**： 结构体和变长数组在一个连续的内存块中，只需要进行一次malloc和一次free
2. **避免多次分配**： 相对于在结构体中定义一个指针，还需要单独对这个指针进行分配内存，多了一次，提升了效率
3. **内存连续性**： 保证了数据访问的局部性，对CPU缓存友好

```c title=传统的指针成员方式
typedef struct {
    int count;
    int *data; // 传统方法：指针
} TraditionalStruct;

// 分配过程：两次 malloc
TraditionalStruct *ts = (TraditionalStruct *)malloc(sizeof(TraditionalStruct));
ts->data = (int *)malloc(N * sizeof(int));
// 释放过程：两次 free
free(ts->data);
free(ts);
```


