#前端 #vue 

# 1.  props的使用

`props` 是父组件向子组件传递数据最主要的方式之一。使用`props`可以让子组件保持可复用性和独立性。

# 1.1 步骤

1. 父组件肯定要定义和管理数据，然后通过标签绑定属性的方式，传递给子组件 

```js
<UserProfile :name="anotherUser.name" :age="anotherUser.age" />
```

2. 子组件中，需要进行props的接收，通过`defineProps`宏函数的方式。

```js
<script setup>
// ✅ 使用 defineProps 宏定义 props
// 传入一个对象，键是 prop 名称，值是 prop 类型
defineProps({
  name: String,
  age: Number,
  bio: {
    type: String,
    // 定义可选 prop，可以设置默认值
    default: '暂无简介'
  }
});
</script>
```

> [!danger]
> 需要注意的是：`defineProps()`函数的调用是一个运行时的调用，在编译阶段不会进行内部的真实格式的校验，
> 所以在实际开发中，很少使用这个方式，更多的是采用`typescript`的方式进行类型校验


## 1.2 简单数据的接收

有时候只需要一些简单的数据的传递，也不太想进行类型校验，可以直接采用数组的方式接收，这样会进行解构

```js  title=子组件
defineProps(['name']);
```

## 1.3 采用类型声明式props，尤其是typescript的校验

1. 首先，定义一个type来进行约束

```ts
// src/types/person.ts
export interface PersonType {
  name: string;
  age: number;
}
```

2. 父组件中定义数据，并传递给子组件

```js
let person = ref<PersonType>({ name: 'zmg', age: 28 });
```
2. 子组件进行数据类型的校验

```js
defineProps<{
  name?: string; // 可以不传
  person: PersonType;
}>();
```

3. 子组件可以设置一个默认值 ，使用`withDefault()`

```js
withDefaults(defineProps<{ name?: string; person?: PersonType }>(), {
  name: 'kobe',
  person: () => ({ name: 'ss', age: 23 }),  // 对象字面量的语法，如果想直接返回需要用()包裹，要不然{}有歧义
});
```

# 2. 生命周期

![[Pasted image 20250824105710.png]]


钩子函数
<span class="red-text">vue3中已经省去了beforeCreate和created，这个直接就是setup整个过程就是常见的过程</span>

1. 挂载阶段
	1. `onBeforeMount(callback func)`
	2. `onMounted(callback func)`
2. 更新阶段
	1. `onBeforeUpdate(callback func)`
	2. `onUpdated(callback func)`
3. 卸载阶段
	1. `onBeforeUnmount(callback func)`
	2. `onUnmounted(callback func)`


还有几个其他的钩子
1. `onRenderTracked`  当一个组件的渲染依赖被追踪时调用，主要用于调试
2. `onErrorCaptured()`  捕获子孙组件传递的错误。可以在这里进行错误上报或处理。

| Vue 2 选项式 API   | Vue 3 组合式 API (`<script setup>`) |
| :-------------- | :------------------------------- |
| `beforeCreate`  | `setup()`                        |
| `created`       | `setup()`                        |
| `beforeMount`   | `onBeforeMount`                  |
| `mounted`       | `onMounted`                      |
| `beforeUpdate`  | `onBeforeUpdate`                 |
| `updated`       | `onUpdated`                      |
| `beforeDestroy` | `onBeforeUnmount`                |
| `destroyed`     | `onUnmounted`                    |

# 3. 自定义Hooks

在vue3中，自定义Hooks是组合式API中一种重要的模式，它的作用就是封装和复用有状态的逻辑。

简单来说，这个hooks就是一个函数，通常用`use`开头，函数内部可以用到`ref  reactive  watch  computed`甚至可以用组件的生命周期（onMounted等）

```ts
// useFetchData.js
import { ref, onMounted } from 'vue';

export function useFetchData(url) {
  const data = ref(null);
  const loading = ref(true);
  const error = ref(null);

  const fetchData = async () => {
    try {
      const response = await fetch(url);
      if (!response.ok) {
        throw new Error('网络请求失败');
      }
      data.value = await response.json();
    } catch (e) {
      error.value = e;
    } finally {
      loading.value = false;
    }
  };

  onMounted(fetchData);

  return { data, loading, error };
}
```


组件中使用

