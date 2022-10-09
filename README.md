# Scoop文档

## 安装 Scoop

### 用户安装路径

```
$env:SCOOP='C:\Scoop'
[Environment]::SetEnvironmentVariable('SCOOP', $env:SCOOP, 'User')
```

### 全局安装路径

```
$env:SCOOP_GLOBAL='C:\Scoop_Global'
[Environment]::SetEnvironmentVariable('SCOOP_GLOBAL', $env:SCOOP_GLOBAL, 'Machine')
```

### 允许 PowerShell 执行本地脚本(必要)

```
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

### 安装 Scoop

```
iwr -useb get.scoop.sh | iex  
iex (new-object net.webclient).downloadstring('https://get.scoop.sh')
```

### 管理员安装 Scoop

```
iex "& {$(irm get.scoop.sh)} -RunAsAdmin"
```





















> https://p3terx.com/archives/scoop-the-best-windows-package-manager.html
>
> https://www.cnblogs.com/Edge-coordinates/p/15130184.html