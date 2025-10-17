#前端 #构建 #webpack 

# 1.  CSS文件单独提取

采用`style-loader`的方式只会将最终的css资源嵌入在html的`<style>`标签中，一般来说我们需要将css文件单独的提取出来

- 安装插件

```shell
npm install mini-css-extract-plugin -D
```

- `webpack.config.js`中使用

```js
// 1. 先引入插件
const MiniCssExtractPlugin = require('mini-css-extract-plugin'); 

// 2. plugins中添加插件
 plugins: [
    // 将css资源从js中提出成单独文件
    new MiniCssExtractPlugin({
      filename: 'styles/styles.[contenthash].css',
    }),
]

// 3. 相关样式资源中替换掉style-loader
// 处理css资源
module : {
  rules: [
	  {
        test: /\.css$/i,
        use: [MiniCssExtractPlugin.loader, 'css-loader'],
      },
  ]
}
```

# 2. 压缩css文件

其实在开发环境中没有必要进行css文件的压缩，但是在生产环境中为了性能的提升，需要将css进行压缩

- 安装插件

```shell
npm install css-minimizer-webpack-plugin --save-dev
```

- 调整`webpack-config.js`

```js title=webpack.config.js
const CssMinimizerPlugin = require('css-minimizer-webpack-plugin');

// 同entry一层，添加属性
optimization: {
	minimize: true,
	// 压缩css
	minimizer: [new CssMinimizerPlugin()],
},

// 第二种方式 直接在plugins里面添加这个插件也可以
// new CssMinimizerPlugin()
```

> [!tips]
> 在生产模式下，js和html自动就会被压缩，无需额外的配置

# 3. CSS的兼容性处理

因为有些css的属性和值，不同浏览器支持的不一样，需要针对一些新的属性或者历史遗留的属性进行转换，以此兼容绝大多数的浏览器

## 3.1 安装模块

- `postcss`  PostCSS的核心引擎
- `postcss-loader`  Webpack中用于加载PostCSS的loader
- `postcss-preset-env` 一个预设，包含多个PostCSS插件，其中就包括`autoprefix` 添加一些浏览器前缀等，他就是转换CSS来兼容浏览器的核心预设

```shell
npm install --save-dev postcss postcss-loader postcss-preset-env
```

## 3.2 使用方法

```js title=webpack.config.js
// 在每个样式的处理loader中添加这个loader
{
	test: /\.css$/i,
	use: [
	  MiniCssExtractPlugin.loader,
	  'css-loader',
	  {
		loader: 'postcss-loader',
		options: {
		  postcssOptions: {
			plugins: [
			  [
				// 用于解决绝大多数的兼容性问题
				'postcss-preset-env',
				{
				  // Options
				},
			  ],
			],
		  },
		},
	  },
	],
  },
```

## 3.3 配置`.browserslistr`文件

这个是关键的一步，需要让`postcss-preset-env`插件知道目标浏览器是啥，它会自动从根目录的`.browserslistrc`文件中读取配置

```yaml title=.browserslistrc
# .browserslistrc
# 兼容全球市场份额大于1%的浏览器，且不是已停止维护的
> 1%
not dead
# 兼容所有主流浏览器最近两个版本
last 2 versions
# 兼容 IE 11
ie 11
```

也可以在`package.json`中添加`browserslist` 字段来替代这个文件

# 4. 封装样式loader称为一个函数

我们发现有很多的样式资源， `css`、`less`、`sass`等，在每一个loader中都要写很多的相同逻辑的代码，所以，可以将这些代码单独抽取出来

```js
/**
 * 获取样式的loader
 * @param {样式的loader，比如less-loader, sass-loader等} pre
 * @returns
 */
function styleLoader(pre) {
  return [
    MiniCssExtractPlugin.loader,
    'css-loader',
    {
      loader: 'postcss-loader',
      options: {
        postcssOptions: {
          plugins: [
            [
              // 用于解决绝大多数的兼容性问题
              'postcss-preset-env',
              {
                // Options
              },
            ],
          ],
        },
      },
    },
    pre,
  ].filter(Boolean);
}
```


