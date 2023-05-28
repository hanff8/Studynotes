## 一、WSL2 安装

启用WSL

```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

启用虚拟平台
```powershell
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

[下载Linux内核升级包](
https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)

设置wsl2为默认版本
```powershell
wsl --set-default-version 2
```

 安装LxRunOffline

先安装scoop [[scoop 相关]]
``` powershell
Scoop install lxrunoffline
```

下载ArchLinux boot

[Index of /archlinux/iso/ (ustc.edu.cn)](http://mirrors.ustc.edu.cn/archlinux/iso/)

安装

```powershell
lxrunoffline.exe i -n ArchLinux -d D:\MyWSL\ArchLinux -f .\archlinux-bootstrap-2022.11.01-x86_64.tar.gz -r root.x86_64

```


进入系统

```shell
wsl -d ArchLinux
```


编辑源

```bash
# 使用explorer.exe打开编辑

cd /etc/pacman.d
explorer.exe .

# 编辑mirrorlist 文件把china源放在最上面

```

配置pacman下载常用软件
```bash
pacman -Syy
pacman-key --init
pacman-key --populate
pacman -S base base-devel vim git wget openssh
```

创建用户

```bash
useradd -m hanfx -G wheel
```


设置密码

```shell
# root 设置密码
passwd

# user设置密码
passwd hanfx

# 加入wheel组
vim /etc/sudoers

# 取消注释
%wheel ALL=(ALL:ALL) ALL
```


添加archlinuxcn 源

```bash
vim /etc/pacman.conf

#尾部添加
[archlinuxcn]
Server=https://mirrors.ustc.edu.cn/archlinuxcn/$arch

# 安装密钥
pacman -Syy archlinuxcn-keyring

```

退出wsl-archlinux
```bash
exit
```

设置wsl默认登陆用户为user
```bash
#先使用user登录进去看user-id
wsl -d ArchLinux -u hanfx

# 查询user-id
id

# 设置默认登陆用户
lxrunoffline.exe su -n ArchLinux -v 1000
```

终端更改为zsh+zimfx
```bash
# zsh 安装
sudo pacman -S zsh

#更改默认shell
chsh -s /usr/bin/zsh

# zimfw 安装
wget -nv -O - https://raw.githubusercontent.com/zimfw/install/master/install.zsh | zsh

#如果安装失败运行以下命令后再此执行上面的命令
rm -rf ~/.zimrc ~/.zim ~/.zshrc

# 安装powerlevel10k
vim ~/.zimrc
# 添加下面这段到最后一行
zmodule romkatv/powerlevel10k
```


安装Nerdfonts
[Nerd Fonts - Iconic font aggregator, glyphs/icons collection, & fonts patcher](https://www.nerdfonts.com/font-downloads)


