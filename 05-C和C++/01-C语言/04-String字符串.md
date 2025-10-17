
# 1. 如何表达字符串

和Java不同，C语言中没有专门的String类型，它只能通过一些模拟的方式来得到字符串

## 1.1 通过char[]数组方式

```c
#include <stdio.h>

int main() {
    // declaring and initializing a string
    char str[] = "Geeks";
    printf("The string is: %s\n", str);
    return 0;
}
```

推荐使用上面的方式，不能使用下面的方式
```c
char str[] = {'a','b','c'}; ❌ ❌ ❌
char str[] = {'a','b','c', '\0'}  // 这个虽然正确，但是没人这么用 
```

为啥不对呢？

因为真正的字符串，最终在底层的数据是
![[Pasted image 20251003121536.png]]
如果是`char str[] = {'a','b','c'};`这样对str进行处理的时候从起始位置开始，持续扫描，知道遇到`\0`才会停止，所以这种方式最终会出现这个诡异的数据或者乱码，导致程序崩溃。

# 2. 访问字符和修改字符

只有这种`char str[]`的方式才可以修改，如果是`char *str` 使用来创建字符串，那么中间的字符都不可以修改，跟Java是一样的； 所以此处只针对`char str[]`

```c
char str[] = "Hello";
char c = str[2];
str[4] = 'K';
```

# 3. 字符串长度

```c
#include <string.h>
char str[] = "Hello";
int size = strlen(str); // 5

char str[] = "中国";
int size = strlen(str) // 6  这个是字节的长度❗️❗️❗️❗️
```

如果需要utf-8字符串的长度，可以使用下面的代码
```c
size_t utf8_strlen(const char *str)
{
    size_t len = 0;

    while (*str)
    {
        unsigned char c = (unsigned char)*str;

        if ((c & 0x80) == 0)
        {
            // ASCII 字符 (0xxxxxxx)
            str += 1;
        }
        else if ((c & 0xE0) == 0xC0)
        {
            // 2字节字符 (110xxxxx)
            str += 2;
        }
        else if ((c & 0xF0) == 0xE0)
        {
            // 3字节字符 (1110xxxx) - 大部分中文
            str += 3;
        }
        else if ((c & 0xF8) == 0xF0)
        {
            // 4字节字符 (11110xxx) - emoji等
            str += 4;
        }
        else
        {
            // 无效的 UTF-8 序列
            str += 1;
        }

        len++;
    }
    return len;
}
```

# 4. 常见的字符串方法

## strcpy()

```c
#include <stdio.h>
#include <string.h>

int main() {
    char src[] = "Hello";
    char dest[20];
    // Copies "Hello" to dest
    strcpy(dest, src);  
    printf("%s", dest);
    return 0;
}
```

> [!danger]
> `strcpy()`函数有一个根本的缺陷：
> 	 <span class='r'>它不知道目标缓冲区有多大，如果src比dest大，会导致缓冲区溢出，这个是C程序最严重的安全漏洞</span>

所以，为了解决`strcpy()`函数的安全性，可以使用`strncpy()`函数来替代
标准的`strncpy()`函数允许你指定拷贝的最大字符数。
- **问题**:  `strncpy()`的一个<span class='blue'>臭名昭著的缺陷：如果src的长度正好等于指定的最大值，它将不自动添加`\0`终止符</span>
- **安全的用法** ：需要手动添加`\0`

```c
#include <string.h>

char dest[20];
const char *src = "SourceString";
size_t dest_size = sizeof(dest);

// 1. 拷贝 N-1 个字符（为 \0 预留空间）
strncpy(dest, src, dest_size - 1); 

// 2. 强制在末尾添加 \0，保证字符串正确终止
dest[dest_size - 1] = '\0';
```

##  strncpy()

上面的例子已经说完了，它的作用就是不会导致缓冲区溢出

## strcat()

两个字符串进行拼接，将src添加到dest的尾部
```c
char *strcat(char *dest, const char *src);
```

注意，要防止缓冲区溢出。

##  strncat()

同样，这个方法也是进行拼接字符串的，但是要指定拼接的大小

## strcmp()

比较两个字符串是否相同

```c
#include <stdio.h>
#include <string.h>

int main() {
    char s1[] = "Apple";
    char s2[] = "Applet";
    
  	// Compare two strings 
  	// and print result
    int res = strcmp(s1, s2);
    if (res == 0) 
        printf("s1 and s2 are same");
  	else if (res < 0)
      	printf("s1 is lexicographically " 
      	        "smaller than s2");
  	else
      	printf("s1 is lexicographically " 
      	       "greater than s2");
    return 0;
}
```

# 5. C的api在线文档

[https://cppreference.com/](https://cppreference.com/)

# 6. 使用指针来表达字符串

```c
char *str = "hello";
```

这个底层同样是`char 数组`，以`\0`结尾，同时这一块内存区域是只读的，我们不能修改这个里面的字符。
str指针一开始是指向第一个字符。

```c
#include <stdio.h>

int main()
{
    char* ptr = "Geeks";

    // While loop will run till 
    // the character value is not
    // equal to null character
    while (*ptr != '\0') {
        printf("%c", *ptr);
        ptr++;
    }
    return 0;
}
```

# 7. 🤡 🤡一段很诡异的代码

```c
#include <stdio.h>
int main()
{
    char str[] = "GeeksQuiz";
    printf("%s %s %s ", &str[5], &5 [str], str + 5);
    printf("%c %c %c ", *(str + 6), str[6], 6 [str]);
    return 0;
}

```


``` title=output
Quiz Quiz Quiz u u u
```

为啥呢？那是因为`数组索引 a[i] 总是等价于指针算术 ∗(a+i)。`，而根据加法的交换律，`*(a + i) 也等价于 *(i + a)`
所以  `&5 [str]` = `&str[5]` = `str + 5`

