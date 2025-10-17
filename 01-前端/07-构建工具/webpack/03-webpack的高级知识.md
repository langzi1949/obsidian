#前端 #webpack #构建 

# 1. 开启sourceMap

- 开发环境
	- `devtool: 'cheap-module-source-map'`
- 生产环境
	- `devtool: 'source-map'`

# 2. 热模块替换

每次不是全局打包，而是只处理更新的变化，需要注意的是在开发模式下，必须要用style-loader，如果是css提取的话就无法实现样式的HMR

但是JS的HMR需要在js的代码中添加

```js
if (module.hot) {
	moudle.hot.accept('xxx.js');
	moudle.hot.accept('yyy.js');
}
```

# 3. oneOf以及Eslint和Babel缓存

保证匹配上的loader只进行check一遍，从而每个文件只能被一个loader配置处理，提升效率
直接在rules下包一个`oneOf`，然后将loader的数组放在`oneOf`下

```js
module: {
      rules: [
        {
          // 保证每个文件只会被一个loader处理
          oneOf: [// 一堆loader
          {
              test: /\.js$/i,
              // 可以采用exclude或者include
              exclude: /node_modules/,
              // include: path.resolve(__dirname, '../src'),
              use: {
                loader: 'babel-loader',
                // 这个option的预设可以放在根目录中的babel.config.js中
                options: {
                  presets: ['@babel/preset-env'],
                  cacheDirectory: true, // 开启babel缓存
                  cacheCompression: false, // 关闭缓存文件压缩
                },
              },
            },
          ]
          }
      ]
  },
plugins: [
new ESLintPlugin({
        // 告诉插件要检查哪些文件
        extensions: ['js', 'jsx', 'ts', 'tsx'],
        // 告诉插件要检查的目录
        // 如果未指定，默认为项目根目录
        context: path.resolve(__dirname, '../src'),
        // 自动修复可修复的问题
        fix: false,
        // 排除目录，提高性能
        exclude: 'node_modules',
        cache: true, // 开启缓存
        })
]
```

# 4. 减少Babel生成的文件的体积

- 下载安装模块
```shell
npm install @babel/plugin-transform-runtime -D
```
- 使用

```js
// 在babel的使用地方，添加这个插件

```

# 5. 压缩图片

主要针对的是程序中的静态图片资源，在线的图片链接资源没有办法进行处理

- 下载安装模块

```shell
npm install --save-dev image-minimizer-webpack-plugin imagemin imagemin-gifsicle imagemin-jpegtran imagemin-optipng imagemin-svgo sharp
```

- 在`webpack.config.js`中处理

```js
const ImageMinimizerPlugin = require('image-minimizer-webpack-plugin');

 new ImageMinimizerPlugin({
          minimizer: {
            implementation: ImageMinimizerPlugin.sharpMinify,
            // 只处理大于10kb的图片
            filter: (source, sourcePath) => {
              // 获取原始文件大小
              const stats = fs.statSync(sourcePath);
              const fileSizeInKb = stats.size / 1024;
              return fileSizeInKb > 10;
            },
            options: {
              encodeOptions: {
                // PNG 无损压缩
                png: {
                  // 使用最佳压缩级别
                  compressionLevel: 9,
                  // 保持透明度
                  progressive: false,
                  // 无损压缩
                  quality: 100,
                },

                // JPEG/JPG 近无损压缩（Sharp 的无损模式）
                jpeg: {
                  // 使用无损压缩模式
                  lossless: false,
                  // 高质量设置 (90-100 为近无损)
                  quality: 95,
                  // 渐进式加载
                  progressive: true,
                  // 优化哈夫曼表
                  optimizeScans: true,
                  // 去除元数据但保持图片质量
                  mozjpeg: true,
                },

                // WebP 无损压缩
                webp: {
                  // 无损压缩模式
                  lossless: true,
                  // 无损压缩质量（0-100，100为完全无损）
                  quality: 100,
                  // 压缩级别 (0-6, 6为最佳压缩)
                  method: 6,
                },

                // AVIF 高质量压缩
                avif: {
                  // 高质量设置
                  quality: 90,
                  // 无损模式
                  lossless: false,
                  // 压缩速度 (0-10, 0最快，10最慢但压缩最好)
                  speed: 5,
                },

                // GIF 优化（Sharp 对 GIF 支持有限）
                gif: {
                  // 保持动画
                  animated: true,
                  // 优化颜色
                  optimizeFrames: true,
                },
              },
            },
          },

          // 可选：生成多种格式
          generator: [
            {
              // 生成 WebP 格式作为现代浏览器的选择
              type: 'asset',
              preset: 'webp-custom-name',
              implementation: ImageMinimizerPlugin.sharpGenerate,
              options: {
                encodeOptions: {
                  webp: {
                    lossless: true,
                    quality: 100,
                    method: 6,
                  },
                },
              },
            },
          ],
        }),
```

# 6. 多入口的以及多HTML

```js
 entry: {
      index: './src/js/index.js',
      about: './src/js/about.js',
    },
 plugins: [
      // 处理html资源
      new HtmlWebpackPlugin({
        template: './public/index.html',
        filename: 'index.html',
        // 使用 chunks 选项，告诉插件只引入 index 入口点相关的资源
        chunks: ['index'],
      }),
      new HtmlWebpackPlugin({
        template: './public/about.html',
        filename: 'about.html',
        chunks: ['about'],
      }),
optimization: {
      splitChunks: {
        chunks: 'all',
        // 其他都用默认值
      },
    },
```

通过这种方式进行处理就会进行多个页面和按需进行加载相关的文件了，但是现代前端开发，几乎都是单页面，所以一般只有一个入口

# 7. preload和prefetch

一般针对按需动态加载的资源，如果在使用的时候再加载有可能需要等待，影响性能

一般采用的是vue团队开发的preload和prefetch的插件

- 安装插件

```shell
npm install @vue/preload-webpack-plugin -D
```

- 在`webpack.config.js`中使用

```js
const PreloadWebpackPlugin = require('@vue/preload-webpack-plugin');
 new PreloadWebpackPlugin({
        rel: 'preload',
        as: 'script',
      }),
```

# 8. JS终极兼容性问题解决

之前的babel可以解决一部分ES6的语法，但是还有很多ES6之后的语法，他不太支持解决。

所以用`babel` 和 `core-js` 联合解决

- 安装`core-js`

```shell
npm install --save core-js
```

- 使用

```js title=babel.config.js
// babel.config.js

module.exports = {
  presets: [
    [
      '@babel/preset-env',
      {
        // 关键配置：告诉 Babel 启用垫片功能
        useBuiltIns: 'usage',

        // 关键配置：指定垫片库的版本
        corejs: {
          version: 3,
          proposals: true
        }
      },
    ],
  ],
};
```
