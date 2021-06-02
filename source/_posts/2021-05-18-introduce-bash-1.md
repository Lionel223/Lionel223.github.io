---
title:  "[Unix] bash基本使用(1)"
date: 2021-05-18 00:00:00
description: "bash介紹與基本使用"
tags: ["unix","bash","command"]
categories: unix
---

## Shell介紹

shell是一種介於使用者與頁面的一種介面，他的功能是讓使用者能夠藉由指令來操作作業系統。第一個流行的 shell 是由 Steven Bourne 發展出來的，為了紀念他，就稱之為 Bourne shell ，或直接簡稱為 sh。

在許多作業系統都會提供shell，以Linux來說，他有bash、ksh、zsh...等等，其中bash是許多人用的一個shell。Bash已成為實際的 Linux標準。 這是因為 Bash 與 Unix 的第一個重大 Shell (Bourne Shell，也稱為 sh) 相容。Bash 納入了其前身的最佳功能。但是 Bash 本身也有其他功能，包括叫用外部程式的內建命令和功能，底下我們就來介紹Bash。

Bash是管理Linux的一個工具，他是**B**ourne **A**gain **Sh**ell的縮寫，他是Bourne Shell的加強版本，我們可以從這個檔案`/etc/shells`來查看系統有哪些shell可以使用

```shell
$ cat /etc/shells
# List of acceptable shells for chpass(1).
# Ftpd will not allow users to connect who are not using
# one of these shells.

/bin/bash
/bin/csh
/bin/dash
/bin/ksh
/bin/sh
/bin/tcsh
/bin/zsh
```

可以看到系統可以使用許多的sh，其中Linux預設的shell就是`/bin/bash`。

## Bash功能

Bash有以下的功能

* 命令記憶（Command history）

  bash可以記憶使用過的命令，只要按下按鍵『上下鍵』即可找到前後一個輸入的指令，預設的記憶數量有1000個，這些都會紀錄在檔案`~/.bash_history`內，不過這個檔案是紀錄前一次登入之前的指令，而此次登入的指令會暫存在記憶體中，等到登出後才會記錄到`~/.bash_history`內。不過這也有缺點，若被入侵時，可以往前翻先前的指令，即可知道你過去做過什麼操做了。

* 補全功能（tab completion）

  使用[tab]可以少打很多字，且可以確定輸入的指令/檔案名稱正確，使用[tab]的時機會依據在指令後或參數後而不同

  * 放在一串指令的第一個字的後面，可進行命令補全
  * 放在一串指令的第二個字以後，可進行檔案補齊

  若要找尋以mk為開頭的指令，只要輸入mk[tab]，即可列出全部mk為開頭的指令了

* 設定別名（alias）

  可使用指令`alias`來設定命令，可使用指令`alias`來取得目前的別名有哪些，也可使用此命令來設定別名

  ```shell
  # 設定pb當作ansible-playbook的別名
  $ alias pb='ansible-playbook'
  ```

* 工作控制（job control）

  可將工作丟到背景執行，而不用擔心不小心按了[Ctrl] + c停下process

* shell scripts

  可設定環境變數與指令設計，寫一個簡單的腳本

- 萬用字元： (Wildcard)

  支援許多的萬用字元來幫助使用者查詢與指令下達。例如想知道`/user/bin`有哪些以X為開頭的檔案，只要使用指令

  ```shell
  ls -al /user/bin/X*
  ```

  即可得知了，還有其他的萬用字元，之後再介紹。

## 指令下達與編輯

以下面的指令為例

```shell
$ command  [-options]  parameter1  parameter2 ...
```

* 第一個輸入的部分一定是『指令(command)』或『可執行檔案(例如批次腳本,script)』
* command 為指令的名稱，例如變換工作目錄的指令為 cd 等等；
* 加入選項設定時，通常選項前會帶`-`號，例如`-h`；若是使用選項的完整全名，則選項前帶有`--`符號，例如`--help`；
* parameter1 parameter2.. 為依附在選項後面的參數，或者是 command 的參數；
* 指令, 選項, 參數等以空格來區分，不管空幾格 shell 都視為一格。
* 使用[Enter]指令即會執行，[Enter]按鍵代表著一行指令的開始啟動。
* 指令太長的時候，可以使用反斜線`\`來跳脫[Enter]符號，使指令連續到下一行，當跳脫後，下一行最前面就會出現`>`符號，就可以繼續輸入指令囉。

※ 在 Linux 系統中，英文大小寫字母是不一樣的。舉例來說， 指令`cd`與`CD`並不同。

若想做編輯，一般可以使用刪除鍵以及左右鍵來移動、修改，或者也可以使用以下的組合鍵來操作

* 刪除指令

  * [ctrl]+u

    從游標處向前刪除指令串

  * [ctrl]+k

    從游標處向後刪除指令串

* 移動游標指令
  * [ctrl]+a

    讓游標移動到整個指令串的最前面

  * [ctrl]+e

    讓游標移動到整個指令串的最後面

## 指令搜尋順序

當`ls`或是`echo`等等的指令被下達時，運作順序如下

1. 從相對/絕對路徑來執行指令，例如`/bin/ls`或是`./ls`
2. 從`alias`找尋是否有該指令並執行
3. 從bash內建（builtin）指令搜尋並執行
4. 透過環境變數 `$PATH` 依序搜尋指令並執行

指令 `/bin/ls` 與指令 `ls` 其實不同，指令`ls`執行後顯示的是有顏色的結果，這是因為他也是alias的，我們可以使用`type -a`來查看指令搜尋的順序

```bash
$ type -a ls
ls is an alias for ls -G
ls is /bin/ls
```

## Reference
* [What is Bash?](https://docs.microsoft.com/en-us/learn/modules/bash-introduction/1-what-is-bash)
* [Bash shell 簡介](https://crmne0707.pixnet.net/blog/post/319685660-bash-shell-%e7%b0%a1%e4%bb%8b)
* [鳥哥的 Linux 私房菜 -- 第十章、認識與學習BASH](http://linux.vbird.org/linux_basic/0320bash.php)

