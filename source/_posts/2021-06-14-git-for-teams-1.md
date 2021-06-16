---
title: git團隊使用手冊讀後心得(1)
date: 2021-06-14 04:39:16
tags: ["git","github"]
categories: git
---

本書以使用者的角度介紹git，觀看團隊的作業方式，以及個別命令的使用時機，以下就來摘要我的理解，本文主要介紹各種作業方式。

<!-- more -->

## 團隊作業的存取模型

Git 是個分散式版本控制系統，所以使用者要記錄更動時，不需使用同樣的地方，每個使用者都可在程式碼代管系統之外獨立工作，以下就來介紹幾種常見的git工作模式：

* 共用的貢獻者儲存庫（Collocated Contributor Repository）

  要貢獻的開發人員建立遠端的專案副本，由專案維護者決定是否接受，也就是用 merge request 或是 pull request 的形式送出更動的需求，這種方式常用於開放原始碼的開發方式

* 共享維護儲存庫

  團隊所有人都是維護者，都有上傳更動到 repository 的權限。這種方式常用於內部團隊或一人團隊，團隊成員彼此有一定的互信

除了以上方式，團隊也常會混用多種模式。

## 團隊作業的分支方法

在 Git 裡面，分支（branch）是一種隔離平行概念的方法，branch 的建立可能用來呈獻成果，或者分離成果。

我們選擇的分支策略（branching strategy）會與發布管理程序有關，其中一個作法如下，依據軟體版本，分成 `v1.0.0`、`v1.0.1`、`v1.0.2`，其中某些版本裡面可能又有其他正在進行的想法，這些想法就會依據他們所代表的意義而分為不同的 branch，這些 branch 可能是 bug 的修正，也可能是新的功能。

在使用 branch 前，我們會希望有一些人為的限制（慣例），讓開發人員可以快速了解專案執行的模式。

### 選擇分支策略的慣例

慣例是指大家一致同意對事情的處理方式，可讓開發人員快速了解專案執行的模式，並將各自的成果可順利整合。

一般的軟體專案，團隊一般會分成兩種主要做法，一個是持續整合[^1]（continuous integration）；另一個則是檢查完所有完成的結果，再一併發佈，在這兩者中還有許多的變形。以下就來介紹數種分支策略的慣例

[^1]: 持續整合（continuous integration）：開發人員每段時間多次將各自的成果整合到專案的主線分支；持續發布（continuous delivery）：開發人員上傳伺服器的部分自動化；持續部署（continuous deployment）：自動化最完整的定義，開發人員上傳後會通過程式測試，通過後自動部署到伺服器

#### 主線分支開發

主線分支（mainline branch）是最簡單的方式，開發人員持續將成果記錄到單一分支，上去的分支都是已測試的結果，通常用於單人作業。但當人員變多時，就越難追蹤所有的概念想法，這時就需要建立新的 branch，等到各自完成工作，並將成果整合後，分支數量才會下降。

到了這種情況下，讓團隊經常將成果整合到主線分支，但只有特定的時間部署可能才是比較合理的做法，也就是離開持續部署策略，轉為定期部署的方式。

持續整合的分支策略有以下優點

* 不會有太多分支，較不會找不到更動發生的位置
* 紀錄較小，發生問題很快能復原
* 發布到主分支的程式應都可供部署，所以較不會有緊急修正，藉由經常性的更新使部署過程越來越頻繁，最終自動化，使用者也不會感覺部署發生

但也有他的缺點

* 若團隊沒有測試基礎設施，隨專案越來越複雜，風險會越來越高
* 『部署』只適合自動下載到使用者設備的程式（ex. 網站），若是需使用者自行下載安裝的軟體則不適合此方式

#### 一功能一分支部署

另一個方式則是引入兩種類型的分支，分別是功能分支（feature branch）與整合分支（integration branch），這兩個不算是不同類型的分支，只是記錄的成果類型不太一樣，以下來詳細介紹。

