title: Sublime Linter-即時語法除錯的好幫手
categories:
  - Development
  - Tools
date: 2015-04-22 16:57:10
---

使用Visual Studio或Eclipse等大型IDE的其中一個好處就是，當你還沒有Compile程式之前，IDE就會幫你指出Syntax Error，避免編譯時才出現Compiler Error。Sublime雖然只是輕量的編輯器，但是只要安裝`SublimeLinter`這個Package就一樣能達到防患未然的效果![^1]

<!--more-->

SublimeLinter支援除錯的語言相當多樣，本文以寫網站時常用到的js檔案的為例，如果你想替別種語言除錯，可以參考Package Control上的安裝說明頁

## 安裝相依

SublimeLinter底層的運作是靠NodeJs，所以必須先在全域下安裝jshint這個Node Package

    $ npm install -g jshint

## 安裝

1.  打開Package Install

    在Sublime按下快速鍵`Ctrl`+`Shift`+`P`打開Command Palette，接著尋找`Package Controll: Install Package`</p>
2.  搜尋並安裝`Sublime​Linter`與`Sublime​Linter-jshint`[^2]兩個Package

3.  重啟Sublime

## 設定

<p>使用上述快速鍵打開Command Palette，尋找`Preferences: SublimeLinter Settings`，選擇後面標註`User`的項目（不要選標註`Default`的）

以我個人的喜好，會把`show_errors_on_save`這個項目設成`false`，避免存檔時跳出擾人視窗

## 效果

字串漏掉單引號時:

![](Sublime-Linter-Demo-1.png)

Statement結尾漏掉分號時:

![](Sublime-Linter-Demo-2.png)

[^1]: [How to Catch Your Errors in Sublime Text 3](https://scotch.io/tutorials/how-to-catch-your-errors-in-sublime-text-3)
[^2]: [SublimeLinter-jshint](https://packagecontrol.io/packages/SublimeLinter-jshint)