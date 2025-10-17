#Nodejs #Node #前端

> 此文档为Nodejs的开发环境搭配和vscode插件安装说明


# 1. 采用asdf进行node的版本管理

```bash
# 关于asdf的说明不做介绍了，之前已经使用过了
# 1. 安装asdf的nodejs插件
asdf plugin add nodejs https://github.com/asdf-vm/asdf-nodejs.git
# 2. 查看可用的nodejs版本
asdf list all nodejs
# 3. 安装指定版本的nodejs(通常找一个比较新的LTS版本)
asdf install nodejs 22.17.0
# 4. 设置tool-version
cd ~/nodeworkspace
asdf set nodejs 22.17.0
# 验证一下 (安装nodejs会自动安装npm和npx)
node -v
npm -v
npx -v
```

# 2. VS code的插件配置和说明

* ESLint
	* 代码风格和潜在错误的检测等，目前是社区最流行的代码质量工具
* Prettier-Code formatter
	* 自动格式化代码，保持代码风格统一
* NPM Intellisense
	* 在`import`或者`require`时自动补全`node_modules`中的模块名称
* Live Server
	* 为静态的html文件提供一个本地开发服务器

# 3. VS code的`settings.json`推荐配置

```JSON
{
    // 编辑器通用设置
    "editor.tabSize": 2, // 现代前端通常用 2 个空格缩进
    "editor.insertSpaces": true,
    "editor.formatOnSave": true,
    "editor.defaultFormatter": "esbenp.prettier-vscode", // 设置 Prettier 为默认格式化工具
    "editor.codeActionsOnSave": {
        "source.fixAll.eslint": "explicit" // 保存时自动执行 ESLint 修复
    },
    "editor.bracketPairColorization.enabled": true, // 开启括号对颜色
    "editor.guides.bracketPairs": "active", // 括号引导线

    // 文件相关
    "files.eol": "\n", // 统一行尾符
    "files.autoSave": "afterDelay",

    // JavaScript/TypeScript 语言特性
    "javascript.updateImportsOnFileMove.enabled": "always", // 文件移动时自动更新导入路径
    "typescript.updateImportsOnFileMove.enabled": "always",

    // ESLint 配置
    "eslint.validate": [
        "javascript",
        "javascriptreact",
        "typescript",
        "typescriptreact"
    ],
    "eslint.format.enable": true, // 允许 ESLint 兼作格式化器（与 Prettier 配合使用）

    // Prettier 配置（可根据项目需要调整）
    "prettier.tabWidth": 2,
    "prettier.semi": true, // 行尾是否使用分号
    "prettier.singleQuote": true, // 是否使用单引号

    // 终端字体（可选）
    "terminal.integrated.fontFamily": "MesloLGS NF", // 推荐使用 Nerd Font，显示更多图标
    "terminal.integrated.fontSize": 14,

    // 排除不必要的文件和文件夹，提高性能和搜索效率
    "files.watcherExclude": {
        "**/node_modules": true,
        "**/build": true,
        "**/dist": true,
        "**/.git": true
    },
    "search.exclude": {
        "**/node_modules": true,
        "**/build": true,
        "**/dist": true,
        "**/.git": true
    }
}
```

# 4. 那就开始前端之旅吧，祝你好运

推荐书籍 ：
*  《HTML & CSS》 
* 《You Dont Know JS》
