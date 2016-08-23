title: 物聯網平台-OpenMTC
date: 2015-03-27 15:20:15
categories:
  - Development
  - IoT
---

承前文[IoT/M2M 架構解析](http://gocreating.github.io/2015/03/22/iotm2m-%E6%9E%B6%E6%A7%8B%E8%A7%A3%E6%9E%90/)所述，OpenMTC是一個實作了IoT的M2M標準的平台，它是一個收費的平台，因為專題課程的關係，可以直接使用學校已經付費的授權。目前版本已經到Release 3，但因為語言考量，所以專題實作上是採用Release 2，使用的是NodeJS語言

<!--more-->

## 安裝步驟

### 安裝MongoDB

```bash
$ sudo apt-get install mongodb
```

### 安裝NodeJS

```bash
$ sudo apt-get install g++ gcc libssl-dev make
$ wget http://nodejs.org/dist/v0.10.36/node-v0.10.36.tar.gz
$ tar -xvzf node-v0.10.36.tar.gz
$ cd node-v0.10.36
$ ./configure
$ make
$ sudo make install
```

測試一下是否安裝成功:

```bash
$ node --version
```

### 安裝OpenMTC

```bash
$ cd OpenMTC_r2
$ chmod 777 -R .
$ sudo su
```

此時要修改一下Makefile，刪除以下行數:

1. 第5行的`MARKDOWN=tools/md2html`
2. 第26行的`$(MARKDOWN) README.md > openmtc-xSCL/lib/transport/simplegreeter/public/README.html`
3. 第64行的`$(MARKDOWN) README.md > openmtc-xSCL/lib/transport/simplegreeter/public/README.html`

> 這三行只是用來把markdown寫好的的Readme轉成html版的Documentation，但會因為symbolic link error導致Make到一半直接終止，如要Debug下去肯定曠日廢時，只好怒砍

接著就可以開始安裝了:

```bash
$ make setup
```

## 執行

打開兩個Terminal，第一個Terminal先執行nscl:

```bash
$ sudo ./openmtc-NSCL/bin/nscl
```

另一個執行gscl:

```bash
$ sudo ./openmtc-GSCL/bin/gscl
```

待兩個server的Terminal畫面都就緒，此時打開瀏覽器就可以分別觀察兩個Server的Resource Tree

NSCL: <http://localhost:15000/m2m>

![](nscl.png)

GSCL: <http://localhost:5000/m2m>

![](gscl.png)