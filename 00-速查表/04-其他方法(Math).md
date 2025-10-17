### 1. Java
Java 的 `java.lang.Math` 是静态类，提供基础数学运算，广泛用于后端、Android 开发。

**高频方法**:
- `Math.abs(double x)`: 绝对值，处理负数场景（如距离计算）。
- `Math.max(double a, double b)` / `Math.min`: 比较大小，常用于边界检查。
- `Math.random()`: 返回 [0.0, 1.0) 的随机 double，游戏/UI 随机化。
- `Math.round(double x)`: 四舍五入到 long，数据格式化。
- `Math.pow(double base, double exp)`: 幂运算，几何/金融计算。
- `Math.sqrt(double x)`: 平方根，几何/物理计算。
- `Math.floor(double x)` / `Math.ceil`: 向下/向上取整，分页/布局。

**代码示例**:
```java
public class Main {
    public static void main(String[] args) {
        double r = 5.6;
        System.out.println("Abs: " + Math.abs(-r)); // 5.6
        System.out.println("Round: " + Math.round(r)); // 6
        System.out.println("Area: " + Math.PI * Math.pow(r, 2)); // 98.5203456165759
        System.out.println("Random: " + Math.random() * 100); // 0-100 随机
        System.out.println("Max: " + Math.max(10, 20)); // 20
    }
}
```

**用途**：Java 的 Math 简单可靠，`random()` 常用于简单随机化，但在高并发需用 `ThreadLocalRandom`。

---

### 2. Go
Go 的 `math` 包提供基础数学函数，`math/rand` 用于随机数，适合服务器/微服务开发。

> [!danger]
> 在`Go 1.21+`版本，内置了`min()`和`max()`方法，不要用Math中方法，Math中的max方法接收的是float64

**高频方法**:
- `math.Abs(float64)`: 绝对值，处理浮点数。
- `math.Max(float64, float64)` / `math.Min`: 比较大小。
- `math.Pow(base, exp float64)`: 幂运算。
- `math.Sqrt(float64)`: 平方根。
- `math.Floor(float64)` / `math.Ceil`: 向下/向上取整。
- `math/rand.Float64()`: [0.0, 1.0) 随机数，需设置种子。
- `math.Mod(float64, float64)`: 取模，处理周期性计算。

**代码示例**:
```go
package main

import (
    "fmt"
    "math"
    "math/rand"
    "time"
)

func main() {
    rand.Seed(time.Now().UnixNano())
    r := 5.6
    fmt.Println("Abs:", math.Abs(-r))          // 5.6
    fmt.Println("Round:", math.Round(r))       // 6
    fmt.Println("Area:", math.Pi*math.Pow(r, 2)) // 98.5203456165759
    fmt.Println("Random:", rand.Float64()*100) // 0-100 随机
    fmt.Println("Max:", math.Max(10, 20))      // 20
}
```

**用途**：Go 的 `math` 性能高，`rand` 需手动种子管理，适合并发后端。

---

### 3. JavaScript
JavaScript 的 `Math` 对象内置于全局，广泛用于 Web 前端、Node.js。

**高频方法**:
- `Math.abs(number)`: 绝对值，处理用户输入。
- `Math.max(...numbers)` / `Math.min`: 比较多值，动态布局。
- `Math.random()`: [0.0, 1.0) 随机数，动画/游戏。
- `Math.round(number)` / `Math.floor` / `Math.ceil`: 取整，UI 像素对齐。
- `Math.pow(base, exp)`: 幂运算，动画曲线。
- `Math.sqrt(number)`: 平方根，Canvas 图形。
- `Math.trunc(number)`: 截断小数，数据处理。

**代码示例**:
```javascript
let r = 5.6;
console.log("Abs:", Math.abs(-r));          // 5.6
console.log("Round:", Math.round(r));       // 6
console.log("Area:", Math.PI * Math.pow(r, 2)); // 98.5203456165759
console.log("Random:", Math.random() * 100); // 0-100 随机
console.log("Max:", Math.max(10, 20, 30));  // 30
```

**用途**：`Math.random()` 是前端随机化的核心，`max/min` 支持多参数，适合动态计算。

---

### 4. PHP
PHP 的数学函数是内置函数（无 `Math` 类），用于 Web 后端、数据处理。

**高频方法**:
- `abs(mixed)`: 绝对值，支持 int/float。
- `max(mixed...)` / `min`: 比较多值，数组/单独值。
- `rand(int min, int max)`: 整数随机数，简单随机化。
- `round(float, precision)`: 四舍五入，可指定精度。
- `pow(base, exp)`: 幂运算。
- `sqrt(float)`: 平方根。
- `floor(float)` / `ceil`: 向下/向上取整。

**代码示例**:
```php
<?php
$r = 5.6;
echo "Abs: " . abs(-$r) . "\n";           // 5.6
echo "Round: " . round($r) . "\n";        // 6
echo "Area: " . M_PI * pow($r, 2) . "\n"; // 98.5203456165759
echo "Random: " . rand(0, 100) . "\n";    // 0-100 随机
echo "Max: " . max(10, 20, 30) . "\n";    // 30
?>
```

