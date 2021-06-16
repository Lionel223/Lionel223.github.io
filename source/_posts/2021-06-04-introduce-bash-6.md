---
title: "[Unix] bash基本使用(6)"
date: 2021-06-04 01:30:16
tags: ["unix","bash","command"]
categories: unix
---

前面所提到的命令別名與自訂變數，若沒做任何設定時，只要登出bash就會失效了，要將這些設定寫入設定檔，才能保留這些設定，下面就來介紹這些設定檔。

<!-- more -->

## Bash的環境設定檔

首先，會根據進入 shell 時是否有登入，分為 login shell 與 non-login shell，若是使用圖形介面登入，並開啟 terminal時，這時不需輸入帳密，此時的 bash 環境就稱為 non-login shell，在此 bash 下達 指令 `bash`，也不需輸入帳密，這個 bash 子程序也是 non-login shell

這兩個情況取得的設定檔資料會不同，login shell會讀取以下兩個設定檔

* `/etc/profile` - 系統設定，通常不會動
* `~/.bash_profile` or `~/.bash_login` or `~/.profile` - 個人設定，只會讀取其中一個，讀取順序依據前面的順序

non-login shell 則只會讀取 `~/.bashrc` 而已[^1]

[^1]: 當修改了 bash 環境設定檔後，一般都要登出再登入，設定才能生效，不過也可以使用指令 `source` 來將設定檔重新讀入現在的環境，以下是使用方式

```bash
$ source 設定檔檔名

# ex. 將~/.bashrc的設定檔讀入目前的環境，下面兩種方式皆可
$ source ~/.bashrc
$ . ~/.bashrc
```

## 終端機環境設定

登入 bash 時，我們可以使用 backspace 按鍵來刪除字元，也可以使用 [ctrl] + c 終止指令，除此以外，還有其餘的按鍵，可以透過指令 `stty`（setting tty）來查看

```bash
# 列出全部的stty參數
$ stty -a
```

底下列出幾個比較重要的按鍵

| 名稱     | 按鍵 | 意義                                      |
| -------- | ---- | ----------------------------------------- |
| intr[^2] | ^C   | 送出interrupt訊號給正在執行的程序（中止） |
| kill     | ^U   | 刪除目前指令的所有文字                    |
| eof      | ^D   | End of file，輸入結束                     |
| start    | ^Q   | 恢復螢幕輸出                              |
| stop     | ^S   | 暫停螢幕輸出                              |
| susp     | ^Z   | 暫停目前命令                              |

[^2]: `intr = ^C` 此部分的 `^` 表示 ctrl 的意思


## Reference
* [鳥哥的 Linux 私房菜 -- 第十章、認識與學習BASH](http://linux.vbird.org/linux_basic/0320bash.php)