#前端 #CSS #Less

# 1. 前期准备

安装：`npm install -g less`

- 在vscode中安装 `easy-less`
然后可以在`settings.json`文件中添加相关的配置
```json
{
// Less编译相关的配置
  "less.compile": {
    "compress": false, // 是否压缩 CSS 输出
    "sourceMap": false, // 生成源映射文件，便于调试
    "out": "../css/$1.css", // 输出 CSS 文件路径
    "autoprefix": true // 自动添加浏览器前缀
  },
  // 启用 Easy LESS 扩展的自动编译
  "easy-less.watch": true,
  // 文件关联，识别 .less 文件
  "files.associations": {
    "*.less": "less"
  },
  "[less]": {
    "editor.suggest.showSnippets": true,
    "editor.quickSuggestions": {
      "other": true,
      "comments": false,
      "strings": false
    }
  }
}
```

# 2. 其他的整体的逻辑

直接看文档吧，这边懒得写了直接参考官方文档： [[https://lesscss.org/]]



