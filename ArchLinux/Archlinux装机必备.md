---
title: Archlinux装机必备
titleicon: 📎
date: 2024-09-22
coverurl: 
type: Post
slug: 
status: Published
category: Archlinux
summary: 
icon: fa-solid fa-camera
tags:
  - archlinux
NotionID-MyBlog: 109fc6bd-fc68-81e1-ba6e-ddaab7a28251
link-MyBlog: https://hanff.notion.site/Archlinux-109fc6bdfc6881e1ba6eddaab7a28251
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

## 2. 截图软件

在`x11`中可以使用 `flameshot` 
```shell
paru -S flameshot
```

在`kde-wayland`中我使用的是自带的`Spectacle`，flameshot在非整数缩放的时候会出现问题
```shell
paru -S Spectacle
```

## 3. terminal 终端
偏向使用的是 `kitty` 
`paru -S kitty`

如果你使用的`display server`是`wayland`，kitty的高斯模糊与中文输入法在wayland下均不能正常使用，建议在配置中，将其改成`x11`
```shell
linux_display_sever    x11
```

当然如果需要中文输入法支持，还需要在`/etc/environment`中加入如下内容
```shell
GLFW_IM_MODULE=ibus
```

### 3.1. 一些常用的快捷键和命令

打开设置 `ctrl+shift+f2`
新建 tab 标签页 `ctrl+shift+t`
在标签页中左右切换 `ctrl+shift+left/right`

切换主题，kitty自带了许多主题
```shell
kitten themes
```

## 4. 电源管理

```shell
# kde 
paru -S tlp tlp-rdw x86_energy_perf_policy powerdevil acpuid 

sudo systemctl enable tlp
```


