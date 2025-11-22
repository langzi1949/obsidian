
# 1. string

## 1.1 构造函数

| 构造函数                                                           | 描述                                                                           |
| -------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| `string(const char *s)`                                        | 将string对象初始化为s指向的C风格字符串                                                      |
| `string(size_type n, char c)`                                  | 创建一个包含n个元素的string对象，每个元素都是c                                                  |
| `string(const string &s)`                                      | 复制构造函数                                                                       |
| `string()`                                                     | 创建一个默认的string对象，长度为0                                                         |
| `string(const char *s, size_type n)`                           | 将string对象初始化为s指向的前n个字符，即使超过了字符串结尾，会一直找到`\0`的位置，所以很危险                         |
| `template <typename T>`<br>`string(T begin, T end)`            | T其实就是指针，将string对象初始化为区间`[begin,end)`内的字符串<br>其中begin和end的行为更像是指针，用于指定位置，左闭右开 |
| `string(const string &s, size_type pos=0, size_type n = npos)` | 其实有点类似subStr的意思，从pos开始，然后到n个字符，如果超过了str的尾部，就到结尾结束                            |
| `string(&& str) noexcept`                                      | 这个是C++11新增的，它将一个string对象初始化为string对象str，并可能修改str。这个就是C++中的移动构造函数             |
| `string(initializer_list<char> il)`                            | C++11新增的，它将一个string对象初始化为初始化列表il中的字符                                         |

```cpp
int main(void)
{

    string one("Lottery Winner");  // 1️⃣ 将传统的C-风格字符串初始化为string
    cout << one << endl;

    string two(20, '$');   // 2️⃣   初始化20个$字符组成的字符串
    cout << two << endl;
    string three(one);    // 3️⃣  复制构造函数
    cout << three << endl;

    one += " Oops!";
    cout << one << endl;

    two = "Sorry! That was ";
    three[0] = 'P';
    cout << "当前one = " << one << endl;
    cout << "当前three = " << three << endl;

    string four;
    four = two + three;
    cout << four << endl;

    char alls[] = "All's well that ends well";
    string five(alls, 20);   // 4️⃣ 截取前20个字符串 ⚠️⚠️⚠️ 但是比较危险，如果20大于字符串长度，会截取结尾后的字符的
    cout << five << "!\n";

    string six(alls + 6, alls + 10);  // 5️⃣ 指向的两个指针，然后取substr的感觉
    cout << six << ", ----";
 
    string seven(&five[6], &five[10]);  // 6️⃣  其实就是上面第5个的另外一种写法
    cout << seven << "...\n";

    cout << "当前four = " << four << endl;
    string eight(four, 7, 16);       // 7️⃣  从第7 index开始，取16个字符，直到结尾
    cout << eight << " in motion!" << endl;

	string nine = {'H','E','L','L','O'};  // 8️⃣ 使用的是构造函数 string(initialize_list<char> il)
    return 0;
}
```

## 1.2 string的移动构造函数

```cpp
string(&& str) noexcept;
```

- `&&str` 这个是右值引用参数，接收一个右值（如临时对象或者即将被销毁的对象）作为源字符串，这个会触发移动语义
- `noexcept` 无异常声明，表明这个函数不会抛出异常。

### 1.2.1 移动构造函数的逻辑

使用一个右值（通常是临时对象）来初始化一个新的`std::string`对象时，编译器会调用这个移动构造函数。

它的核心逻辑：<span class="r" style="font-weight:bold">不进行深度拷贝，只进行浅拷贝和资源接管</span>
1. **浅拷贝/资源阶段**：将源字符串`str`内部管理的堆内存指针、大小和容量等资源新，直接复制给新的对象
2. **"窃取"资源**： 将源字符串`str`的内部指针指向空


### 1.2.2 如何使用string的移动构造函数

在C++中通常不需要我们显式的调用移动构造函数，编译器在遇到右值作为源对象的时候，自动优化并选择调用它。

1. 返回值优化

这个是最常见和自然的方式，当一个函数返回大型对象时，编译器会优化拷贝逻辑，转而使用移动构造函数。