```vue
<template>
  <div v-if="loading">加载中...</div>
  <div v-else-if="error">错误: {{ error.message }}</div>
  <div v-else>
    <li v-for="user in data" :key="user.id">{{ user.name }}</li>
  </div>
</template>

<script setup>
import { useFetchData } from './useFetchData';

const { data, loading, error } = useFetchData('https://api.example.com/users');
</script>
```

# 4. 路由

这个主要讨论 `vue router`  这个是官方维护且推荐使用的，已经是事实标准了

`npm install vue-router`

同时，我们一般在开发规范中，会有一个专门的目录，用于存放路由配置信息`./src/router`
同时一般还有一个`./src/pages` 或者`./src/views`目录，用于存放『页面』级组件，他们通常就是路由最后对应的页面


## 4.1 基本路由的实现

1. 创建路由器实例，并定义路由器信息

单独一个目录，专门存放路由信息，`./src/router`
```js
import About from '@/components/About.vue';
import Home from '@/components/Home.vue';
import News from '@/components/News.vue';
import { createRouter, createWebHistory } from 'vue-router';

// 创建路由器

const router = createRouter({
  history: createWebHistory(), // 这个属性是必须的，需要告诉app用的是什么history
  routes: [
    { path: '/home', component: Home, alias: '/' },
    { path: '/about', component: About },
    { path: '/news', component: News },
  ],
});

export default router;

```

2. 在`APP`上注册路由器

在`main.js`中处理，通过`app`的`use`方法


```js title=main.js
// omiting other import
import router from './router';

createApp(App).use(router).mount('#app');

```

3. 通过`<RouterLink>` 创建路由点击

```vue
 <ul>
	<RouterLink to="/home" active-class="active">首页</RouterLink>
	<RouterLink to="/about" active-class="active">关于我</RouterLink>
	<RouterLink to="/news" active-class="active">新闻</RouterLink>
</ul>
```

注意：
- `active-class='active'`  这个代表选中激活，会自动添加一个`class='active'`， 这样可以动态调整样式
- `to`有两种写法
	- 直接 `to='/home'`   采用字符串
	- 绑定属性  `:to="{path:'/home'}"`  采用对象

>[!danger]
>`to`还可以采用命名方式跳转`to="{name:'home'}"`   ，需要在路由中对每个路由添加`name`属性即可 #命名路由


4. 通过`<RouterView>`  通知路由获取的组件渲染在什么地方

`RouterView`组件不用显示导入，因为这个注册到app全局组件上了，任何vue都能直接使用

```vue  hl:13
<template>
  <div>
    <h1>这是一个测试区域</h1>
    <hr />
    <nav>
      <ul>
        <RouterLink to="/home" active-class="active">首页</RouterLink>
        <RouterLink to="/about" active-class="active">关于我</RouterLink>
        <RouterLink to="/news" active-class="active">新闻</RouterLink>
      </ul>
    </nav>
    <main>
      <RouterView />
    </main>
  </div>
</template>
```


## 4.2 嵌套路由

1. 定义路由的时候，在一级路由中添加`children`属性

```js
const routes = [
  { path: '/', component: Home },
  { 
    path: '/user', 
    component: User,
    // ✅ 定义子路由
    children: [
      { path: 'profile', component: UserProfile },
      { path: 'posts', component: UserPosts },
    ]
  }
];

const router = createRouter({
  history: createWebHistory(),
  routes,
});

export default router;
```

>[!danger]
>注意，在二级`children`属性中，path前面不用加『/』， 因为`children`数组中的子路由会自动继承父路由的路径， 如果加上`/`，就认为是一级目录了，所以一定要注意

## 4.3 传递query参数

- 先在`RouteLink`中处理一下`to`属性

```vue
 <RouterLink
      v-for="it in newsList"
      :key="it.id"
      :to="{ path: '/news/detail', query: { id: it.id } }"
      :class="{ active: $route.query.id === it.id }"
    >
      {{ it.title }}
</RouterLink>
```

- 子组件使用的方式（`useRoute()`）

```js
const { query } = toRefs(useRoute());  // 需要要用toRefs包装，要不然解构出来就不响应式了
console.log(query.id)
```

## 4.4 传递params参数

- 在路由配置中需要调整

