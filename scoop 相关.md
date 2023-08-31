# Scoop windows 安装与配置

## 1. Scoop安装
```shell
Invoke-Expression (New-Object System.Net.WebClient).DownloadString('https://get.scoop.sh')

# or shorter
iwr -useb get.scoop.sh | iex
```

可能会碰到powershell的组权限问题

```shell
Set-ExecutionPolicy RemoteSigned -scope CurrentUser
##test
```

设置环境变量，安装到自定义目录
``` powershell
$env:SCOOP='D:\Applications\Scoop'
[Environment]::SetEnvironmentVariable('SCOOP',$env:SCOOP,'User')

##global app
$env:SCOOP_GLOBAL='D:\Applications\Scoop\globalapp'
[environment]::setEnvironmentVariable('SCOOP_GLOBAL',$env:SCOOP_GLOBAL,'Machine')

```

设置代理
```powershell
scoop config proxy 127.0.0.1:{port}
```

安装需要用到的bucket

```powershell
# 先安装git,才能添加bucket
scoop install git

scoop bucket add extras
```


#scoop