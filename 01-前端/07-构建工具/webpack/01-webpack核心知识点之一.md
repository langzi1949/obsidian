#前端  #webpack #构建

# 1. 核心的5个概念

1. **Entry**
	1. 入口，提示webpack以哪个文件作为入口起点开始打包，分析构建内部依赖图
2. **Output**
	1. 输出，告诉webpack打包后的资源bundles输出到什么地方，如何命名
3. **Loader**
	1. 加载器，让webpack能够去处理那些非JavaScript文件（因为webpack自身只理解Js）
4. **Plugins**
	1. 插件 丰富的插件可以让webpack执行更多的任务，一般插件主要在包优化、压缩、定义一些环境变量等
5. **Mode**
	1. 模式  告诉webpack当前处在什么模式下，类似Java的dev、prod等
	2. 主要有三种
		1. `none`  关闭所有webpack内置优化功能，一般大家不用
		2. `development`  开发模式，启动快速、非压缩代码、启动Source Map等方便调试
		3. `production`    生产模式， 启用代码压缩和混淆，启动Tree Shaking来移除未使用的代码、生成确定的hash值来用于浏览器缓存策略等

# 2. webpack的初体验

```shell
# 在项目的根目录下，初始化一个npm项目 -y表示跳过所有提问，直接创建默认的package.json文件
npm install -y 
# 安装webpack  -D 表示开发环境
npm install webpack webpack_cli -D
# 运行指令， 以./main.js作为入口文件开始打包，打包最终输出到：./build/built.js
npx webpack ./main.js --output-path ./build --output-filename built.js --mode=development
``` 

```
--mode=development  代表的是开发模式
--mode=production   代表的是生产模式
```

> [!question]
> Q：webpack4.x 和webpack5.x中webpack-cli的命令的区别
> A：webpack5.x为了提升一致性和减少歧义而进行webpack-cli的升级处理
> - webpack4中 `webpack -o <path>` 表现的更加智能，或者说更加模糊，它的处理逻辑就是 `-o` 的路径如果是一个文件名(比如`.js`结尾)结束，webpack4会推断是同时输出目录和文件名
> - 但是在webpack5中，CLI变得更加严格和规范，他的处理逻辑就是`-o`不管是什么值，他都被理解为`output.path`(输出目录)，默认的文件名为`main.js`
> 
> webpack能够处理js/json资源，不能处理css/img等其他资源
> 生产模式比开发模式多一个压缩js的功能
> 

# 3. 处理HTML资源

引入webpack的插件
```shell
npm install --save-dev html-webpack-plugin
```

在`webpack.config.js`中处理

```js
const HtmlWebpackPlugin = require('html-webpack-plugin');
// 最后在plugins中处理
plugins: [
    // 处理html资源
    new HtmlWebpackPlugin({
	  // 以./src/index.html为模板处理
      template: './src/index.html',
      // 最终输出index.html
      filename: 'index.html',
    }),
    // 可以设置多个html资源
    new HtmlWebpackPlugin({
      template: './src/about.html',
      filename: 'about.html',
      chunks: ['about']
    })
]    
```

# 4.  采用`webpack.config.js`配置文件进行处理

在项目根目录中中创建`webpack.config.js`文件，用于webpack的启动脚本

```js
// webpack.config.js文件
// 用nodejs的path模块，用于获取绝对路径
const { resolve } = require('path');
module.exports = {
  // webpack的配置
  // 入口
  entry: './src/index.js',
  // 输出
  output: {
    // 文件名
    filename: 'built.js',
    // 输出路径
    path: resolve(__dirname, 'build'),
  },
  // loader配置
  module: {
    rules: [],
  },
  // plugins的配置
  plugins: [],
  // 模式
  mode: 'development',
};
```

核心的骨架就是这5个属性，代表了webpack的核心概念，最后运行`npx webpack` 即可，它会自动读取`webpack.config.js`文件，然后以此作为配置脚本进行构建。

>[!tips]
>不一定配置脚本的名称叫`webpack.config.js`，可以是其他自定义的名称，然后`npx webpack --config webpack.custom.js`

# 5. 处理css样式资源