```js
{
      path: '/news',
      component: News,
      children: [
        {
          name: 'detail',   // 🆘 这个地方记得要写name属性，后续要用到
          path: 'detail/:id',
          component: NewsDetial,
        },
      ],
    },
```

- `RouteLink`上的`to`属性使用对象，会更加方便和直观

```vue
<RouterLink
      v-for="it in newsList"
      :key="it.id"
      :to="{
        name: 'detail',
        params: {
          id: it.id,
        },
      }"
      :class="{ active: $route.params.id === it.id }"
>
```

- 后续子组件就通过`useRoute().params` 来接收

## 4.5 路由的props配置

上面的传参数相对来说比较复杂，我们可以采用路由props的方式会大大简化代码

- 修改路由规则

```js title=router.js
 {
      path: '/news',
      component: News,
      children: [
        {
          name: 'detail',
          path: 'detail/:id',
          component: NewsDetial,
          // 这个是方式1: 开启这个就将params参数自动通过props传到路由组件中
          props: true,
          // 方式2: 函数，可以将params和query的东西都通过props传给路由组件，可以决定哪些值传
          // props: (route) => {
          //   // return route.params;
          //   // return route.query;
          //   return {
          //     id: route.params.id,
          //   };
          // },
        },
      ],
    },
```

- 路由组件中如何得到props值？

```vue
<script setup>
import { computed, ref, toRef, toRefs } from 'vue';
import { useRoute } from 'vue-router';

// 不需要响应式，直接展示
const newsInfos = [
  { id: 'sdfasd-01', title: '标题1', content: '这个是标题1的内容' },
  { id: 'sdfasd-02', title: '标题2', content: '这个是标题22222的内容' },
  { id: 'sdfasd-03', title: '标题3', content: '这个是标题1333333的内容' },
];

const props = defineProps(['id']);  // ✅
const { id } = toRefs(props); // ✅
const currentNews = computed(() => {
  return newsInfos.find((it) => it.id === id.value);
});
</script>
```

## 4.6 编程式路由

```js
const router = useRouter();

router.push({
  name: 'detail',
  params: {
    id: newsList[2].id,
  },
});
```

## 4.7 重定向

在路由配置中添加如下

```js
{
  path: '/',
  redirect: '/home',
},
```

# 5 Pinia

集中式状态管理

```shell
npm install pinia
```

## 5.1 使用步骤

1. 先在`src`创建一个目录`stores`，这个里面存放的是集中状态的文件
2. 在`main.ts`中创建`pinia`实例并进行挂载

```ts
import { createPinia } from 'pinia';
// 创建pinia实例
const pinia = createPinia();
// 通过use函数进行挂载
createApp(App).use(router).use(pinia).mount('#app');
```

3. 编写相关的`store`文件

```js
import { defineStore } from 'pinia';

export const useCountStore = defineStore('counter', {
	//存放的状态数据
  state: () => ({
    count: 0,
  }),

  // 获取器： 类似组件中的计算属性-computed
  getters: {
    // 箭头函数中拿不到那个this
    doubleCount: (state) => state.count * 2,
    // 普通方法可以拿到this
    doubleCountV2(): number {
      return this.count * 10;
    },
  },

  // 行动，用于修改状态的方法，可以是同步也可以是异步
  actions: {
    increment(m: number) {
      this.count += m;
    },
    decrement(m: number) {
      this.count -= m;
    },
    incrementAsync(m: number) {
      setTimeout(() => {
        this.count += m;
      }, 500);
    },
  },
});

```

4. 在组件中使用

```vue
<template>
  <div class="counter">
    <h1>当前的值为：{{ count }}</h1>
    <select v-model.number="selectd">
      <option value="1">1</option>
      <option value="2">2</option>
      <option value="3">3</option>
    </select>

    <button @click="increment">加</button>
    <button @click="decrement">减</button>
    <button @click="incrementOdd">奇数加</button>
    <button>异步加</button>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { useCountStore } from '@/stores/counter';
import { storeToRefs } from 'pinia';

let counterStore = useCountStore();
// storeToRefs 只针对数据进行ref化，不会对函数进行ref化
const { count } = storeToRefs(counterStore);
const selectd = ref(1);

function increment() {
  // 第一种，直接修改
  count.value++;
  // 第二种，patch的方式
  // counterStore.$patch({
  //   count: count.value,
  // });
  // 第三种方式
  // counterStore.increment(selectd.value);
}
function decrement() {
  counterStore.decrement(selectd.value);
}
function incrementOdd() {
  if (count.value % 2 != 0) {
    counterStore.increment(selectd.value);
  }
}
</script>

```

