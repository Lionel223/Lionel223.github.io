---
layout: post
title:  "[Unix] tee 指令介紹"
subtitle: "介紹常用的unix指令"
tags: ["unix","bash","command"]
---

## Introduction

`tee`是unix的一個指令，他可以將資料流同時分送到檔案與螢幕（screen），預設只有stdout，沒有stderr，其示意圖如下

<img src="https://upload.wikimedia.org/wikipedia/commons/2/24/Tee.svg" alt="Tee" style="zoom:67%;" />

透過指令`man tee`可以看到他的描述

> The tee utility copies standard input to standard output, making a copy in zero or more files.  The output is unbuffered.

## Command Syntax

```bash
tee [OPTIONS] [FILE ...]
```

* `OPTIONS`
  * `-a`（`--append`） - 不覆蓋（overwrite）檔案，而是附加（append）在檔案上（若沒加此選項，預設為覆蓋）
  * `-i`（`--ignore-interrupts`）- 忽略中斷訊號（ignore interrupt signals）
* `FILE` - 檔案名稱，可放一或多個檔案，將output data寫入這些檔案

## Example

* 將stdout結果導向到檔案

  ```bash
  $ ls > result.txt
  ```

* 將stdout結果同時導向到螢幕與檔案（overwrite file）

  ```bash
  $ ls | tee result.txt
  ```

* 將stdout結果同時導向到螢幕與多個檔案（overwrite file）

  ```bash
  $ ls | tee result.txt result2.txt result3.txt

* 將stdout結果同時導向到螢幕與檔案（append file）

  ```bash
  $ ls | tee -a result.txt
  ```

* 將stdout結果同時導向到螢幕與檔案，並使用pipeline做後續其他處理

  ```bash
  $ ls | tee -a result.txt | grep abc
  ```

* 將標準輸出stdout和錯誤輸出stderr結果都導向至螢幕和檔案

  ```bash
  $ ls XXX 2>&1 | tee result.txt
  ```

* 將stdout結果同時導向到螢幕與檔案，並忽略中斷訊號。
  當指令運行時，使用`CTRL + c`中斷指令，選項`-i`可使`tee`指令正常退出

  ```bash
  $ command | tee -i result.txt
  ```

## Reference
* [Linux Tee Command with Examples](https://linuxize.com/post/linux-tee-command/)
* [[Linux] tee 指令：將結果同時輸出到螢幕和檔案 – OneJar 的隧道](https://www.onejar99.com/linux-command-tee/#more-590)
* [鳥哥的 Linux 私房菜 -- 第十章、認識與學習BASH](http://linux.vbird.org/linux_basic/0320bash.php)

