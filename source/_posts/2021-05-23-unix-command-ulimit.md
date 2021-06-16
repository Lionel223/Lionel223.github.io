---
title:  "[Unix] ulimit 指令介紹"
date: 2021-05-23 00:00:00
tags: ["unix","bash","command"]
categories: unix
---

`ulimit`是unix的一個指令，他是shell使用來管控系統資源的指令，可以限制使用者的某些系統資源，本文章會介紹 ulimit 的設定以及使用方式。

<!-- more -->

使用指令`man ulimit`來看他的描述

> Provides control over the resources available to the shell and to processes started by it, on systems that allow such control.

## Command Syntax

```bash
ulimit [-SHacdfltu] [limit]
```

* `OPTIONS`
  * `-H` - hard limit，不可超過這個設定數值
  * `-S` - soft limit，可以超過這個設定數值，但會有警告訊息，通常 soft limit 比 hard limit 小
  * `-a` - 後面若不接選項、參數，則列出所有限制額度
  * `-c` - 程式發生錯誤時，系統可能會將該程式在記憶體中的資訊寫成檔案(除錯用)，這種檔案就被稱為核心檔案(core file)。此為限制每個核心檔案的最大容量。
  * `-f` - 此 shell 可以建立的最大檔案容量（Kbytes）
  * `-d` - 程序可使用的最大斷裂記憶體(segment)容量
  * `-l` - 可用於鎖定 (lock) 的記憶體量
  * `-t` - 可使用的最大 CPU 時間（Second）
  * `-u` - 單一使用者可以使用的最大程序(process)數量
  * `-n` - 開啟檔案數限制
* `limit` - 給予的配額

## Example

* 列出目前身份的所有限制

  ```bash
  $ ulimit -a
  core file size          (blocks, -c) 0 # 0表示沒限制
  data seg size           (kbytes, -d) unlimited
  scheduling priority             (-e) 0
  file size               (blocks, -f) unlimited
  pending signals                 (-i) 31356
  max locked memory       (kbytes, -l) 64
  max memory size         (kbytes, -m) unlimited
  open files                      (-n) 1024
  pipe size            (512 bytes, -p) 8
  POSIX message queues     (bytes, -q) 819200
  real-time priority              (-r) 0
  stack size              (kbytes, -s) 8192
  cpu time               (seconds, -t) unlimited
  max user processes              (-u) 4096
  virtual memory          (kbytes, -v) unlimited
  file locks                      (-x) unlimited
  ```

* 取得使用者的開啟檔案數的soft limit與hard limit

  ```bash
  $ ulimit -Hn
  1048576
  $ ulimit -Sn
  1024
  ```

* 限制使用者僅能建立 10MBytes 以下的容量的檔案

  ```bash
  $ ulimit -f 10240
  $ ulimit -a | grep 'file size'
  core file size          (blocks, -c) 0
  file size               (blocks, -f) 10240
  ```

※ 此方式設定後，只要登出再登入即可復原，一般身份使用者如果以`ulimit`設定了`-f`的檔案大小， 那麼他『只能繼續減小檔案容量，不能增加檔案容量』

## 其餘的設定

### /etc/security/limits.conf

除了使用指令`ulimit`來控制資源外，還可以透過`/etc/security/limits.conf`這個檔案來設定，這個檔案是通過PAM登入的使用者設定資源限制，其內容如下

```bash
$ vim /etc/security/limits.conf
...ignore
#<domain>      <type>  <item>         <value>
@student        hard    fsize          90000
...ignore

# 第一個欄位是帳號或群組，若是群組前面需加上@，若是*，表示所有使用者
# 第二個欄位是限制的依據，可以是hard或是soft
# 第三個欄位是相關限制，這邊限制的是檔案容量
# 第四個欄位是限制的值，這邊單位是KB
# 也可開啟檔案查看敘述
```

這個檔案是設定完即生效，但他對於已登入的使用者是無效的，需要再次登入才能生效