>[!danger]
>1. 如果需要解构store的数据，记得要用`storeToRefs`进行包装，要不然会失去响应式。为啥不用`toRefs`，那是因为`toRefs`会把用不到的很多函数和中间的数据都进行包装成refs，浪费资源，影响性能
>
>2. 修改数据的方式
>	1. 最直接的方式就是直接修改，不借助任何函数等  （这就是官方说的，符合直觉， 推荐使用）
>	2. 通过`$patch`的方式进行多属性批量一次修改
>	3. 通过`store`中定义的`actions` 来进行处理

## 5.2 $subscribe的使用

用这个可以监视`pinia`对应的store中状态的变化，如果数据发生变化，就会进行调用，类似`watch`机制

```js
const counterStore = useCountStore();
couterStore.$subscribe(() => console.log('数据发生了变化'));
// 他还可以接收两个参数
counterStore.$subscribe((mutate, state) => console.log('数据发生变化'，mutate, state));
```

## 5.3  store的组合式写法

在定义`store`文件的时候，上面的写法是一个`options API`的写法，其实vue还支持`composition API`的写法

```js title=coutStore
export function useCountStore() {
	// 属性就直接是数据
	const count = 0;
	function modify() {
		count++;
	}
	// 记得需要return出去
	return {count, modify}
}
```

# 6. 组件之间通信

## 6.1 props

### 6.1.1 父传给子

```vue title=父组件
<template>
<Child :info="数据"/>
</template>
```

子组件通过`defineProps`去接收

```vue title=子组件
<script>
defineProps(['info'])  // 就接收到了父传过来的info
</script>
```

### 6.1.2 子传给父

父亲定义一个函数，把这个函数传给子组件，然后通过函数把数据再传给父组件

```vue title=父组件
<template>
<Child :info="数据" :sendInfo='getInfo'/>
</template>
<script>
function getInfo() {
	console.log('xxxx');
}
</script>
```

```vue title=子组件
```vue title=子组件
<script>
let props = defineProps(['info','sendInfo'])  // 就接收到了父传过来的info
props.sendInfo('给父传的数据');
</script>
```


## 6.2 自定义事件的方式

子传父信息，还可以自定义事件的方式来进行处理

1. 首先，在父组件中给子组件绑定一个自定义事件，未来一旦这个自定义事件被`emit`触发，那么就可以执行父组件中的回调函数

```vue title=父组件
<Child @some-event='callbackInfo'>  // 自定义一个事件`some-event`， 用这个风格

<script>
function callbackInfo(value) {
	// 处理这个value信息
}
</script>
```

2. 子组件中，就需要处理这个自定义事件，那就是在适当的时机去进行`emit`

```vue title=子组件
<button @click='emit('send-toy','要传的数据')'>点击给父组件传值</button>

<script>
// 接收这个自定义函数，并声明这个事件
const emit = defineEmits(['some-event']);
// 接收后就可以用了
</script>
```

## 6.2 mitt   任意组件通信

mitt就类似`消息订阅和发布`，通过广播的方式进行处理

1. 安装`mitt`

```shell
npm install mitt
```

2. 创建`mitt`实例，并暴露出去

```js title=emitter.js
// src/utils/eventBus.js
import mitt from 'mitt';

// 创建并导出 mitt 实例
export default mitt();
```

2. 在组件中使用

```vue title=组件1
<script>
import mitt from '@/utils/emitter';
// 通过mitt绑定函数
mitt.on('send-info', (value) => {
  console.log('@@@@', value);
});

