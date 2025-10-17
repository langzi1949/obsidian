#PHP #asdf

> 本文档是Mac下PHP的开发环境的搭建指南
# 1. 环境准备

## 1. 🧰  安装PHP (使用asdf进行版本管理)

```bash
# 安装asdf (需要进行环境变量写入./zshrc中，自行查询)
brew install asdf
# 安装php依赖
brew install autoconf bison re2c libxml2 openssl@3 curl icu4c pcre2 zlib bzip2
# 安装php的插件
asdf plugin add php
# 可以看一下php当前的版本列表
asdf list all php
# 然后选择一个版本进行安装，比如8.4.7版本
asdf install php 8.4.7
```
	📌 补充注意： 使用`asdf`安装php的时候会顺便安装了`composer`(类似maven包管理的工具)，但是composer最好是全局的，不用绑定在某个php版本下，因为依赖的第三方包跟php版本无关，所以就需要安装`composer`同时删除asdf下的composer
	
```bash
# 下载 Composer 安装器
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"

# 运行安装器，将其安装到 /usr/local/bin 或某个你希望的位置
# 这会使 Composer 在所有 PHP 版本下都可用
sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer

# 清理安装文件
php -r "unlink('composer-setup.php');"

# 进入asdf的php下将composer删除或者重命名
mv $Home/.asdf/shims/composer $Home/.asdf/shims/composer.bak
mv $Home/.asdf/installs/php/8.4.7/bin/composer $Home/.asdf/installs/php/8.4.7/bin/composer.bak

# 重新刷新asdf
asdf reshim

# 验证安装
composer --version
```

## 2. 🚜 安装Laravel Valet(通过composer)

```bash
composer global require laravel/valet
export PATH="$HOME/.composer/vendor/bin:$PATH"  # ~/.zshrc 中添加
source ~/.zshrc
valet install
```

## 3. ⛱ 安装调试工具xdebug

```bash
# 使用asdf安装的php会自动安装了pecl，可以check一下  which pecl
pecl install xdebug
# 如果要输出必要的信息的话，可以试试命令
# pecl install xdebug -v
# 配置php.ini
# php.ini 一般在~/.asdf/installs/php/<version>/conf.d/ 下
cd ~/.asdf/installs/php/8.4.7/conf.d/
```

编辑`php.ini`文件

```bash
[XDebug] 
zend_extension="xdebug.so"
xdebug.mode=debug 
xdebug.start_with_request=yes 
xdebug.client_host=127.0.0.1 
xdebug.client_port=9003 
xdebug.discover_client_host=true
```

> [!tips]
> `pecl`命令是啥？ 是管理PECL存储库中的PHP拓展的，一般用于安装C/C++编写的扩展。
> 同时，不同版本的pecl是不同的，所以pecl是跟着PHP版本进行绑定的，需要注意

## 4. 🍟 使用valet创建Laravel项目

安装Laravel项目最常见的方式就是**Park** 和 **Link** 

* 使用`valet park`方式
```bash
cd ~/phpworkspace/项目根目录
# 会将根目录下所有的子目录作为独立的网站提供服务，相当于一次性管理多个服务
valet park
# 采用composer创建项目
composer create-project  --prefer-dist laravel/laravel my-laravel-app
```

然后就可以在浏览器访问  [`http://my-laravel-app.test`](`http://my-laravel-app.test`)

* 使用`valet link`方式
```bash
# 自行先创建一个Laravel项目
composer create-project laravel/laravel my-laravel-app
cd my-laravel-app
# 执行link
valet link [custom-name-for-app] # 可以不指定名称，不指定的话就是当前目录名称
```