准确来说，一般的样式资源有`CSS、Less、Sass、Scss、Stylus`等不同的样式资源
由于webpack本身不支持样式资源的打包和构建，所以就需要借助webpack中的`Loader` 来帮助wepack解析样式资源

## 5.1 安装`css-loader`和`style-loader`

```shell
npm install --save-dev css-loader style-loader
```

- `css-loader`是处理解析css文件，将css资源编译成commonjs的模块到js中
- `style-loader`  将js中的css通过创建style标签添加到HTML文件中

## 5.2 `index.js`中需要引入`css`资源

```js
import './styles/index.css';
```

## 5.3 `webpack.config.js`中添加loader

```js
module.exports = {
  module: {
    rules: [
      {
	    // 这个是正则表达式，处理css文件
        test: /\.css$/i,
        // 这个是采用哪些loader，从右到左或者从下到上的执行顺序
        use: ["style-loader", "css-loader"],
      },
    ],
  },
};
```

> [!danger]
> 需要注意的是，有时候死活不行，会报一个错`Module parse failed: 'import' and 'export' may appear only with 'sourceType: module' (2:0) File was processed with these loaders:`
> 解决办法：
> 1. 不用import的ES6 modules的方式，而改成commonjs的`require`，比如`require('./styles/index.css')`
> 2. 安装`babel-loader`去解析
> 	1. `npm install --save-dev @babel/core @babel/preset-env babel-loader`
> 	2. 在根目录下创建`babel.config.js`
> 	3. 然后在`webpack.config.js`中添加相关loader

- 根目录创建`babel.config.js`

```js
module.exports = {
  presets: [
    ['@babel/preset-env', {
      targets: {
        browsers: ['> 1%', 'last 2 versions']
      }
    }]
  ]
};
```

- `webpack.config.js`中添加`babel-loader`的配置

```js
module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env']
          }
        }
      },
    ]
}
```

# 6.  处理less样式资源

很简单，和处理css的逻辑是一样的
## 6.1 安装`less`和`less-loader`

```shell
npm install less less-loader --save-dev
```

## 6.2 `index.js`中引入less资源

```js
import('./less/index.less');
```

## 6.3 `webpack.config.js`中添加loader

```js title=webpack.config.js hl:7
{
        test: /\.less$/i,
        use: [
          // compiles Less to CSS
          'style-loader',
          'css-loader',
          'less-loader',
        ],
      },
```

# 7. 处理sass样式资源

处理和less一样
## 7.1 安装`sass`和`sass-loader`

```shell
npm install sass-loader sass --save-dev
```

## 7.2 `webpack.config.js`中添加loader

```js
{
        test: /\.(scss)|(sass)$/i,
        use: [
          // compiles Less to CSS
          'style-loader',
          'css-loader',
          'sass-loader',
        ],
      },
```

# 8. 处理图片资源

在之前的`webpack4`时，处理图片的资源通过`file-loader`和`url-loader`进行处理，而且分为两种情况，一种是处理css中引用的图片资源，第二种是处理HTML中引入图片资源  这两个方式是不一样的，如果遇到的话，请执行GPT处理

现在`webpack5`已经将两个loader的功能内置到webpack中了，现在只需要简单配置即可处理图片资源。
在实际的开发中，为了优化图片的处理，会将较小的图片直接转为base64的方式，以减少资源的请求，减轻服务器的资源。

## 8.1 处理css中引入的图片资源

针对这个，我们可以直接使用，无需其他配置，但是一般我们会优化，将较小的图片直接转为base64
在`webpack.config.js`中的loader规则中添加相对应的处理

```js webpack-config.js
{
	test: /\.(png|jpe?g|gif|webp|svg)$/i,
	type: 'asset',
	parser: {
		dataUrlCondition: {
		// 小于10kb的图片就会转为base64格式
		maxSize: 10 * 1024,
		},
	},
},
```

## 8.2 处理HTML中引入的图片资源

如果在`html`文件中使用`<img src="./images/001.jpeg" alt="" />`，这种方式直接用webpack进行打包的话，它不会处理，这样导致的问题就是最终没有办法找到这个图片，因为它没有处理。
所以，需要一个特定的loader去解析HTML的内容，并找到其中的资源引用

