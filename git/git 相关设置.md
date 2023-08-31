# git 相关设置

## 1. 1、 问题合集
### 1.1. gitee push 文件时需要输入用户名与密码

问题与使用的是http方式来推送，关于推送方式可以使用以下命令查看
```shell
git remote -v
```

需要重新配置origin的地址
```shell
git remote rm origin 

git remote add origin [ssh 仓库地址]

# other
## 显示remote
git remote 

## 显示remote 详细信息
git remote -v

```

多账户设置
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



