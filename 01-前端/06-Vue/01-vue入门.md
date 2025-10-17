#前端 #vue

# 0. 补充一下vue的指令

vue支持在html标签里面加上不同的指令，来拓展整个响应式的逻辑

这些指令的作用是**将响应式数据和 DOM 元素进行动态绑定**，从而实现模板的动态渲染和交互。

## 0.1  `v-bind`  绑定数据

`v-bind` 用于将组件的数据绑定到 HTML 元素的属性上。它的简写形式是` :`。

```vue
<template>
// ✅ 将imageSrc的值赋值给img的 src属性
  <img v-bind:src="imageSrc" />  
  <a :href="url">我的链接</a>
  <div :class="{ active: isActive }"></div>
</template>
```

## 0.2 `v-on` 绑定事件

`v-on `用于监听 DOM 事件，并在事件发生时执行 JavaScript。它的简写形式是` @`。

```vue
<template>
  <button v-on:click="handleClick">点击我</button>
  <input @input="onInput" />
</template>
```

## 0.3 `v-if / v-else-if / v-else`  条件渲染

这些指令用于根据表达式的真假来决定是否渲染一个元素或组件。
作用：控制元素的显示和销毁。当条件为假时，元素会被完全移除出 DOM。

```vue
<template>
  <div v-if="type === 'A'">类型 A</div>
  <div v-else-if="type === 'B'">类型 B</div>
  <div v-else>其他类型</div>
</template>
```

## 0.4` v-show` 条件显示

作用：根据表达式的真假来切换元素的 **display CSS** 属性。当条件为假时，元素依然存在于 DOM 中，只是被隐藏了。

**与 v-if 的区别：**
- v-if 有更高的切换开销，因为它会销毁和重建元素。
- v-show 有更高的初始渲染开销，因为它总是会渲染元素。
- 结论：如果需要频繁切换，用 `v-show`；如果运行时条件很少改变，用 `v-if`。

```vue
<template>
  <div v-show="isLoading">加载中...</div>
</template>
```

## 0.5 `v-for`列表渲染

`v-for `用于遍历数组或对象，并为每个项目重复渲染一个元素。

```vue
<template>
  <ul>
    <li v-for="item in items" :key="item.id">
      {{ item.message }}
    </li>
  </ul>
  <div v-for="(value, key) in myObject" :key="key">
    {{ key }}: {{ value }}
  </div>
</template>
```

## 0.6 `v-model` 双向绑定

`v-model` 用于在表单输入元素或组件上创建双向绑定。

作用：它会把` v-bind:value `和 `v-on:input` (或 `v-on:change`) 结合起来，自动同步数据和表单输入。

```vue
<template>
  <input v-model="message" placeholder="请输入..." />
  <span>你的输入: {{ message }}</span>
</template>
```

## 0.7  动态绑定多个属性

```js
const objectOfAttrs = {
  id: 'container',
  class: 'wrapper',
  style: 'background-color:green'
}
```

```vue
<div v-bind="objectOfAttrs"></div>
```

## 0.8  修饰符的方式

有时候表单的submit，需要阻止他的默认事件，改用我们自己实现的事件方法，这个时候可以使用修饰符处理

这个就是onsubmit的调用，会处理`event.preventDefault()`

```vue
<form @submit.prevent="onSubmit">...</form>
```

还有其他的，如果需要可以查看vue的官方文档

# 1.  之前vue2组件的方式

整个vue的开发逻辑没有变，一个组件一个文件 (SFC)，都写在`.vue`文件中，这个`.vue`文件包含三大块

* **`<template>` 标签：**
    * **只能有一个**。一个 `.vue` 文件代表一个独立的组件，这个组件的渲染模板必须是唯一的。Vue 的编译器会强制执行这个规则，如果你放了多个 `<template>` 标签，它会报错。
* **`<script>` 标签：**
    * **可以有多个**。在 Vue 3 中，你可以使用多个 `<script>` 标签来组织代码。最常见的模式是结合 `<script setup>` 和普通的 `<script>` 标签，一个用于核心逻辑，一个用于组件的配置选项（比如 `name`、`inheritAttrs` 等）。
