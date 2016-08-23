title: Nodejs實作驗證碼系統
date: 2015-02-11 06:33:26
categories:
  - Nodejs
---

要實作驗證碼系統，其實可以分成兩種機制，一種是使用第三方工具，像是Google的`reCaptcha`，另一種則是安裝Package。

<!--more-->

## 方法一：reCaptcha

本文要探討的不是使用第三方工具的機制，但自己曾經用過reCaptcha，其實會感受到一些不便之處以及隱含的問題

- 必須申請
  這就不用多說了，使用第三方工具都是要申請、要填Domain Name的，Domain換了，就要到官網去修改
- 占空間
  雖然在設定上可以把整體寬高調整成最小化，可是仍然有個極限，讀者不妨試試將reCaptcha嵌入Bootstrap的回應式表單，在手機開啟後根本是個悲劇...
- 大陸網站可能無法使用
  這個問題我尚未證實，是因為剛好接到大陸公司的案，認證碼系統又剛好需要重寫，考慮到reCaptcha時，陸方和我對談的窗口疑慮所有Google的服務都被擋掉，reCaptcha照理說應該也無法使用

整體而言，個人並不推薦reCaptcha

## 方法二：Package

### [ccap](https://github.com/DoubleSpout/ccap)

大部分Packages都必須安裝很多相依套件，在各種平台上也會遇到各種問題，ccap這套件也不例外，必須安裝`.net framework 2.0 SDK`或`VS 2010`，但這又再次卡到軟體商業化的一個問題，今天我都用Nodejs這免費的工具了，為了一個套件，卻得花錢買VS，再進一步考慮安裝相依Libraries時併發的各種安裝細節問題，副作用實在多到讓人無法忍受，怎麼想都不划算，所以我也不推ccap

### [captchapng](https://github.com/GeorgeChan/captchapng/blob/master/lib/captchapng.js)

這個套件是目前為止我最滿意的，主要原因是它不用安裝任何相依，換句話說，不管在什麼樣的環境下，只要一句`npm install`就搞定，也不用花大把鈔票買一套Visual Studio

具體用法也一目了然，底下是簡單的範例程式，可以產生四位數字的驗證碼，並且儲存在session中

```js
router.get('/captcha', function(req, res){
  var captchapng = require('captchapng');
  var txt = parseInt(Math.random()*9000+1000);
  req.session.captcha = txt;
  var p = new captchapng(50,22,txt);
  p.color(255, 255, 255, 255);  // First color: background (red, green, blue, alpha)
  p.color(10, 10, 100, 255); // Second color: paint (red, green, blue, alpha)

  var img = p.getBase64();
  var imgbase64 = new Buffer(img,'base64');
  res.writeHead(200, {
      'Content-Type': 'image/png'
  });
  res.end(imgbase64);
});
```

整體而言易用無負擔，但是請不要高興的太早，這個套件只支援數字驗證碼，如果使用英文字母的話會出錯，原因可能與這個套件的寫法有關，不確定有無方法可以修正，有空再來研究吧...