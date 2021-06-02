---
title:  "[Unix] bash基本使用(4)"
date: 2021-05-23 00:00:01
description: "bash的命令別名與歷史命令"
tags: ["unix","bash","command"]
categories: unix
---

## 命令別名

**設定指令**

使用bash時，有時候會有一些很長又很常用的指令，每次打都會覺得花費很多時間，這時候就可以使用命令別名來設定指令，例如`ls -al`我們想把它簡化成`ll`，就可以這樣做

```bash
$ alias ll='ls -al'
$ ll # 此指令就會執行ls -al
```

使用方式就是`alias 別名='指令'`

`alias`的定義規則和變數規則幾乎相同，只是`alias`後面接的是你要用的別名而已。`alias`除了可以新增指令外，也可取代現有的指令，例如使用刪除`rm`時，有時候會失手刪除一些資料，就可以將rm做點修改

```bash
alias rm='rm -i'
```

這樣的話，使用rm時就會再次確認是否移除檔案。

**列出指令**

要列出目前的alias命令時，可以直接輸入指令`alias`不帶其他參數

```bash
$ alias
```

**移除指令**

若要移除alias命令時，可以使用指令`unalias`

```bash
$ unalias ll
```

※ 其實 `ls` 也是alias的指令，其由來如下

```bash
$ alias ls='ls --color=auto'
```

## 歷史命令

bash也有查詢歷史命令的服務，就是指令`history`，他有許多的用法，底下就來簡單介紹

### Command Syntax

```bash
history [n]
history [-c]
history [-anrw] histfiles
```

* `n` - 數字，表示要印出最近n筆的命令
* `-c` - 將目前shell中所有history內容消除
* `-a` - 將目前新增的history指令新增放入histfiles，若沒有加histfiles，則預設寫入~/.bash_history
* `-r` - 將histfiles的內容讀到目前shell的history記憶中
* `-w` - 將目前的history寫入histfiles

### 範例

* 列出目前記憶體內的所有history記憶

  ```bash
  $ history
  # ...ignore
  10008  alias
  10009  man history
  10010  git hist
  # 第一欄表示這個指令在shell中的代碼，第二欄表示指令
  ```

* 列出目前最近的三筆資料

  ```bash
  $ history 3
  10008  alias
  10009  man history
  10010  git hist
  ```

* 將目前的資料寫入histfile中

  ```bash
  $ history -w
  # 預設情況會將歷史紀錄寫入~/.bash_history中
  ```

### 歷史命令的讀取與紀錄流程

正常情況下歷史命令的讀取與紀錄如下

* 使用bash登入時，會主動從`~/.bash_history`讀取命令，儲存幾筆資料則是由環境變數`HISTSIZE`決定
* 若此次登入下達20筆指令時，登出時才會將這些資料更新到`~/.bash_history`中
* 也可使用`history -w`來強制寫入，當寫入時，就會將舊的資料移除，僅保留最新的

除了前面這些外，`history`還有其餘的功能

```bash
# 執行第幾筆指令的意思
$ !number
# 往前搜尋command開頭的指令，並執行
$ !command
# 執行前一筆指令(相當於按上，再按Enter)
$ !!
```

Example:

```bash
$ history
# ...ignore
10008  alias
10009  man history
10010  git hist
# 執行第10010筆指令，也就是git hist
$ !10010
# 往前搜尋man開頭的指令，並執行，此處及man history
$ !man
# 執行前一筆指令，此處就是!man
$ !!
```

※ 當同一帳號被同時登入時，每個bash都有自己儲存的1000筆記錄存在記憶體中，當登出時才會被記錄下來，所以最後一個登出的bash才會被存在`~/.bash_history`中，其他的bash不會被記錄下來，會被覆蓋掉

## Reference
* [鳥哥的 Linux 私房菜 -- 第十章、認識與學習BASH](http://linux.vbird.org/linux_basic/0320bash.php)