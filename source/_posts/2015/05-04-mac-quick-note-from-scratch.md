title: Mac Quick Note from Scratch
date: 2015-05-04 17:50:29
categories:
  - Mac
---
　
<!--more-->

## App List

- iterm
  more customizable terminal
- alfred[^1]
  hotkey
- home brew
  package manager
- xcode
  ???
- nodejs
- git
- iwork
  refer to <http://mrmad.pixnet.net/blog/post/165923400/2>

## Useful Tips

### Add alias to terminal[^2]

1. Enter your home directory
2. Create `.bash_profile`

  ```
  if [ -f ~/.bashrc ]; then
      source ~/.bashrc
  fi
  ```

3. Create `.bashrc`

  ```
  alias ll='ls -al'
  alias ptt='ssh bbsu@ptt.cc'
  ```

[^1]: <http://appleuser.com/2014/05/28/alfred/>
[^2]: <http://coder.aqualuna.me/2012/03/bashrc-in-mac-terminal-os-x-lion.html>