所有的新成果都要在 feature branch 完成，且盡量不要太大，只包含一個完整的概念，功能。featute branch 可透過 integration branch 來取得其他人的最新成果，到了需要發布時，再選擇此次要包含哪些功能，並為部署建立新的 integration branch

![03fig03-integration-branch](/image/2021-06-14-git-for-teams-1/03fig03-integration-branch.png)

#### Github Flow

所有在 master branch 的內容都是可部署的狀態，若要開發新的功能時，則需用一個可描述的名稱建立新的 branch，功能完成時會對 master branch 發出 pull request。

到這裡都和前一個方式相同，但其差異就在部署的方式。在前一個模型部署時，需明確選擇要納入部署的 feature，但 Github Flow 則是 pull request 被接受時，就會將其 feature branch 轉為可供部署，先部署 feature branch，若沒有錯誤，就會將 feature branch 合併進入 master branch；若 feature branch 有任何問題，也可以重新部署 master branch

![03fig03-integration-branch](/image/2021-06-14-git-for-teams-1/03fig04-github-flow.png)

使用以上兩種方式有以下優點：

* 與主線開發類似，可專注在程式碼的快速部署
* 有個可選擇性採用的建置步驟，可選擇哪些功能要納入主線部署

以下是其缺點

* 若有功能在 feature branch，且沒立即合併至 master branch，在合併至可部署之前，需額外維持其功能與 master branch 一致
* 額外維護分支結構，舊的 branch 合併至 master branch 後，也須將舊的 branch 移除

#### 狀態分支

與前面不同，將各個分支部署到特定的環境，此方式又被稱為 GitLab Flow 模型。GitlLab FLow 透過分支命名明確顯示此程式碼的環境，例如 release 表示正式環境。

此方式有以下優點

* 不用猜測個分支作用

但也有以下缺點

* 需有指引才能明確知道該從哪個分支開始
* 分支名稱與情境有緊密關係，較難在不同專案間建立相同結構

### 範例

接下來的範例是使用一個專案，逐步實作 GitFlow 的過程

一開始專案只有一個 develop branch，開發人員都從這個 branch 建立各自的 branch 來開發功能、修 bug、或是重構等等，開始時做時可以以工單（ticket）來描述，且 branch 的名稱也會包含工單名稱。

![03fig08-gitflow-dev-features](/image/2021-06-14-git-for-teams-1/03fig08-gitflow-dev-features.png)

之後開始工作，到了某個時間後會決定不會有新功能了，此時稱為功能凍結（feature freeze）開始測試，這個時候會從 develop branch 建立一個新的分支，此處稱為 release 1.0

![03fig09-gitflow-release-branch](/image/2021-06-14-git-for-teams-1/03fig09-gitflow-release-branch.png)

，只有 bug 修正才會記錄到這個 branch。需注意，feature freeze 時並非表示功能都已完成，此時仍有其餘工作成果被合併到 develop branch，若有 bug 也會修正回去 develop branch，測試的時間越長，越長需要在 develop 與 release1.0 branch 上作業。

![03fig10-gitflow-release-bug](/image/2021-06-14-git-for-teams-1/03fig10-gitflow-release-bug.png)

經過一段時間的測試後，產品即可上線，經過測試的程式碼會被記錄到新的 branch master，並且加上 tag

![03fig11-gitflow-master](/image/2021-06-14-git-for-teams-1/03fig11-gitflow-master.png)

如果發現一個需立即修正的 bug 時，則需做緊急修正（hotfix），此時會從 master branch 中建立新的 branch，所以發布緊急修正時，不會包含後來的功能，最後這個修正的 bug 也要被合併回去 develop branch 中

![03fig12-gitflow-hotfix](/image/2021-06-14-git-for-teams-1/03fig12-gitflow-hotfix.png)

使用這種定期部署的方式有以下優點

* 不需在初期就建立全面的測試
* 有分版本的軟體很適合這種方式，不需要每隔幾天就部署一份新的版本

也有以下缺點

* 用錯 branch 需要費一番功夫才能回復

## 團隊的作業程序

