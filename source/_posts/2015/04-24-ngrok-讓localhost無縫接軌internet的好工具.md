title: ngrok-讓localhost無縫接軌internet的好工具
date: 2015-04-24 18:25:49
categories:
  - Development
  - server
---

你是否曾經在以wifi上網的時候，臨時需要讓朋友連進你的localhost server呢?假如AP的管理者是自己，或許還可以設個port forwarding，再給出AP的IP位址，順利搞定!但如果使用的是公用AP?或是server是在虛擬機內?甚至是更複雜的情況呢?別擔心，`ngrok`這套工具就是為了解決這種問題而產生的!

<!--more-->

## 下載

請至[ngrok官方下載頁](https://ngrok.com/download)，依照自己的作業系統下載

ngrok是免安裝的，windows版下載回來解壓後就是一個獨立的執行檔，本文也是使用windows版本作為說明範例

## 如何使用

雙擊打開`ngrok.exe`或是使用cmd進入ngrok所在目錄，並輸入`ngrok`，兩種方式都可以

經典用法就是單純expose localhost:

```
$ ngrok http localhost:4000
```

![](ngrok-cmd.png)

稍後片刻就會出現簡單易懂的domain name和local端的對應關係:

![](ngrok-result.png)

複製http網址後直接在瀏覽器打開就可以連到囉!

![](ngrok-browser.png)

## 進階用法

### 自訂Subdomain

預設使用的subdomain都是一串亂數hash，如果想要自訂的Subdomain，可以到官網註冊一個帳號，註冊完成後登入並進到Dashboard會看到一串可用的token

![](authtoken.png)

複製後照著提示的指令將token寫入設定:

```
$ ./ngrok authtoken [your_token]
```

接著只要在啟動ngrok時加個switch指定domain name即可:

```
$ ./ngrok http -subdomain=[your_custom_subdomain] localhost:4000
```

> #### Note
> 註冊時未必要填真實資料，註冊完後就會自動登入了，所以Token算是頗容易拿到
>
> #### 官方文件
> - [Custom subdomain names](https://ngrok.com/docs#subdomain)
> - [docs](https://ngrok.com/docs)

## 原理

這樣的技術是使用了`reverse proxy`，在遠端操控軟體（如:teamviewer）中很常見，不過技術細節我也不是很懂，就不贅述以免誤人子弟

## 結論

這工具對於Web的Demo真的是個大福音，就舉個我親自碰到的故事作為例子

一位黑客松的戰友曾在某天找我重開server，希望讓他在面試的時候可以Demo作品，但我的server是透過宿舍房間內的AP上網，也就是在NAT背後，那時還不知道有ngrok，傻傻地認為除了port forwarding就別無他法了

另外，自己在架設專題作品的運作環境時，也碰到了需要使用VM當作server的麻煩事，有了ngrok也許能夠簡化環境的配置

除了上面親身經歷的故事，其實在公共空間裡要分享slides、demo網站都不是一件容易的事情，如果能善用ngrok，想必能更加提升講者與聽眾的互動品質