// ㊙️ 需要需要组件卸载的时候，就要解绑
onUnMounted() {
	mitt.off('send-info');
}
</script>
```

```vue title=组件2
<script>
import mitt from '@/utils/emitter';
// ✅ mitt 调用绑定的函数
mitt.emit('send-info', '消息体1111'); 
</script>
```

## 6.3 组件间的v-model

<span class='red-text'>注意：v-model 只能传递数据，不能传递函数</span>

`v-model`的本质是啥？ 本质是一个语法糖，他是父组件向子组件传递一个`modelValue` props，并监听子组件发出的`update:modealValue`事件。

也就是`v-model`核心就是简化以下手动写的代码

```vue title=原生写法
<MyComponent :modelValue='info' @update:modelValue= "info = $event" />
```

然后通过`v-model` 可以简化写成

```vue
<MyComponent v-model='info' />  // 这个info就是数据
```

那么子组件应该如何做呢？
就跟普通的`props`和`自定义事件`是一样的

```vue title=子组件
<template>
  <button @click="triggerParentFunction">触发父组件函数</button>
</template>

<script setup>
import { defineEmits } from 'vue';

// 声明一个自定义事件
const emit = defineEmits(['update:modelValue']);

const triggerParentFunction = () => {
  // 发送自定义事件，父组件可以监听这个事件并执行函数
  emit('update:modelValue', 'Hello from child');
};
</script>
```

在`vue 3.5+` 版本推出了一个`defineModel`宏，在子组件中处理这个回调逻辑

```vue
<template>
  <button @click="triggerParentFunction">触发父组件函数</button>
</template>

<script setup>
// 使用宏
const model  = defineModel();
function triggerParentFunction() {
model.value = '消息体数据等';
}
</script>
```

PS：补充：
可以给一个组件传递多个`v-model` ，这样可以给多个数据进行双向绑定

```vue title=父组件
<UserName
  v-model:first-name="first"
  v-model:last-name="last"
/>
```

```vue title=子组件处理
<script setup>
const firstName = defineModel('firstName')
const lastName = defineModel('lastName')
</script>
```

## 6.4  $attrs  实现祖孙之间的通信

有合适为了避免一层层的`props`的方式传递数据，因为可能中间有的组件不需要这些`props`，但是也得接收，就很麻烦

vue就用`$attrs`来实现相关的通信

```vue title=爷爷组件
<Child :a='数据1' :b='数据2' v-bind='{k-v}' :c='function'>
```

```vue title=子组件（用不到这些属性，直接打包扔给下一层）
<GrandChild v-bind="$attrs" />
```

```vue title=孙组件（直接通过props的方式处理即可）
defineProps(['a','b','c'])
```

## 6.5  $refs 和 $parent

### 6.5.1 $refs

首先我们要知道`ref` 标签中的`ref`属性。具体请看[[01-vue入门#7. 标签中的`ref`属性]]

`ref` 是 父 -> 子的操作
就是子组件暴露出来可以修改的东西，然后父组件调用这个暴露出来的东西然后修改
这个在一个`ref`的情况下可以操作，但是如果是多个子组件，都有`ref`这属性，父组件批量调用子组件暴露出来的东西，应该如何做呢？

答案是 ：`$refs`
他可以将父组件中所有拥有`ref`标签的元素收集起来，然后进行调用

```vue  hl:10
<Child1 ref='child1' />
<Child2 ref= 'child2' />
<button @click=updateAll($refs)>修改所有子元素</button>

<script>
const child1 = ref();
const child2 = ref();

function updateAll(refs) {
	for (let key in refs) {
		refs[key].子组件的属性 = 新值
	}
}

</script>
```

### 6.5.2 $parent

`$parent` 是 子 -> 父的操作

那么`ref` 反向的是啥？ 就是这个`$parent`， 由于在子组件中没有办法给父组件添加属性，所以只能通过`$parent`来进行处理了，但是同样需要父组件暴露出相关信息，子组件才能操作

## 6.6 provide和 inject

实现  **祖孙**之间通信，之前的`$attrs`也可以实现祖孙之间通信，为啥还要有一个`provide`和`inject`呢？
那是因为`$attrs` 会打扰到中间的子组件，`v-bind="$attrs"`， 但是`provide-inject`实现的是祖孙之间直接通信，不会打扰到中间的子组件

具体用法
 1. 祖组件中进行`provide`

```vue
<script>
import {provide} from 'vue';

provide('key', value); // 注意，如果value需要响应式，就传响应式的，可以是对象，可以是函数

</script>
```

 2. 孙组件之间进行`inject`

```vue
<script>
import {inject} from 'vue';

const key = inject('key', 第二个参数可以设置默认值); // 这样就拿到祖组件中的数据了