# 5. 多环境下的`webpack.config.js`如何处理

1. 一个通用的所有环境共享的配置`webpack.common.js`，存放所有环境共享的配置，如入口，输出，各种文件类型以及loader规则等
2. `webpack.dev.js`  存放开发环境特有的配置，例如本地服务器devServer，sourceMap模式等
3. `webpack.prod.js`  存放生产环境的特有配置，例如代码压缩，CSS文件提取，性能优化等
4. `webpack-merge`  webpack提供的工具，用于将环境特定的配置和通用配置合并

以dev举例
```js title=webpack.dev.js
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
  mode: 'development',
  devtool: 'eval-source-map', // 方便调试
  devServer: {
    static: './dist', // 本地服务器根目录
    hot: true,        // 启用模块热替换
  },
});
```

最终不能每次执行指定--config的具体的配置，所以可以在`package.json`中指定

```json title=package.json
// package.json

"scripts": {
  "start": "webpack serve --config config/webpack.dev.js",
  "build": "webpack --config config/webpack.prod.js"
},
```


一个相对完整的`webpack.config.js`

```js title=webpack.config.js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const ESLintPlugin = require('eslint-webpack-plugin');
const CssMinimizerPlugin = require('css-minimizer-webpack-plugin');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'static/js/bundle.[hash:10].js',
    path: path.resolve(__dirname, 'dist'),
    clean: true, // 自动清空上次打包内容
  },
  module: {
    rules: [
      // 处理html中的图片资源
      {
        test: /\.html$/i,
        use: ['html-loader'],
      },
      // 兼容js语法
      {
        test: /\.js$/i,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          // 这个option的预设可以放在根目录中的babel.config.js中
          options: {
            presets: ['@babel/preset-env'],
          },
        },
      },
      // 处理css资源
      {
        test: /\.css$/i,
        use: styleLoader(),
      },
      // 处理less资源
      {
        test: /\.less$/i,
        use: styleLoader('less-loader'),
      },
      // 处理sass资源
      {
        test: /\.s[ac]ss$/i,
        use: styleLoader('sass-loader'),
      },
      // 处理图片资源
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
      // 处理字体或者字体图标资源
      {
        test: /\.(woff|woff2|eot|ttf|otf)$/i,
        type: 'asset/resource',
        generator: {
          // 指定字体文件的输出路径和名称
          // 例如，所有字体文件都会被放在 dist/fonts 目录下
          filename: 'static/fonts/[name].[hash:10][ext]',
        },
      },
      // 处理媒体资源
      {
        test: /\.(mp3|mp4|avi)$/i,
        type: 'asset/resource',
        generator: {
          // 其他媒体资源统一输出到static/meida目录下
          filename: 'static/media/[name].[hash:10][ext]',
        },
      },
    ],
  },
  plugins: [
    // 处理html资源
    new HtmlWebpackPlugin({
      template: './src/index.html',
      filename: 'index.html',
    }),
    // 将css资源从js中提出成单独文件
    new MiniCssExtractPlugin({
      filename: 'styles/style.[contenthash].css',
    }),
    // 2. 添加插件实例到 plugins 数组
    // Js的语法检查
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
  ],
  mode: 'development',
  devServer: {
    static: './dist',
    port: 3000,
    hot: true,
  },
  optimization: {
    splitChunks: false,
    minimize: true,
    // 压缩css
    minimizer: [new CssMinimizerPlugin()],
  },
};

/**
 * 获取样式的loader
 * @param {样式的loader，比如less-loader, sass-loader等} pre
 * @returns
 */
function styleLoader(pre) {
  return [
    MiniCssExtractPlugin.loader,
    'css-loader',
    {
      loader: 'postcss-loader',
      options: {
        postcssOptions: {
          plugins: [
            [
              // 用于解决绝大多数的兼容性问题
              'postcss-preset-env',
              {
                // Options
              },
            ],
          ],
        },
      },
    },
    pre,
  ].filter(Boolean);
}
```
