---
title: 網路基本概念
date: 2021-06-17 00:00:19
tags: ["network"]
categories: network
---

在現今的時代，每個人都在使用網路，舉凡是手機、電腦、或是其餘穿戴的設備等等，可能都會使用到網路，底下就來介紹網路到底是什麼東西。

<!-- more -->

## 什麼是網路

網際網路的起源可以追朔到1960年代末期，當時美國國防部為了軍事的需要而主導一個計畫，此計畫由 DARPA（Defense Advanced Research Project Agency）負責，其目的是研究一個可在不同網路硬體上運作的軟體技術，讓各個電腦可透過此軟體達成資料的溝通，此系統稱為 ARPANET（Advanced Research Projects Agency Network），這個即是 TCP/IP 的雛形。

在1975年左右，APPANET已經可以各種硬體平台底下互通資料了。後來在1980年，DAPRA正式推出 TCP/IP，並將 TCP/IP 植入BSD Unix系統內，後來 TCP/IP 這項技術就吸引越來越多人使用，而這個連接網路的技術也被稱之為 Internet。

## 網路的組成

接下來介紹網路的組成，可參考以下圖片

![network_components](/image/2021-06-17-network-basic/network_components.png)

<center><font color=#BBBBBB>圖片來源：</font><a href="https://linux.vbird.org/">鳥哥伺服器篇</a></center>

上圖大致上由以下硬體組成：

* 節點（node）

  節點主要是具有網路位址（IP）的設備，一般PC、Linux伺服器、印表機、ADSL數據機都可視為一個節點，但中間的集線器（hub）因為不具有 IP，所以不是節點

* 伺服器主機（server）

  可提供資料來回應給用戶的主機

* 用戶端（client）

  可主動發起連線去要求資料，都可稱為用戶端，例如一台電腦對 google 搜尋資料，則電腦可稱為用戶端

* 網路卡（NIC，Network Interface Card）

  內建或外插在主機上的一個設備，可提供網路連線，一般的節點都有一個或以上的網路卡

* 網路介面

  利用軟體設計出來的網路介面，主要在提供網路位址（IP）的任務。 一張網卡至少可以搭配一個以上的網路介面

* 網關（route）或通訊閘（gateway）

  具有兩個以上的網路介面， 可以連接兩個以上不同的網段的設備，例如 IP 分享器就是一個常見的網關設備。而上面的 ADSL 數據機則不太能算是網關，因為數據機通常視為一個在主機內的網卡設備。

## 網路的區域範圍

依據距離不同、線材不同，網路的使用大致上又分為 LAN、WAN，底下就來詳細介紹

* 區域網路（LAN，Local Area Network）

  距離較近，可使用較昂貴的線材，通常使用於校區內或是一棟大樓內，網路速度較快且連線品質較佳。

* 廣域網路（WAN，Wide Area Network）

  傳輸距離較遠，可能是城市之間的距離，線材較便宜

## 電腦網路通訊協定： TCP/IP

由 ARPANET 發展而來的 TCP/IP 是使用 OSI（Open System Interconnection） 七層協定的觀念， 所以同樣具有分層的架構，只是將它簡化為四層，在結構上面比較沒有這麼嚴謹