</script>
```

## 6.7 slots插槽

有时候，一组页面，大体的样式布局一样，只有一些间的核心部分不同，我们不可能这多个页面都对应一个组件，那么有没有一个方法，写一个页面，然后通过父组件，传不同的内容(HTML+样式) 然后直接在子组件中展示呢？

插槽 Slot就派上用场了

### 6.7.1 默认Slot

这是最简单的插槽类型，它没有名字。当你需要一个组件只有一个内容区域时，就可以使用它

- 子组件中使用`slot`标签

```vue hl:3
<template>
  <button class="my-button">
    <slot></slot>
  </button>
</template>
```

- 父组件中进行传递   在父组件中使用子组件时，直接在标签内部写入你想要插入的内容即可。

```vue
<template>
  <div>
    <MyButton>
      点击我
    </MyButton>
    <MyButton>
      <strong>提交</strong>
    </MyButton>
  </div>
</template>
```
创建了两个button，每个内容就是组件标签之间的内容

### 6.7.2 具名Slot

当一个组件有多个内容区域时，就需要给插槽命名，以便父组件能够精确地指定内容应该放在哪里。

1. 在子组件中，给不同的插槽赋予name属性

```vue
<template>
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot> </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</template>
```

2. 在父组件中插入内容，使用 `v-slot:插槽名` 或其简写 `#插槽名` 来指定要插入哪个插槽。

<span class="red-text">注意：指定的插槽名必须写在一个组件或者template上</span>

```vue
<template>
  <PageLayout>
    <template v-slot:header>
      <h1>页眉内容</h1>
    </template>

    <template #default>
      <p>这是默认内容区域</p>
    </template>

    <template #footer>
      <p>版权信息</p>
    </template>
  </PageLayout>
</template>
```

### 6.7.3  作用域插槽

作用域插槽强大，它是子组件负责数据，然后将数据给到父组件，比如让父组件进行样式的调整等

1. 子组件中创建一个带有数据的插槽，在 `<slot>` 标签上绑定数据，这些数据将被暴露给父组件。

```vue hl:4
<template>
  <ul>
    <li v-for="item in items" :key="item.id">
      <slot :item="item"></slot>
    </li>
  </ul>
</template>

<script setup>
const items = [
  { id: 1, text: 'Vue' },
  { id: 2, text: 'React' },
];
</script>
```

2. 父组件中使用这个数据，并将插槽插入数据

使用 `v-slot` 或` #` 语法来接收子组件传递的数据，并通过一个变量来访问。

```vue
<template>
  <MyList>
    <template v-slot="{ item }">
      <li>{{ item.text }}</li>
    </template>
  </MyList>
</template>

// 如果是具名插槽，直接v-slot:name="slotProps"  也可以简写#name="slotProps" 
<template>
  <MyList>
    <template #name="{ item }">
      <li>{{ item.text }}</li>
    </template>
  </MyList>
</template>
```

# 7. 另外常用的一些API

## 7.1 shallowRef和shallowReactive

在vue中`shallowRef`和`shallowReactive`是两种特殊的响应式API。
<span class='rt-b'>创建浅层响应式数据</span>
- 一般一个大的对象或者嵌套太深的对象，如果用`ref`的话，可能会导致所有的属性都拆包成为深度响应式，这样会浪费资源和消耗资源
- `shallowRef`和`shallowReactive`只会在数据的第一层建立响应式追踪

`shallowRef`
- 只让其`.value`的变化具备响应式，而不会对`.value`内部的对象进行响应式处理

```js
import { shallowRef } from 'vue';
const state = shallowRef({
  count: 1,
  nested: {
    a: 1
  }
});
```

只有当你替换`state.value={新的对象}`整个对象时，才会是响应式的，更新`.value.count= 2` 这种就不是响应式的

`shallowReactive`
- 只会给对象的**顶层属性**建立响应式
- 当顶层属性的值被修改时，会触发视图的更新，其他非顶层数据无法成为响应式

## 7.2 readonly 和shallowReadoly

它们的作用都是确保一个对象以及属性不能被修改，保护数据的安全，两者的区别在于**只读的深度**

`readonly` 创建一个==深度只读==代理对象，不能修改里面的任何数据，不管是否是顶层还是嵌套内的都不能修改

`shallowReadonly`  创建一个==浅层只读==的代理对象，只有对象的顶层属性是只读的，内部嵌套的对象和数据都能修改

