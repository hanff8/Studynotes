---
title: git常用操作
titleicon: 📎
date: 2024-09-22
coverurl: 
type: Post
slug: 
status: Published
category: Git
summary: 
icon: fa-solid fa-camera
tags:
  - git
  - github
NotionID-MyBlog: 109fc6bd-fc68-81ce-8f31-ee83a9a4b585
link-MyBlog: https://hanff.notion.site/git-109fc6bdfc6881ce8f31ee83a9a4b585
---

## 1. Git常用命令

```shell
# 查看tag列表
git tag -l

# 切换到指定tag
git checkout tag_name

# 切换到指定tag,并创建分支
git checkout -b branch_name tag_name

# fetch remote到新分支
git fetch origin <remote branch>:<new_branch>

```

## 2. 遇到的问题
### 2.1. gitee push 文件时需要输入用户名与密码

问题与使用的是http方式来推送，关于推送方式可以使用以下命令查看
```shell
git remote -v
```

### 2.2. 需要重新配置origin的地址
```shell
git remote rm origin 

git remote add origin [ssh 仓库地址]

# other
## 显示remote
git remote 

## 显示remote 详细信息
git remote -v

```

### 2.3. 多账户设置
```bash
# 配置config
vim ~/.ssh/config


# config

Host github.com
	User xxx
	IdentityFile ~/.ssh/loomingF/id_rsa
	PreferredAuthentications publickey


# 检测链接
ssh -T git@github.com
```

### 2.4. 已配置ssh key 但是无法推送
	使用在httos端口使用ssh
https://docs.github.com/zh/authentication/troubleshooting-ssh/using-ssh-over-the-https-port
```shell
$ ssh -T -p 443 git@ssh.github.com
> Hi USERNAME! You've successfully authenticated, but GitHub does not
> provide shell access.


git clone ssh://git@ssh.github.com:443/YOUR-USERNAME/YOUR-REPOSITORY.git
```

	在~/.ssh/config文件下添加如下内容
```conf
Host github.com
Hostname ssh.github.com
Port 443
User git
```



#git #配置