title: Chocolatey-Windows下的套件管理工具
date: 2015-03-25 12:45:21
categories:
  - Uncategorized
---

`Chocolatey`[^1]是一個在windows環境下的package manager，類似Ubuntu/Debian的apt-get，透過簡單的命令列指令，就能方便且輕鬆地管理電腦上安裝的軟體!

<!--more-->

## 安裝Chocolatey

1. 以系統管理員身分打開cmd

2. 貼上以下指令後按下Enter

```
@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%chocolateybin
```

> #### 自訂Chocolatey安裝路徑
> 新增環境變數`ChocolateyInstall`，內容是安裝路徑，例如`D:\Programs\Chocolatey`
>
> #### 移除Chocolatey
> 直接刪除`C:\ProgramData\chocolatey`資料夾與環境變數`ChocolateyInstall`即可，詳細說明請參考[官方連結](https://github.com/chocolatey/chocolatey/wiki/Uninstallation)

## 安裝套件

進入官方的[Packages列表](https://chocolatey.org/packages)，挑選想要安裝的Package，每個Package後面都有附安裝指令，不過形式都是`choco install 套件名稱`，所以其實只要知道套件名稱，直接打開cmd來安裝就可以囉

> #### 自訂套件安裝路徑
> 新增環境變數`chocolatey_bin_root`，內容是路徑，但不包含磁碟代號，例如設定成`ChocolateyPrograms`，且Chocolatey本身是安裝在D槽，則套件會被安裝到`D:\ChocolateyPrograms\`，但並不是所有套件都支援，測試後的結果滿令人失望的
> 詳細說明請參考[官方連結](https://github.com/chocolatey/chocolatey/wiki/GettingStarted)

> ### Note
> 只要需要執行`choco`指令，就必須以系統管理員身分來執行cmd

[^1]: [Chocolatey](https://chocolatey.org/)