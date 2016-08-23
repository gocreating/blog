title: OpenShift架站
date: 2015-02-09 12:47:32
categories:
  - Development
---

OpenShift是一個RedHat提供的雲端Paas系統，支援許多常見的語言、資料庫及框架，主機是使用AWS的服務，也有提供免費使用的方案，本文將帶你使用OpenShift架出WordPress部落格

<!--more-->

## 註冊OpenShift

進入[OpenShift官方網站](https://www.openshift.com/)，點選右上角的`SIGN UP`，註冊完收取驗證信即可登入使用

## 建立應用程式

點選`Add Application...`來建立新的應用程式

![](Applications-OpenShift-Online-by-Red-Hat.png)

接著請搜尋`WordPress`，會篩出一個內建的`WordPress 4`映像檔，請選擇它來建立應用程式

![](Create-a-New-Application.png)

選完會出現詳細的表單給你填寫，照著自己的規劃去填寫即可
其中要特別注意的是`Source Code`欄位，請填寫`master`，代表WordPress的Source Code來源是Github的Master branch
填完表單建立應用程式的時候會等待特別久，耐心稍候吧！

## 設定WordPress

打開剛剛建立的應用程式的網址就可以開始WP的初始化設定囉!

### 圖片外框

如果安裝外掛，會和Markdown的Latex語法產生的圖片衝突，所以必須自己調CSS

```css
img:not(.latex) {
  -webkit-box-shadow: 0 0 6px 6px #d8d8d8 !important;
  box-shadow: 0 0 6px 6px #d8d8d8 !important;
}
```

### WordPress外掛

#### JetPack

如果你想使用Markdown語法，建議可以安裝`JetPack`這支外掛。裝完啟用Markdown後，在新增文章時預設會是文字模式，在這邊打上你的Markdown語法就可以囉！切記不要切換到預覽模式，它會更動到你的Markdown文字，導致縮排通通消失！

在預設的佈景主題中，inline-formatting的樣式很不顯眼，可以用Github的樣式取代

```css
code:before {
    letter-spacing: -.2em;
    content: "0a0";
}

code:after {
    letter-spacing: -.2em;
    content: "0a0";
}

code {
    border-radius: 3px;
    padding: .2em 0;
    background-color: rgba(0,0,0,0.04);
    font-size: 85%;
}
```

#### Crayon Syntax Highlighter

如果你和我一樣會需要在Blog中嵌入程式碼，可以試試這個Syntax Highlighter

#### Disqus Comment System

很常見也很實用的第三方留言系統

預設的樣式會100%填滿文章區塊的寬度，如果需要，可以用下面的語法修正

```css
#disqus_thread {
    max-width: 1280px;
    padding: 1.5em 8.333% 3em;
}
```