- 步骤1： 安装所需的Loader和插件

`npm install --save-dev html-loader html-webpack-plugin`

- 步骤2：处理webpack.config.js

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  // ... 其他配置
  module: {
    rules: [
      // 1. html-loader：处理 HTML 文件，将图片等资源路径转换为模块
      {
        test: /\.html$/,
        use: ['html-loader']
      },
      // 2. Asset Modules：处理图片文件
      {
        test: /\.(png|svg|jpg|jpeg|gif)$/i,
        type: 'asset/resource', // 将图片作为单独的文件输出
        generator: {
          // 自定义输出文件名，保证每次构建都生成新的文件名以解决缓存问题
          filename: 'images/[name].[hash:8][ext]'
        }
      }
    ]
  },
  plugins: [
    // 使用 HtmlWebpackPlugin，它会根据模板文件生成最终的 index.html
    new HtmlWebpackPlugin({
      template: './src/index.html', // 指定你的 HTML 模板文件
      filename: 'index.html'
    })
  ]
};
```

# 9. 修改输出的文件目录

如果不处理的话，会导致所有的文件都输出在一个目录下，这样很混乱，所以需要按照`src`编写的输出目录

针对CSS的文件，需要使用一个插件`mini-css-extract-plugin`来使用，不用原生的`style-loader` 这个是会将css资源直接通过`<style>`标签的方式嵌入到HTML文件中。

图片资源的话，处理方式就是
```js webpack.config.js hl:10-12
{
        test: /\.(png|jpe?g|gif|webp|svg)$/i,
        type: 'asset',
        parser: {
          dataUrlCondition: {
            // 小于10kb的图片就会转为base64格式
            maxSize: 10 * 1024,
          },
        },
        generator: {
          filename: 'static/images/[hash:10][ext][query]',
        },
      },
```

# 10. 处理字体图标资源

字体图标文件比较特殊，首先我们需要找到字体文件`ttf`、`woff`、`woff2`等文件。
将其放在`fonts`目录下，然后`css`文件进行引入这个字体文件

```css title=iconfont.css
@font-face {
  font-family: 'iconfont'; /* Project id 4999400 */
  src: url('../fonts/iconfont.woff2?t=1755167282822') format('woff2'),
    url('../fonts/iconfont.woff?t=1755167282822') format('woff'),
    url('../fonts/iconfont.ttf?t=1755167282822') format('truetype');
}

