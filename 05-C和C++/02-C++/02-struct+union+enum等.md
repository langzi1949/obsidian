
# 1. struct

C++的struct是从C语言中的struct继承而来的，它极大的扩展了C语言中的`struct`的功能，使其本质上和C++的`class几乎相同

**C++的struct和C语言中struct的区别**
最大的区别在于C++的`struct`获得了面向对象的能力

| 特性        | C中的struct         | C++中的struct                    |
| --------- | ----------------- | ------------------------------ |
| 成员函数      | 不能包含成员函数，只能包含数据   | 可以包含成员函数                       |
| 构造函数、析构函数 | 没有。初始化必须手动完成      | 可以有构造函数和析构函数                   |
| 继承        | 不支持               | 支持继承，可以从其他的`struct`或者`class`继承 |
| 静态成员      | 不支持静态数据成员或者静态成员函数 | 支持静态成员（数据和函数都支持）               |

```cpp
#include <iostream>
using namespace std;
struct Stu
{
    char name[20];
    void sayHello()
    {
        cout << "Hello World! " << this->name << endl;
    }
};
int main(void)
{
    Stu stu;
    // 或者  Stu stu{"zmglove"};
    stu.sayHello();
    return 0;
}
```

> [!danger]
> 有一个问题，为啥`Stu stu;` 没有初始化，而能够调用sayHello()方法呢？
> 这个和Java不一样，Java如果`Stu stu;` 只是为**引用变量stu**在栈上分配空间，但是没有给这个变量指向任何内存空间， 但是C++中，已经在栈上为整个`stu`对象分配了所需的内存空间，此时`stu`已经是一个真实的对象了，它的内存已经分配完成了
> 

会发现，在C++中，在声明struct的变量的时候，可以省略`struct`关键字，直接`Stu stu;`
同样的，可以使用列表初始化的方式进行初始化struct
```cpp
Stu stu{"zmglove"};

// 结构数组
 Stu stus[2] = {
        {"kobe", "LosAngle"},
        {"Beckham", "London"}};
```

# 2. union

C++的union和C语言中的union几乎一样，除了C++的union包含成员函数，同时成员函数和成员变量不共用一块内存地址。
**核心概念**
- `union` 是一种特殊的数据结构，它允许在**同一块内存空间**中存储不同的数据类型的数据
- `union`的大小，由最大的成员的大小来决定
- 在任何时候，只有一个成员是活跃的，即存储了有效的数据
C++所做的拓展和限制
1. **成员限制**
在C++中，`union`对其可以拥有的成员类型有更加严格的限制。
2. **C++支持成员函数，包括普通的方法以及静态方法**
3. **拥有访问修饰符**， 默认是public
4. **C++的union支持静态成员**
5. **C++支持匿名union

```cpp
// C++ 匿名联合体示例
struct Data {
    int type;
    union { // 匿名 union
        int i;
        float f;
        char c;
    }; // 注意这里没有名称
};

Data d;
d.i = 10; // 可以直接访问联合体成员
```

# 3. enum

C++中的传统enum和C语言中的一脉相承，但是在C++引入了枚举类，目前是推荐使用的。

| 特性    | C/C++的传统enum                                        | C++枚举类                                                     |
| ----- | --------------------------------------------------- | ---------------------------------------------------------- |
| 作用域   | <span class="r">全局作用域</span>。枚举量会污染周围的作用域，容易引起命名冲突。 | **强作用域**， 枚举量被封装在`enum class`下，使用的时候必须用名称限定词，比如 Color::RED |
| 类型安全  | 弱类型，允许与整数之间进行隐式转换                                   | 强类型，不允许隐式转换，必须显式类型转换                                       |
| 底层类型  | 底层通过`int`存储，不可指定                                    | 可以**显式指定**底层类型                                             |
| 跨枚举比较 | 允许不同枚举类型的值进行比较                                      | **不允许**比较来自不同枚举类的值，除非使用显式类型转换                              |

```cpp title=传统的enum的缺陷
enum Color { Red, Green, Blue };
enum State { Running, Stopped, Red }; // ❌ 命名冲突！Red 被定义了两次
int x = Red; // ✅ 隐式转换为 int
```

```cpp title=C++的枚举类
enum class Color { Red, Green, Blue };
enum class State { Running, Stopped, Red }; // ✅ 不冲突！需要用 Color::Red 或 State::Red 访问

int x = static_cast<int>(Color::Red); // ✅ 必须显式转换
// int x = Color::Red;                 // ❌ 不允许隐式转换
```




