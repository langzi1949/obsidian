#前端 #vue #实战

项目技术栈
1. vue3 + vite + TypeScript
2. npm包管理器


# 1. 项目搭建

`pnpm create vite`  然后在Terminal中跟着选择即可，要求是 `vue + vite + ts`

项目生成完毕后，执行以下步骤
1. `cd 项目`
2. `pnpm install`
3. `pnpm run dev`

> [!notes]
> 可以对生成的项目进行裁剪，移除不相干的内容，同时在`package.json`中将`dev`的命令调整`"dev": "vite --open"` 这样就会启动的时候，自动打开浏览器
# 2. 采用Eslint校验代码

1. 首先安装：
`pnpm install eslint -D`    注意：在dev环境安装即可
2. 然后去生成配置文件
`npx eslint --init`  根据相关的问题直接回答就OK了
相关的配置相当的诡异，不看文档和教程压根就不知道是干啥的

# 3. Prettier 进行代码风格统一处理

1. 安装相关的插件

```shell
pnpm install prettier eslint-plugin-prettier eslint-config-prettier -D
```

2. 使用说明

在项目的根目录下创建一个`prettier.config.js`文件

```js title=prettier.config.js
export default {
  semi: false, // 末尾是否添加分号
  singleQuote: true,
  tabWidth: 2,
  trailingComma: "es5",
  printWidth: 80,
  bracketSpacing: true,
  endOfLine: "auto",
  vueIndentScriptAndStyle: false,
};
```

创建一个`.prettierignore`文件

```ymal
# 忽略构建和分发目录
dist/
build/

# 忽略日志文件和依赖
*.log
node_modules/

# 忽略某些特定文件类型
*.min.js
*.lock

# 忽略一些不需要格式化的配置文件
.vscode/
prettier.config.js

```

在`package.json`的script添加一个命令
`"format":"prettier --write ."`

# 4.Stylelint

对css的代码进行检查，因为我这个项目是用vue + scss写的，所以相关配置是`scss`为主

1. 安装

```shell
npm install -D stylelint stylelint-config-standard-scss stylelint-config-recommended-vue stylelint-config-recess-order stylelint-scss postcss-html postcss-scss
```

2. 创建`stylelint.config.js`文件  
	1. 具体看代码
3. 创建 `stylelintignore`文件
4. 添加`script`

```json
{
	"lint:style": "stylelint \"src/**/*.{css,scss,vue}\" --fix",
    "lint:style:check": "stylelint \"src/**/*.{css,scss,vue}\""
}
```

# 5. editorconfig

有时候为了保证团队的风格统一，我们需要进行格式约束，虽然已经有了prettier了，但是还是有必要有editorconfig
因为editorconfig可以支持所有文件类型，同时保持编辑器一致性

```yml 
# http://editorconfig.org
root = true

[*]
indent_style = space
indent_size = 2
end_of_line = lf
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true

[*.md]
trim_trailing_whitespace = false
```

# 6. husky  和 lint-staged

这个是用于Git的hooks函数的配置，lint-staged是用于只处理本次变化的，历史已经处理的文件不需要进行校验

1. 安装`husky`和`lint-staged`

```shell
pnpm install -D husky lint-staged
# 初始化
npx husky init
```
2. 写`lint-staged.config.js`的配置文件

```js
export default {
  'src/**/*.{js,cjs,ts,vue}': (stagedFiles) => [
    'npm run lint:fix',
    `prettier --write ${stagedFiles.join(' ')}`,
  ],
  'src/**/*.{html,css,scss,json}': (stagedFiles) => [
    `prettier --write ${stagedFiles.join(' ')}`,
  ],
}
```

3. 写`pre-commit` hooks文件

```shell
#!/bin/sh

echo "Husky pre-commit hook is running..."
npx lint-staged
```

> [!danger]
> 有时候需要给`pre-commit`添加执行权限`chmod +x ./.husky/pre-commit`

这样，就会在`git commit`的时候会进行执行相关的校验检查逻辑，保证代码没有明显的问题

# 7. commitLint

有时候，我们要保证大家的`git commit` 的message保持同一个风格，所以需要进行相关的配置

```shell
pnpm install -D @commitlint/cli @commitlint/config-conventional
```
然后配置文件

