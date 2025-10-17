#前端 #TypeScript  #Ts 

# 1.  什么是TypeScript

1. `TypeScript` 是`JavaScript`的一个扩展语言
2. `TypeScript` 包含了 `JavaScript`的所有内容，一句话说：`TypeScript`是`JavaScript`的超集
3. `TypeScript` 增加了： 静态类型检查、接口、泛型等，适合大型项目的开发
4. `TypeScript`需要编译成`JavaScript`才可以在浏览器运行


# 2. 如何使用typescript

# 2.1 命令行手动编译

1. 安装`typescipt`   `npm install -g typescript`
2. 然后编写一个`ts`文件
3. 使用`tsc hello.ts`编译成`hello.js`

# 2.2  自动化编译

1. 使用`tsc --init`  会生成一个`tsconfig.json`配置文件
2. 使用`tsc --watch`  监听整个目录下的`ts`文件，最后自动会生成对应的js文件


# 3. 类型的定义

```ts
let a: string
let b:number = 20

function fund(x:number, y:number) {
	return x+y;
}

function fund(x:number, y:number) : number {
	return x+y;
}
```

# 4. 类型推断

```ts
let a = 90;  // 推断a为number
```

# 5. 数据类型

JavaScript的数据类型
1. number
2. string
3. boolean
4. null
5. undefined
6. bigint
7. symbol
8. object

TypeScript的数据类型 (除了JS的所有类型之外)
1. any
	1. 任意类型  完全放弃类型安全， <span class="red-text">很诡异的东西，any类型的变量可以赋值给任意类型的变量</span>
2. unknown
	1. 和`any`类似，但是它是一个类型安全版本的`any`， 强制在使用这个变量之前必须要进行类型检查，要缩小类型以便使用
	2. 使用的时候 
		1. 缩小类型  `if (typeof a === 'number') {x = a}`
		2. 通过断言  `x = a as string`  或者` x = <number>a`
3. never
	1. 永远都不会发生的值的类型，比`void`严格，通常用于以下两种场景
		1. 抛出异常的函数

```js
// 抛出异常的函数
function demo(): never {
	throw new Error('抛出异常......')
}

// 最重要的用法： 用于switch语句中，确保处理了所有的情况
type Shape = 'circle' | 'square' | 'triangle';

function getArea(shape: Shape) {
  switch (shape) {
    case 'circle':
      return 10;
    case 'square':
      return 20;
    default:
      // ✅ 当你添加新的类型（如 'triangle'）而没有处理时，这里会报错
      // 强制你处理所有情况
      const _exhaustiveCheck: never = shape; 
      return _exhaustiveCheck;
  }
}
```

3. void
	1. 通常用于表达函数没有返回值，它的行为和`undefined`类似

```js
// 一个没有返回值的函数，其返回类型被推断为 void
function logMessage(message: string): void {
  console.log(message);
  // return 1; // ❌ 报错：不能返回任何值
}

// 赋值给一个 void 变量
let result: void = undefined;

// ✅ 一个返回 undefined 的函数，也兼容 void 类型
function returnUndefined(): undefined {
    return undefined;
}
let voidResult: void = returnUndefined();
```

> [!tips]
> `undefined`是 `void`可以接受的一种『空』
 
4. tuple
	1. tuple是一种特殊的数组类型，它拥有**固定数量**的元素，并且每个元素的类型是已知的，不一定相同

```ts
// 定义一个元组类型，它有两个元素：第一个是字符串，第二个是数字
let user: [string, number];

// ✅ 正确赋值：类型和顺序都匹配
user = ['Alice', 25];

// ❌ 错误赋值：类型或顺序不匹配
// user = [25, 'Alice']; // 报错：第一个元素应为 string，第二个应为 number

// ❌ 错误赋值：数量不匹配
// user = ['Bob', 30, true]; // 报错：元组只允许有两个元素

```