![osi_tcpip](https://linux.vbird.org/linux_server/centos6/0110network_basic//osi_tcpip.gif)

<center>OSI 與 TCP/IP 協定之相關性<br /><font color=#BBBBBB>圖片來源：</font><a href="https://linux.vbird.org/">鳥哥伺服器篇</a></center>

以下就來介紹 TCP/IP的運作流程，假設要使用 google 的網站，整個連線過程如下：

1. 應用程式階段：打開瀏覽器，在瀏覽器上面輸入網址列，按下 [Enter]。此時網址列與相關資料會被瀏覽器包成一個資料， 並向下傳給 TCP/IP 的應用層
2. 應用層：由應用層提供的 HTTP 通訊協定，將來自瀏覽器的資料包起來，並給予一個應用層表頭，再向傳送層丟去
3. 傳送層：由於 HTTP 為可靠連線，所以將該資料丟入 TCP 封包內，並給予一個 TCP 封包的表頭，向網路層丟去
4. 網路層：將 TCP 包裹包進 IP 封包內，再給予一個 IP 表頭（來源與目標的 IP），向鏈結層丟去
5. 鏈結層：若使用乙太網路，此時 IP 會依據 CSMA/CD 的標準，包裹到 MAC 訊框中，並給予 MAC 表頭，再轉成位元串後， 利用傳輸媒體傳送到遠端主機上。

當 Google 收到包裹時，會再依據相反方向拆解，並交由對應的分層處理，Google 伺服器就會得到資料，然後再包裝起來傳回去瀏覽器。

下面就分別介紹 TCP/IP 各層的功能

### TCP/IP 的鏈結層

最底層的連結層主要和硬體比較有關係，接下來就介紹 WAN 與 LAN 的硬體，以及MAC訓匡格式

#### WAN使用的設備

廣域網路設備通常較便宜，下面是常用的設備

* 傳統電話撥接

  早期網路使用方式，透過數據機加上電話線以及電腦的九針序列埠，使用ppp協定（Point-to-Point Protocol）配合撥接程式來取得 IP 並上網

* 整合服務數位網路（ISDN，Integrated Services DIgital Network）

  也是利用電話線路，但連線兩端都需要 ISDN 的數據機才能使用連線功能，在台灣較少見

* 非對稱數位用路回路 (ADSL，Asymmetric Digital Subscriber Line)

  也是透過電話線路撥接來取得 IP，但使用的是電話的高頻，與一般電話的頻率不同，所以可以上網並使用電話，由於上傳/下載的頻寬不同， 因此才稱為非對稱的回路。
  ADSL也是使用數據機，但他使用的是 PPPoE（PPP over Ethernet）的方法，就是將PPP協定在乙太網路卡上使用，所以主機還需要一張網路卡才能連接到數據機，並用撥接程式取得新的介面（ppp0）

* 纜線數據機 (Cable modem)

  使用有線電視的纜線作為網路訊號媒體，同樣需要數據機才能連線到 ISP（Internet Service Provider）

#### LAN使用的設備

區域網路最常用的就是乙太網路，另外還有光纖網路等等的設備，乙太網路已經標準化，所以設備費用相對較低廉。

早先 IEEE 制定乙太網路標準為 802.3 的 IEEE 10BASE5，10BASE5表示『資料傳輸速度為 10Mbps（M bits/second），BASE 表示使用基頻信號傳輸，每個節點最長可達500公尺』。後來又制訂了 802.3u 來支援 100Mbps 傳輸速度的 100BASE-T，線材製作更精密。

傳輸速度增加時，因為線材互相電磁效應會加強，所以線材製作要求就越來越嚴格，所以網路傳輸速度想增加時，除了網卡要升級，連接主機的集線器、交換器要升級，網路線也需要升級

※ 有些網路傳輸速度寫下行/上行 2Mbps/128Kbps，那個 Kb 是指 bits，所以還要除以8，也就是 256KBps/16KBps（KBytes per second）

#### 乙太網路傳輸協定 - CSMA/CD

乙太網路的傳輸就是網路卡跟網路卡之間的資料傳遞，每個乙太網路卡都有一個唯一的卡號，稱為 MAC（Media Access Control），網卡之間的資料傳輸則是透過 IEEE 802.3 的標準 CSMA/CD（Carrier Sense Multiple Access with Collision Detection），我們以下圖來解釋機制

![csmacd](https://linux.vbird.org/linux_server/centos6/0110network_basic//csmacd.gif)

<center>連線示意圖：A發送資料至C<br /><font color=#BBBBBB>圖片來源：</font><a href="https://linux.vbird.org/">鳥哥伺服器篇</a></center>

上圖的 Hub 就是集線器，各主機都是連線到集線器，透過集線器向所有主機發起連線，集線器是一種網路共享媒體，在單一時間點僅能被一部主機使用，當要將 A 的資料發送至 C 時，流程如下：

1. 監聽媒體使用狀況（Carrier Sense）

   A 主機發送封包錢，要先監聽確認沒人使用時，才能發送訊框（frame）

2. 多點傳輸（Multiple Access）

   A 主機發送的訊息會被集線器複製一份，並傳送給連線他的所有主機，因為目標是 C 主機，所以其餘主機會將此訊框拋棄，只有 C 會抓取

3. 碰撞偵測（Collision Detection）

   若其餘主機也剛好發送訊框給 C 主機時，那麼會發生損毀，那麼發送的主機要各自隨機等待一段時間再重新發送

根據上面的機制可以知道以下結論

* 就算沒使用網路，但集線器的燈號也會閃爍，是因為可能有其他主機在對外連線
* 訊框也會被其餘電腦攔截，所以資料必須加密

#### 訊框的封裝格式

前面提的標準標準 CSMA/CD，傳輸的訊框（fram）資料其實就是 MAC。訊框資料上有兩個重要的資料：目標網卡卡號、來源網卡卡號，所以我們又簡稱網卡卡號為 MAC，可以想像 MAC 是一個傳遞的包裹，這個包裹式傳送資料的最小單位，下面這個就是 MAC 訊框的內容

| 前導碼<br />8 Bytes | 目的位址<br />6 Bytes | 來源位址<br />6 Bytes | 資料欄位通訊<br />2 Bytes | 主要資料<br />46~1500 Bytes | 檢查碼<br />4 Bytes |
| ------------------- | --------------------- | --------------------- | ------------------------- | --------------------------- | ------------------- |

上面的目的與來源位址指的就是網卡卡號（硬體位址，hardward address），每個卡號都是唯一，其範圍是 00:00:00:00:00:00 ~ FF:FF:FF:FF:FF:FF，這 6 bytes 中，前面 3 bytes 為廠商代碼， 後面3 bytes 為廠商自行設定的裝置碼，在 Linux 中，可以使用指令 `ipconfig` 來查詢網路卡號；另外若跨不同網域時，來源位址與目的位址也會跟著改變，因為會在不同網域的主機之間傳送，所以硬體位址會改變，所以同一訊框的表頭會跟著變化

※ 訊框內的主要資料為何最小要 46 bytes，是因為若要偵測碰撞時，訊框總資料最少要有 64 bytes，扣除其餘資料，主要資料至少要有 46 bytes 才能達成此條件，所以傳輸資料若小於 46 bytes，系統也會主動填上填充碼來補齊至 46 bytes

#### MTU 最大傳輸單位

MTU（Maximum Transmission Unit）是指網路訊框所能傳送的最大資料量，在乙太網路上，標準定義為1500 bytes。

在 IP 封包裡，封包最大可到 65535 bytes，比 MTU 還要大，所以 IP 封包是可以拆解並放到 MAC 中，到達目的後再組裝回來的。

### TCP/IP 的網路層

前面說到，在硬體部分，乙太網路上面的傳輸使用網路卡卡號為基準的 MAC 訊框，配合 CSMA/CD 的標準來傳送訊框；而在軟體部分，Internet 可以算是 TCP/IP的通稱，其中一個重點就是 IP（Internet Protocol），目前 IP 有兩個版本，分別是 IPv4（Internet Protocol version 4），另一種則是 IPv6 （Internet Protocol version 6）

IPv4 可紀錄的位址只有 32 位元，而 IPv6 可紀錄的位址則有 128 位元，所以未來 IPv6 的使用會越來越多。

#### IP 的表頭

![ipv4-header](/image/2021-06-17-network-basic/ipv4-header.png)

<center>IPv4封包的表頭<br /><font color=#BBBBBB>圖片來源：</font><a href="https://advancedinternettechnologies.wordpress.com/ipv4-header/">IPv4 Header</a></center>

上圖每一行佔用的位元數皆是 32 bits，以下是個別內容介紹

* Version

  IP 封包的版本，例如這邊使用的是 IPv4

* Header Length

  IHL（Internet Header Length），這個 IP 封包的表頭長度，使用單位是字組（word），一個自組為 4bytes

* Type of Service

  IP 封包的服務類型，主要分為以下

  * PPP：此封包的優先度，目前少使用
  * D：0 表示一般延遲（delay），1 表示低延遲
  * T：0 表示一般傳輸量（throughput），1 表示高傳輸量
  * R：0 表示一般可靠度（reliability），1 表示高可靠度
  * UU：保留未使用

* Total Length

  IP 封包的總長度，包含表頭與內容（Data），最大為 65535 bytes

* Identifier

  辨別碼，前面說到 IP 要放入 MAC 訊框內，若 IP 過大時，需要重組再放入 MAC 內，當重組時，就需要識別碼來知道這些重組的 IP 封包是屬於哪個大封包

* Flags

  此表示特殊旗標

  * D：0 表示可分段，1 表示不可分段
  * M：0 表示此 IP 為最後分段，1 表示此 IP 非最後分段

* Fragment Offset

  此 IP 分段在原始 IP 封包的位置（在第幾段），透過接收 Total Length, Identification, Flags 以及這個 Fragment Offset 即可將這些分段組合起來

* Time to Live

  TTL，表示此 IP 封包的存活時間，範圍是 0 - 255，當 IP 封包通過一個路由器時，TTL會減一，變成 0 時，封包會丟棄。

* Protocol

  協定代碼，此欄位紀錄每種資料封包的內容

* Header Checksum

  標頭檢查碼，用來檢查此 IP 表頭

* Source Address

  來源 IP 位址，此項為 32 位元

* Destination Address

  目標 IP 位址，此項為 32 位元

* Options

  額外功能，可放其他參數

* Padding

  因為 Options 的內容不一定，所以 當 Options不滿 32 bits 時，會由 padding 補齊

後續 IP 的組成與機制，留在另一文章再討論

### TCP/IP 的傳輸層

在 OSI 的協定中，網路層的 IP 之上就是傳送層，其資料最常見的就是打包成 TCP 封包，並且需要放到 IP 的封包內，如下圖

![packet_total](https://linux.vbird.org/linux_server/centos6/0110network_basic//packet_total.png)

<center>封包間的相關性<br /><font color=#BBBBBB>圖片來源：</font><a href="https://linux.vbird.org/">鳥哥伺服器篇</a></center>

#### TCP的表頭

以下就是 tcp 的表頭，下面就逐一介紹其資料，其中

![tcp-header](/image/2021-06-17-network-basic/tcp-header.png)

<center>tcp表頭示意圖<br /><font color=#BBBBBB>圖片來源：</font><a href="https://linux.vbird.org/">鳥哥伺服器篇</a></center>

* Source Port

  來源埠口，連線的通道，client 與 server需要有這個通道來傳遞封包資料

* Destination Port

  目標埠口，意義同上

* Sequence Number

  封包的序號，當 tcp 的資料超過 IP 封包的大小時就必須分段，此序號可用來將 tcp 的資料組合

* Acknowledge Number

  回應序號，用來確認 server 有收到 client 送出的封包資料，當 client 發送封包至 server 後，server 就會發送此序號給 client，表示先前的封包已收到

* Data Offset

  用來說明整個封包區段的起始位置

* Reserved

  未使用的保留欄位

* Code（Control Flag）

  控制標誌碼，用來說明連線狀態，此欄位共有 6 個 bits，分別代表 6 個控制碼，1 表示啟動，以下為各個控制碼：

  * URG（Urgent）

    1 表示緊急封包，接收端要緊急處理

  * ACK（Acknowledge）

    1 表示回應封包， 則與上面的 Acknowledge Number 有關

  * PSH（Push function）

    1 表示要求對方立即傳送緩衝區內的其他對應封包

  * RST（Reset）

    1 表示連線馬上結束，無需等待終止確認手續（發送端已斷線，強制結束）

  * SYN（Synchronous）

    1 表示發送端希望雙方建立同步處理（主動要連接對方建立連線）

  * FIN（Finish）

    1 表示傳送結束，詢問對方是否同意斷線

* Window

  滑動視窗，單位是 byte，用來控制封包流量，可告知對方目前緩衝器容量（Receive Buffer）是否仍可接收封包。當 Window=0 時，表示緩衝器已滿

* Checksum

  確認檢查碼

* Urgent Pointer

  前面的 Code 欄位中，URG=1時才有作用

* Options

  目前此欄位僅應用於表示接收端可以接收的最大資料區段容量，若此欄位不使用， 表示可以使用任意資料區段的大小。

* Padding

  用來補足 Optional 的大小，兩者加起來必須是 32 bit

#### port

前面有提到 Source Port 與 Destination Port，這兩個都是 16 bit，對大皆可達 65535（2^16）個 port，當 server 與 client 要連線時，兩邊都需有一個對應的 port 來形成連線通道，假設沒有對應到對的 port，就算 IP 正確了也沒有用。

Internet 有需多規範好的 port，這些 port number 通常小於1024，在 linux 環境下，這些 port number 的對應預設寫在 `/etc/services`，底下則是常見的 port number 與服務的對應

| 連接 port | 服務                                                 |
| --------- | ---------------------------------------------------- |
| 20        | FTP-data，檔案傳輸協定所使用的主動資料傳輸 port      |
| 21        | FTP，檔案傳輸協定的命令通道                          |
| 22        | SSH，較為安全的遠端連線伺服器                        |
| 23        | Telnet，早期的遠端連線伺服器軟體                     |
| 25        | SMTP，簡單郵件傳遞協定，用在作為 mail server 的 port |
| 53        | DNS，用在作為名稱解析的領域名稱伺服器                |
| 80        | HTTP（HyperText Transport Protocol）                 |
| 110       | POP3，郵件收信協定                                   |
| 443       | HTTPS，有安全加密機制的 HTTP                         |

上面這些小於 1024 的 port 皆需要 root 才能啟動，所以又被稱為 Privileged Ports，通常以上限制都在 server 上，因為 client 通常都是要資料而已，所以 client 的 port number 只要取一組大於 1024 且沒在用的 port number 即可。

網路要連線的話除了剛剛提的 port，也需要 IP 才能達成雙向連線，以下資料又稱為 Socket pair

* Source Address + Source Port
* Destination Address + Destination Port

#### TCP 的連線機制

在封包連接時，建立連線需要三個確認的步驟，此方式被稱為三向交握（Three-way handshake），底下就來說明流程

![TCP-connection-1](https://media.geeksforgeeks.org/wp-content/uploads/TCP-connection-1.png)

<center>Three-way handshake<br /><font color=#BBBBBB>圖片來源：</font><a href="https://www.geeksforgeeks.org/tcp-3-way-handshake-process/">TCP 3-Way Handshake Process</a></center>

上圖的 HOST P 可先視為 client，HOST Q 可視為 server，首先

1. 發起封包

   client 想對 server 發起連線時，要發出一個要求連線的封包，此時 client要隨機取一個大於 1024 的 port，並在 TCP 的表頭中，帶著 SYN 的主動連線（SYN=1），並記下發送連線封包給伺服器端的序號（Sequence number = x）

2. 封包接收與確認封包傳送

   當 server 收到封包並確定接收時，就會製作另一個封包（syn=y，ack=x+1）傳給 client

3. 回送確認封包

   client 收到 server 傳來的 ACK 後（ack=x+1）以及序號SYN（syn=y）就知道發起封包被正確的接受了，若 client 也同意與 server 建立連線，那麼 client 就會再傳一個確認封包給 server（ack=y+1）

4. 取得最後確認

   server 收到 ack=y+1的封包後，就可以建立此次連線了

#### 另一種協定：UDP

UDP（User Datagram Protocol）與TCP不同，他不提供可靠的傳輸模式，因為他不是連線導向的機制，在 UDP 的傳送中，不會回覆 ACK 給發送端，所以他不會確認對方是否正確的收到資料，所以其表頭資料也比較少，相對的，他可以在 Data 放入更多的資料，他的資源消耗小，處理速度快。

UDP 適合用於需及時反應，且不考慮資料的正確性的資料流，例如即時影像、音頻、視頻等等的資料。

### 網路的防火牆

防火牆是一種可以監控傳入傳出的流量的資安裝置，其中封包過濾式的防火牆可以抵當一些可能有問題的封包，在 Linux 上，我們可以對封包表頭的分析，並且設定分析的規則，當發現某些特定的 IP 、特定的 port 以及特定的封包資訊（例如 SYN/ACK），就將那個封包給他丟棄， 這就是基本的防火牆原理。

若以 OSI 的七層協定來說，底下幾個都可以做特定的阻擋

* 第二層（鏈結層）

  針對來源與目標的 MAC 阻擋

* 第三層（網路層）

  針對來源與目標的 IP 阻擋

* 第四層（傳送層）

  針對 TCP/UDP 的 port 阻擋

## Reference

* [關於網路的一些基本概念](https://arcobaleno18.pixnet.net/blog/post/201733936)
* [基礎網路概念](http://linux.vbird.org/linux_server/0110network_basic.php#whatisnetwork_tcpip)