# Scoop文档

> 纯纯照抄别人的,不用看了,给自己用的

## 1.安装 Scoop

### 用户安装路径

```
$env:SCOOP='D:\Scoop'
[Environment]::SetEnvironmentVariable('SCOOP', $env:SCOOP, 'User')
```

### 全局安装路径

```
$env:SCOOP_GLOBAL='D:\Scoop'
[Environment]::SetEnvironmentVariable('SCOOP_GLOBAL', $env:SCOOP_GLOBAL, 'Machine')
```

### 允许 PowerShell 执行本地脚本(必要)

```
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

### 安装 Scoop

```
# 官方
iwr -useb get.scoop.sh | iex
# 国内
iwr -useb https://raw.iqiq.io/Goojoe/scoop-docs/master/install-scoop.sh | iex
```

### 管理员安装 Scoop

```
# 官方
iex "& {$(irm get.scoop.sh)} -RunAsAdmin"
# 国内
iex "& {$(irm https://raw.iqiq.io/Goojoe/scoop-docs/master/install-scoop.sh)} -RunAsAdmin"
```

### 安装Git(万物之源)

```
scoop install -g git aria2 sudo
```

### 代理设置

```
# 设置代理
scoop config proxy 127.0.0.1:7891
# 检查代理
scoop config proxy
# 取消代理
scoop config rm proxy
```

### 开启多线程下载

```
scoop install aria2
```

使用 `scoop config` 命令可以对 Aria2 进行设置，比如 `scoop config aria2-enabled false` 可以禁止调用 Aria2 下载。以下是与 Aria2 有关的设置选项：

- `aria2 enabled`: 开启 Aria2 下载，默认`true`
- `aria2-retry-wait`: 重试等待秒数，默认`2`
- `aria2-split`: 单任务最大连接数，默认`5`
- `aria2-max-connection-per-server`: 单服务器最大连接数，默认`5` ，最大`16`
- `aria2-min-split-size`: 最小文件分片大小，默认`5M`

更详细的见[这里](https://aria2.github.io/manual/en/html/aria2c.html#cmdoption-retry-wait)，博主在这里推荐以下优化设置，单任务最大连接数设置为 `32`，单服务器最大连接数设置为 `16`，最小文件分片大小设置为 `4M`

```
scoop config aria2-retry-wait 4
scoop config aria2-split 64
scoop config aria2-max-connection-per-server 16
scoop config aria2-min-split-size 4M
```

### 利用 aria2 进行断点续传

先看具体示例：

scoop 更新 vscode 时下载到 80% 的时候 失败了（安装时处理方法也一样）。我们需要在提示中找到如下内容：

```
'D:\Scoop\Applications\apps\aria2\current\aria2c.exe' --input-file='D:\Scoop\Applications\cache\vscode-portable.txt' 
--user-agent='Scoop/1.0 (+http://scoop.sh/) PowerShell/5.1 (Windows NT 10.0; Win64; x64; Desktop)' 
--allow-overwrite=true --auto-file-renaming=false --retry-wait=2 
--split=5 --max-connection-per-server=5 --min-split-size=5M 
--console-log-level=warn --enable-color=false --no-conf=true 
--follow-metalink=true --metalink-preferred-protocol=https 
--min-tls-version=TLSv1.2 --stop-with-process=15584 --continue
```

我们从上面的信息中提取出下面的命令；若要进行断点续传，只需再次执行下面的命令即可：

```
aria2c.exe --input-file='D:\Scoop\Applications\cache\vscode-portable.txt'
```

当提示下载完成后，我们需要再次运行 scoop 对应的 app 更新命令（或安装命令），即可完成 app 更新（或安装）：

```bash
scoop update vscode-portable
```

## 2.基础使用

最基础的使用方法和其它包管理器类似，这里就不做赘述了，直接上命令列表：

- `scoop search <app>` - 搜索软件
- `scoop install <app>` - 安装软件
- `scoop info <app>` - 查看软件详细信息
- `scoop list` - 查看已安装软件
- `scoop uninstall <app>` - 卸载软件，`-p`删除配置文件。
- `scoop update` - 更新 scoop 本体和软件列表
- `scoop update <app>` - 更新指定软件
- `scoop update *` - 更新所有已安装的软件
- `scoop checkup` - 检查 scoop 的问题并给出解决问题的建议
- `scoop help` - 查看命令列表
- `scoop help <command>` - 查看命令帮助说明

### 迁移到新电脑

将文件夹拷贝到新电脑,将文件夹添加到环境变量然后`scoop reset *`

## 3.进阶使用

### 添加 bucket

```
$ scoop bucket known
main
extras
versions
nightlies
nirsoft
php
nerd-fonts
nonportable
java
games
jetbrains
```

这里给出一个第三方软件源[Scoop Directory](https://github.com/rasa/scoop-directory),它记录了 Github 上各种 bucket。

### 删除bucket

```
scoop bucket rm <bucket>
```

### 主要Bucket

```
scoop bucket add extras
scoop bucket add nerd-fonts
scoop bucket add java
scoop bucket add jetbrains
# bbdwon
scoop bucket add scoop-bucket https://github.com/canvascat/scoop-bucket
# 国内常用软件
scoop bucket add scoopet https://github.com/ivaquero/scoopet
```

### 第三方bucket

```
scoop bucket add <url>
```

### 清理安装包缓存

- `scoop cache show` - 显示安装包缓存
- `scoop cache rm <app>` - 删除指定应用的安装包缓存
- `scoop cache rm *` - 删除所有的安装包缓存

### 禁用安装缓存

- `scoop install -k <app>`
- `scoop update -k *`

### 删除旧版本软件

- `scoop cleanup <app>` - 删除指定软件的旧版本
- `scoop cleanup *` - 删除所有软件的旧版本

### 在同一程序的不同版本之间切换

使用命令：`scoop reset [app]@[version]`
实例：

```
scoop reset idea-ultimate-eap@201.6668.13