```js title=commitlint-config.js
const Configuration = {
  extends: ['@commitlint/config-conventional'],
  formatter: '@commitlint/format',
  rules: {
    // 强制 body 最多为 50 个字符
    'body-max-line-length': [2, 'always', 50],
    // 强制 header 格式为 <type>(<scope>): <description>
    'header-full-stop': [2, 'never', '.'],
    // 强制 type 必须在以下列表中
    'type-enum': [
      2,
      'always',
      [
        'feat', // 新功能
        'fix', // bug 修复
        'docs', // 文档修改
        'style', // 格式修改（不影响代码运行）
        'refactor', // 代码重构
        'perf', // 性能优化
        'test', // 测试相关
        'ci', // CI/CD 相关
        'chore', // 杂项，不属于上述类型
        'revert', // 版本回退
        'build', // 构建相关
      ],
    ],
  },
  ignores: [(commit) => commit === ''],
  defaultIgnores: true,
  helpUrl:
    'https://github.com/conventional-changelog/commitlint/#what-is-commitlint',
}

export default Configuration

```

配置最终的`git hooks`  文件为`commit-msg`

```bash
#!/usr/bin/env sh

echo "Husky commit-msg hook is running..."
npx --no-install commitlint --edit ${1}
```

# 8. 强制统一使用pnpm包管理工具

团队在开发的时候，需要统一包管理工具，因为不同工具下载的依赖包可能有点问题，会导致一些无法预知的问题

通过`npm`的生命周期hooks`preinstall`来进行校验


```json title=package.json
// package.json
{
  "name": "my-project",
  "version": "1.0.0",
  "scripts": {
    "preinstall": "npx only-allow pnpm"
  },
  "devDependencies": {
    "only-allow": "^1.1.1"
  }
}
```

这样其实就OK了，但是有时候我们想要一个单独的shell脚本来输出相关的一格式化的提示信息

```shell
#!/bin/sh

# 获取当前使用的包管理器
PACKAGE_MANAGER=$(basename "$0")

echo "$PACKAGE_MANAGER"

# 检查当前是否是直接运行的脚本
if [ "$PACKAGE_MANAGER" = "preinstall.sh" ]; then
  echo "✔ Preinstall script is being run directly. Exiting successfully."
  exit 0
fi

# 判断是否是 pnpm
if [ "$PACKAGE_MANAGER" = "pnpm" ]; then
  echo "✔ Correct package manager detected: pnpm."
  echo "✔ Proceeding with installation."
  exit 0
else
  # 如果不是 pnpm，则输出格式化的错误信息
  echo ""
  echo "❌ Error: This project requires pnpm to install dependencies."
  echo "❌ Detected: $PACKAGE_MANAGER"
  echo ""
  echo "Please use the following command instead:"
  echo "  pnpm install"
  echo ""
  # 以非零状态码退出，阻止安装
  exit 1
fi
```

在`package.json`中添加script

`"preinstall": "sh ./scripts/preinstall.sh",`

# 9. 集成Element-plus组件库（按需引入）

集成`Element-plus`有3种方式
1. 最暴力的全局引入
	1. 这样的话，就无需在用到的时候直接使用，不用单独import
	2. 缺点：会导致包的体积很大，导致页面的性能变差
2. 手动引入
	1. 在用到的地方单独的`import`，这也是我们之前最常见的方式
	2. 优点：更加直观，且可控，相对而言import就会有点多
3. 按需引入
	1. 需要借助插件来实现，将需要的组件和图标都放在一个单独的文件里面
	2. 然后通过构建工具进行处理即可