4. enum
```js
// 数字枚举
enum Direction {
  Up,      // 0
  Down,    // 1
  Left,    // 2
  Right    // 3
}

let myDirection: Direction = Direction.Up; // 值为 0

// 字符串枚举
enum HttpMethods {
  GET = 'GET',
  POST = 'POST',
  DELETE = 'DELETE'
}

let requestMethod: HttpMethods = HttpMethods.POST; // 值为 'POST'
```


还有两个自定义类型的方式
	1. type
	2. interface

> [!danger]
>  1. 需要注意的是，不能将类型`String`分配给基本类型`string` ； 但是反过来是可以的，`string`可以分配给`String`
>  2. `let a: any; a = false;  let b: string;  b = a;` // ==**注意，这个居然是对的，因为any可以赋值给任务类型变量**== 

# 6. 重要的object类型

在`TypeScript`中，`object`类型用来表示非原始类型的值
简单来说，`object`类型可以用来表示对象、函数以及数组。

主要用途：当你需要一个函数来接受任何非原始类型的值，但又不想对该值的具体结构做任何假设时，`object`类型就很有用。

```ts
// 一个函数接受一个 object 类型作为参数
function getKeys(obj: object): string[] {
  return Object.keys(obj);
}

const myObject = { a: 1, b: 2 };
const myArray = [1, 2, 3];
const myFunction = () => {};

// ✅ 正确：传入非原始类型
console.log(getKeys(myObject)); // ['a', 'b']
console.log(getKeys(myArray));   // ['0', '1', '2']
console.log(getKeys(myFunction)); // ['length', 'name', 'prototype']

// ❌ 错误：传入原始类型，编译器会报错
// getKeys('hello'); // 报错：类型“string”的参数不能赋给类型“object”的参数
// getKeys(123);     // 报错：类型“number”的参数不能赋给类型“object”的参数
```

1. 定义`object`对象

```ts
let person: { name: string; age: number };  // 官方推荐属性变量之间用; ,当然用逗号也可以
person = { name: 'Tom', age: 89 };
```

2. 索引签名

它允许你为那些键名不确定，但键值类型已经确定的对象定义类型

```ts
interface StringDictionary {
  // ✅ 这是一个索引签名
  // key 的类型是 string，值的类型是 string
  [key: string]: string;
}

// 使用这个接口来定义一个对象
const myDictionary: StringDictionary = {
  "hello": "world",
  "name": "Alice",
  "city": "New York"
};

// 你可以添加任意多的属性，只要键和值都符合类型
myDictionary.greeting = "Hello!"; // ✅ 正确
myDictionary[123] = "number key"; // ✅ 正确，因为数字索引会被转换为字符串

// myDictionary.age = 30; // ❌ 错误：值必须是 string 类型


let person: { name: string; age: number; [key: string]: string | number }; // 键的值一定要是string或者数字
person = { name: 'Tom', age: 89, gender: '男' };
```

# 7. type类型

使用`type`可以常见类型的别名

1. 为复杂的类型创建一个别名

```ts
// ✅ 使用 type 为一个联合类型命名
type ID = number | string;

function printID(id: ID) {
  console.log(`你的 ID 是: ${id}`);
}

printID(101); // 正确
printID("abc"); // 正确
// printID(true); // 错误：类型不匹配
```

2. 最常用的为类创建别名

```ts
// ✅ 使用 type 为一个对象类型命名
type User = {
  name: string;
  age: number;
  isStudent?: boolean; // 可选属性
};

function createUser(user: User) {
  console.log(user);
}

const alice: User = { name: "Alice", age: 30 };
createUser(alice);
```

3. 引用自身，创建类似树形结构

```ts
// ✅ 定义一个树节点类型
type TreeNode = {
  value: number;
  left?: TreeNode;
  right?: TreeNode;
};
```

## 7.1 一个重要的关于type的问题

```ts
type MyFunc = () => void;  // 定一个类型MyFunc，他是一个函数，且没有参数返回void

const s: MyFunc = () => {
  return 56;
};

console.log(s());  // 没有报错

```

这是因为`TypeScript`针对void函数没有进行检查，这个函数的返回值会被忽略，这是为了兼容`js`中很多的箭头函数忽略返回的问题，除了 void 之外，其他返回类型都是严格检查的。void 的这种宽松处理就是它的独特之处。

