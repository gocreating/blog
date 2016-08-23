title: Sublime vs. Atom
date: 2016-02-07 00:19:03
tags:
---

Sublime和Atom是目前堪稱最受好評的兩款編輯器，我本來是Sublime忠實用戶，不過最近感受到可以幾乎無痛地移植到Atom，於是開始深入體驗Atom。先說結論，Atom大勝，但是仍然需要一點時間適應轉換過程...

<!--more-->

這篇文章其實有點有感而發，大部分列出來的項目都是我開發上曾經遇過的痛處或是有助提升寫code效率的優良功能，像是Sublime的廣告、快速鍵使用性...等，其他細節功能就不在本文裡詳記了

## 常用功能差異比較表

在此部分首先比較`內建`功能，透過套件才能做到的功能在後面部分比較

項目 | Sublime | Atom
-|-|-
廣告 | ✔ |
Mini Map | ✔ |
套件管理 |   | ✔
GUI套件/主題管理 |   | ✔
Git整合輔助 |   | ✔
顯示Git隱藏檔 |   | ✔
支援開啟大檔 | ✔ |
自動開啟上次關閉前的檔案 | ✔ |
Markdown預覽 |   | ✔
選取單字時Highlight其餘單字 | ✔ |
快速鍵使用性 | 正常 | Mac下需要切換為英文模式
自動完成 | Tab鍵 | 需要打冗字後按Enter鍵
快速鍵(Ctrl+[shift]+tab)換頁 | 爛透了 | 正常使用

共有的功能（像是可以多行編輯）就不列在表上了

## 常用快速鍵差異比較

項目 | Sublime | Atom
-|-|-
Toggle File Tree | cmd + b | cmd + \
Multiple Line Comment | cmd + shift + / | 無

## 結論

選擇Atom

- 無廣告
- GUI Package Control
- 完整的Package系統  
  有統一的Package README介紹，主要是有Gif預覽效果，而且在Atom編輯器內的GUI就可以看到，省去許多google爬文的時間
- 套件的便利性和功能性都比Sublime高出一個層級
- 介面上與Git整合  
  可以看到Untracked Files、Uncommited Files、Modified Lines

## Atom基本設定

### 移除多餘空行

ref: [Atom is unusable with PHP, ASP, etc](https://discuss.atom.io/t/atom-is-unusable-with-php-asp-etc/9911)

### 顯示Space字元

### 每行限制80字元寬

## Atom進階設定

### UI主題

TO-DO

### Syntax主題

內建其實就有許多主題了，我認為Light系列太傷眼睛，一律不考慮，Dark系列雖然好點，但是對比實在是太高了，看著code十秒鐘我的眼睛就快瞎了，如果你和我一樣也是習慣Sublime預設的Syntax主題，推薦使用[Monokai Shade](https://atom.io/themes/monokai-shade)

> # ps.
> 千萬別裝[Monokai Dark](https://atom.io/themes/monokai-dark)（背景是全黑，對比太強烈，很傷眼）或是[Atom Monokai Dark](https://atom.io/themes/atom-monokai-dark)（背景色正常，但程式碼反白時的顏色有問題）這兩套

### 套件

#### block-comment

由於Atom看起來並沒有`Multiple Line Comment`的快速鍵，只好透過這款套件來彌補了

#### [minimap](https://atom.io/packages/minimap)

#### language-babel

這款套件相較於Sublime的版本實在好太多了，我認為大勝的點在於支援JSX語法內HTML的Code Snippet和Comment，更詳細的介紹請看[官網](https://atom.io/packages/language-babel)

#### (syntax linter)

TO-DO

#### (terminal)

TO-DO (Not recommended)
