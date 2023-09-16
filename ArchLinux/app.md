---
link: https://www.notion.so/app-194a008d6c8c469394d93283028dd3cb
notionID: 194a008d-6c8c-4693-94d9-3283028dd3cb
---
## 1. zsh以及美化

- 安装 zim
```shell
curl -fsSL https://raw.githubusercontent.com/zimfw/install/master/install.zsh | zsh
```

- 编辑zsh配置文件`~/.zimrc`
```shell
vim ~/.zimrc
```
- 添加以下内容
```shell
zmodule romkatv/powerlevel10k
```
- 然后终端输入以下内容
```shell
zimfw install
```
接着会进入`powerlever10k`的自动配置