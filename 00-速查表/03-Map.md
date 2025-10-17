
# 1. 创建map


```java title=java fold
Map<String, Integer> map = new HashMap<>();
```

```go title=go fold
// 方式1: make创建
m := make(map[string]int)
// 方式2: 字面量创建
m2 := map[string]int{"name": 18, "age": 25}
```

```js title=javasript fold
let obj = {};
// 或者
let obj2 = {"name": 18, "age": 25};
```

```php title=php fold
$map = array();
// 或者
$map = ["name" => 18, "age" => 25];
```

```python title=python fold
d = {}
# 或者
d = {"name": 18, "age": 25}
# 或者使用dict()
d2 = dict(name=18, age=25)
```

```groovy title=groovy fold
def map = [:]
// 或者
def map2 = [name: 18, age: 25]
```

```kotlin title=kotlin fold
val map = mutableMapOf<String, Int>("a" to 1, "b" to 2, "c" to 3)
```

```c title=c fold

```

```cpp title=c++ fold
std::map<std::string, int> map = {{"a", 1}, {"b", 2}, {"c", 3}};
```

```dart title=dart fold
var map = {'a': 1, 'b': 2, 'c': 3};
```

```scala title=scala fold
val map = Map[String, Int]("a" -> 1, "b" -> 2, "c" -> 3)
```

# 2. 获取key-value


```java title=java fold
Integer value = map.get("key1");
Integer valueWithDefault = map.getOrDefault("key1", 0);
```

```go title=go fold
value := m["key1"]
// 获取并检查是否存在
value, ok := m["key1"]
```

```js title=javasript fold
let value = obj["key1"];
// 或者
let value2 = obj.key1;
```

```php title=php fold
$value = $map["key1"] ?? null;
// 或者使用 isset 检查
if (isset($map["key1"])) {
    $value = $map["key1"];
}
```

```python title=python fold
value = d.get("key1")
value_with_default = d.get("key1", 0)
# 或者直接访问（key不存在会报错）
# value = d["key1"]
```

```groovy title=groovy fold
def value = map['key1']
// 或者使用点语法
def value2 = map.key1
// 带默认值
def value3 = map.get('key1', 0)
```

```kotlin title=kotlin fold
println(map["a"]) // 输出: 1
println(map.getOrDefault("x", 0)) // 默认值 0
```

```c title=c fold

```

```cpp title=c++ fold
std::cout << map["a"] << std::endl; // 输出: 1
std::cout << map.find("x") != map.end() ? map["x"] : 0 << std::endl; // 默认 0
```

```dart title=dart fold
print(map['a']); // 输出: 1
print(map['x'] ?? 0); // 默认值 0
```

```scala title=scala fold
println(map("a")) // 输出: 1
println(map.getOrElse("x", 0)) // 默认值 0
```

# 3. 判断key是否存在


```java title=java fold
boolean exists = map.containsKey("key1");
```

```go title=go fold
_, exists := m["key1"]
if exists {
	fmt.Println("key存在")
}
```

```js title=javasript fold
let exists = "key1" in obj;
// 或者
let exists2 = obj.hasOwnProperty("key1");
```

```php title=php fold
$exists = array_key_exists("key1", $map);
// 或者
$exists = isset($map["key1"]);
```

```python title=python fold
exists = "key1" in d
```

```groovy title=groovy fold
def exists = map.containsKey('key1')
// 或者
def exists2 = 'key1' in map
```

```kotlin title=kotlin fold
println(map.containsKey("a")) // 输出: true
println("x" in map) // 输出: false
```

```c title=c fold

```

```cpp title=c++ fold
std::cout << (map.find("a") != map.end()) << std::endl; // 输出: 1 (true)
std::cout << map.count("x") << std::endl; // 输出: 0 (false)
```

```dart title=dart fold
print(map.containsKey('a')); // 输出: true
```

```scala title=scala fold
println(map.contains("a")) // 输出: true
```

# 4. 添加key-value


```java title=java fold
map.put("name", 18);
```

```go title=go fold
m["name"] = 18
```

```js title=javasript fold
obj["name"] = 18;
obj.age = 25;
```

```php title=php fold
$map["name"] = 18;
```

```python title=python fold
d["name"] = 18
```