```cpp
#include <iostream>
#include <string>

std::string create_large_string() {
    std::string temp = "Very large string data..."; // 假设这是一个非常大的字符串
    // ... 填充数据
    return temp; // 返回 temp 时，编译器自动选择移动构造函数
}

int main() {
    // 这里的 s 通过移动构造函数（或 RVO）从 temp 接管资源
    std::string s = create_large_string(); 
    // s 获得了 temp 的内部资源，避免了对大量字符串数据的拷贝
    std::cout << "String size: " << s.size() << std::endl;
    return 0;
}
```

2. 使用`std::move`强制触发移动语义

当你使用一个具名的左值对象的时候，但你直到本次操作后不在需要这个对象，可以使用`std::move`强制转为右值，从而触发移动构造函数

```cpp
#include <iostream>
#include <string>
#include <utility> // 包含 std::move

void process_and_transfer(std::string new_str) {
    // ...
}

int main() {
    std::string old_str = "Data to move";

    // 使用 std::move 将 old_str 转换为右值，触发移动构造函数
    // new_str 获得了 old_str 的内部资源
    std::string new_str = std::move(old_str); 
    
    std::cout << "New string: " << new_str << std::endl;
    // std::cout << "Old string: " << old_str << std::endl; // ⚠ 不安全，old_str 内容可能已清空

    // 再次使用移动语义作为函数参数
    // 避免了将 new_str 的内容拷贝一份给函数参数
    process_and_transfer(std::move(new_str)); 

    return 0;
}
```

## 1.3 find方法

```cpp
// 从pos位置开始查找，查找子串是否存在str，如果找到，返回首次出现的index，否则，返回string::npos
size_type find(const string &str, size_type pos = 0) const;

// 从pos位置开始找，查找str的前n个字符组成的子字符串。
size_type find(const char *str, size_type pos = 0, size_type n);
// 查找 target_data 的前 3 个字符 ("CDE")，从 main_str 的索引 0 开始
std::string::size_type pos = main_str.find(target_data, 0, 3);
```

在C++中find相关的方法还有`rfind()`、`find_first_of()`、`find_last_of()`等方法。

## 1.4 其他的方法


```cpp
std::string str = "Hello";

// 下面两个都是获取str的长度的，但是是字节长度
size_t size = str.size();
size_t len = str.length();

// 获取str的容量
int capacity = str.capacity();

// 扩容
str.reserve();

#include <string>

// 创建和初始化
std::string s1;                    // 空字符串
std::string s2 = "Hello";          // 初始化
std::string s3("World");           // 构造函数
std::string s4(5, 'a');            // "aaaaa"

// 常用方法
s1.length() / s1.size();           // 获取长度
s1.empty();                        // 是否为空
s1.clear();                        // 清空

// 访问元素
s2[0];                             // 下标访问
s2.at(0);                          // 带边界检查的访问
s2.front();                        // 第一个字符
s2.back();                         // 最后一个字符

// 修改操作
s1 += s2;                          // 连接
s1.append(s2);                     // 追加
s1.insert(0, "Hi ");               // 插入
s1.erase(0, 3);                    // 删除
s1.replace(0, 2, "XX");            // 替换

// 查找
s1.find("llo");                    // 查找子串位置
s1.rfind("l");                     // 反向查找
s1.substr(0, 5);                   // 提取子串

// 比较
s1 == s2;
s1.compare(s2);

```


## 1.5 智能指针的使用

在C++中，有时候普通的指针会引来内存泄露的问题，因为人可能会犯错而忘记了delete指针

1. 包含头文件`<memory>`
2. 将指向`string`的指针替换为指向`string`的智能指针对象
3. 删除`delete`语句

```cpp
std::string kobe = "Jiangsu";
// 此处就是使用智能指针
std::shared_ptr<std::string> ps(new std::string(kobe));
cout << *ps << endl;
```


# 2. vector

