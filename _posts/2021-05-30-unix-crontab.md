---
layout: post
title:  "[Unix] crontab介紹"
subtitle: "crontab的使用方式"
tags: ["unix","command"]
---

## Introduction

Linux系統內，有時需要在指定時間自動執行工作，此種工作又被稱為「例行性工作」。依據其性質又分為「一次性工作」與「循環性工作」。一次性工作就是在指定時間執行後便不再執行，而循環性工作則會依照指定的時間間隔或時間點重複執行，此處我們就來介紹循環性工作如何使用。

循環性的例行性工作排程是由cron（crond）這個系統服務所控制，使用者也可以使用指令 `crontab` 來控制。crontab 是 Linux 內建的機制，可以根據設置的時間參數來執行例行性的工作排程，使用指令 `man crontab` 可查看其描述

> The crontab utility is the program used to install, deinstall or list the tables used to drive the cron(8) daemon in Vixie Cron.  Each user can have their own crontab, and they are not intended to be edited directly

底下就來介紹他的語法與詳細使用方式。

## Command Syntax

```bash
crontab [-u username] [-e|-l|-r]
```

* `-u` - 此參數只有root可以使用，可幫其餘使用者建立或移除crontab的工作排程
* `-e` - 編輯crontab的工作內容
* `-l` - 查詢crontab的工作內容
* `-r` - 移除所有crontab的工作內容，若只要移除一項可使用`-e`

ex. 每天12:00發信給自己

```bash
$ crontab -e
# 此時進入vim的編輯畫面
 0  12 *  *  * mail -s "at 12:00" lionel < /home/lionel/.bashrc
#分 時 日 月 週 |<====================command==================>|
```

由範例可以看到，進入vim編輯畫面後就可以新增工作，每一行都是一項例行性工作，都會有六個欄位，意義如下

| 分鐘 | 小時 | 日期 | 月份 |             週              |                      指令                      |
| :--: | :--: | :--: | :--: | :-------------------------: | :--------------------------------------------: |
| 0-59 | 0-23 | 1-31 | 1-12 | 0-7<br />※ 0和7都表示星期天 | 指令<br />※ 下達指令使用 **絕對路徑** 避免錯誤 |

另外還有一些輔助的符號如下

* `*`（星號）- 表示任何時刻，例如小時放入 `*` 表示每一小時
* `,`（逗號）- 表示分隔時段，例如要下達的工作是每天的3:00與5:00，則可使用`0 3,5 * * * command`
* `-`（減號）- 表示一段時間，例如要下達的工作是每天的3:00、4:00、5:00，則可使用`0 3-5 * * * command`
* `/n`（斜線）-  n表示數字，表示每隔n單位，例如要下達的工作是每五分鐘執行一次，則可使用`*/5 * * * * command`

※ 週與日月不可同時設定，若設定了週，就不可設定日跟月，反之亦然

### Example

* 每年的5/30 8:00 執行測試程式

  ```vim
  0 8 30 5 * /home/ubuntu/test.sh
  ```

* 每五分鐘執行一次測試程式

  ```vim
  */5 * * * * /home/ubuntu/test.sh
  ```

* 每天早上八點，除了禮拜天以外，執行report.js這個程式

  ```vim
  0 8 * * 1-6 node /home/ubuntu/report.js
  ```

## 使用者權限設定

為了安全性，我們還可以限制使用crontab的使用者帳號。我們可以使用root去修改以下資料

* `/etc/cron.allow`

  將可以使用crontab的帳號寫在此，若不在這個檔案內的使用者將無法使用crontab

* `/etc/cron.deny`

  將不可以使用crontab的帳號寫在此，若不在這個檔案內的使用者即可使用crontab

這兩個檔案只能選擇一個來使用，且 */etc/cron.allow* 優先於 */etc/cron.deny*。

當使用者使用指令 `crontab` 建立工作排程後，工作就會被記錄到 */var/spool/cron/{account}* 裡面，例如user lionel的新增工作排程後，就會被記錄到 */var/spool/cron/lionel* 裡面，但不要直接修改這個檔案，若因語法錯誤，可能會無法執行cron。

另外cron所執行的工作都會被記錄到 */var/log/cron* 這個登入檔裡面，若需要查看crontab的log，可使用 `tail -f /var/log/cron` 來查看（不可使用指令 `cat`，因為檔案可能正在更新）

## 系統設定檔

前面說的指令 `crontab` 是針對使用者設計的，指令 `crontab` 實際上修改的執行檔 */usr/bin/crontab*。但如果是「系統的例行性任務」時，就不能使用這個指令，而是需要編輯 */etc/crontab*，編輯完並儲存後，即可自動執行，因為cron最低偵測限制是「分鐘」，所以cron每分鐘會去讀取 */etc/crontab* 與 */var/spool/cron* 內的資料內容

※ 一般linux系統會自動每分鐘讀取一次 */etc/crontab* 的工作，但其他unix系統中可能crontab是讀取到記憶體的，所以修改完 */etc/crontab* 可能不會立刻生效，此時就需要使用以下指令重啟crond服務

```bash
$ systemctl restart crond
```

底下就來看看 */etc/crontab* 的內容

```bash
$ cat /etc/crontab
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root

# For details see man 4 crontabs

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed
```

以下幾個地方提出說明

* SHELL

  使用的shell

* PATH

  輸入執行檔的搜尋路徑

* MAILTO

  當 */etc/crontab* 執行出錯時，或是執行結果有stdout或stderr時，要將訊息傳給誰，預設是發一封mail給root，也可將這個部分改成其他信箱

  ※ 若有些命令一定會有stdout或stderr，但又不想收到，可使用data stream redirection將資料導到 `/dev/null` 內

* 欄位設定

  跟 `crontab -e` 的設定稍微不同，多了一個user-name，需設定執行這串指令的身份，預設是root執行


## Reference

* [鳥哥的 Linux 私房菜 -- 第十五章、例行性工作排程(crontab)](http://linux.vbird.org/linux_basic/0430cron.php#cron)
* [linux 例行性工作](https://crmne0707.pixnet.net/blog/post/322385712-linux-%e4%be%8b%e8%a1%8c%e6%80%a7%e5%b7%a5%e4%bd%9c)
* [利用 crontab 來做 Linux 固定排程](https://code.kpman.cc/2015/02/11/%E5%88%A9%E7%94%A8-crontab-%E4%BE%86%E5%81%9A-Linux-%E5%9B%BA%E5%AE%9A%E6%8E%92%E7%A8%8B/)