### /etc/sysctl.conf

前一個說的`/etc/security/limit.conf`是對特定使用者設定資源限制，`/etc/sysctl.conf` 則是用於設定系統範圍的資源限制，他的內容都對應於`/proc/sys/`這個目錄的子目錄與文件，`/proc`是每次系統啟動時重新掛載的，他顯示系統內存的一些狀態，透過`/proc`可以了解當前系統的一些訊息，而`/proc/sys/`只是其一小部分而已

以下就來顯示`/etc/sysctl.conf`的內容

```bash
$ vim /etc/sysctl.conf
fs.file-max = 10000000 
fs.nr_open = 10000000
```

等號左邊的`fs.file-max`，可以這樣解讀，每個`.`分割的代表一個目錄，`fs.file-max`代表的是`/proc/sys/fs.file-max`

一般設定`sysctl.conf`參數的方法有以下幾個

1. 使用指令直接編輯

   ```bash
   $ echo value > /proc/sys/oo/xx
   ```

2. 使用vim編輯

   ```bash
   $ vim /etc/sysctl.conf
   #...編輯後使用以下指令使其生效
   $ sysctl -p
   ```

3. 使用指令sysctl編輯

   ```bash
   $ sysctl -w fs.file-max="10000000"
   ```

※ 需注意`/etc/sysctl.conf`與指令`sysctl`的修改都要root才有權限執行

## Example 2

* 執行程式時遇到錯誤`Can't open so many files`的處理方式

  此問題可能是允許可開啟檔案的上限不夠，需要增加 `open files` 的數目，可能需要增加open file的數目，需要在`/etc/security/limits.conf`新增nofile的設定

  ```bash
  $ vim /etc/security/limits.conf
  # 新增以下兩行
  * soft nofile unlimited
  * hard nofile unlimited
  ```

  設定完並跳出後，此設定才生效，此時你會發現你無法登入VM了，這是因為除了`/etc/security/limits.conf`，還有`/proc/sys`需要修改，在這邊列出遇到此問題需要修改的參數

  | 參數                                          | 意義                                               |
  | :-------------------------------------------- | :------------------------------------------------- |
  | `soft nofile` ( `/etc/security/limits.conf` ) | 使用者自己設的最大控制代碼數限制（超過後會有警告） |
  | `hard nofile` ( `/etc/security/limits.conf` ) | root 給每個使用者設定的最大控制代碼數限制。        |
  | `/proc/sys/fs/nr_open`                        | 單程序的最大控制代碼數限制。                       |
  | `/proc/sys/fs/file-max`                       | 系統最多可以分配的檔案控制代碼。                   |

  這幾個參數由大到小分別是

  `/proc/sys/fs/file-max `> `/proc/sys/fs/nr_open` > `hard nofile` > `soft nofile`

  若是設定了`/etc/security/limits.conf`的`nofile`而已，可能就會導致無法登入VM（root也一樣）

  這個例子是先前開發程式時遇到的一個錯誤，也在此紀錄一下

## Reference
* [鳥哥的 Linux 私房菜 -- 第十章、認識與學習BASH](http://linux.vbird.org/linux_basic/0320bash.php)
* [鳥哥的 Linux 私房菜 -- 第十三章、Linux 帳號管理與 ACL 權限設定](http://linux.vbird.org/linux_basic/0410accountmanager.php)
* [HOWTO: Use ulimit command to set soft limits](https://www.osc.edu/resources/getting_started/howto/howto_use_ulimit_command_to_set_soft_limits)
* [How to set ulimit and file descriptors limit on Linux Servers](https://www.linuxtechi.com/set-ulimit-file-descriptors-limit-linux-servers)
* [Linux系統控制文件 /etc/sysctl.conf](http://www.unixlinux.online/unixlinux/linuxjc/linuxjc/201702/36274.html)
* [SSH 登陸問題及排查思路](https://www.gushiciku.cn/pl/23TZ/zh-tw)