* **`<style>` 标签：**
    * **可以有多个**。你可以在一个 `.vue` 文件中使用多个 `<style>` 标签，它们会按顺序被处理。这通常用于混合不同类型的样式，例如：
        * 一个 `<style scoped>` 标签用于组件私有样式。
        * 一个 `<style>` 标签用于全局样式或导入公共样式。
        * 一个 `<style lang="scss">` 标签用于使用 CSS 预处理器。

# 2. vue2的方式写组件


```vue
<script lang="ts">
export default {
  name: 'Person01',
  data() {
    return {
      name: 'zmg',
      age: 18,
      address: '上海',
    };
  },

  methods: {
    modifyName() {
      this.name = 'zmg0001';
    },

    modifyAge() {
      this.age++;
    },
    showAddress() {
      console.log(`地址是: ${this.address}`);
    },
  },
};
</script>

<template>
  <h2>我的名字是: {{ name }}</h2>
  <h2>我的年龄是: {{ age }}</h2>
  <button @click="modifyName">修改名字</button>
  <button @click="modifyAge">增加年龄</button>
  <button @click="showAddress">展示地址</button>
</template>

<style lang="css" scoped></style>

```


vue底层通过数据劫持的方式来针对`data`中变化的数据进行响应式的动态处理。
但是这种方式，写起来非常麻烦，这种方式就是Options API 的方式，`data`和行为`methods`是分隔开来的，在两个属性包裹的对象中，每次修改都要两边都要看，而且还有`computed`和`watch`等，这样处理会很麻烦
# 3.  vue3的Composition API

主要是用`setup()函数进行处理，这个函数需要返回数据还有方法，比如上面的例子

```vue
<script lang="ts">
export default {
 // 使用setup方法，进行composition API
setup() {
	let name = 'zmg';
	let age = 18;
	let address = '上海';
	function modifyA(){}
	function modifyB(){}
return  {name, age, address, modifyA, modifyB}
}
}
```

这个方式就是composition API的方式，但是每次这样写，也是够麻烦的，都需要用到`setup()`函数， 所以，vue官方提供了语法糖`<script setup>`

```vue
// 需要加上setup
<script setup lang="ts">
let name = 'zmg';
let age = 18;
let address = '上海';
function modifyName() {
  name = 'zmg-0001';
}
function modifyAge() {
  age++;
}
function showAddress() {
  console.log(`地址是: ${address}`);
}
</script>
```

但是有一个问题，采用这种`setup`的方式，会导致数据并不是响应式的，即使数据变化了，在页面上也看不到

就需要用到`ref` 和`reactive`，他们负责创建响应式数据。

> [!tips]
> 在使用`setup`语法糖过后，如何给组件命名呢？
> 1. 使用插件  `vite-plugin-vue-component-extend`  ，这样可以在`<script>`标签中添加一个`name`属性，比如 `<script setup name='APP'>` 
> 2. 用官方原生的`defineOptions`进行处理，比如`defineOptions({name: 'APP'})`

## 3.1 ref的使用

一般`ref` 用在**基本数据类型**以及**对象**上，用`ref`包裹变量，那么这个变量就是响应式的。
但是在`script`标签中用的话，需要`.value`才能取到这个数据，对其进行修改和读取等操作

`ref`针对对象的处理，底层是采用`reactive`，它的value也是一个代理对象。

## 3.2 reactive的使用

`reactive`用在对象身上，包装对象，返回这个对象的响应式代理。

- **直接操作**，不需要用`.value`来拿到这个数据
- **深度响应**： `reactive` 会自动将嵌套在对象内部的所有属性都转换为响应式。
- **代理对象**：`reactive` 返回的是一个代理对象，因此如果你解构它的属性，会失去响应式。

> [!danger]
> 如果想将响应式对象作为属性传递给其他函数或组件，使用 ref，因为 reactive 对象的解构会失去响应性。
> -
> 如果想直接给包装后的reactive的代理对象重新赋值，会导致失去响应式
> ✅`let car = reactive({name:'',brand:''}); car = reactive({name:'新的',brand:'新的'})`， 这样会导致car失去响应式，官方用法是`Object.assign(car, {name:'新的',brand:'新的'}) `； 但是`ref`没有这个问题，`ref`包装的直接`car.value = {name:'新的',brand:'新的'};`



- 如果是基本类型的数据，必须用`ref`
- 如果是对象类型，且嵌套不深，用`ref`会更方便
- 如果对象类型很复杂，且嵌套很深，必须要用`reactive`  性能更好
- 如果想将响应式对象作为属性传递给其他函数或组件， 必须要用`ref`，因为`reactive`解构后会失去响应式

## 3.3 toRef 和toRefs的用法

上一节中我们发现，`reactive`包装的对象不能进行解构，一旦解构赋值，就会导致解构出来的数据没有响应式。

但是`toRef`和`toRefs`就是为了解决这个问题而存在的

- toRefs

```vue
<script setup>
import { reactive, toRefs } from 'vue';

