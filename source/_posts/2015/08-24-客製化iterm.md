title: 客製化iterm
date: 2015-08-24 23:31:25
tags:
    - iterm
    - bash
---
　
<!-- more -->

## 主題

1. 前往[Iterm2-color-schemes](http://iterm2colorschemes.com/)下載主題包並解壓縮
2. 打開iTerm，按下`cmd+,`調整設定檔
3. `Profiles` -> `Colors` -> `Load Presets...` -> `Import...`
  
  ![](preferences.png)

4. 選擇主題包schemes資料夾內的任意`.itermcolors`檔案
5. 再按一次`Load Presets...` -> 選擇主題

個人認為`Chalk`這款主題最耐看而且很柔和不刺眼

![](chalk.png)

## Pormpt

編輯bash設定檔

```
$ vim ~/.bashrc
```

到[Bash $PS1 Generator](https://www.kirsle.net/wizards/ps1.html)製作屬於自己的prompt訊息，例如：

```
export PS1="\[$(tput setaf 6)\]\A \[$(tput setaf 3)\]\u\[$(tput setaf 7)\]@\h \[$(tput setaf 2)\][\w] \[$(tput setaf 7)\]$ \[$(tput sgr0)\]"
```

將這段script加到.bashrc檔案結尾，重啟terminal或是用以下指令就會生效了

```
$ source ~/.bashrc
```

效果圖如下：

![](bash_prompt.png)