# 8. 类的使用

## 8.1 定义类

```ts
// 正常写法
class Person {
  name: string;
  age: number;
  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
}
let p = new Person('zmg', 20);


//  ✅ 简写语法：在构造函数中直接定义属性
class Person {
  constructor(public name: string, public age: number) {} // 注意如果不加public，这个属性不在实例上
}

let p = new Person('zmg', 20);
```

## 8.2 访问修饰符

和`Java`一样，有`private  protected public readonly`  默认是`public` ，`readonly`  代表属性不能直接修改

他们的可见性和Java一样，就不说了

```ts
class Person {
  constructor(name: string, private age: number) {}

  speak(): string {
    return `Result = ${this.age}`;  // 要想用这个age必须将上面的构造器中的age修饰一下
  }
}

```
## 8.3  使用抽象类

`JavaScript`中没有抽象类的概念，但是ts实现了，而且他的逻辑和`Java`几乎一模一样

```ts
abstract class Person {
  constructor(public name: string, public age: number) {}

  abstract speak(): string;

  public find(): any {
    return `${this.name} --- ${this.age}`;
  }
}

class Student extends Person {
  constructor(public name: string, public age: number, private grade: string) {
    super(name, age);
  }

  public speak(): string {
    return `一个学生在说话:${this.name}-${this.grade}`;
  }
}

let s1 = new Student('kobe', 29, '三年二班');
console.log(s1.speak());
console.log(s1.find());
```

# 9. interface的使用

在`TypeScript`中`interface`是一个**契约**，定义了某种格式来规范和约束对象的形状

**interface 的核心作用**
- 类型检查：确保你的对象、函数参数或类的实例符合预期的结构。
- 代码可读性：为复杂的数据结构提供一个有意义的名称，让代码意图更清晰。
- 代码复用：可以在多个地方复用同一个接口定义。

## 9.1  为对象定义类型

```ts
interface Person {
  name: string;
  age?: number;
}

const p: Person = {
  name: 'zmg',
};

```

## 9.2 为类定义类型

一个类可以实现多个接口，只能继承一个类  这个和Java一样

```ts
interface Person {
  name: string;
  age?: number;

  speak(name: string): string | number;
}

class Student implements Person {
  constructor(public name: string) {}

  speak(name: string): string | number {
    return '';
  }
}
```

## 9.3 为函数定义类型

```ts
// ✅ 定义一个函数接口，描述一个参数为 string，返回值为 void 的函数
interface GreetFunc {
  (message: string): void;
}

const myGreet: GreetFunc = (msg) => {
  console.log(`Hello, ${msg}`);
};

myGreet('world');
```

## 9.4 接口继承

和Java类似，接口也可以继承另外一个接口

```ts
// 定义一个基本接口
interface Shape {
  color: string;
}

// 定义一个继承 Shape 的接口
interface Square extends Shape {
  sideLength: number;
}

const square: Square = {
  color: 'blue',
  sideLength: 10
};
```

## 9.5 声明合并（Declaration Merging）

这是一个 `interface` 独有的特性。你可以多次声明同名的接口，TypeScript 会自动将它们合并为一个完整的接口。这在给第三方库添加类型定义时非常有用。
```ts
// 第一次声明
interface Box {
  width: number;
}

// 第二次声明，与第一次合并
interface Box {
  height: number;
}

// Box 现在拥有 width 和 height 两个属性
const myBox: Box = {
  width: 10,
  height: 20
};
```

# 10. `.d.ts`文件是啥

简单来说，.d.ts 文件是 TypeScript 的“翻译官”或“说明书”**。

它的唯一作用就是描述类型信息，它不包含任何可执行的代码。
一个` .d.ts` 文件只包含 `interface, type, class, function` 等的类型声明，没有具体的函数实现或变量赋值。

在`ts`文件中直接引入js的模块，最终会发现没有任何提示，代码虽然没有错，但是语法提示没有，就无从谈起静态检查了

```ts
export declare function add(a: number, b: number): number;
```



