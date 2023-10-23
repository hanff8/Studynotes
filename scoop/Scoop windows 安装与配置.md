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
```powershell
$env:SCOOP='D:\Applications\Scoop'
[Environment]::SetEnvironmentVariable('SCOOP',$env:SCOOP,'User')

##global app
$env:SCOOP_GLOBAL='D:\Applications\Scoop\globalapp'
[environment]::setEnvironmentVariable('SCOOP_GLOBAL',$env:SCOOP_GLOBAL,'Machine')

```
## 2. Scoop配置
设置代理
```powershell
scoop config proxy 127.0.0.1:{port}
```

安装需要用到的bucket

```powershell
# 先安装git,才能添加bucket
# 先安装git,才能添加bucket
scoop install git

# sudo
scoop install sudo

scoop bucket add dorado https://github.com/chawyehsu/dorado.git
scoop bucket add scoopet https://github.com/ivaquero/scoopet.git
scoop bucket add iszy https://github.com/ZvonimirSun/scoop-iszy.git
scoop bucket add echo https://github.com/echoiron/echo-scoop.git
scoop bucket add zapps https://github.com/kkzzhizhou/scoop-zapps.git
scoop bucket add tomato https://github.com/zhoujin7/tomato.git
scoop bucket add MorFans-apt https://github.com/Paxxs/Cluttered-bucket.git
scoop bucket add sushi https://github.com/kidonng/sushi.git
scoop bucket add aki https://github.com/akirco/aki-apps.git
scoop bucket add lemon https://github.com/hoilc/scoop-lemon.git

scoop bucket add extras
# 加快search速度
scoop install scoop-search
```