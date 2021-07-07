---
title: swap 基本介紹
date: 2021-07-07 01:23:14
tags: ["linux", "swap"]
categories: linux
---

本文將簡易介紹 Linux 系統上的 swap 以及其機制

<!-- more -->

## swap 介紹

Linux 系統會將隨機讀取記憶體（RAM，Random Access Memory）分割成一塊一塊的小區域（pages），每個小區域都是 4KB 大小的區塊，當 RAM 不足的時候，就會透過 swap 將 page 的資料搬移到預先配置好的硬體置換空間（swap）中儲存，然後釋放出 page，所以 Linux 系統上的虛擬記憶體（virtual memory）就是包含隨機讀取記憶體（RAM）與硬碟的置換空間（swap）這兩個部分。

Linux 上的 swap space 是在 RAM 用完後才會使用到。當系統要更多的記憶體資源，但 RAM 已經用完時，記憶體中不活動的 page 就會被移到 swap，

當安裝 Linux 時，都會有一個獨立的磁碟分割區給 swap 交換空間使用，不過若是系統需要大量的記憶體時，我們也可以把一般的檔案提供給 swap 交換空間，雖然 swap 空間可以幫助系統增加一小部份的 RAM，但還不能將它當作更多記憶體的替代品，因為 swap space 是在硬碟上，它的存取速度比起 RAM 慢。

那為何速度那麼慢，還需要 swap 呢？有以下兩點原因

### 記憶體不足

當系統需要的記憶體超過了可用的實體記憶體時，會將記憶體中不常使用的 page 交換到磁碟上為當前程序讓出記憶體，保證正在執行的程序的可用性，這個記憶體回收的過程是強制的直接記憶體回收（Direct Page Reclaim）

### 記憶體閒置

應用程式啟動時，常常會使用到大量的記憶體，但後續都不會使用，此時可以透過後台的程序將這些只使用一次的記憶體交換到磁碟上，使其他記憶體有預留的空間。kswapd 是 Linux 上負責做 page replacement 的程序，他也是交換閒置記憶體的程序，當記憶體的量低於一定量時，他可以保證其他程序可以盡快獲得所需的記憶體。而在 Linux 裡面是使用 LRU（Least Recently Used Page Replacement）的演算法來置換記憶體的 page，他會將存在於實體記憶體頁框中最久沒用到的 page 給取代掉，系統中的每個區都會在記憶體中持有 active_list 和 inactive_list 兩種連結串列，其中前者包含活躍的記憶體頁，後者中儲存的記憶體頁都是回收的候選頁面

## Reference

* [Linux 系統 Swap 交換空間管理教學：Swap 分割區與檔案的使用與管理](https://blog.gtwang.org/linux/linux-swap-space-tutorial/)
* [【Linux】Linux中Swap与Memory内存简单介绍](https://blog.csdn.net/zwan0518/article/details/12059213)
* [為什麼 Linux 需要 Swapping](https://www.gushiciku.cn/pl/phgV/zh-tw)