作用和场景：
- 性能优化：`readonly`会递归遍历所有属，创建只读，会带来性能问题； 如果可以的话，使用`shallowReadonly`
- 与外部库集成： 某些第三方库可能需要对对象内部进行修改，使用`readonly`可以避免某些修改，保护数据安全

## 7.3 toRaw和markRaw

它们的作用就是让你能够脱离vue的响应式

1. **toRaw**
	1. 作用：返回一个响应式的代理对象（reactive或者readonly创建的）的原始数据

```js
import { reactive, toRaw } from 'vue';

const user = reactive({
  name: 'Alice',
  age: 25,
});

const rawUser = toRaw(user);

console.log(rawUser === user); // ❌ false
console.log(rawUser === user.__v_raw); // ✅ true
```

作用：
- 临时脱离响应式：当需要对响应式数据进行操作时，但又不希望这些操作触发视图的更新，可以使用`toRaw`进行包装

```js
import { reactive, toRaw } from 'vue';

const user = reactive({
  name: 'Alice',
  age: 25,
});

const rawUser = toRaw(user);

console.log(rawUser === user); // ❌ false
console.log(rawUser === user.__v_raw); // ✅ true
```

2. **markRaw**

`markRaw`的作用是标记一个对象，使其永远不会被转为响应式代理

```js
import { reactive, markRaw } from 'vue';

const userProfile = {
  name: 'Alice',
};

const user = reactive({
  id: 1,
  profile: markRaw(userProfile), // ✅ 标记 userProfile 为非响应式
});

// 尝试修改顶层属性
user.id = 2; // ✅ 成功，会触发视图更新

// 尝试修改被 markRaw 标记的属性
user.profile.name = 'Bob'; // ✅ 成功，但不会触发视图更新
```

## 7.4 customRef的使用

`customRef` 是vue3提供的高级API，它允许我们自定义`ref`的响应行为。
正常情况使用`ref`，比如输入框双向绑定后，会导致只要数据变化，页面立马会渲染展示，如果频繁的输入的话，会影响性能，那么可以用到`customRef`来自定义处理这个

<span class='rt-b'>底层原理</span>
`customRef`函数接受一个工厂函数作为参数，这个工厂函数会接受两个参数`track`和`trigger`
1. `track`：一个函数，用于追踪依赖。当`customRef`的值在模板或者计算属性中被读取时，你需要调用`track()`来告诉vue- 这个值被使用了，请记录他
2. `trigger`：一个函数，用于触发更新。当`customRef`的值被修改时，需要调用`trigger()`来告诉vue-这个值变化了，请重新渲染依赖它的视图

经典用法：**实现防抖**
假设你想创建一个 ref，但它的值只有在用户停止输入 500 毫秒后才更新。这在处理搜索输入框时非常有用。

```js title=可以写到一个自定义hooks函数中
import { customRef } from 'vue';
// 封装一个 customRef，实现防抖功能
export default function useDebouncedRef(value, delay = 500) {
  let timeout;
  return customRef((track, trigger) => {
    return {
      get() {
        // ✅ 当值被读取时，调用 track() 收集依赖
        track();
        return value;
      },
      set(newValue) {
        clearTimeout(timeout);  // 这个还是很关键的，要不然频繁更新会导致定时器乱套了
        timeout = setTimeout(() => {
          value = newValue;
          // ✅ 当值被修改并延迟后，调用 trigger() 触发更新
          trigger();
        }, delay);
      }
    };
  });
}
```

这样就可以在组件中使用了
```vue
<template>
  <div>
    <input v-model="searchText" placeholder="输入搜索内容..." />
    <p>正在搜索: {{ searchText }}</p>
  </div>
</template>

<script setup>
import { useDebouncedRef } from './useDebouncedRef.js';

// ✅ 使用自定义的防抖 ref
const searchText = useDebouncedRef('');
</script>
```

# 8. vue3一些高级组件

## 8.1 Teleport  

传送门，它将模板内容渲染到DOM树的另一个完全不同的位置，而组件本身的逻辑还在原始位置
使用场景很多
1. 全屏模态框(Modal)  ： 静态框需要覆盖整个屏幕，并且不受父组件的`z-index`样式影响
2. 通知或者提示： 一些消息提醒的组件，不受组件层级的影响
3. 下拉菜单： 下拉菜单需要突破父组件的边界，完全独立于其他内容。