scoop reset idea-ultimate-eap@201.6073.9

# 切换到最新版本
scoop reset idea-ultimate-eap
```

### 全局安装

```
scoop install sudo
sudo scoop install -g <app>
```

使用 `scoop list` 命令查看已装软件时，全局安装的软件末尾会有 `*global*` 标志。

```
➜ scoop list
Installed apps:

  7zip 19.00
  adb 30.0.0
  aria2 1.35.0-1
  busybox 3466-g53c09d0e1
  CascadiaCode-NF 2.1.0 [nerd-fonts]
  colortool 1904.29002
  dark 3.11.2 *global*
  ffmpeg 4.2.3
  figlet 1.0-go
  FiraCode-NF 2.1.0 [nerd-fonts]
  git 2.26.2.windows.1 *global*
  innounp 0.49
  iperf3 3.1.3
  lessmsi 1.6.91 *global*
  lxrunoffline 3.4.1 [extras]
  nano 4.9-4
  neofetch 7.0.0
  nodejs-lts 12.17.0 *global*
  python 3.8.3 *global*
  rclone 1.52.0
  rufus 3.10 [extras]
  screentogif 2.24.2 [extras]
  sudo 0.2020.01.26
```

此外对于全局软件的更新和卸载等其它操作，都需要加上 `-g` 选项：

- `sudo scoop update -g *` - 更新所有软件（且包含全局软件）
- `sudo scoop uninstall -g <app>` - 卸载全局软件
- `sudo scoop uninstall -gp <app>` - 卸载全局软件（并删除配置文件）
- `sudo scoop cleanup -g *` - 删除所有全局软件的旧版本
- `sudo scoop cleanup -gk *` - 删除所有全局软件的旧版本（并清除安装包包缓存）

## 4.一些使用实例

### 安装和切换JDK、Python的版本

> 转载自[Scoop包管理器的相关技巧和知识](https://www.thisfaner.com/p/scoop/#安装和切换jdkpython的版本)，相关内容仅作介绍，一般来说版本管理还是交给专门软件比较好，python可以使用虚拟环境或Anaconda.

这里需要使用`scoop reset`它的作用是：重置一个应用程序来解决冲突。
命令格式为：`scoop reset <java>[@<version>]`

安装和切换不同的 JDK 版本：

```powershell
PS C:> scoop bucket add java

PS C:> scoop install oraclejdk
Installing 'oraclejdk' (12.0.2-10) [64bit]

PS C:> scoop install zulu6
Installing 'zulu6' (6.18.1.5) [64bit]

PS C:> scoop install openjdk10
Installing 'openjdk10' (10.0.1) [64bit]

PS C:> java -version
openjdk version "10.0.1" 2018-04-17
OpenJDK Runtime Environment (build 10.0.1+10)
OpenJDK 64-Bit Server VM (build 10.0.1+10, mixed mode)

PS C:> scoop reset zulu6
Resetting zulu6 (6.18.1.5).
Linking ~\scoop\apps\zulu6\current => ~\scoop\apps\zulu6\6.18.1.5

PS C:> java -version
openjdk version "1.6.0-99"
OpenJDK Runtime Environment (Zulu 6.18.1.5-win64) (build 1.6.0-99-b99)
OpenJDK 64-Bit Server VM (Zulu 6.18.1.5-win64) (build 23.77-b99, mixed mode)

PS C:> scoop reset oraclejdk

PS C:> java -version
java version "12.0.2" 2019-07-16
Java(TM) SE Runtime Environment (build 12.0.2+10)
Java HotSpot(TM) 64-Bit Server VM (build 12.0.2+10, mixed mode, sharing)
```

### 安装和切换不同的 Python 版本：

```powershell
scoop bucket add versions # add the 'versions' bucket if you haven't already

scoop install python27 python
python --version # -> Python 3.6.2

# switch to python 2.7.x
scoop reset python27
python --version # -> Python 2.7.13