第一种和第二种很简单，想尝试一下第三种的方式
1. 安装相关包
```shell
pnpm install -D unplugin-vue-components unplugin-auto-import unplugin-icons @iconify/json
```
2. 然后在`vite.config.js`中进行配置
```js
import AutoImport from 'unplugin-auto-import/vite'
import IconsResolver from 'unplugin-icons/resolver'
import Icons from 'unplugin-icons/vite'
import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'
import Components from 'unplugin-vue-components/vite'
import { fileURLToPath, URL } from 'url'
import { defineConfig } from 'vite'

import vue from '@vitejs/plugin-vue'

// https://vite.dev/config/
export default defineConfig({
  plugins: [
    vue(),
    AutoImport({
      // 自动导入 Vue 相关函数，如：ref, reactive, toRef 等
      imports: ['vue', 'vue-router', 'pinia'],
      resolvers: [
        // 自动导入 Element Plus 相关函数，如：ElMessage, ElMessageBox... (带样式)
        ElementPlusResolver(),
        // 自动导入图标组件
        IconsResolver({
          prefix: 'Icon',
          enabledCollections: ['ep'], // 关键：自动导入 ep 图标
        }),
      ],
      dts: true, // 生成类型声明文件,
      eslintrc: {
        enabled: true, // 生成 .eslintrc-auto-import.json
      },
    }),
    // 按需导入组件
    Components({
      resolvers: [
        // 自动注册 Element Plus 组件
        ElementPlusResolver({
          importStyle: 'sass', // 使用 sass 样式，也可以用 'css' 或 false
        }),
        // 自动注册图标组件
        IconsResolver({
          prefix: 'Icon',
          enabledCollections: ['ep'], // Element Plus 图标集
        }),
      ],
      // 所有我们自己项目的组件也可以自动导入
      dirs: ['src/components', 'src/layout/components'],
      dts: true, // 生成类型声明文件
    }),

    // 图标插件
    Icons({
      autoInstall: true,
    }),
  ],
  // 如果使用 sass 样式，需要配置
  css: {
    preprocessorOptions: {
      scss: {
        additionalData: `@use "@/styles/element/index.scss" as *;`,
      },
    },
  },
  resolve: {
    alias: {
      '@': fileURLToPath(new URL('./src', import.meta.url)),
    },
  },
})

```

最终会生成三个文件`components.d.ts`、`auto-imports.d.ts`以及`.eslintrc-auto-import.json`，这些文件主要是针对ts的一些declare，同时为了vscode编码有提示和ts能够识别相关语法，需要配置`tsconfig.js`
```js
// ....
"types": ["element-plus/global"],
"include": [
    "src/**/*.ts",
    "src/**/*.tsx",
    "src/**/*.vue",
    "src/**/*.d.ts",
    "auto-imports.d.ts",
    "components.d.ts"
  ]
```

在`eslint.config.js`文件中同样需要加上相关配置
```js
const require = createRequire(import.meta.url)
const autoImport = require('./.eslintrc-auto-import.json')
// 在js的配置中添加
 ...autoImport.globals,
```

> [!danger]
> 需要注意的是目前element-plus icons-vue  最终组件的名称命名格式：`IconEp{组件名}`，这是因为我们使用了`unplugin-icons`， 需要对`element-plus`的图标有一个namespace
> 
> 同时：比如使用组件的时候，需要用到icon属性来设置icon，直接写自动导入插件无法识别，比如 `<el-button icon='Edit'/>`  这种情况无法识别`Edit`，即使使用`IconEpEdit`也是不可以的，官方给出的解决方式是==组件插槽==的方式  
> 1. default 插槽，可以直接 `<el-button type="primary"><icon-ep-edit /></el-button>`
> 2. 具名插槽，需要用一个`template`进行包裹，`<template #prefix><icon-ep-user /></template>`
> 
> ❗️在Edge浏览器中如果安装了`vue developer tools`扩展，那么使用`el-element`自动导入的时候，会出现一个问题，在console中总是有一些错误，那是因为Edge`vue developer tools`扩展已经过时了，需要到chrome浏览器中下载最新版本的
> 
> 还可以用一个包 `@iconify/vue`，运行时的图标库，后续有时间可以尝试一下

<span class='rt-b'>注意：</span>
1. 如果要覆盖`Element-plus`的样式，在`@apply`方式发现不行后，可以采用`:deep`穿透的方式试试

```css
:deep(.el-input__wrapper.is-focus) {
  box-shadow: 0 0 0 1px red inset !important;
} 
```

2. 如果使用`ElMessage`这些全局的组件，会自动引入`element-plus/theme-chalk/base.css`， 这个是一个重置的样式文件，会把之前的样式都重置掉，所以，我们需要手动引入这个文件，放在`index.scss`的上面
3. 未来关于样式的问题，直接先怀疑是自动引入导致的，然后看到底是哪个css，这样可以针对性的排查


