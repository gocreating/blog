title: 使用hexo架設github page
date: 2015-07-22 23:42:26
tags:
    - nodejs
    - github
    - hexo
---

`github page`說難不難，說簡單卻也不簡單，花了很長一段時間總算漸漸搞懂它的運作，未來將把舊的部落格遷移到github page上，順便以此文稍作整理

<!-- more -->

## [Github Pages](https://pages.github.com/)

Github pages是一個免費靜態網站空間，原是提供給開源專案作為免費官方網站使用，但因為提供了免費1GB[^1]的容量，現在也有許多geeks使用github pages來當作個人部落格

### Quick Start

如果你對git已十分熟悉，那麼要使用github pages絕對輕而易舉:

1. 建立新repo，並命名為`[your_github_username].github.io`
   請將[your_github_username]替換為自己的帳號
2. 撰寫你的靜態網頁`index.html`
3. 將`index.html`與使用到的靜態檔案push至剛剛建立的repo上

接著打開`http://[your_github_username].github.io`就可以看到上傳上去的index檔案了

### Advance Usage

上面所述的使用方式過於陽春，因此通常會搭配部落格框架使用，github pages官網推薦的框架是`jekyll`，但jekyll使用的是ruby語言，與我慣用的nodejs語言不同，因次改用hexo框架

這些框架會提供簡易的cli操作與自動化，讓作者們輕鬆的增修文章、產生靜態檔案、部署網站，撰寫一篇新文章的流程大致上會是

1. 使用cli新增markdown文章檔案
2. 打開編輯器撰寫文章
3. 使用cli產生靜態檔案
   此步驟會將撰寫好的markdown檔案套上框架內的layout，產生相對應的html檔案
4. 使用cli部署到Github pages或是其他站點(例如:heroku)

## [Hexo](https://hexo.io/)

### 安裝

```
$ npm install hexo-cli -g
```

### 新增並初始化專案

```
$ hexo init [your_blog]
$ cd [your_blog]
$ npm install
```

### 常用指令

```
$ hexo new post "新文章標題"
```

會在`source/_post`下新增一個markdown檔案，編輯此檔案即可開始撰寫文章

```
$ hexo g -w # hexo generate --watch
```

將markdown文章檔案轉換成靜態html, css等檔案，並監視檔案變動

```
$ hexo s # hexo server
```

啟動local端的伺服器，port預設是4000

```
$ hexo d # hexo deploy
```

部署你的網站

### 部署到github pages

先安裝部署所需的Plugin[^2]

```
$ npm install hexo-deployer-git --save
```

修改`_config.yml`，新增底下的設定，並將[your_github_username]替換掉

```
# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: https://github.com/[your_github_username]/[your_github_username].github.io.git
```

從此以後只要在command line輸入

```
$ hexo d
```

就會自動產生靜態檔案，並且上傳到github上了!

### 升級Markdown Plugin

hexo內建的markdown處理器`hexo-renderer-marked`無法支援footnote語法，功能也不夠強大，因此筆者使用`hexo-renderer-markdown-it`取代

使用下方指令將Plugin汰舊換新

```
$ npm un hexo-renderer-marked --save
$ npm i hexo-renderer-markdown-it --save
```

此Plugin支援的所有`_config.yml`設定參數如下

```
# Markdown-it config
## Docs: https://github.com/celsomiranda/hexo-renderer-markdown-it/wiki
markdown:
  render:
    html: true
    xhtmlOut: false
    breaks: true
    linkify: true
    typographer: true
    quotes: '“”‘’'
  plugins:
    - markdown-it-footnote
    - markdown-it-sup
    - markdown-it-sub
  anchors:
    level: 2
    collisionSuffix: 'v'
    permalink: true
    permalinkClass: header-anchor
    permalinkSymbol: ¶
```

### 客製化佈景主題

內建的主題`landscape`其實滿醜的，如果想自製一份客製化的主題，可以複製`themes/landscape`資料夾，改成自訂的主題名稱，然後進行客製化調整，最後再將`_config.yml`中的`theme`名稱改為新主題的資料夾名稱即可

官網與社群也有許多好看的佈景主題可以直接下載回來使用，不過我想體驗一下google最近釋出的material design lite，所以自己複製了內建的佈景主題進行了客製化，佈景主題的製作太過於瑣碎，就不贅述了

[^1]: [Github pages site size limits?](http://stackoverflow.com/questions/28706454/github-pages-site-size-limits)
[^2]: [hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git)