此时同样可以在浏览器上访问 [http://my-laravel-app.test](http://my-laravel-app.test)

> 补充一下：如果想看valet有哪些命令，直接`valet`一下，会展示命令列表

## 5. 👨🏿‍🦱 安装vs code的PHP插件

| 插件                         | 说明           |
| -------------------------- | ------------ |
| PHP Intelephense           | 语法检查         |
| PHP Debug（+ Xdebug）        | 调试           |
| Laravel Extra Intellisense | Laravel 智能提示 |
| Laravel Artisan            | Laravel 命令执行 |
| Laravel Blade Snippets     | Blade 文件支持   |
| Laravel goto view          | 快速跳转视图       |
相关的`setting.json`文件，请参考第一个项目hello-word

## 6. 开始开发PHP项目吧

作为PHP的新手，可以直接先创建一个learning目录，用于学习php的各种语法等，用`valet link`，然后在浏览器上查看运行的结果信息

# 2. Laravel项目的结构

![[Pasted image 20250911151233.png\|700]]

==大致讲一下，浏览器发起请求后，整个的链路流程是啥？（只针对SPA单页面应用）==

浏览器访问 ->  后端`Laravel`路由(routes目录下) -> 找到`views`中的blade模板文件 -> 返回HTML外壳 
-> 浏览器接到后端返回的HTML后 -> 前端加载`vue应用` -> `vue router`进行渲染组件  -> 后续的逻辑就跟我们之前开发前端一致。

# 3. 使用Phpstorm

我感觉使用vscode开发PHP会非常麻烦，而且，需要配置的东西太多了，直接使用Phpstorm进行开发吧

## 3.1 配置php版本

直接进入到setting中，然后调整php的版本即可
![[Pasted image 20250911134645.png]]
## 3.2 配置npm

用上面的`composer create-project  --prefer-dist laravel/laravel my-laravel-app`命令创建一个新的laravel的项目
- `--prefer-dist` 这个表示的**优先下载压缩的发行版本，而不是clone完整的Git仓库**

## 3.3  安装依赖和运行

**安装项目依赖**
- `composer install`   -- 下载php和laravel依赖的包
- `npm install`   -- 这个是下载前端的依赖包

**运行**
- `npm run dev`  
	- 这个是运行的一个前端的代码，任务就是处理前端资源
- `php artisan serve`
	- 这个是后端服务，用于处理各种请求的
- 其实可以直接用`composer.json`中的命令去运行
	- `composer run dev`  它包含了上面了两个命令，同时还包含了日志监控等

## 3.4 到底如何运行PHP代码呢？

首先，PHP是一个服务端脚本语言，在运行PHP端代码的时候，需要一个服务器。
它工作的原理就是  当一个请求到服务端时，服务端会用PHP解释器来执行你的代码，然后将生成的内容或者HTML返回给浏览器。
在某些特定场景下，也可以通过命令行CLI来执行PHP代码。

## 3.5  应该如何debug

1. 安装和配置Xdebug
	1. 参考[[PHP开发指南(重要)#3. ⛱ 安装调试工具xdebug]]
2. 在PhpStorm中配置
![[Pasted image 20250911143459.png]]
注意： Use Path一定要勾选
3. 开始Debug
	1. 监听调试连接。 在Phpstorm的右上角，会看到一个虫子的图标『Start Listening for php debug connections』
	2. 点击，让它变成绿色，表示phpstorm正在等待来自xdebug的调试连接
	3. 其他的操作懂得都懂

## 3.6 安装插件

估计只需要一个插件就OK了，就是`Laravel IDE` 这个插件

至此phpstorm的配置也完成了


# 4. 无力吐槽的地方 🤮🤮🤮

1. 变量名必须加上`$`
2. 类方法的访问用`->`
3. 字符串的很多用法，居然区分单字节和多字节字符串，无语，说的就是你golang
4. 居然用arr来表示map，神人想出来的
5. 变量名大小写敏感，但是函数，类这些居然大小写不敏感，我只能说握草了
6. 字符串的拼接居然使用`.`
7. 内置的函数，命名风格都不统一，一会儿是下划线风格、一会儿是小驼峰风格
8. debug异常麻烦，居然还要配置各种php.ini文件，无语
9. 很多中间件的扩展都需要`pecl`安装
10. 著名的梗  `PHP6`  -- PHP6因画饼太大最终项目取消了

# 5. 未来可能要学习的框架

- Laravel    最流行且最前面，但是性能很一般
- swoole    PHP的拓展，底层用C语言编写，提供异步、并行、以及高性能的网络编程能力
- webman   - 基于swoole的一个高性能web框架