---
title: wsl2 ubuntu 迁移
titleicon: 📎
date: 2024-09-22
coverurl: 
type: Post
slug: 
status: Published
category: WSL2
summary: 
icon: fa-solid fa-camera
tags:
  - wsl2
  - ubuntu
  - windows
NotionID-MyBlog: 8c2bc4e9-27a6-48c6-9741-c59bcf7059f7
link-MyBlog: https://hanff.notion.site/wsl2-ubuntu-8c2bc4e927a648c69741c59bcf7059f7
---

自动开启wsl环境并安装Ubuntu 子系统
```powershell
wsl --install
```
查看wsl系统状态
```powershell
wsl -l -v
```
如果系统正在运行的话需要先停止系统
```powershell
wsl --shutdown
```
导出Ubuntu 到tar
```powershell
wsl --export Ubuntu d:\Ubuntu.tar
```
注销当前分发版
```powershell
wsl --unregister Ubuntu
```
重新导入并安装在D盘
```powershell
wsl --import Ubuntu d:\MyWSL\ubuntu d:\Ubuntu.tar --version 2
```
设置默认登陆用户
```powershell
ubuntu config  --default-user hanfx
```
删除Ubuntu.tar
```powershell
del d:\Ubuntu.tar
```

#wsl2 #wsl迁移 #ubuntu