const state = reactive({
  count: 0,
});

// ✅ 正确用法：使用 toRefs 进行解构
// toRefs 将 state 里的每个属性都转换为一个 ref
const { count } = toRefs(state);

const increment = () => {
  // 修改 count.value 会同步更新 state.count
  // 这里的 count 已经是一个 ref 对象，所以需要 .value
  count.value++; 
  console.log(state.count); // 输出：1
};
</script>
```

- toRef

上面那个是将对象的属性全部转为`ref`，这个不划算，其实我们可能需要用到里面的一个属性而已

```vue
<script setup>
import { reactive, toRef } from 'vue';

const state = reactive({
  count: 0,
  otherProp: 'some value'
});

// ✅ 正确用法：使用 toRef 转换单个属性
// countRef 是一个指向 state.count 的 ref
const countRef = toRef(state, 'count');

const increment = () => {
  // 修改 countRef.value 会同步更新 state.count
  countRef.value++;
  console.log(state.count); // 输出：1
};
</script>
```

# 4. computed函数

有时候，我们需要根据页面上两个响应式数据，得到一个新的数据(比如拼接等)，那么这个时候就要用到`computed`函数了

## 4.1 computed 的核心作用

computed 解决了两个主要问题：

- **简化模板逻辑**：避免在模板中写复杂的表达式，让模板更简洁、可读。
- **性能优化**：`computed` 具有==缓存==功能。只有当它依赖的响应式数据发生变化时，它才会重新计算。如果依赖没有变化，即使组件重新渲染，它也会直接返回缓存的结果。

## 4.2 基本用法

在`setup`中使用`computed`函数获取一个计算属性

```vue
<script setup>
import { ref, computed } from 'vue';

const price = ref(10);
const quantity = ref(2);
const balance = ref(30);

// ✅ 1. 创建一个只读的计算属性// 这个属性可以直接使用
// 它的值依赖于 price 和 quantity
const totalPrice = computed(() => {
  return price.value * quantity.value;
});
<script>
```

`computed`计算出来的属性，默认是只读的，而且这个结果是一个`ref`

可以写一个带有`setter`和`getter`的`computed`函数

```vue
<template>
  <div>
    <p>姓名: {{ fullName }}</p>
    <input v-model="fullName" placeholder="请输入姓名" />
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

const firstName = ref('John');
const lastName = ref('Doe');

// ✅ 创建一个带有 setter 的可写计算属性
const fullName = computed({
  // getter：当读取 fullName 的值时被调用
  get() {
    return firstName.value + ' ' + lastName.value;
  },
  // setter：当 fullName 被赋值时被调用
  set(newValue) {
    const names = newValue.split(' ');
    firstName.value = names[0];
    lastName.value = names[1];
  }
});
</script>
```

# 5. watch的实现

watch很好理解，就是监听某些数据，如果出现变化，执行一个回调函数进行处理
那么，在vue中有哪些数据可以被watch呢？
- `ref`包装的数据
- `reactive`包装的数据
- `computed`计算得到的数据 -- 其实这个也是ref
- `getter`函数   ： 你可以侦听一个返回响应式数据的 getter 函数。
-  `数组`：你可以侦听一个响应式数组。(以上组成的一个数组)

## 5.1 watch-ref

```vue
<script setup>
import { ref, watch } from 'vue';

const question = ref('');

