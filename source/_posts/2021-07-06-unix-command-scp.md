---
title: [Unix] scp 指令介紹
date: 2021-07-06 20:10:08
tags: ["linux","command"]
categories: linux
---

當我們需要從遠端加密複製檔案時，常常會用到 `scp` 這個指令，本文會介紹這個指令的語法，以及他的使用方法

<!-- more -->

## 簡介

SCP（secure copy）是一個指令，可以讓使用者在兩個 host 間加密的複製檔案或者資料夾，當使用了指令 `scp`，你就可以

* 從本機複製一個檔案或者資料夾到遠端
* 從遠端複製一個檔案或者資料夾到本機
* 從 A 端複製一個檔案或者資料夾到 B 端

使用指令 `man scp` 即可看到他的描述

> scp copies files between hosts on a network.  It uses ssh(1) for data transfer, and uses the same authentication and provides the same security as ssh(1).  scp will ask for passwords or passphrases if they are needed for authentication.
>
> The source and target may be specified as a local pathname, a remote host with optional path in the form [user@]host:[path], or a URI in the form scp://[user@]host\[:port\][/path]. Local file names can be made explicit using absolute or relative pathnames to avoid scp treating file names containing `:' as host specifiers.

## Command Syntax

```sh
scp [OPTION] [user@]SRC_HOST:]file1 [user@]DEST_HOST:]file2
```

- `OPTION` - 許多選項，例如 cipher、ssh 設定、port、limit、recursive copy等等，詳細資訊可使用 `man scp` 查詢，此處列出常用的數個選項
  - `-P` - 自定義 ssh port（一般預設 22）
  - `-p` - 保留原本的修改時間、存取時間與權限
  - `-C` - 這個選項會讓資料壓縮之後再傳送，可以減少網路頻寬的使用量
  - `-r` - recurisively copy，可複製整個目錄以及其下的所有檔案，若沒設定，只會傳輸當前目錄底下的檔案
  - `-l` - 限制傳輸時使用頻寬（Kbit/s）
  - `-v` - 顯示傳輸進度與資訊（本地端複製無法顯示進度）
- `[user@]SRC_HOST:]file1` - 來源檔案
- `[user@]DEST_HOST:]file2` - 目標檔案

## 使用前的注意事項

* 指令 `scp` 在傳輸時需要借助 `ssh`，所以他會需要一組 ssh key 或者密碼去認證 remote server
* 冒號 `:` 是 `scp` 用來辨認位置是 local 還是 remote 的方法
* 為了確保可以複製檔案，來源檔案必須至少可以讀取（read permission），目標位置則是可以寫入（write permission）
* 複製檔案時要注意是否有相同檔名，若有的話，`scp` 會將它覆蓋（overwrite），且不會有任何警告
* 若要傳送大型檔案，建議在 `screen ` 或者 `tmux` 內執行 `scp`

## Example

* 複製本機檔案至遠端

  ```bash
  $ scp file.txt remote_username@10.10.10.10:/remote/directory
  ```

  ※ 若沒有指定 remote directory，那預設會放到 home directory 裡面

* 複製本機檔案至遠端，且保留原本檔案權限

  ```bash
  $ scp -p file.txt remote_username@10.10.10.10:/remote/directory
  ```

* 複製本機檔案至遠端，且將檔案壓縮後再傳送

  ```bash
  $ scp -C file.txt remote_username@10.10.10.10:/remote/directory
  ```

* 複製本機檔案至遠端，並使用其他的 port（本次使用 port 8888）

  ```bash
  $ scp -P 8888 file.txt remote_username@10.10.10.10:/remote/directory
  ```

  ※ 若沒有使用 `-P`，預設使用 port 22

* 複製本機檔案至遠端，並限制傳輸速度（單位是 Kbit / s）

  ```bash
  $ scp -l 300 file.txt remote_username@10.10.10.10:/remote/directory
  ```

* 複製本機資料夾至遠端

  ```bash
  $ scp -r /local/directory remote_username@10.10.10.10:/remote/directory
  ```

  ※ 檔案最後面 `/local/directory` 不加斜線 `/`，就可以複製目錄本身，包含內容，而不是只有目錄的內容而已

* 複製遠端檔案至本機

  ```bash
  $ scp remote_username@10.10.10.10:/remote/file.txt /local/directory
  ```

* 在兩個遠端之間複製檔案

  ```bash
  $ scp user1@host1.com:/files/file.txt user2@host2.com:/files
  ```

## Reference

* [Linux好用指令-scp-遠端加密複製目錄與檔案](http://it.givemepower.net/linux-command-scp-remote-encrypted-copy-directory-and-file/#:~:text=%E9%81%A0%E7%AB%AF%E5%82%99%E4%BB%BD%E3%80%82-,SCP%E6%98%AFLinux(Unix%20Like)%E8%B6%85%E5%A5%BD%E7%94%A8%E5%91%BD%E4%BB%A4%E4%B9%8B,%E8%83%BD%E5%9C%A8MAC%20OSX%E4%BD%BF%E7%94%A8%E3%80%82)
* [Linux 的 scp 指令用法教學與範例：遠端加密複製檔案與目錄](https://blog.gtwang.org/linux/linux-scp-command-tutorial-examples/)
* [How to Use SCP Command to Securely Transfer Files](https://linuxize.com/post/how-to-use-scp-command-to-securely-transfer-files/)
* [How do I copy a folder from remote to local using scp?](https://stackoverflow.com/questions/11304895/how-do-i-copy-a-folder-from-remote-to-local-using-scp)