.iconfont {
  font-family: 'iconfont' !important;
  font-size: 16px;
  font-style: normal;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.icon-icon_safelp-4:before {
  content: '\e605';
}

.icon-service:before {
  content: '\e606';
}

.icon-feedback:before {
  content: '\e607';
}
```

正常的写代码，在`index.js`中导入这个`css`文件。
```js title=index.js
import('./style/iconfont.css')
```

最后，在`webpack.config.js`文件中添加loader

```js
{
        test: /\.(woff|woff2|eot|ttf|otf)$/i,
        type: 'asset/resource',
        generator: {
          // 指定字体文件的输出路径和名称
          // 例如，所有字体文件都会被放在 dist/fonts 目录下
          filename: 'static/fonts/[name].[hash:10][ext]',
        },
      },
```


> [!tips]
> 上面用到的`iconfont`是阿里出的字体图标库[[https://www.iconfont.cn]]


# 11. 处理其他资源

开发中可能还会存在一些其他资源，如音频、视频等文件，我们也需要进行处理

其实这些资源webpack5不需要配置就能处理，但是我们需要将这些文件统一管理输出到一个目录下，比如`media`下

直接罗列出哪些资源需要处理的，统一在`webpack.config.js`中处理即可

```js title=webpack.config.js
{
	test: /\.(mp3|mp4|avi)$/i,
	type: 'asset/resource',
	generator: {
	  // 统一输出到static/meida目录下
	  filename: 'static/media/[name].[hash:10][ext]',
	},
},
```


# 12. 处理JS资源

为啥需要单独的处理JS资源呢？
- 兼容性问题，针对不同的浏览器进行不同的处理
- 代码规范检查

## 12.1 Eslint 代码规范检查

### 12.1.1 配置文件
- `.eslintrc.*` : 新建文件，位于项目根目录
	- `.eslintrc`
	- `.eslintrc.js`
	- `.eslintrc.json`
- 也可以在`package.json`中`eslintconfig` 进行配置，不需要创建文件

但是大家一般都使用新建文件的方式进行处理。
当前在eslint的8.x版本只有，推荐使用的是`mjs`文件，放在项目的根目录下。

```js title=eslint.config.mjs
import globals from 'globals';
import tseslint from 'typescript-eslint';
import { defineConfig } from 'eslint/config';
import eslint from '@eslint/js';

export default defineConfig([
  // 第一个配置对象，定义文件、环境和解析器，并添加自定义规则
  {
    files: ['**/*.{js,mjs,cjs,ts,mts,cts}'],
    languageOptions: {
      globals: {
        ...globals.browser,
        ...globals.node,
      },
      parserOptions: {
        ecmaVersion: 'latest',
        sourceType: 'module',
      },
    },
    rules: {
      'no-console': 'warn',
      'no-var': 'error',
      indent: ['error', 2],
      quotes: ['error', 'single'],
      semi: ['error', 'always'],
    },
  },
  // 第二个配置对象：集成 ESLint 的推荐规则
  eslint.configs.recommended,
  // 第三个配置对象：集成 TypeScript ESLint 的推荐规则
  tseslint.configs.recommended,
]);
```

### 12.1.2 安装相关模块

```shell
npm install --save-dev eslint eslint-webpack-plugin
```

### 12.1.3 webpack中进行配置

采用的是插件的方式，在webpack4的时候，采用的是loader的方式，现在webpack5为了方便管理，将其包装成插件了

```js title=webpack.config.js
// 2. 添加插件实例到 plugins 数组
new ESLintPlugin({
  // 告诉插件要检查哪些文件
  extensions: ['js', 'jsx', 'ts', 'tsx'],
  // 告诉插件要检查的目录
  // 如果未指定，默认为项目根目录
  context: path.resolve(__dirname, 'src'),

  // 自动修复可修复的问题
  fix: false,

  // 排除目录，提高性能
  exclude: 'node_modules',
}),
```

## 12.2 Babel的处理

babel的作用主要是用于兼容处理，将现代的js语法处理成旧有浏览器能够识别的语法

### 12.2.1 配置文件

在根目录下，可以创建babel的配置文件，他有多种写法
- ` babel.config.*` 形式
	- `babel.config.js`
	- `babel.config.json`
- `.babelrc.*` 形式
	- `.babelrc`
	- `.babelrc.js`
	- `.babelrc.json`

以`babel.config.js`为例

```js
// babel.config.js
module.exports = {
  presets: [
    [
      '@babel/preset-env',
      {
        targets: {
          browsers: ['> 1%', 'last 2 versions'], // 大于1%的浏览器，兼容所有主流浏览器最近的两个版本
        },
      },
    ],
  ],
};
```

- `@babel/preset-env`  这个代表是最重要的babel智能预设（预设就是一堆插件包，简化配置，集中管理)
- `@babel/preset-react`：用于处理 React 项目中的 JSX 语法。
- `@babel/preset-typescript`：用于处理 TypeScript 语法，并将其编译成标准的 JavaScript。
### 12.2.2 安装babel模块

```
npm install -D babel-loader @babel/core @babel/preset-env
```

### 12.2.3 webpack进行配置

```js
{
        test: /\.js$/i,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          // 这个option的预设可以放在根目录中的babel.config.js中
          //options: {
            //presets: ['@babel/preset-env'],
          //},
        },
      },
```

# 13. 开发模式和服务器配置

开发环境中经常修改，不能每次修改后要打包，直接热部署

- 安装模块

```shell
npm install webpack-dev-server -D
```

- `webpack.config.js`配置

```js
devServer: {
    static: './dist',
    port: 3000,
    open: true, // 自动打开浏览器
    hot: true,
  },
```

- 最后必须要用`npx webpack server`  启动才可以，相当于启动了一个dev-server

> [!tips]
> 使用dev-server的话，是没有任何输出的，在内存中编译，而且也不会生成目标`dist`目录