如何使用？
`<Teleport>`组件有两属性： `to`和`disabled`
1. `to` 属性，必须
	1. `to`属性，指定内容要传送到的目标位置，这个值可以是一个CSS选择器字符串(#modal-con)或者一个DOM元素(body)
2. `disabled`属性  - 可选
	1. 默认为 false, 如果为true的话，teleport不起作用，就相当于没写一样

## 8.2 Suspese 

用于处理**异步组件**的加载状态，并在异步操作完成前显示一个加载中的回退内容。

`<Suspense>`的作用：解决**异步组件加载时的用户体验问题**
在没有`<Suspense>`的情况下，如果一个异步组件正在加载，用户可能会看到一个空白页面，或者一个不完整的页面
有了`<Supsense>`，可以提供一个友好的加载页面，可以是一个过场动画，也可以是一个骨架屏或者一个Loading提示等

**如何使用`Suspense`呢？**
有两个插槽
1. `#default` 插槽  用于放置异步组件，当组件加载成功后，这里的内容会被渲染
2. `#fallback`插槽， 用于放置回退内容，也就是加载过程中显示的内容

有两种方式将一个组件成为『异步组件』
1. 使用vue提供的`defineAsyncComponent`函数来完成

```js  title=AsyncComponent.js
// views/AsyncComponent.js
import { defineAsyncComponent } from 'vue';

const AsyncComponent = defineAsyncComponent(() =>
  import('./UserList.vue') // 异步加载的组件 
);
export default AsyncComponent;
```

2. 在`setup`中直接有异步的数据加载等逻辑，会自动成为一个『异步组件』

```vue
<template>
  <div>
    <h2>用户列表</h2>
    <ul>
      <li v-for="user in users" :key="user.id">{{ user.name }}</li>
    </ul>
  </div>
</template>

<script setup>
import { ref } from 'vue';

// 这是一个模拟的异步请求函数
const fetchUsers = () => {
  return new Promise((resolve) => {
    setTimeout(() => {
      const users = [
        { id: 1, name: 'Alice' },
        { id: 2, name: 'Bob' },
      ];
      resolve(users);
    }, 2000); // 模拟网络延迟 2 秒
  });
};

const users = ref([]);

// ✅ 在 setup 函数中等待异步操作完成
const fetchedUsers = await fetchUsers();
users.value = fetchedUsers;
</script>
```

在父组件中使用`<Suspense>`组件

```vue
<template>
  <main>
    <h1>异步组件示例</h1>

    <Suspense>
      <template #default>
        <AsyncComponent />
      </template>

      <template #fallback>
        <div class="loading">
          <p>数据正在加载中...</p>
          <img src="/loading.gif" alt="加载中" />
        </div>
      </template>
    </Suspense>
  </main>
</template>

<script setup>
import AsyncComponent from './views/AsyncComponent';
</script>
```

## 8.3 原生的`<component>`组件

vue中`<component>`组件是一个动态组件，它允许你根据数据来动态的渲染不同的组件
可以简单的理解为：占位符，它本身不渲染任何东西，而是根据`is`属性的值来决定最终是渲染哪个组件

1. 如何使用？
`<componenet>` 组件只有一个核心属性`is`
`is`属性的值可以是:
	 a. 一个组件的名称字符串
	 b. 一个组件的定义对象或者导入的组件

```vue
<template>
  <div>
    <button @click="currentTab = 'Home'">首页</button>
    <button @click="currentTab = 'About'">关于我们</button>

    <component :is="currentTabComponent" />
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import Home from './Home.vue';
import About from './About.vue';

const currentTab = ref('Home');

const currentTabComponent = computed(() => {
  // 根据 currentTab 的值返回对应的组件
  switch (currentTab.value) {
    case 'Home':
      return Home;
    case 'About':
      return About;
    default:
      return Home;
  }
});
</script>
```

正常情况下， `<component>`与`<keep-alive>` 组合使用，相当于一个缓存组件，避免组件在切换时被销毁和重新创建，可以保留组件的状态，并提高性能

```vue
<template>
  <keep-alive>
    <component :is="currentTabComponent" />
  </keep-alive>
</template>
```