> [!tips]
> 使用`unplugin-icons` 可能无法处理一些图标的属性，因为它的工作原理
> 3. 将组件标签直接替换为 SVG 代码。原生的SVG没有那些属性，比如size等
> 4. ` unplugin-vue-components`: 负责导入组件。它处理的是组件级别的逻辑，所以它能正确地将 prop 传递给组件，由组件自身来处理。
> 5. ` unplugin-icons`: 负责代码替换。它处理的是文件级别的逻辑，它将一个组件标签替换为原始的 SVG 标签，这个标签没有 prop 的概念。


# 10. 设置别名@

1. 在`vite.config.js`文件中添加相关配置

```js
  resolve: {
    alias: {
      '@': fileURLToPath(new URL('./src', import.meta.url)),
    },
  },
```

2. 在`tsconfig.app.json`中添加相关配置 <span class='rt-b'>如果不加，会导致代码没有提示</span>

```json
/*别名设置*/
"baseUrl": "./",
"paths": {
  "@/*": ["src/*"]
}
```

# 11. 设置统一的import 顺序

有时候大家import相关模块的时候，顺序很随意，所以我们需要约束，需要大家保持统一

1. 安装模块包

```shell
pnpm install -D eslint-plugin-simple-import-sort
```

2. 在`.eslint.config.js`中配置

```js
import simpleImportSort from 'eslint-plugin-simple-import-sort'

{
    plugins: {
      'simple-import-sort': simpleImportSort,
    },
    rules: {
      'simple-import-sort/exports': 'error',
      // 配置排序规则
      'simple-import-sort/imports': [
        'error',
        {
          groups: [
          // 直接用一个数组接收，如果多个数组的话，会导致有空行分隔符
            // Node.js 内置模块
            // 第三方包 (以字母开头)
            // 别名导入，比如 @/ 或 ~/
            // 父级相对路径导入
            // 同级相对路径导入
            // css 导入 (以 .css 结尾)
            [
              '^node:',
              'npm:',
              '^[a-zA-Z]',
              '^@/',
              '^~/',
              '^#',
              '^\\.\\.(?!/?$)',
              '^\\.\\./?$',
              '^\\./(?=.*/)(?!/?$)',
              '^\\.(?!/?$)',
              '^\\./?$',
              '^.+\\.s?css$',
            ],
          ],
        },
      ],
      // 设置新行行为
      'padding-line-between-statements': 'off',
      'import/newline-after-import': 'off',
    },
  },
```

# 12. 引入vue-router 和 pinia

