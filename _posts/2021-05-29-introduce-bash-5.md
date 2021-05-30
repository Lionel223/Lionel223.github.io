---
layout: post
title:  "[Unix] bash基本使用(5)"
subtitle: "bash的資料流"
tags: ["unix","bash","command"]
---

## Introduction

什麼是資料流？在bash裡的資料流（data stream）指的就是執行指令時，指令可能從檔案讀取資料，也就是stdin，經過處理後，將資料輸出到螢幕上，輸出至螢幕時又分為成功與失敗的情況，分別是stdout與stderr。

底下就來介紹這三個資料流（data stream）

## stdin, stdout and stderr

當執行linux指令時，會有這三種資料流，底下就來詳細介紹

* 標準輸入（stdin，standard input）

  將資料輸出給檔案或程式。例如將檔案資料輸入至指令 `cat` 

  ```bash
  $ cat ./XXX.txt
  ```

* 標準輸出（stdout，standard output）

  當執行一個執行檔，若執行成功時，會在螢幕上輸出的訊息。例如使用指令 `cat` 顯示XXX.txt檔案後所得到的訊息

  ```bash
  $ cat ./XXX.txt
  ...ignore
  ```

* 標準錯誤輸出（stderr，standard error output）

  當執行一個執行檔，若執行失敗時，會在螢幕上輸出的錯誤訊息。例如使用指令`cat`顯示一個不存在的檔案所得到的錯誤訊息

  ```bash
  # 此檔案不存在
  $ cat ./YYY.txt
  [bat error]: 'XXX.txt': No such file or directory (os error 2)
  ```

一般正確或錯誤的資料預設都是輸出至螢幕上，如果要分析時就會很麻煩，我們也可以透過資料流重導向（data stream redirection）將資料導向至其他地方

## 資料流重導向(Data stream redirection)

對於輸出而言，資料流重導向就是將原本輸入的訊息，就是將顯示在螢幕的訊息改為存放到檔案裡；對於輸入而言，則是將輸入訊息的來源，從「使用者輸入」改為「從檔案輸入」，要達成這件事情，就需要使用到特殊字元

| Data stream            | value | redirection symbol |
| :--------------------- | ----- | ------------------ |
| 標準輸入（stdin）      | `0`   | `<` or `<<`        |
| 標準輸出（stdout）     | `1`   | `>` or `>>`        |
| 標準錯誤輸出（stderr） | `2`   | `2>` or `2>>`      |

### Example

1. 列出根目錄 `/` 的檔案、屬性

   * **將結果顯示在螢幕上**

     ```bash
     $ ll /
     ...
     ```

   * **結果不顯示在螢幕上，而是新增一個檔案rootfile，並將原本顯示在螢幕的資料放入此檔案（overwrite）**

     ```bash
     # 沒有output
     $ ll / > ~/rootfile
     ```

     * 若檔案不存在時，使用 `>` 與 `>>` 皆會自動建立檔案
     * 使用 `>` 輸出檔案至一個已存在的檔案時，檔案會被覆蓋（overwrite）
     * 若想要檔案被累加（append），可使用 `>>`
     * 使用 `>` 或 `>>`，預設表示 `1>` 或 `1>>`

   * **結果不顯示在螢幕上，而是將資料累加在檔案rootfile裡（append）**

     ```bash
     # 沒有output
     $ ll / >> ~/rootfile
     ```

