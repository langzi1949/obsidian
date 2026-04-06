
> [!note]
> 首先，我们要了解这个编辑器可能是不如专业的IDE的，比如`vs-code`，而且它更加偏向进行前端的全栈开发，或者写一些`Python`、`Go`、`C/C++`估计应该是可以的，但是对于`Java`这种巨无霸感觉不太适合。


# 1. 安装

```shell
# 1. 先安装neovim
brew install neovim
# 2. 安装git
brew install git
# 3. 安装字体
brew search nerd-font # 先搜索一下有哪些字体
# 4. 我对IDEA的mono情有独钟，所以我选择
brew install font-jetbrains-mono-nerd-font
```

> [!danger]
> 为了方便后续使用，建议直接将vim指向nvim
> `alias vim=nvim`  这个是临时有效，如果想永久生效，写到bash或者zshrc文件中
> `echo alias vim="nvim" >> ~/.zshrc`

# 2. 需要一些Setting

关于`nvim`所有的配置都在`~/.config/nvim`目录中，这个目录正常情况下可以直接上传到Github中，方便后续的维护

# 3. 使用插件包管理器

# 4. 安装插件

# 5. 参考资料

[lazyVim](https://www.lazyvim.org/)
[https://github.com/FledgeXu/NeovimZero2Hero](https://github.com/FledgeXu/NeovimZero2Hero)
[知乎专栏-告别 VS Code！2026 版 NeoVim 配置全攻略：30分钟打造 AI 编程神器](https://zhuanlan.zhihu.com/p/1994127486221575639)
[https://github.com/xinghe98/neovim_lua](https://github.com/xinghe98/neovim_lua)