前面介紹了存取模型、分支方法，到最後開發人員使用 git 的方式就會越來越複雜，這時候最好將開發時的程序記錄下來，例如底下這個作業程序的樣板

```markdown
PM: <name>
Site: <URL>
Branch deployed on dev site: <branch_name>
Start a dev ticket: <branch_name>
hotfix ticket: <branch_name>
update work, use: <git_command>
merge work post review, use: <git_command>
```

以下介紹數個情境下的作業程序

### 基本作業程序

此流程適合沒有同儕審核需求的小型團隊，當團隊成長、或是採用的特殊的品保程序，此方式可能會不太成熟，不能符合需求，其特色如下

* 治理模型：貢獻者共同維護
* 整合合併：開發人員操作
* 整合分支：develop branch

作業程序如下：

1. 開發人員開始作業程序時，將工單更新為進行中，並從 develop branch 建立工單的 branch，名稱包含工單編號以及工作內容的簡單說明
2. 處理工單時，要注意隨時與 master branch 的狀態一致，且所有的紀錄訊息（commit message）都應以中括號標記工單名稱，例如工單為1234，commit message 就應以 `[#1234]` 開頭，後面補上訊息的描述
3. 處理完畢時，將工單標記為 Resolved，commit message也需做最後一次的紀錄，例如工單1234完成後，commit message標註為 Resolves #1234
4. 將工單的 branch 推送至遠端儲存庫，因紀錄訊息已包含必要的關鍵字，可讓工單追蹤系統將功單移往下個階段
5. 於工單系統填寫處理紀錄，確保工單的 branch 有反映主分支的其他更動後，將工單 branch 合併至 develop branch
6. 若沒其他問題，即可關閉工單，刪除工單 branch

※ 某些工單系統在前面加 `#` 可以自動將commit message連結到工單號碼，不用一定要用 `#` 來紀錄工單訊息

### 受信任開發人員搭配同儕審核

接下來再加上同儕審核作為延伸，所有的工單都要其他成員審核程式碼，其特色如下

* 治理模型：貢獻者共同維護
* 整合合併：審核者執行
* 整合分支：develop branch

作業程序如下：

1. 開發人員開始作業程序時，將工單更新為進行中，並從 develop branch 建立工單的 branch，名稱包含工單編號以及工作內容的簡單說明
2. 處理工單時，要注意隨時與 master branch 的狀態一致，且所有的紀錄訊息（commit message）都應以中括號標記工單名稱，例如工單為1234，commit message 就應以 `[#1234]` 開頭，後面補上訊息的描述
3. 將工單的 branch 推至遠端儲存庫作為備份
4. 處理完工單後，確保 branch 與 develop branch 最新狀態一致，再將工單狀態改為需測試
5. 若需人工審核，審核者須根據工單描述檢視成果，必要的話需將工單送回給開發人員進行修正，或是解決與 develop branch 不一致的狀況
6. 審核者將工單 branch 合併至 develop branch，若沒有錯誤的話，審核者關閉工單，並告知開發人員其功單已經合併，並由審核者刪除工單 branch 的遠端複本

### 未信任開發人員搭配 QA 守門員

此為前一個的變形，未信任的開發人員不可將各自成果合併至主分支，而是透過品保（quaility assurance，QA）進行最終的合併，其特色如下

* 治理模型：共險者使用同一個代管系統
* 整合合併：審核者執行
* 整合分支：develop branch

作業程序如下：

首先開發人員須建立專案的 fork，並從 fork 建立主機副本，以下行為皆是在 fork 操作，其步驟大致上類似前一節

1. 前面步驟 1、2、3、4 皆一樣，開發人員須為自己成果建立 pull request
2. 接下來由審核者執行，審核者須根據工單描述檢視成果，必要的話需將工單送回給開發人員進行修正，或是解決與 develop branch 不一致的狀況

## Reference

* [Git 團隊使用手冊 (Git for Teams: A User-Centered Approach to Creating Efficient Workflows in Git)](https://www.tenlong.com.tw/products/9789864762453)