// ✅ 侦听 question 的变化
watch(question, (newValue, oldValue) => {
  console.log(`问题从 "${oldValue}" 变成了 "${newValue}"`);
  
  // 模拟异步请求
  if (newValue.includes('天气')) {
    console.log('正在获取天气数据...');
    // 发起 API 请求...
  }
});
</script>
```

## 5.2 watch-reactive

当你侦听一个 reactive 对象时，默认是浅层侦听。如果你想侦听对象内部深层的变化，需要传入第三个参数` { deep: true }`。

```vue
<script setup>
import { reactive, watch } from 'vue';

const state = reactive({
  user: {
    name: 'Alice',
    age: 30,
  },
});

const changeName = () => {
  state.user.name = 'Bob';
};

// ✅ 浅层侦听：只会侦听 state 对象的顶层属性
// watch(state, (newValue) => {
//   console.log('state 变化了', newValue);
// }); // 当 changeName 被调用时，这个侦听器不会触发

// ✅ 深度侦听：使用 deep: true 侦听对象内部的变化
watch(
  state, 
  (newValue) => {
    console.log('state 深度变化了', newValue);
  }, 
  { deep: true }
);

// ✅ 更精确的侦听：侦听 getter 函数
// 这样更高效，只侦听 user.name 的变化
watch(
  () => state.user.name, 
  (newName, oldName) => {
    console.log(`用户名从 ${oldName} 变成了 ${newName}`);
  }
);
</script>
```

## 5.3 watch- getter函数

有时候，我们只需要监听对象中的某一个属性就OK了，并不需要监听整个对象，这个使用`getter`函数就派上用场了

```js
import { reactive, watch } from 'vue';

const state = reactive({
  user: {
    name: 'Alice',
    age: 30,
  },
  status: 'active'
});

// ✅ 侦听 getter 函数
watch(
  () => state.user.name, 
  (newName, oldName) => {
    console.log(`用户名从 ${oldName} 变成了 ${newName}`);
  }
);
```

## 5.4 watch-数组

### 5.4.1 浅层监听

实现：Vue 只会侦听数组本身的引用地址是否发生变化，以及数组的变异方法（如 push, pop, splice）调用。

1. 触发条件：
	- 你给数组重新赋值（myArray = newArray）。
	- 你调用了数组的变异方法（myArray.push(...)）。

2. 不触发条件：
	-  你直接通过索引修改数组的元素（myArray[0] = newValue）。
	- 你修改了数组中某个对象的属性（myArray[0].name = 'Bob'）。

```js
import { ref, watch } from 'vue';

const numbers = ref([1, 2, 3]);

watch(numbers, (newVal, oldVal) => {
  console.log('数组变化了 (浅层)');
  console.log('新值:', newVal, '旧值:', oldVal);
});

// ✅ 触发: 调用变异方法
numbers.value.push(4); 

// ❌ 不触发: 直接通过索引修改
numbers.value[0] = 99; // 侦听器不会触发
```

### 5.4.2 深度监听

实现：Vue 会递归地遍历数组中的所有元素，并为每个元素创建侦听器。它会追踪数组中每个元素的值，甚至是元素内部对象的属性。

触发条件：
- 调用数组的变异方法。
- 数组中的任何一个元素的值发生变化。
- 数组中任何一个对象的深层属性发生变化。

```js
import { ref, watch } from 'vue';

const list = ref([
  { id: 1, name: 'Apple' },
  { id: 2, name: 'Banana' }
]);

watch(list, (newVal, oldVal) => {
  console.log('数组深度变化了');
  console.log('新值:', newVal, '旧值:', oldVal);
}, { deep: true });

// ✅ 触发: 修改了数组中对象的属性
list.value[0].name = 'Orange'; 

// ✅ 触发: 调用变异方法
list.value.push({ id: 3, name: 'Cherry' });
```

# 6. watchEffect

以上写`watch`都需要进行指定某个监听对象，我才可以进行监听，那么有没有一种自动的进行监听呢？

`watchEffect`就出场了。

watchEffect 的核心工作原理
- **立即执行**：当 watchEffect 被创建时，它会立即执行一次传入的函数。
- **自动收集依赖**：在函数执行期间，Vue 的响应式系统会追踪所有被访问过的响应式属性（由 ref 或 reactive 创建）。只会监听在回调函数中用到的对象，其他的不相关的对象变化，不会触发监听
- **自动重新运行**：当其中任何一个被追踪的响应式属性发生变化时，这个函数就会被重新执行。

watchEffect 的优点是代码更简洁，因为它省去了手动声明依赖的步骤。

```vue
<template>
  <div>
    <button @click="increment">增加</button>
    <p>计数器: {{ count }}</p>
    <p>双倍计数: {{ doubled }}</p>
  </div>