2. 使用一般身份查詢`.bashrc`檔案是否存在

   * **將全部結果顯示在螢幕上**

     ```bash
     # 搜尋根目錄時，可能會有permission denied的錯誤訊息
     $ find /home -name .bashrc
     find: /home/xxx/: Permission denied  <=stderr
     /home/lionel/.bashrc                 <=stdout
     ```

   * **將stderr儲存入檔案裡**

     ```bash
     # 將stderr放入檔案裡，此時stdout的資料仍會顯示在螢幕上
     $ find /home -name .bashrc 2> ~/errorlog
     /home/lionel/.bashrc
     ```

     * 若檔案不存在時，使用 `2>` 與 `2>>` 皆會自動建立檔案
     * 使用 `2>` 輸出檔案至一個已存在的檔案時，檔案會被覆蓋（overwrite）
     * 若想要檔案被累加（append），可使用 `2>>`

   * **將stdout跟stderr的資料分別寫到不同檔案**

     ```bash
     # 此時螢幕不會顯示任何output訊息
     $ find /home -name .bashrc > list_right 2> list error
     ```

   * **將stdout跟stderr的資料分別寫到同一檔案**

     ```bash
     # 錯誤
     $ find /home -name .bashrc > list 2> list
     # 正確
     $ find /home -name .bashrc > list 2>&1 list
     $ find /home -name .bashrc &> list
     ```

     * 第一個錯誤的原因是因為兩種資料同時寫入一個檔案，沒使用特殊語法的話，會造成資料交叉寫入，造成次序錯亂
     * 要寫入同一個檔案可使用特殊語法 `2>&1` 或是 `&>`

   * **【特殊情況】將stdout內容轉寫到stderr**

     ```bash
     # 仍會顯示，但已將stdout轉到stderr
     $ fine /home/lionel -name .bashrc 1>&2
     /home/lionel/.bashrc
     
     # 不會顯示，因為將stdout轉到stderr，但stderr會被拋入/dev/null內
     $ fine /home/lionel -name .bashrc 2> /dev/null 1>&2
     ```

     * 前面使用`2>&1`的方式來將 `2>` 的內容轉到 `1>` 去，那 `1>&2` 則是反過來的情況

   * **僅顯示stdout，拋棄stderr**

     ```bash
     $ find /home -name .bashrc 2> /dev/null
     /home/lionel/.bashrc
     ```

     * 若要將資訊拋棄，不顯示也不儲存，可將資料導入`/dev/null`，`/dev/null`可以吃掉導向這裡的資訊

3. 指令 `cat` 的進階應用

   前面了解的stdout與stderr，`<` 表示的則是「將原本由鍵盤輸入的資料，改由檔案來取代」，也就是「使用者輸入」改為「從檔案輸入」

   * **使用指令 `cat` 建立一個檔案的簡單流程**

     ```bash
     $ cat > catfile
     abc
     cat file test
     # 按下[ctrl] + d 離開
     
     $ cat catfile
     abc
     cat file test
     ```

     * 將 `>` 加在指令 `cat` 之後，*catfile* 會被主動建立，其內容就是後面使用鍵盤輸入的兩行資料，但我們也可以使用某個檔案的內容來取代鍵盤輸入

   * **使用stdin取代鍵盤輸入，並使用指令 `cat` 建立一個檔案的簡單流程**

     ```bash
     $ cat > catfile < ~/.bashrc
     ```

     * 此時會將 *~/.bashrc* 內的檔案寫入 *catfile* 內，*catfile* 與 *~/.bashrc* 兩個檔案會幾乎一模一樣，就像是使用指令`cp`一樣

   * **使用指令 `cat` 將輸入的訊息輸出至 *catfile* 中，且鍵盤輸入eof時，當次輸入即結束**

     ```bash
     $ cat > catfile << "eof"
     > abc
     > cat file test
     > eof
     # 輸入eof關鍵字即結束，不需輸入 [ctrl] + d
     
     $ cat catfile
     abc
     cat file test
     ```

     * `<<` 表示結束的「輸入字元」，當看到此輸入字元，即可中止一次輸入，而不必輸入 `[ctrl] + d` 來結束

## 命令的連續執行

有些指令想要一次輸入去執行，可用以下的符號來一次執行多重指令

* `;`

  ```bash
  cmd;cmd
  ```

  此指令是使用在不考慮指令相關性的連續指令下達，指令之間使用分號 `;` 分隔，分號前的指令執行後會立刻執行後面的指令

  ex. 執行兩次sync同步然後shutdown電腦

  ```bash
  $ sync; sync; shutdown -h now
  ```

* `$?`

  若是指令有相依性，需判斷前面指令是否正確，此時可使用指令回傳值 `$?`，指令回傳值是一個變數，其表示

  「當前一個指令執行結果為正確時，會回傳 `$?=0` 的值」，可使用指令 `echo $?` 來查看其值。

  可藉由 `&&`、`||` 來查看指令回傳值，判斷後續指令是否執行

* `&&`、`||`

  以下介紹這兩個符號的使用方式

  | command        | description                                                  |
  | -------------- | ------------------------------------------------------------ |
  | `cmd1 && cmd2` | 1. `cmd1`執行完畢且正確執行（`$?=0`），執行`cmd2`<br />2. `cmd1`執行完畢且錯誤（`$?≠0`），不執行`cmd2` |
  | `cmd1 || cmd2` | 1. `cmd1`執行完畢且正確執行（`$?=0`），不執行`cmd2`<br />2. `cmd1`執行完畢且錯誤（`$?≠0`），執行`cmd2` |