**用途**：PHP 的数学函数简单，`rand()` 常用于简单 Web 随机化，`round` 适合货币计算。

---

### 5. Python 3
Python 的 `math` 模块和内置函数提供数学支持，`random` 模块用于随机数，广泛用于数据科学、脚本。

**高频方法**:
- `abs(x)`: 内置绝对值，整数/浮点数。
- `max(iterable, *args)` / `min`: 比较多值或序列。
- `random.random()`: [0.0, 1.0) 随机数（`random` 模块）。
- `math.round(x)` / `math.floor` / `math.ceil`: 取整，数据处理。
- `math.pow(x, y)`: 幂运算（返回 float）。
- `math.sqrt(x)`: 平方根，科学计算。
- `random.choice(sequence)`: 随机选元素，游戏/测试。

**代码示例**:
```python
import math
import random

r = 5.6
print("Abs:", abs(-r))           # 5.6
print("Round:", round(r))        # 6
print("Area:", math.pi * r ** 2) # 98.5203456165759
print("Random:", random.random() * 100) # 0-100 随机
print("Max:", max(10, 20, 30))   # 30
```

**用途**：Python 的 `math` 和 `random` 易用，`numpy` 常替代 `math` 用于高性能数组计算。

---

### 6. Groovy
Groovy 是 JVM 语言，基于 Java 的 `Math`，但语法更简洁，适合脚本和 Jenkins。

**高频方法**:
- 同 Java 的 `Math.abs`, `Math.max`, `Math.min`, `Math.random`, `Math.round`, `Math.pow`, `Math.sqrt`。
- 额外：Groovy 支持运算符重载，`r**2` 替代 `Math.pow`。

**代码示例**:
```groovy
def r = 5.6
println "Abs: ${Math.abs(-r)}"          // 5.6
println "Round: ${Math.round(r)}"       // 6
println "Area: ${Math.PI * (r**2)}"     // 98.5203456165759
println "Random: ${Math.random() * 100}" // 0-100 随机
println "Max: ${Math.max(10, 20)}"      // 20
```

**用途**：与 Java 一致，但 `**` 和字符串插值使代码更简洁，适合快速脚本。

---

### 7. Kotlin
Kotlin 的 `kotlin.math` 提供数学函数，基于 JVM，适合 Android 和后端。

**高频方法**:
- `kotlin.math.abs(x)`: 绝对值，Int/Double。
- `maxOf(a, b, ...)` / `minOf`: 比较多值。
- `kotlin.math.random()`: [0.0, 1.0) 随机数（需 `Random` 对象）。
- `kotlin.math.round(x)` / `floor` / `ceil`: 取整。
- `kotlin.math.pow(x, y)`: 幂运算（或 `x.pow(y)`）。
- `kotlin.math.sqrt(x)`: 平方根。

**代码示例**:
```kotlin
import kotlin.math.*
import kotlin.random.Random

fun main() {
    val r = 5.6
    println("Abs: ${abs(-r)}")           // 5.6
    println("Round: ${round(r)}")        // 6.0
    println("Area: ${PI * r.pow(2)}")    // 98.5203456165759
    println("Random: ${Random.nextDouble() * 100}") // 0-100 随机
    println("Max: ${maxOf(10, 20, 30)}") // 30
}
```

**用途**：Kotlin 的 `pow` 扩展函数和 `maxOf` 更现代化，Android 开发常用。

---

### 8. C
C 的 `<math.h>` 提供基础数学函数，`<stdlib.h>` 提供随机数，适合嵌入式/系统编程。

**高频方法**:
- `fabs(double)`: 绝对值（浮点数）。
- `fmax(double, double)` / `fmin`: 比较大小。
- `rand()`: 0 到 RAND_MAX 随机整数，需种子。
- `round(double)` / `floor` / `ceil`: 取整。
- `pow(double, double)`: 幂运算。
- `sqrt(double)`: 平方根。

**代码示例**:
```c
#include <stdio.h>
#include <math.h>
#include <stdlib.h>
#include <time.h>

int main() {
    srand(time(NULL));
    double r = 5.6;
    printf("Abs: %.1f\n", fabs(-r));          // 5.6
    printf("Round: %.0f\n", round(r));        // 6
    printf("Area: %.4f\n", M_PI * pow(r, 2)); // 98.5203
    printf("Random: %.2f\n", (double)rand() / RAND_MAX * 100); // 0-100 随机
    printf("Max: %.0f\n", fmax(10, 20));      // 20
}
```

**用途**：C 的数学函数高效但类型严格，`rand()` 简单但伪随机性较弱。

---

### 9. C++
C++ 继承 C 的 `<cmath>`，添加 STL 算法，适合高性能计算、游戏开发。