# switch back (to 3.x)
scoop reset python
python --version # -> Python 3.6.2
```

# 5.常用命令总结

emm想要记住上文那么多的知识有些困难，这里总结一下 Scoop 在日常使用中常用的命令：

```powershell
# 更新 scoop 及软件包列表
scoop update

## 安装软件 ##
# 非全局安装（并禁止安装包缓存）
scoop install -k <app>
# 全局安装（并禁止安装包缓存）
sudo scoop install -gk <app>

## 卸载软件 ##
# 卸载非全局软件（并删除配置文件）
scoop uninstall -p <app>
# 卸载全局软件（并删除配置文件）
sudo scoop uninstall -gp <app>

## 更新软件 ##
# 更新所有非全局软件（并禁止安装包缓存）
scoop update -k *
# 更新所有软件（并禁止安装包缓存）
sudo scoop update -gk *

## 垃圾清理 ##
# 删除所有旧版本非全局软件（并删除软件包缓存）
scoop cleanup -k *
# 删除所有旧版本软件（并删除软件包缓存）
sudo scoop cleanup -gk *
# 清除软件包缓存
scoop cache rm *
```

## 遗留的问题

### 网络问题

#### fatal: unable to access 'https://github.com/lukesampson/scoop-extras/': OpenSSL SSL_read: Connection was reset, errno 10054



```powershell
fatal: unable to access 'https://github.com/lukesampson/scoop/': OpenSSL SSL_read: Connection was reset, errno 10054
Update failed.
fatal: unable to access 'https://github.com/lukesampson/scoop-extras/': OpenSSL SSL_read: Connection was reset, errno 10054
```

遇到这种情况请**检查代理**，参考[代理设置](https://www.cnblogs.com/Edge-coordinates/p/15130184.html#代理设置)

#### 手动配置http代理后aria2多线程下载不可使用

如题，求解决方案，如果有大佬知道可以分享在评论区，谢谢~

### 安装软件失败

#### 网络问题导致app安装失败

step1:**检查代理**,下载7zip,aria2失败需要开代理，可以直接将代理软件设为全局，也可以自己配置。
step2:**手动下载安装文件**
一个实例：



```ruby
ERROR Download failed! (Error 1) An unknown error occurred
ERROR https://mediaarea.net/download/binary/mediainfo/19.09/MediaInfo_CLI_19.09_Windows_x64.zip
    referer=https://mediaarea.net/download/binary/mediainfo/19.09/
    dir=D:\Scoop\Applications\cache
    out=mediainfo#19.09#https_mediaarea.net_download_binary_mediainfo_19.09_MediaInfo_CLI_19.09_Windows_x64.zip

ERROR & 'D:\Scoop\Applications\apps\aria2\current\aria2c.exe' --input-file='D:\Scoop\Applications\cache\mediainfo.txt'
```

我们可以发现文件的下载路径和下载后的文件名称，这里 `out=` 后面的压缩包就是下载后 文件的名称，(也可以在 scoop 的 `cache` 目录下的 `mediainfo.txt` 文件中找到下载路径与文 件名称)

然后我们可以尝试在浏览器或其他下载程序中（可以 fq 的程序中）下载该程序，下载完成 后再更改文件名并将其放入 scoop 的 `cache` 目录，最后再次运行 `scoop install mediainfo` 即可安装。

#### 其它问题导致安装失败

[![img](https://fastly.jsdelivr.net/gh/Edge-coordinates/PicBed/imgs_for_blogs/scoop安装软件失败.png)](https://fastly.jsdelivr.net/gh/Edge-coordinates/PicBed/imgs_for_blogs/scoop安装软件失败.png)
下载其他软件失败看看你有没有装某些软件，如sudo(用于全局安装的，导致某些语法无法使用，详见前文说的全局安装),反正我吧sudo删掉就好了~

#### Scoop安装软件失败的原因分类

1. 未及时更新仓库
2. 连不上Github，因为众所周知的原因，解决方案的话可以参考代理设置中的重启/更换代理（参考：[代理设置](https://www.cnblogs.com/Edge-coordinates/p/15130184.html#代理设置)），还不可以的话可以尝试在浏览器中打开Github，然后再尝试。

# 6.常用软件

全局软件

```
# 编程语言_运行库
scoop install -g nodejs-lts
scoop install -g python
scoop install -g oraclejdk-lts
# 工具软件
scoop install -g rclone
scoop install -g adb
scoop install -g ffmpeg
scoop install -g bbdown
# 字体
scoop install FiraCode
# 自动补全
scoop install scoop-completion
# 标签增强
scoop install QTTabBar
# 虚拟机
scoop install VMware-Workstation-Pro
scoop install virtualbox-with-extension-pack
# 录屏
scoop install obs-studio
# 百度网盘
scoop install baidunetdisk
scoop install 
scoop install 
```













> https://p3terx.com/archives/scoop-the-best-windows-package-manager.html
>
> https://www.cnblogs.com/Edge-coordinates/p/15130184.html