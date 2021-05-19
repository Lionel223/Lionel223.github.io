---
layout: post
title:  "[Unix] bash基本使用(2)"
subtitle: "bash的變數"
tags: ["unix","bash","command"]
---

## Introduction

當在編寫較大型的shell script時，某些資料，例如路徑，可能會用在很多地方，如果我們把它設定為變數時，那麼當它需要修改時，我們就只需要修改變數的資料即可，所以變數在shell是一個十分方便的工具。

變數（variable）是一組文字或是符號所組成的字串，可以用來取代設定或是一串資料。以下就來說明如何取得、設定變數。

## 變數的取得與設定

* 取得變數

  變數的取得可使用`echo`指令，取得變數時，需在變數的前面加上`$`，以下面指令為例，我們想取得`user`內的值，可以這樣使用

  ```bash
  $ echo $user
  Lionel
  # 也可將變數加上大括號
  $ echo ${user}
  Lionel
  ```

* 設定、修改變數

  設定、修改變數，只要使用`=`即可設定，例如我們想將`user`的值修改為LIONEL，只要這樣即可

  ```bash
  $ user=LIONEL
  $ echo ${user}
  LIONEL
  ```

  ※ 當一個變數名稱尚未被設定時，預設內容是空的

  ```bash
  $ echo ${abc}
    （這邊會是空的）
  ```

* 取消變數

  使用指令`unset`即可取消變數

  ```bash
  $ unset user
  ```

## 變數的設定規則

* 變數與變數內容要使用等號連結，且等號兩邊不可空白，以下兩個是錯誤的設定

  ```bash
  $ user = Lionel
  $ user=Lionel Chen
  ```

* 變數名稱只能由英文與數字組成，且開頭不可是數字，以下為錯誤的變數名稱

  ```bash
  1user
  user_name
  ```

* 若變數內容包含空白字元，可使用`""`或是`''`將變數內容包起來，例如以下範例

  ```bash
  $ user="Lionel Chen"
  # 也可使用單引號
  $ user='Lionel Chen'
  ```

  不過單引號跟雙引號也有差異

  * 使用雙引號時，若裡面包含特殊字元，可以保留原先的特性

    ```bash
    # 雙引號內，$可以保留原先的特性
    $ var="Current lang is $LANG"
    $ echo var
    Current lang is zh_TW.UTF-8
    ```

  * 使用單引號時，若裡面包含特殊字元，會視為一般字元（純文字）

    ```bash
    # 單引號內，$視為一般字元
    $ var='Current lang is $LANG'
    $ echo var
    Current lang is $LANG
    ```

  * 也可使用跳脫字元`\`將特殊字元變成一般字元

    ```bash
    # 跳脫字元後面的空白視為一般字元
    $ user=Lionel\ Chen
    $ echo user
    Lionel Chen
    ```

* 若需藉由其餘指令的資訊，可使用此方式`` `command` ``或是`$(command)`

  ```bash
  # 取得核心版本設定
  $ version=$(uname -r)
  # 這樣也可
  $ version=`uname -r`
  $ echo $version
  19.6.0
  ```

  ※ 也可在執行指令時使用此變數

  ```bash
  # 進入核心模組目錄，此指令會先把內部的指令執行後，在執行外部的指令
  $ cd /lib/modules/$(uname -r)/kernel
  ```

* 若需要擴充變數內容，可使用`"$變數"`或是`${變數}`來累加內容

  ```bash
  PATH="$PATH":/home/bin
  PATH=${PATH}:/home/bin
  ```

* 若需要此變數在其他子程序內執行，需使用指令`export`使變數變成環境變數

  ```bash
  export PATH
  ```

* 通常大寫字元都是系統預設變數，建議自行設定的變數可使用小寫字元，以利區分

※ 可以將常用的路徑也寫成環境變數，加快速度

```bash
# 將工作目錄切成變數，即可快速切換
$ work="~/Desktop/company/"
$ cd $work
```

## Reference
* [bash shell變數設定](https://crmne0707.pixnet.net/blog/post/319842818-bash-shell-%e8%ae%8a%e6%95%b8%e8%a8%ad%e5%ae%9a)
* [鳥哥的 Linux 私房菜 -- 第十章、認識與學習BASH](http://linux.vbird.org/linux_basic/0320bash.php)

