---
title: "jvm parameter介紹"
date: 2021-05-25 00:00:00
description: "jvm的參數介紹"
tags: ["java","jvm"]
categories: java
---

## JVM介紹

JVM是Java Virtual Machine的縮寫，根據文件，其介紹如下

>The Java Virtual Machine is the cornerstone of the Java platform. It is the component of the technology responsible for its hardware- and operating system-independence, the small size of its compiled code, and its ability to protect users from malicious programs.
>
>The Java Virtual Machine is an abstract computing machine. Like a real computing machine, it has an instruction set and manipulates various memory areas at run time. It is reasonably common to implement a programming language using a virtual machine; the best-known virtual machine may be the P-Code machine of UCSD Pascal.
>
>
>The Java Virtual Machine knows nothing of the Java programming language, only of a particular binary format, the class file format. A class file contains Java Virtual Machine instructions (or bytecodes) and a symbol table, as well as other ancillary information.

可將JVM其視為一個抽象的計算機，且他不能直接解譯java語言，只能解譯特定的二進位格式的檔案（.class檔案）。JVM屏蔽了與操作系統平臺相關的信息，使得Java只要生成在 JVM上運行的目標代碼（.class），就可在多種平臺上不加修改的運行，這也是Java能夠**一次編譯，到處運行的**原因。

## JVM參數介紹

底下來介紹幾個常用的 JVM 參數

### Explicit Heap Memory

使用顯式的指令分配和釋放堆中的記憶體，heap memory指的是存放參考資料型別的物件實體，我們可以根據應用程式的需求設定初始heap（`Xms`）與最大heap（`Xmx`）的大小，使用方式如下

```bash
-Xms<heap size>[unit]
-Xmx<heap size>[unit]
```

* `unit` - 表示heap size的單位，可以是**g**(GB), **m**(MB), **k**(KB)
* `heap size` - heap大小

#### Example

* JVM的minimum heap size設定為2G，maximum heap size設定為5G

  ```bash
  -Xms2G -Xmx5G
  ```

### Out of Memory

大型程式遇到`out of memory`的錯誤是很常見的，他常常是導致應用程式崩潰的原因，所以 JVM 有一些參數可以來調整，可將heap memory轉存到實體文件中，以便後續查找memory leak的原因

參數如下

* **HeapDumpOnOutOfMemoryError**

  ```bash
  -XX:+HeapDumpOnOutOfMemoryError
  ```

  當 JVM 發生 *OutOfMemoryError* 時，生成dump文件

* **HeapDumpPath**

  ```bash
  -XX:HeapDumpPath=./java_pid<pid>.hprof
  ```

  表示OutOfMemoryError時，要寫入的檔案路徑，可自行命名檔案名稱。若 JVM 發現tag `<pid>`在檔案名稱內的話，還會放上發生問題的 process id，另外副檔名都是 *.hprof* 格式

* **OnOutOfMemoryError**

  ```bash
  -XX:OnOutOfMemoryError="< cmd args >;< cmd args >" 
  ```

  當發生 *OutOfMemoryError* 時，可用來執行緊急指令。可用來終止程式，或者重新啟動server

  ```bash
  -XX:OnOutOfMemoryError="shutdown -r"
  ```

* **UseGCOverheadLimit**

  ```bash
  -XX:+UseGCOverheadLimit
  ```

  在拋出 *OutOfMemoryError* 錯誤前，用來限制VM在GC花費的時間比例

### Garbage Collection

為了應用程式的穩定性，選擇一個合適的Garbage Collection演算法是很重要的。JVM有四種Garbage Collection的演算法可以選擇，分別如下

- Serial Garbage Collector
- Parallel Garbage Collector
- CMS Garbage Collector
- G1 Garbage Collector

我們可以用以下參數來選擇要使用哪種GC

```bash
-XX:+UseSerialGC
-XX:+UseParallelGC
-XX:+USeParNewGC
-XX:+UseG1GC
```

### GC Logging

為了監控應用程式，我們可以監控JVM的GC的效能，最簡單的方式就是將GC的活動轉成人類可讀的log格式，我們可以用以下參數來紀錄GC的log

* **UseGCLogFileRotation**

  ```bash
  -XX:+UseGCLogFileRotation 
  ```

  這個參數指定log檔案的rolling policy，很像log4j、s4lj等等。

* **NumberOfGCLogFiles**

  ```bash
  -XX:NumberOfGCLogFiles=< number of log files > 
  ```

  這個參數可以設定在單一應用程式的生命週期中，可以寫入的最大量的log file

* **GCLogFileSize**

  ```bash
  -XX:GCLogFileSize=< file size >[ unit ]
  ```

  此參數則是限制log檔案大小

* **Xloggc**

  ```bash
  -Xloggc:/path/to/gc.log
  ```

  此為設定log檔案的路徑位置

* PrintGCTimeStamps

  ```bash
  -XX:+PrintGCTimeStamps
  ```

  設定log的印出的時間區間。

* PrintGCDateStamps

  ```bash
  XX:+PrintGCDateStamps
  ```

  設定log的印出的時間區間

#### Example

若我們想指定一個最大有100個GC的log檔案，每個檔案最大值是50 MB，且我們想將其儲存在`/home/user/log/`裡面，可以添加參數如下

```bash
-XX:+UseGCLogFileRotation  
-XX:NumberOfGCLogFiles=10
-XX:GCLogFileSize=50M 
-Xloggc:/home/user/log/gc.log
```

※ 另外要注意，這個會產生一個額外的daemon thread在背景執行來監控系統，導致效能產生瓶頸，所以要避免把這些參數使用在正式環境

### Misc

待新增

## Reference

* [Java Virtual Machine Specification - Chapter 1. Introduction](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-1.html)
* [JVM是什麼？深入解析JVM原理！](https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/654140/#:~:text=JVM%EF%BC%88Java%20Virtual%20Machine%EF%BC%8C%20Java,%E7%84%A1%E9%97%9C%EF%BC%8C%E5%AE%8C%E6%88%90%E8%B7%A8%E5%B9%B3%E8%87%BA%E6%80%A7%E3%80%82)
* [Guide to the Most Important JVM Parameters](https://www.baeldung.com/jvm-parameters)
* [基礎語言-資料類別](https://travis90736.pixnet.net/blog/post/106172000)
* [JVM參數介紹](https://www.twblogs.net/a/5e6f5812bd9eee2116861057)