这一块请参考 [[02-vue实战#4. 路由]]  和 [[02-vue实战#5 Pinia]]

# 13. 引入环境变量

类似Java中的dev、test、prod等。
社区规范来说，在项目的根目录下创建四个文件，一看就懂
- `.env`
- `.env.development`
- `.env.test`
- `.env.production`

自定义的变量，必须以`VITE_`开头才行，要不然无法暴露
```yaml title=.env.development
# 变量必须以VITE_为前缀才能暴露给外面使用
NODE_ENV = 'development'
VITE_APP_TITLE = '魔方'
VITE_APP_BASE_API = '/dev-api'
VITE_SERVER = "http://localhost:3001"
```

如何使用呢？

通过`import.meta.env` 来访问变量

```vue
<script setup>
import { onMounted } from 'vue';

onMounted(() => {
  // 访问环境变量
  const apiUrl = import.meta.env.VITE_API_URL;
  
  // 打印当前模式
  console.log('当前环境模式:', import.meta.env.MODE);
  
  console.log('API 地址:', apiUrl);
  
  fetch(apiUrl + '/data')
    .then(response => response.json())
    .then(data => console.log(data));
});
</script>
```

# 14. 集成SaSS

```shell
pnpm install -D sass
```

然后在`vue`文件中开启`lang='scss'`，当然，也可以使用独立的`scss`文件。

```css
// src/styles/variable.scss
$primary-color: #3498db;
```

使用
```css hl:3
<style lang="scss" scoped>
// 使用 as * 别名，无需命名空间
@use '@/styles/variable' as *;

.container {
  // 直接访问变量
  background-color: $primary-color;
}
</style>
```

整一个全局的样式文件


# 15. SVG的处理和组件封装

一般来说前端处理SVG有三种方式
1. 直接使用`svg`标签
	1. 优点：简单，无需依赖
	2. 缺点：代码冗余，难以维护
2. 使用`<img src='icon.svg'>`
	1. 优点：简单，代码干净
	2. 缺点：不能用css样式调整颜色等
3. 封装成组件
	1. 通过自动化工具`vite-plugin-svg-icons` 扫描这些svg文件，并将它们打包成一个SVG雪碧图，然后通过`<svg>标签的<use>元素来引用雪碧图的图标`

---
1. 安装`svg`插件

```shell
pnpm install vite-plugin-svg-icons -D
```

2. 在`vite.config.js`中配置插件

```js
 // SVG的处理
    createSvgIconsPlugin({
      // 指定要缓存的图标文件夹
      iconDirs: [resolve(__dirname, 'src/assets/icons')],
      // 指定symbolId格式
      symbolId: 'icon-[dir]-[name]',
    }),
```

3. 入口`main.ts`文件中导入

```js
import 'virtual:svg-icons-register'
```

# 16. Tailwind CSS的集成

目前tailwind css 是4.x版本，之前网上很多的东西都是3.x版本的，4.x版本简化了很多配置

> [!notes]
> 其实，不建议在`tailwindcss 4.x`版本中配合`scss`使用 ， 但是可以在同一个`vue`文件中，创建两个`<script>`标签，一个是用于`scss`的，一个是用`原生css`的，这样就可以享受到`scss`的便利，同时`tailwind`的很多拓展也可以在`vue`中实现

1. 安装包

```shell
pnpm install tailwindcss @tailwindcss/vite 
```

2. 配置`vite.config.js`

```js
import { defineConfig } from 'vite'
import tailwindcss from '@tailwindcss/vite'
export default defineConfig({
  plugins: [
    tailwindcss(),
  ],
})
```

3. 写一个`css`文件，可以在任意css中，但是记得需要import

```css
@import "tailwindcss";
```

然后就可以使用了，但是可能会有一些问题

1. `tailwind.config.js`   这个文件我不太确定是否需要，反正我项目中是已经加上的，里面的配置相对比较简单
2. 自定义的颜色等配置，直接写在刚刚`@import 'tailwindcss'` 这个css中就可以

```css
@import 'tailwindcss';

:root {
  --el-color-primary: theme(colors.blue.500);
  --el-color-success: theme(colors.green.500);
  --el-color-warning: theme(colors.yellow.500);
  --el-color-danger: theme(colors.red.500);
}

@theme {
  --color-primary: var(--el-color-primary);
  --color-success: var(--el-color-success);
  --color-warning: var(--el-color-warning);
  --color-danger: var(--el-color-danger);
}

@layer base {
  .el-button {
    @apply rounded-lg font-semibold;
  }
}
```

使用换css的一些变量的值，可以采用

```js
document.documentElement.style.setProperty('--el-color-primary', 'red')
```

# 17. vite-plugin-mock  (Mock)

`mock`很好理解，就是不去真实的请求后端的接口，然后通过Mock的方式给出API的返回数据即可

1. 安装`vite-plugin-mock`

```shell
pnpm install vite-plugin-mock
```

2. 然后配置`vite.config.js`

```js
import { UserConfigExport, ConfigEnv } from 'vite'

import { viteMockServe } from 'vite-plugin-mock'
import vue from '@vitejs/plugin-vue'

export default ({ command }: ConfigEnv): UserConfigExport => {
  return {
    plugins: [
      vue(),
      viteMockServe({
        // default
        mockPath: 'mock',  // 找mock以及mock下所有的子目录中的mock请求
        enable: true,
      }),
    ],
  }
}
```

3. 在`mock`目录下，建一些ts文件，代表某些请求就行

```ts
import { MockMethod } from 'vite-plugin-mock'

function createUserList() {
  return 返回信息;
}

export default [
  {
    url: '/api/user/login',
    method: 'post',
    response: ({ body }) => {
      const { email, password } = body
      const checkUser = createUserList().find(
        (it) => it.email === email && it.password === password
      )
      if (!checkUser) {
        return {
          code: 201,
          msg: '账号或者密码错误',
          data: null,
        }
      }
      const { token } = checkUser
      return {
        code: 200,
        msg: 'success',
        data: token,
      }
    },
  },
  {
    url: '/api/user/info',
    method: 'get',
    response: ({ headers }) => {
      const token = headers.token
      const checkUser = createUserList().find((it) => it.token === token)
      if (!checkUser) {
        return {
          code: 401,
          msg: '获取用户信息失败，请重新登录',
        }
      }

      return {
        code: 200,
        msg: 'success',
        data: checkUser,
      }
    },
  },
] as MockMethod[]

```

但是好像有一个坑，不知道如何设置动态设置HttpStatusCode，未来解决。 
好像找到一个方案： 通过`rawResponse`的方式

# 18. 二次封装axios

虽然通过axios能够实现API请求，但是实际开发中我们需要将axios进行二次封装
1. 使用请求拦截器，可以在请求拦截器中处理一些公共的逻辑，比如统一处理Header等
2. 使用响应拦截器，可以在响应拦截器中处理返回的结果，同时处理一些异常情况

这边不贴代码了，直接看代码即可。

# 19. 统一API管理

将请求后端API的接口统一管理，这样会方便处理

在根目录下创建`api`目录，然后在里面针对不同的api类型创建不同的目录，比如`user`、`dashboard`、`product`等

1. 比如先写一个`user/index.ts`  这个里面都是`user`相关的api

```ts
import request from '@/utils/request'
import type { LoginRequest, LoginResponse, UserInfoResponse } from './type'

const API = {
  LOGIN_URL: '/user/login',
  USERINFO_URL: 'user/info',
} as const

export const reqLogin = (params: LoginRequest) =>
  request.post<any, LoginResponse>(API.LOGIN_URL, params)

export const reqUserInfo = () =>
  request.get<any, UserInfoResponse>(API.USERINFO_URL)

```

> [!danger]
>  针对接口异常，可以不同接口异常后续的处理，比如弹窗等逻辑，需要调整

2. 使用

```ts
import { reqLogin } from '@/api/user'
async function login() {
  const data = await reqLogin({ email: 'zmglove@126.com', password: '111111' })
  let token = data.data
  console.log(token)
}
```

# 20. layouts 和 pages

正常来说，项目中会有两个目录`layouts`和`pages`或者叫`views`
1. `layouts`
	1. 这个主要写的就是整个的布局，相当于项目的『骨架』结构，比如是左右布局，上下布局，大布局里面又分小布局等
	2. 它主要关注的『页面』整体的『样子和形状』
2. `pages`
	1. 它就是一些实实在在用户看到的页面，这个page里面准确来说就是一个路由对应的页面
	2. 每个page不包含那些公共的东西，比如侧边栏，导航栏等，它关注的就是自身应该呈现的内容

# 21. 导入字体

有时候，页面展示的字体需要我们单独指定，所以需要保证字体能否在浏览器上正常显示

1. 安装字体
	1. 从`Google Fonts`去下载相关的字体，正常情况下需要下载所有可能用到所有型号的字体
	2. <span class='rt-b'>注意</span> :  有时候下载出来的字体文件是`VariableFont_wght.tff` 这个表示是可变字体，他可能不需要单独下载bold、semibold等文件，如果不行的话，还是需要导入多个型号的文件
2. 将字体文件放入项目的`assets/fonts`目录下，进行统一管理
3. 写一个字体css文件`fonts.scss`

```scss
/* src/styles/fonts.scss */

/* Regular (400) */
@font-face {
  font-family: 'Noto Sans SC';
  src: url('../assets/fonts/NotoSansSC-Regular.woff2') format('woff2');
  font-weight: 400;
  font-style: normal;
}

/* Bold (700) -- 如果需要的话*/ 
@font-face {
  font-family: 'Noto Sans SC';
  src: url('../assets/fonts/NotoSansSC-Bold.woff2') format('woff2');
  font-weight: 700;
  font-style: normal;
}
nt-style: normal;
}
// ...以此类推，定义所有需要的字重
```

4. 将其引入到主css文件`index.scss`中

```scss 
@use './fonts.scss';
```

5. 设置html和body的fonts，把字体加进去

```scss
html,
body {
  font-family: 'Nunito', sans-serif;
}
```


# 22. 组件缓存

为什么需要组件缓存？ 在页面切换的时候，再切回来会发现上一个变化的数据又被初始化或者重新渲染了。
所以，我们需要在页面切换的时候，保持数据不变，方便我们来回进行操作

`<keep-alive>`组件的使用

# 23. 如何跳转到外部链接
1. 如果是在页面处理的话，比较简单，就相当于是`a`标签的处理而已
2. 如果是在`menu`菜单里面的话，估计稍微有点麻烦 

直接给`el-menu`添加事件`select`，然后通过API路由的方式进行处理吧






