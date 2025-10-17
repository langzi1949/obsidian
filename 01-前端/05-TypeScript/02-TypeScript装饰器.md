#前端 #TypeScript  #Ts  #装饰器

# 1. 装饰器逻辑核心

装饰器的逻辑可以概括为：

1.  **在声明时调用**：装饰器函数在你的类、方法等被**声明**时立即执行，而不是在运行时。
2.  **接收元数据**：它接收一组参数，这些参数是关于被装饰目标的元数据（例如，类的构造函数、方法的名称、属性描述符等）。
3.  **可选的返回值**：装饰器可以返回一个值，这个返回值可以用来**替换或修改**被装饰的目标。

-----

# 2. 不同类型装饰器的参数

##  2.1  类装饰器 (Class Decorators)

  * **应用位置**：类声明之前。
  * **参数**：
      * `constructor: Function`：被装饰的类的构造函数。
  * **逻辑**：你可以访问类的构造函数，并可以返回一个新的构造函数来替换它。
  * **示例**：
    ```typescript
    function sealed(constructor: Function) {
      Object.seal(constructor);
      Object.seal(constructor.prototype);
      console.log('Class has been sealed.');
    }

    @sealed
    class Greeter {
      greeting: string;
      constructor(message: string) { this.greeting = message; }
    }
    ```

##  2.2 方法装饰器 (Method Decorators)

  * **应用位置**：方法声明之前。
  * **参数**：
      * `target: Object`：对于静态成员，是类的构造函数；对于实例成员，是类的原型对象。
      * `propertyKey: string | symbol`：方法的名称。
      * `descriptor: PropertyDescriptor`：方法的属性描述符（包含 `value`、`writable`、`enumerable` 等）。这是最重要的参数。
  * **逻辑**：你可以通过修改 `descriptor.value` 来替换或包装原始方法，实现注入逻辑。
  * **示例**：
    ```typescript
    function LogMethod(target: Object, propertyKey: string, descriptor: PropertyDescriptor) {
      const originalMethod = descriptor.value;
      descriptor.value = function(...args: any[]) {
        console.log(`调用方法: ${propertyKey}`);
        return originalMethod.apply(this, args);
      };
    }

    class Calculator {
      @LogMethod
      add(a: number, b: number): number {
        return a + b;
      }
    }
    ```

## 2.3 属性装饰器 (Property Decorators)

  * **应用位置**：属性声明之前。
  * **参数**：
      * `target: Object`：对于静态成员，是类的构造函数；对于实例成员，是类的原型对象。
      * `propertyKey: string | symbol`：属性的名称。
  * **逻辑**：主要用于添加元数据，因为它们**无法**直接访问或修改属性的初始值。
  * **示例**：
    ```typescript
    const formatMetadataKey = Symbol("format");

    function format(formatString: string) {
      return Reflect.metadata(formatMetadataKey, formatString);
    }

    class Example {
      @format("YYYY-MM-DD")
      date: string;
    }
    ```

-----

##  2.4参数装饰器 (Parameter Decorators)

  * **应用位置**：参数列表中的参数声明之前。
  * **参数**：
      * `target: Object`：对于静态成员，是类的构造函数；对于实例成员，是类的原型对象。
      * `propertyKey: string | symbol`：参数所属方法的名称。
      * `parameterIndex: number`：参数在方法参数列表中的索引位置。
  * **逻辑**：主要用于为参数添加元数据，常用于依赖注入等框架中。

-----

### 总结表格

| 装饰器类型 | 接受的参数 | 作用 |
| :--- | :--- | :--- |
| **类** | `constructor: Function` | 替换或扩展整个类 |
| **方法** | `target, propertyKey, descriptor` | 修改方法的行为，最常见用法 |
| **属性** | `target, propertyKey` | 为属性添加元数据 |
| **参数** | `target, propertyKey, parameterIndex` | 为方法的参数添加元数据 |