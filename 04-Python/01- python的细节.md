
# 1. Package和Module

一般来说`Package`就是包含`__int__.py`文件的一个目录/文件夹。 其实在`Python 3.3+`可以不必包含`__int__.py`文件，但是开发约定里面建议加上。

> [!danger]
> 没有`__int__.py`的Package，python是如何实现包呢？
> 
> 引入了一个『命名空间包』的概念，没有`__int__.py` 文件会被自动识别为`命名空间包`。即使分散在不同的目录下，最后会被组合到同一个命名空间包下。

那么在这个包里面的其他文件其实都是Module，它只是文件的概念化而已。

# 2. 如何导入模块呢

## 2.1 绝对导入

这个是最推荐的，它是用项目的根目录进行寻找，这个很稳健，不太容易出错

```python
# 导入特定的类或函数
from package_a.module_a import A_Class, a_function

# 或者导入整个模块
from package_a import module_a

def b_task():
    obj = A_Class()
    module_a.a_function()
```

## 2.2 相对导入

```python
from ..package_a.module_a import A_Class
```

不太推荐，因为这个很容易出错，而且一旦用了相对导入，只能在被当作『包』运行时有效，如果你直接右键运行 `module_b.py`，Python 会报错 `ImportError: attempted relative import with no known parent package`。

![[Pasted image 20260421112720.png]]

# 3.  ModuleNotFoundError如何解决

![[Pasted image 20260421113013.png]]
## 3.1 为什么会出现ModuleNotFoundError

这个是我们之前遇到的一个问题，是因为进入到当前的python文件下，然后执行的脚本命令，最后导致了模块找不到。

**核心原理**： 当执行一个python文件的时候，python会自动将==**当前运行脚本所在的目录**==加入到`sys.path`中。

❌❌❌ 错误的做法：我们进入到`module_b.py`所在目录执行`python module_b.py`，最后会把`module_b.py`所在的目录加入到`sys.path`中，所以它找不到`module_a`所在的目录。

✅✅✅ 正确的做法：始终在根目录下去执行。??????

# 4. `__init__`的进阶

有时候，我们不想每次都写module的名称，可以在`__init__.py`中进行导出

```python
# 在 package_a/__init__.py 中写：
from .module_a import A_Class
```

然后我们使用的使用，就无需`from package_a.module_a import A_Class`，而是`from package_a import A_Class`

```python
from package_a import A_Class  # 不需要写到 .module_a 了
```

# 5. `__all__`的作用

我们知道python没有强制的private的逻辑，更多的是一个开发规范的约束而已，你自己强行的调用一个别人的私有方法也是可以的。
但是，在包或者模块导入的时候，我们不希望模块中的临时变量或者一些私有方法被IDE或者调用者看到，至少不用智能提示，可以使用`__all__`标明哪些成员是可以被别人使用的。

```python
# 是在__init__.py文件中
## ⚠️⚠️⚠️⚠️ from my_module import *   这个要慎用，因为可能会污染当前的命名空间

__all__ = ["public_func"]

def public_func():
    print("我是公开的")

def internal_func():
    print("我是隐藏的")
```

# 6. 深入探讨一下加载模块逻辑

## 6.1 首先，import的逻辑是和C语言中的`#include` 是相同的吗？

python的import和c语言中的`#include`这种简单文本替换的逻辑是不同的，它不是copy代码到当前的python文件中。

1. **创建一个新的命名空间**  ： 为模块单独开辟一个内存空间
2. **执行模块中代码**  ： 会在这个内存空间中，执行模块中的所有代码，从上至下执行，所以，`import`这一行代码会导致模块中的代码全部被执行一遍
3. **创建索引**  ： 在当前文件的命名空间里，创建一个名为 `module_a` 的变量，指向那个内存区域。


> [!note]
> 一定要记住，`import`的时候，会实实在在的执行内部的全部代码的
## 6.2 python如何查找包路径的

python的包查找都是从`sys.path`列表中进行查询的
- 当前python文件所在的目录
- 项目的根目录
- `PYTHONPATH`  :  这个你可以手动设置这个环境变量来增加搜索路径。  实际使用很少人用。
- python自带的包或者模块
- 第三方的包，在`site-packages`中去查找

## 6.3 包重名、函数和引入的模块的重名，怎么办

包重名，有个先来后到，比如我们引入了一个包A，再安装同名包A的话，会直接替换掉
函数重名，是后面的一个覆盖前面的一个。

## 6.4 sys.modules缓存机制

python对于已经加载过的module不会再重复的进行加载，在加载后，会在`sys.modules`中记录一下缓存信息，等以后再用到后的时候，会直接优先看`sys.modules`是否存在，没有的话，才会继续加载执行模块

> [!note]
> 补充一下单例！
> 因为有`sys.modules`的存在，所以，python的module在同一个进程下是单例的，都是指向同一个命名空间的内存空间

##  6.5 为啥有的是直接import，有的需要from package？

首先，我们要明确一下，不管`import`还是`from package` 都是进行路径匹配，不可能去深入每个python文件中的。
import一般是从全部的包中进行找，找到就停止。
一般python内置的或者一些很简单的会直接import，其余都是通过`from package`，在第三方包的设计中，如果`__init__.py`的文件是空的，那么它下面的子模块不会被自动加载，需要手动进行导入子模块

```python
# 以下两种都可以导入子模块
from package import sub_module
import package.sub_module
```