</template>

<script setup>
import { ref, computed, watchEffect } from 'vue';

const count = ref(0);
const doubled = computed(() => count.value * 2);

const increment = () => {
  count.value++;
};

// ✅ 使用 watchEffect
watchEffect(() => {
  // 当 count 或 doubled 变化时，这个函数会自动重新运行
  console.log(`当前计数器是: ${count.value}, 双倍值是: ${doubled.value}`);
});
</script>
```


# 7. 标签中的`ref`属性

在vue中，`ref`属性是一个特殊的属性，它允许你在组件中**直接获取对DOM元素或者子组件实例的引用**

- 直接操作DOM，比如获取焦点、获取元素尺寸等
- 调用子组件的方法或者访问子组件的属性

在`vue3`中，`ref`属性通常配合`ref()`函数来使用

步骤
1. 在`<script setup>` 中创建一个空的`ref`变量
2. 在`template`中，给需要引用的元素或者组件加上`ref`属性，这个`ref`的值<span class="red-text">就是第一步中创建的变量的名称</span>
3. 在组件挂载后，就可以通过`ref`变量的`.value`属性来访问该元素或者组件实例

## 7.1 在原生普通的标签中使用

```vue
<script setup lang="ts">
import { ref, watchEffect } from 'vue';
let name = ref('zmg');
let age = ref(18);
let age2 = ref();  // ❌ 注意，创建一个空的ref，在下面的标签中不能用age，虽然他也是一个ref，但是是不允许的

function getAgeElement() {
  console.log(age2.value);
}
</script>

<template>
  <h2>我的名字是: {{ name }}</h2>
  <h2 ref="age2">我的年龄是: {{ age }}</h2>
  <button @click="getAgeElement">获取age元素</button>
</template>

<style lang="css" scoped></style>
```

## 7.2 在组件上使用

在vue中，使用`ref`属性来获取子组件实例的引用，**这样父组件就可以访问子组件的相关属性和方法**

步骤
1. 在父组件`<script setup>`中创建一个空的`ref`变量
2. 在父组件的`template`中，给子组件的标签上加上`ref`属性，属性的值就是刚刚创建的`ref`变量名
3. 在子组件内部，需要显示的『暴露defineExpose』你希望父组件可以访问的方法和属性。这个vue3的安全机制
4. 在父组件中，通过`ref`变量的`.value`就能访问子组件暴露的内容

```vue  title=子组件的vue
<template>
  <p>子组件计数器: {{ count }}</p>
</template>

<script setup>
import { ref } from 'vue';
const count = ref(0);
const increment = () => {
  count.value++;
};
const decrement = () => {
  count.value--;
};
// ✅ 使用 defineExpose 暴露方法和数据
defineExpose({
  count,
  increment,
  decrement,
});
</script>
```

```vue title=父组件
<template>
  <div>
    <h1>父组件</h1>
    
    <Child ref="childRef" />
    
    <button @click="callChildMethod">调用子组件方法</button>
    <button @click="accessChildData">访问子组件数据</button>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import Child from './Child.vue';

// ✅ 2. 创建一个空的 ref 变量
const childRef = ref(null);

const callChildMethod = () => {
  // ✅ 3. 通过 childRef.value 访问子组件实例，并调用其方法
  if (childRef.value) {
    childRef.value.increment();
    console.log('已调用子组件的 increment 方法');
  }
};

const accessChildData = () => {
  // ✅ 4. 访问子组件暴露出来的数据
  if (childRef.value) {
    console.log('子组件当前的计数是:', childRef.value.count);
  }
};
</script>
```

> [!tips]
> `defineExpose`不需要import，vue3的编译器宏会自动的处理，无需我们显式的导入




