---
title: WindowsPowerShell常用命令
titleicon: 📎
date: 2024-09-22
coverurl: 
type: Post
slug: 
status: Published
category: Powershell
summary: 
icon: fa-solid fa-camera
tags:
  - powershell
  - windows
NotionID-MyBlog: 109fc6bd-fc68-8130-b232-e2bd1f83be28
link-MyBlog: https://hanff.notion.site/WindowsPowerShell-109fc6bdfc688130b232e2bd1f83be28
---

## 1. 解决端口占用
```powershell
#找出占用端口的进程号
netstat -ano|findstr [port]
#通过进程名称结束进程：
taskkill /f /t /im $name
#通过PID结束进程： 
taskkill /f /t /pid $pid

```
说明：
- `/F` 指定强制终止进程。
- `/T` 终止指定的进程和由它启用的子进程。
- `/IM imagename` 指定要终止的进程的映像名称。通配符 '*'可用来指定所有任务或映像名称。
- `/PID processid` 指定要终止的进程的 PID。使用 TaskList 取得 PID。