```cpp
#include <vector>

std::vector<int> vec;              // 空vector
std::vector<int> vec2(10);         // 10个元素,默认值0
std::vector<int> vec3(10, 5);      // 10个元素,值都是5

// 添加元素
vec.push_back(1);                  // 尾部添加
vec.emplace_back(2);               // 原地构造(更高效)
vec.insert(vec.begin(), 0);        // 指定位置插入

// 删除元素
vec.pop_back();                    // 删除最后一个
vec.erase(vec.begin());            // 删除指定位置
vec.clear();                       // 清空

// 访问元素
vec[0];                            // 下标访问
vec.at(0);                         // 带检查的访问
vec.front();                       // 第一个元素
vec.back();                        // 最后一个元素

// 容量操作
vec.size();                        // 元素个数
vec.capacity();                    // 容量
vec.reserve(100);                  // 预留空间
vec.resize(50);                    // 改变大小
```

# 3. deque

deque是双向队列
```cpp
#include <deque>

std::deque<int> dq;
dq.push_back(1);                   // 尾部添加
dq.push_front(0);                  // 头部添加
dq.pop_back();                     // 尾部删除
dq.pop_front();                    // 头部删除
```

# 4. list

C++中的list是双向链表

```cpp
#include <list>

std::list<int> lst;
lst.push_back(1);
lst.push_front(0);
lst.insert(lst.begin(), 5);
lst.remove(1);                     // 删除值为1的所有元素
lst.sort();                        // 排序
lst.reverse();                     // 反转
```


# 5. set

```cpp
#include <set>

std::set<int> s;
s.insert(1);                       // 插入
s.erase(1);                        // 删除
s.find(1);                         // 查找,返回迭代器
s.count(1);                        // 计数(0或1)
s.size();                          // 大小


# 可以使用unsorted_set 这个采用的是hash表的方式
```

# 6. map

```cpp
#include <map>

std::map<std::string, int> m;
m["apple"] = 1;                    // 插入/修改
m.insert({"banana", 2});           // 插入
m.erase("apple");                  // 删除
m.find("apple");                   // 查找
m.count("apple");                  // 是否存在
m.size();                          // 大小

// 遍历
for (auto& pair : m) {
    std::cout << pair.first << ": " << pair.second << "\n";
}
```

# 7. stack

```cpp
#include <stack>

std::stack<int> stk;
stk.push(1);                       // 入栈
stk.pop();                         // 出栈，不返回值
stk.top();                         // 栈顶元素
stk.empty();                       // 是否为空
stk.size();                        // 大小
```

# 8. queue

```cpp
#include <queue>

std::queue<int> q;
q.push(1);                         // 入队
q.pop();                           // 出队
q.front();                         // 队首
q.back();                          // 队尾
```

# 9. priority_queue

```cpp
#include <queue>

std::priority_queue<int> pq;      // 大顶堆
pq.push(1);
pq.top();                          // 堆顶元素
pq.pop();                          // 删除堆顶

// 小顶堆
std::priority_queue<int, std::vector<int>, std::greater<int>> min_pq;
```

# 10. 迭代器

```cpp
std::vector<int> vec = {1, 2, 3, 4, 5};

// 传统for循环
for (auto it = vec.begin(); it != vec.end(); ++it) {
    std::cout << *it << " ";
}

// 范围for循环(C++11)
for (int x : vec) {
    std::cout << x << " ";
}

// 引用方式(可修改)
for (int& x : vec) {
    x *= 2;
}

// const引用(只读,高效)
for (const auto& x : vec) {
    std::cout << x << " ";
}
```

# 11. 常见的算法

```cpp
#include <algorithm>

std::vector<int> vec = {3, 1, 4, 1, 5};

std::sort(vec.begin(), vec.end());              // 排序
std::reverse(vec.begin(), vec.end());           // 反转
std::find(vec.begin(), vec.end(), 4);           // 查找
std::count(vec.begin(), vec.end(), 1);          // 计数
std::max_element(vec.begin(), vec.end());       // 最大值
std::min_element(vec.begin(), vec.end());       // 最小值
std::accumulate(vec.begin(), vec.end(), 0);     // 求和(需要#include <numeric>)
```