**高频方法**:
- `std::abs(double)` / `std::fabs`: 绝对值。
- `std::max(T, T)` / `std::min`: 比较（支持多类型）。
- `std::rand()`: 随机整数（C 风格，现代用 `<random>`）。
- `std::round` / `std::floor` / `std::ceil`: 取整。
- `std::pow(base, exp)`: 幂运算。
- `std::sqrt(double)`: 平方根。
- `std::mt19937` (from `<random>`): 现代随机数生成。

**代码示例**:
```cpp
#include <iostream>
#include <cmath>
#include <random>

int main() {
    std::mt19937 rng(std::random_device{}());
    std::uniform_real_distribution<double> dist(0, 100);
    double r = 5.6;
    std::cout << "Abs: " << std::abs(-r) << std::endl;          // 5.6
    std::cout << "Round: " << std::round(r) << std::endl;       // 6
    std::cout << "Area: " << M_PI * std::pow(r, 2) << std::endl; // 98.5203
    std::cout << "Random: " << dist(rng) << std::endl;          // 0-100 随机
    std::cout << "Max: " << std::max(10, 20) << std::endl;     // 20
}
```

**用途**：C++ 的 `<random>` 提供高质量随机数，`std::max` 泛型化，适合游戏引擎。

---

### 10. Dart
Dart 的 `dart:math` 提供数学支持，适合 Flutter 开发。

**高频方法**:
- `abs()`: 绝对值（内置方法）。
- `max(a, b)` / `min`: 比较大小。
- `Random().nextDouble()`: [0.0, 1.0) 随机数。
- `round()` / `floor` / `ceil`: 取整（内置方法）。
- `pow(x, y)`: 幂运算。
- `sqrt(x)`: 平方根。

**代码示例**:
```dart
import 'dart:math';

void main() {
  var rng = Random();
  var r = 5.6;
  print("Abs: ${(-r).abs()}");         // 5.6
  print("Round: ${r.round()}");        // 6
  print("Area: ${pi * pow(r, 2)}");    // 98.5203456165759
  print("Random: ${rng.nextDouble() * 100}"); // 0-100 随机
  print("Max: ${max(10, 20)}");        // 20
}
```

**用途**：Dart 的数学函数简洁，`Random` 易用，Flutter UI 计算常用。

---

### 11. Scala
Scala 的 `scala.math` 和 Java 的 `Math` 类似，结合 FP 特性，适合 JVM 后端。

**高频方法**:
- `scala.math.abs(x)`: 绝对值。
- `scala.math.max(a, b)` / `min`: 比较大小。
- `scala.util.Random.nextDouble()`: [0.0, 1.0) 随机数。
- `scala.math.round(x)` / `floor` / `ceil`: 取整。
- `scala.math.pow(x, y)`: 幂运算。
- `scala.math.sqrt(x)`: 平方根。

**代码示例**:
```scala
import scala.math._
import scala.util.Random

object Main extends App {
  val r = 5.6
  println(s"Abs: ${abs(-r)}")           // 5.6
  println(s"Round: ${round(r)}")        // 6
  println(s"Area: ${Pi * pow(r, 2)}")   // 98.5203456165759
  println(s"Random: ${Random.nextDouble() * 100}") // 0-100 随机
  println(s"Max: ${max(10, 20)}")       // 20
}
```

**用途**：Scala 的数学函数与 Java 一致，FP 风格使代码简洁，适合大数据处理。

---

### 总结与比较
| 语言         | 核心模块                  | 随机数方法                 | 取整方法                              | 独特特点               |
| ---------- | --------------------- | --------------------- | --------------------------------- | ------------------ |
| Java       | `java.lang.Math`      | `Math.random()`       | `round`, `floor`, `ceil`          | 静态类，线程安全           |
| Go         | `math`, `math/rand`   | `rand.Float64()`      | `Floor`, `Ceil`, `Round`          | 需手动种子，性能高          |
| JavaScript | `Math`                | `Math.random()`       | `round`, `floor`, `ceil`, `trunc` | 支持多参数 `max/min`    |
| PHP        | 内置函数                  | `rand(min, max)`      | `round`, `floor`, `ceil`          | 简单但精度控制弱           |
| Python     | `math`, `random`      | `random.random()`     | `round`, `math.floor`, `ceil`     | 生态丰富，`numpy` 更常用   |
| Groovy     | `Math` (Java)         | `Math.random()`       | `round`, `floor`, `ceil`          | `**` 运算符简化幂运算      |
| Kotlin     | `kotlin.math`         | `Random.nextDouble()` | `round`, `floor`, `ceil`          | 扩展函数 `pow` 更现代     |
| C          | `<math.h>`            | `rand()`              | `round`, `floor`, `ceil`          | 高效但类型严格            |
| C++        | `<cmath>`, `<random>` | `mt19937`, `rand()`   | `round`, `floor`, `ceil`          | `<random>` 提供高质量随机 |
| Dart       | `dart:math`           | `Random.nextDouble()` | `round`, `floor`, `ceil`          | Flutter UI 计算友好    |
| Scala      | `scala.math`          | `Random.nextDouble()` | `round`, `floor`, `ceil`          | FP 风格，简洁优雅         |