```groovy title=groovy fold
map['name'] = 18
map.age = 25
// 或者使用put
map.put('address', 'Beijing')
```

```kotlin title=kotlin fold
map["d"] = 4
// 或: map.put("d", 4)
```

```c title=c fold

```

```cpp title=c++ fold
map["d"] = 4;
// 或: map.insert({"d", 4});
```

```dart title=dart fold
map['d'] = 4;
```

```scala title=scala fold
map("d") = 4
// 或者: map += ("d" -> 4)
```

# 5. 删除和修改key


```java title=java fold
map.remove("name");  // 删除
map.put("age", 26);  // 修改（直接覆盖）
```

```go title=go fold
delete(m, "name")  // 删除
m["age"] = 26      // 修改（直接赋值）
```

```js title=javasript fold
delete obj.name;    // 删除
obj.age = 26;       // 修改
```

```php title=php fold
unset($map["name"]);  // 删除
$map["age"] = 26;     // 修改
```

```python title=python fold
del d["name"]        # 删除
# 或者
d.pop("name", None)  # 删除并返回值
d["age"] = 26        # 修改
```

```groovy title=groovy fold
map.remove('name')   // 删除
map['age'] = 26      // 修改
```

```kotlin title=kotlin fold
map.remove("b") // 删除
map["a"] = 10 // 修改
```

```c title=c fold
map.erase("b"); // 删除
map["a"] = 10; // 修改
```

```cpp title=c++ fold

```

```dart title=dart fold
map.remove('b'); // 删除
map['a'] = 10; // 修改
```

```scala title=scala fold
map -= "b" // 删除
map("a") = 10 // 修改
```

# 6. 清空map


```java title=java fold
map.clear();
```

```go title=go fold
// Go没有clear方法，需要重新创建
m = make(map[string]int)
// 或者 Go 1.21+ 可以使用 clear(m)
```

```js title=javasript fold
obj = {};
// 或者删除所有属性
for (let key in obj) {
    delete obj[key];
}
```

```php title=php fold
$map = array();
// 或者
unset($map);
$map = [];
```

```python title=python fold
d.clear()
```

```groovy title=groovy fold
map.clear()
```

```kotlin title=kotlin fold
map.clear()
```

```c title=c fold

```

```cpp title=c++ fold
map.clear();
```

```dart title=dart fold
map.clear();
```

```scala title=scala fold
map.clear()
```

# 7.  获取所有的key


```java title=java fold
var keys = map.keySet();
```

```go title=go fold
keys := make([]string, 0, len(m))
for k := range m {
	keys = append(keys, k)
}
```

```js title=javasript fold
let keys = Object.keys(obj);
```

```php title=php fold
$keys = array_keys($map);
```

```python title=python fold
keys = list(d.keys())
```

```groovy title=groovy fold
def keys = map.keySet()
// 转换为列表
def keysList = map.keySet() as List
```

```kotlin title=kotlin fold
map.keys
```

```c title=c fold

```

```cpp title=c++ fold
for (const auto& pair : map) std::cout << pair.first << " "; // 输出: []
```

```dart title=dart fold
print(map.keys); // 输出: ()
```

```scala title=scala fold
println(map.keys) // 输出: Set()
```

# 8. 获取所有的value


```java title=java fold
var values = map.values();
```

```go title=go fold
values := make([]int, 0, len(m))
for _, v := range m {
	values = append(values, v)
}
```

```js title=javasript fold
let values = Object.values(obj);
```

```php title=php fold
$values = array_values($map);
```

```python title=python fold
values = list(d.values())
```

```groovy title=groovy fold
def values = map.values()
// 转换为列表
def valuesList = map.values() as List
```

```kotlin title=kotlin fold
map.values
```

```c title=c fold

```

```cpp title=c++ fold
for (const auto& pair : map) std::cout << pair.second << " "; // 输出: []
```

```dart title=dart fold
print(map.values); // 输出: ()
```

```scala title=scala fold
println(map.values) // 输出: Iterable()
```

# 9. map大小


```java title=java fold
int size = map.size();
```

```go title=go fold
size := len(m)
```

```js title=javasript fold
let size = Object.keys(obj).length;
```

```php title=php fold
$size = count($map);
```

