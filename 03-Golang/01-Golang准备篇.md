#golang #Go 

# 1. 开发环境准备

首先，go的环境搭建是基于`asdf`的tool version版本工具， 具体的设置，请看[[00.adsf的使用]]

## 1.1 配置vscode

学习的话，用vscode进行开发就OK了，但是企业级项目的建议使用`GoLand`

配置vscode的流程如下
1. 安装核心插件 `Go`  这个是官方出品的，这个插件集成了Go语言开发所需的所有功能，包括智能感知，代码跳转、以及调试和格式化等
2. 安装`Go Test Explorer` 在侧边栏可视化运行和debug
3. 开启`gopls` ，它是Go官方的语言服务器，Go插件默认会使用他

```json title=settings.json
{
    "files.readonlyInclude": {
        "**/golang/**/pkg/mod/**": true
    },
    "go.goroot": null,
    "go.gopath": null,
    "go.toolsManagement.autoUpdate": true,
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
        "source.organizeImports": "explicit"
    },
    "go.formatTool": "goimports",
    // 在保存时自动格式化 Go 文件
    "go.formatOnSave": true,
    // 在保存时运行 goimports 自动管理导入包
    "go.useGoImports": true
}
```

3. 安装调试工具

```shell
go install github.com/go-delve/delve/cmd/dlv@latest

# 查看dlv的版本
dlv version
```

4. 配置`.vscode/launch.json`

```json
{
	"version": "0.2.0",
    "configurations": [
        {
            "name":"Launch Package",
            "type": "go",
            "request": "launch",
            "mode": "auto",
            "program": "${workspaceFolder}"
        }
    ]
}
```

至此，大功告成！！！ 

> [!danger]
> 如果现在vscode里面go文件的右上角出现运行的按钮，可以安装`code runner`插件

# 2. 创建第一个go项目

1. 创建项目目录

```shell
mkdir my-go-app
```

2. 初始化Go Module
在项目的根目录下，使用`go mod init`命令来初始化Go modules，它会创建一个`go.mod`文件，用于管理项目的依赖，类似`maven`

```shell
cd my-go-app
go mod init example.com/my-go-app
```

3. 创建一个入口文件，其实就是main函数

```go
package main
import "fmt"
func main() {
    fmt.Println("Hello, Go!")
}
```

4. 运行即可

```shell
go run main.go
```

# 3. go是如何管理第三方的包或者库的？

首先，Go的包管理系统没有像Java的`Maven center repository`或者Node的`npmjs`等， 它是直接托管在代码管理平台上的，比如Github等

Go的包管理工具 `go get` 会直接从这些代码平台下载代码，工具链的执行顺序大致
1. 解析命令中的模块路径，比如`github.com/gin-gonic/gin`
2. 自动从Github clone该仓库的源代码
3. 将代码下载到Go模块缓存起来  ($GOPATH/pkg/mod)
4. 更新项目中的`go.mod`和`go.sum`文件

> [!tips] #go代理
> 众所周知的原因，直接下载Github的代码，有时候会比较慢，可以配置Go代理
> 全局设置
> 在`~/.bash_profile`或者`~/.zshrc`文件中，添加`export GOPROXY=https://proxy.golang.org,direct`
> 如果在国内的话，可以配置阿里云的代理
> `export GOPROXY=https://mirrors.aliyun.com/goproxy/,direct`

# 4.  go开发需要知道的一些事

1. 对于import的第三方的包，如果没有实际用到，在编译的时候，go编译器会自动删除这个引用，以减少包体积
	1. 原理：通过类似Java中的可达性，从入口函数开始，通过生成依赖树，最后分析而来
	2. <span class='rt-b'>例外:</span>  ==显式导入==，如果用到这个包了，但是这个包可能只有类似一个init方法，没有显式调用，需要调整import方式，使用下划线 `import _ "database/sql"` 这种方式
2. 每行的行尾不需要分号`;`
3. 函数定义的左边的括号`{`必须和方法定义在同一行
4. 按照社区惯例，在package声明的上方需要添加`包注释`，用于解释这个包的作用和功能
5. 区分大小写   `heapSide`和`HeapSide`是两个变量
6. 变量或者方法或者type，首字母是大小的就相当于 `public`，是外部可以访问的；如果首字母是小写，相当于是`protected`，只能是包内可见
7.  字符串和数字类型的不能通过 加号`+` 进行拼接，可以通过`fmt`的`Sprintf`的方法来操作或者其他库里面的方法
8. go中没有`++i` 这种前缀自增或者自减，而且也不能`j = i++`， `i++`只能作为语句使用，不能作为表达式使用
9. 一个目录中只能声明一个package，不同go文件中的package名称，在同一个目录下需要保持一致且唯一
10. 导入包的时候，如何起别名？  `import 别名 "需要导入的包"`
11. 如果有变量没有使用，在go中编译就失败了