### Example

1. 建立檔案

   * 檢查目錄是否存在，若存在則在底下建立檔案

     ```bash
     $ ls /tmp/abc && touch /tmp/abc/def.txt
     ls: cannot access /tmp/abc: No such file or directory
     # ls指令出錯，但沒有touch指令的錯誤，touch指令沒有執行
     
     $ mkdir /tmp/abc
     $ ls /tmp/abc && touch /tmp/abc/def.txt
     # 此時可成功執行touch
     ```

   * 檢查目錄是否存在，若不存在則建立，存在則不做其他事

     ```bash
     $ ls /tmp/abc || mkdir /tmp/abc
     # 成功建立目錄
     ```

   * 不管目錄存不存在，要在底下建立檔案

     ```bash
     $ ls /tmp/abc || mkdir /tmp/abc && touch /tmp/abc/def.txt
     ```

     Linux的指令都是由左往右執行的，此範例有以下幾種情況

     * 情況一
       1. 若 `/tmp/abc` 不存在，則回傳 `$?≠0`
       2. `||` 遇到 `$?≠0`，執行指令 `mkdir` 並成功執行，回傳 `$?=0`
       3. `&&` 遇到 `$?=0`，執行指令 `touch`，成功建立檔案
     * 情況二
       1. 若 `/tmp/abc` 存在，則回傳 `$?=0`
       2. `||` 遇到 `$?≠0`，不執行執行指令 `mkdir`，`$?=0` 繼續往後傳
       3. `&&` 遇到 `$?=0`，執行指令 `touch`，成功建立檔案

## Pipe

與前面的連續執行命令不同，有時會需要將輸出的資料經過一些處理後，才會得到我們所需要的格式，這時我們就可以使用pipe（`|`）。例如我們想查看 */etc/* 底下有多少檔案，但他底下有太多檔案，會立刻將螢幕塞滿，這時就可以搭配指令 `less` 的協助將指令 `ls` 的輸出的內容被指令 `less` 讀取

```bash
$ ls -al /etc | less
```

Pipe（`|`）主要有以下幾點要注意

* Pipe可以處理前一個指令的stdout，對於stderr沒有直接處理能力（遇到stderr會忽略）
* Pipe會接受前一個指令的stdout，並變成下個指令的stdin繼續處理
* 後面的指令必須可接受stdin的資料，例如指令 `less`、`more`、`grep`、`tail` 皆可，而`ls`、`cp`、`mv` 則不可

※ 若想讓stderr也被pipe處理的話，可使用 `2>&1`，即可讓stderr跟stdout一起被處理

使用Pipe時，經常需要使用前一個指令的stdout作為下個指令的stdin，有些指令會需要使用檔案名城處理，這時stdin與stdout可以使用減號 `-` 來替代，例如以下例子

```bash
$ mkdir /tmp/homeback
$ tar -cvf - /home | tar -xvf - -C /tmp/homeback
```

這個例子是將 */home* 的檔案打包，但打包的資料不紀錄到檔案，而是傳到stdout，經由pipe將 `tar -cvf - /home` 傳送給後面的 `tar -xvf -`，後面的`-`是來取用前一個指令的stdout，所以此時就不需要使用filename了。

## Reference
* [鳥哥的 Linux 私房菜 -- 第十章、認識與學習BASH](http://linux.vbird.org/linux_basic/0320bash.php)
* [bash 標準輸入與輸出](https://crmne0707.pixnet.net/blog/post/319927706-bash-%e6%a8%99%e6%ba%96%e8%bc%b8%e5%85%a5%e8%88%87%e8%bc%b8%e5%87%ba)
* [bash 連續執行命令](https://crmne0707.pixnet.net/blog/post/320177606-bash-%e9%80%a3%e7%ba%8c%e5%9f%b7%e8%a1%8c%e5%91%bd%e4%bb%a4)
* [bash 管線符號](https://crmne0707.pixnet.net/blog/post/320177804-bash-%E7%AE%A1%E7%B7%9A%E7%AC%A6%E8%99%9F)