```python title=python fold
size = len(d)
```

```groovy title=groovy fold
def size = map.size()
```

```kotlin title=kotlin fold
val size = map.size
```

```c title=c fold

```

```cpp title=c++ fold
std::cout << map.size() << std::endl; // 输出: 0
```

```dart title=dart fold
print(map.length); // 输出: 0
```

```scala title=scala fold
println(map.size) // 输出: 0
```

# 10. 遍历map


```java title=java fold
for (Map.Entry<String, Integer> entry : map.entrySet()) {
	System.out.println(entry.getKey() + ": " + entry.getValue());
}

// 方式2: 遍历key
for (String key : map.keySet()) {
	System.out.println(key + ": " + map.get(key));
}

// 方式3: forEach (Java 8+)
map.forEach((key, value) -> {
	System.out.println(key + ": " + value);
});
```

```go title=go fold
for key, value := range m {
	fmt.Printf("%s: %d\n", key, value)
}

// 只遍历key
for key := range m {
	fmt.Printf("%s: %d\n", key, m[key])
}
```

```js title=javasript fold
// 方式1: for...in
for (let key in obj) {
    console.log(key + ": " + obj[key]);
}

// 方式2: Object.entries()
for (let [key, value] of Object.entries(obj)) {
    console.log(key + ": " + value);
}

// 方式3: forEach
Object.keys(obj).forEach(key => {
    console.log(key + ": " + obj[key]);
});
```

```php title=php fold
foreach ($map as $key => $value) {
    echo "$key: $value\n";
}

// 方式2: foreach 只遍历value
foreach ($map as $value) {
    echo "$value\n";
}

// 方式3: array_walk
array_walk($map, function($value, $key) {
    echo "$key: $value\n";
});
```

```python title=python fold
# 方式1: 遍历key
for key in d:
    print(f"{key}: {d[key]}")

# 方式2: 遍历键值对
for key, value in d.items():
    print(f"{key}: {value}")

# 方式3: 只遍历value
for value in d.values():
    print(value)

# 方式4: 遍历key（显式）
for key in d.keys():
    print(f"{key}: {d[key]}")
```

```groovy title=groovy fold
// 方式1: each遍历键值对
map.each { key, value ->
    println "$key: $value"
}

// 方式2: eachWithIndex
map.eachWithIndex { key, value, index ->
    println "$index - $key: $value"
}

// 方式3: for循环
for (entry in map) {
    println "${entry.key}: ${entry.value}"
}

// 方式4: collect转换
def result = map.collect { key, value ->
    "$key: $value"
}
```

```kotlin title=kotlin fold
for ((key, value) in map) {
	println("$key: $value")
}
// 或: map.forEach { (k, v) -> println("$k: $v") }
```

```c title=c fold

```

```cpp title=c++ fold
for (const auto& [key, value] : map) {
	std::cout << key << ": " << value << std::endl;
}
```

```dart title=dart fold
map.forEach((key, value) => print('$key: $value'));
```

```scala title=scala fold
map.foreach { case (key, value) => println(s"$key: $value") }
```


# 11. JavaScript的Map（ES6+）

```js title=Jsavascript fold
// 1. 创建
let map = new Map();
// 或者
let map2 = new Map([["name", 18], ["age", 25]]);

// 2. 获取key-value
let value = map.get("key1");

// 3. 判断key是否存在
let exists = map.has("key1");

// 4. 添加key-value
map.set("name", 18);
map.set("age", 25);

// 5. 删除和修改key-value
map.delete("name");   // 删除
map.set("age", 26);   // 修改

// 6. 清空map
map.clear();

// 7. 获取所有的key
map.set("name", 18);
map.set("age", 25);
let keys = Array.from(map.keys());
// 或者
let keys2 = [...map.keys()];

// 8. 获取所有的value
let values = Array.from(map.values());

// 9. map大小
let size = map.size;

// 10. 遍历map
// 方式1: forEach
map.forEach((value, key) => {
    console.log(key + ": " + value);
});

// 方式2: for...of
for (let [key, value] of map) {
    console.log(key + ": " + value);
}

// 方式3: 只遍历key
for (let key of map.keys()) {
    console.log(key + ": " + map.get(key));
}
```