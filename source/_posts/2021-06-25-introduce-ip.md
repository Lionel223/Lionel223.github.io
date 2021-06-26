---
title: IP的基本介紹與使用
date: 2021-06-25 23:27:03
tags: ["network", "ip"]
categories: network
---

先前曾介紹過網路的組成，以及通訊協定 TCP/IP，本章會針對 IP 的組成與機制再做詳細的解說。

<!-- more -->

## IP位址的組成

IP（Internet Protocol）其實就是一種網路封包，在表頭中最重要的就是來源位址與目標位址，又稱為 IP 位址（Internet Protocol address），其大小都一樣是 32 位元，所以他其實就是由 32 個 0 與 1 所組成的一串數字，但因為人類叫習慣看十進位的資料，所以我們又把 32bit 的 IP 拆成四小段，每段各為 8bit，再將每段的 8bit 轉換成十進位顯示，並以小數點隔開，就變成我們現在常看到的 IP 顯示方式了，此方式又稱為點分十進位，顯示方式如下：

```markdown
IP 的表示式：
00000000.00000000.00000000.00000000   => 0.0.0.0
11111111.11111111.11111111.11111111   => 255.255.255.255
```

上面就是 IP 最小到最大的全部範圍，總共有 2^32 個 IP 位址可使用，但因分配上的問題，很多區域的編碼實際上被空出或不能使用，所以也即將用盡了，後來人們又研究出 IPv6，未來也會逐漸轉為使用 IPv6 的 IP，但在此章節還是以 IPv4 為主。

在前面提到的這一串數字中，其實還可以再細分為兩個部分，分別是 **Net_ID**（網域號碼）以及 **Host_ID**（主機號碼），以下以 192.168.0.0 ~ 192.168.0.255 這個 Class C 的網域來說明

```markdown
192.168.0.0~192.168.0.255
11000000.10101000.00000000.00000000
11000000.10101000.00000000.11111111
|----------Net_ID---------|Host_ID|
```

上面範例中，`192.168.0` 就是 **Net_ID**，最後一組的數字 `0~255` 則是 **Host_ID**，這個範圍內的 IP 表示都在同一個網域，其定義為『IP 具有相同的 Net_ID，並具有不同的 Host_ID』，在此處的主機都是使用同一個網路串在一起，要注意的是不同主機不可使用同一個 Host_ID，否則會發生 IP 衝突，可能導致無法使用網路。

另外還有幾點要注意

* Host_ID 不可全是 0 或者 1，以上面例子為例就是不可以是 0 或者 255，Host_ID 若全部都是 0，表示此網段的 **Network IP**（網段位址）；而若全部都是 1，則表示 **Broadcast IP**（廣播位址），所以可以使用的 IP 為 0~254
* 同個網域內的主機都可以透過 CSMA/CD 的功能直接在區網內用廣播進行網路連線，也就是網卡對網卡傳遞資料 (透過 MAC 訊框)
* 不同網域內的主機則需要透過 router（路由器）的溝通才能將網域連結再一起
* Host_ID 佔用的位元越多，則同個網域內可設定的 IP 也越多

## IP的分級

為了 IP 的管理與發放註冊的方便性，InterNIC 將整個 IP 網段分為五個等級，定義如下：

```
# 二進位的形式
Class A : 0xxxxxxx.xxxxxxxx.xxxxxxxx.xxxxxxxx  ==> Net_ID 的開頭是 0
          |--net--|---------host------------|
Class B : 10xxxxxx.xxxxxxxx.xxxxxxxx.xxxxxxxx  ==> Net_ID 的開頭是 10
          |------net-------|------host------|
Class C : 110xxxxx.xxxxxxxx.xxxxxxxx.xxxxxxxx  ==> Net_ID 的開頭是 110
          |-----------net-----------|-host--|
Class D : 1110xxxx.xxxxxxxx.xxxxxxxx.xxxxxxxx  ==> Net_ID 的開頭是 1110
Class E : 1111xxxx.xxxxxxxx.xxxxxxxx.xxxxxxxx  ==> Net_ID 的開頭是 1111

# 十進位的形式
Class A :   0.xx.xx.xx ~ 127.xx.xx.xx
Class B : 128.xx.xx.xx ~ 191.xx.xx.xx
Class C : 192.xx.xx.xx ~ 223.xx.xx.xx
Class D : 224.xx.xx.xx ~ 239.xx.xx.xx
Class E : 240.xx.xx.xx ~ 255.xx.xx.xx
```

一般常見的就是 Class A，B，C，Class D是作為群播（multicast）的特殊功能，Class E 則是保留沒有使用的網段。

### IP的種類

在 IPv4 裡面，共有兩種 IP 類別，如下：

* Public IP

  公共 IP，由 INTERNIC 規劃的 IP，此種 IP 才能連到 Internet

* Private IP

  私有 IP，通常使用在區域網路內的主機連線，不能對外傳播，使用 Private IP 作為來源或目的位址的封包，不可透過 Internet 傳送

私有 IP 在 Class A，B，C 中，各自保留一段為 Private IP 的網段，如下

```
# 二進位的形式
Class A : 00001010.xxxxxxxx.xxxxxxxx.xxxxxxxx  ==> Net_ID 的開頭是 00001010
          |--net--|---------host------------|
Class B : 10101100.0001xxxx.xxxxxxxx.xxxxxxxx  ==> Net_ID 的開頭是 10101100.0001
          |----net----|-----------host------|
Class C : 11000000.10101000.xxxxxxxx.xxxxxxxx  ==> Net_ID 的開頭是 11000000.10101000
          |-------net------|-------host-----|

# 十進位的形式
Class A :  10.0.0.0  - 10.255.255.255
Class B : 172.16.0.0 - 172.31.255.255
Class C : 192.168.0.0 - 192.168.255.255
```

這三段的 Private IP 是預留使用，不可直接用在 Internet 上的連接，所以他只存在於內部網路，若想讓這些 Private IP 上 Internet 的話，需要設定簡易的防火牆，並加上 NAT（Network Address Transfer）服務，就可以透過 IP 偽裝讓 Private IP 連上 Internet。

### 特殊的 IP

除了剛剛提的預留 IP，還有一個 Loopback 網段，這個網段可用來測試作業系統的內部迴圈，並給系統內部需要網路介面的服務使用，當要測試程式可否在 server 上運作時，我們有時會使用此內部迴圈網路來進行測試，這個網段在 Class A 的 `127.0.0.0/8`，且預設的主機（localhost）的 IP 是 `127.0.0.1 `，此方式甚至可以不用安裝網卡也可執行，用來測試很方便。

另外讓主機發 mail 給 root 時也是使用這個內部迴圈，要測試 TCP/IP 的封包是否正常也可以使用它。

### 取得 IP

IP 與相關網域的設定主要有以下方式：

* 手動設定（static）

  可向網管拿到相關 IP 參數，手動設定網路

* 撥接取得

  向 ISP（Internet Service Provider）註冊，取得帳密後，ISP 會透過他們的設定讓系統取得網路參數，例如 ADSL撥接、光纖等等，大部分都是使用撥接的方式取得固定 IP

* 自動取得網路參數（DHCP，Dynamic Host Configuration Protocol）

  在區域網路內會有一部主機負責管理所有電腦的網路參數，網路啟動時就會主動向主機要求 IP 參數， 若取得網路相關參數後，你的主機就能夠自行設定好所有伺服器給你的網路參數了。最常使用於企業內部、IP 分享器後端、 校園網路與宿舍環境，及纜線寬頻等連線方式。

## Netmask, 子網路與 CIDR

前面介紹過 Class 的 IP 等級劃分，是為了管理的考量，但事實上我們使用時，並不會直接使用，而會把網域再切得更細一點，以 Class C 來說，我們會將 IP 的 32 bit 切分成 24 bit 的 Net_ID 與 8 bit 的 Host_ID，此時這個 Class C 共有 2^8 - 2 = 254 個 IP可以使用；若我們想將這個網域再切成兩個子網域，可以將第一個 Host_ID拿來當作 Net_ID，此時會變成 25 bit 的 Net_ID 與 7 bit 的 Host_ID，每個子網域就有 2^8/2 - 2 = 126 個可用的 IP。

```
Class C : 110xxxxx.xxxxxxxx.xxxxxxxx.xxxxxxxx
          |-----------net-----------|-host--| ==> 共 24bit 的 Net_ID，8bit 的 Host_ID
# 切成兩個子網域
Class C : 110xxxxx.xxxxxxxx.xxxxxxxx.xxxxxxxx
          |-----------net------------|-host-| ==> 共 2ㄓbit 的 Net_ID，7bit 的 Host_ID
```

### Netmask

前文說的就是子網路的切分，可以使用 Netmask 來表示，Netmask 又稱為Subnet mask（子網路遮罩），他的顯示方式是如下：

因為 Net_ID 是不可變的，所以假設他所佔據的 bit 被用光了，所以為 1，而 Host_ID 是可變的，所以假設他是保留著的，所以是 0

底下以 Class C 的 `192.168.0.0 ~ 192.168.0.255` 為範例來看 Netmask 如何顯示

```
Class C：192.168.0.0~192.168.0.255
first IP： 11000000.10101000.00000000.00000000
last IP：  11000000.10101000.00000000.11111111
           |----------Net_ID---------|-host--|
Netmask：  11111111.11111111.11111111.00000000  <== Netmask 二進位
Netmask：    255   .  255   .  255   .   0      <== Netmask 十進位

Network IP：   192.168.0.0     <==第一個 IP
Broadcast IP： 192.168.0.255   <==最後一個 IP
可設定的 IP：   192.168.0.1 ~ 192.168.0.254
```

下面則是 Class A，B，C 的 Netmask 的表示方式

```markdown
Class A : 11111111.00000000.00000000.00000000 ==> 255.  0.  0.  0
Class B : 11111111.11111111.00000000.00000000 ==> 255.255.  0.  0
Class C : 11111111.11111111.11111111.00000000 ==> 255.255.255.  0
```

### 子網路切分

將前面的子網路切分，再加上 Netmask 後，即可做進一步的了解，我們以前面的 `192.168.0.0 ~192.168.0.255` 為例子來解說

```
192.168.0.0 ~192.168.0.255 屬於 Class C，原本 Class C 的 Net_ID 與 Host_ID如下
共有24個 Net_ID 與8個 Host_ID
first IP: 11000000.10101000.00000000.00000000      
last IP:  11000000.10101000.00000000.11111111      
          |----------Net_ID---------|-host--|
Network IP  :   192.168.0.0
Broadcast IP: 192.168.0.255

==> 切成兩個子網路後，Net_ID 加 1，Host_ID 減 1
11000000.10101000.00000000.0 0000000  多了一個 Net_ID, 數字是 0 (第一個子網)
11000000.10101000.00000000.1 0000000  多了一個 Net_ID, 數字是 1 (第二個子網)
|----------Net_ID-----------|-host--|

第一個子網路
first IP:  11000000.10101000.00000000.0 0000000      
last IP:   11000000.10101000.00000000.0 1111111 
           |----------Net_ID-----------|-host--|
Network:   11000000.10101000.00000000.0 0000000   192.168.0.0
Broadcast: 11000000.10101000.00000000.0 1111111   192.168.0.127
Netmask:   11111111.11111111.11111111.1 0000000   255.255.255.128

第二個子網路
first IP:  11000000.10101000.00000000.1 0000000      
last IP:   11000000.10101000.00000000.1 1111111 
Network:   11000000.10101000.00000000.1 0000000   192.168.0.128
Broadcast: 11000000.10101000.00000000.1 1111111   192.168.0.255
           |----------Net_ID-----------|-host-|
Netmask:   11111111.11111111.11111111.1 0000000   255.255.255.128
```

### CIDR

我們常常會以 Network 與 Netmask 來表示網域，這時候就會使用下面的寫法

```markdown
Network/Netmask
192.168.0.0/255.255.255.0
或是以下寫法
192.168.0.0/24    （因為 Net_ID 共有 24bit）
```

這種 `Network/Netmask` 的寫法又被稱為 CIDR（Classless Interdomain Routing）

若要表示在前面的 `192.168.0.0 ~192.168.0.255` 切成兩個網域後的形式，則是如此

```markdown
192.168.0.0/255.255.255.128
192.168.0.128/255.255.255.128
或是
192.168.0.0/25
192.168.0.128/25
```

切成四個網域後，表示如下

```
192.168.0.0/255.255.255.64
192.168.0.64/255.255.255.64
192.168.0.128/255.255.255.64
192.168.0.192/255.255.255.64
或是
192.168.0.0/26
192.168.0.64/26
192.168.0.128/26
192.168.0.192/26
```

※ 某些特殊的情況下，可能會反過來，將 Net_ID 借用給 Host_ID的情況，例如 192.168.0.0/16，將 192 開頭的 Class C 變成 Class B 的樣子，透過 Netmask 的規範打破原本 IP 的層級劃分，所以才稱為 CIDR（Classless Interdomain Routing）

## 路由

前面提過，同一區網可使用 Broadcast IP 來溝通，但非同一區網的話，則需要使用路由器（router）才能溝通

例如 `192.168.10.100/25` 與 `192.168.10.200/25` 這兩個網域因為 Net_ID 不同，所以不在同一個網段，所以需要透過 routing 才能達成資料傳遞。當主機要傳送資料時，需要參考 route table（路由表），每個主機都有自己的路由表，底下就是兩個不同網域的主機傳遞資料的方式：

1. 查詢 IP 封包、目標 IP 位址

   主機先查閱封包表頭的目標 IP 位址，分析主機自己的路由表，若目標與本機的 Net_ID 相同時，表示同一網域，可透過區網將資料傳遞至目的地

2. 查詢default gateway（預設路由）

   但此情況 Net_ID 不同，表示非同一網域，所以會再分析路由表是否有其他相符合的路由設定，若沒有，就會將此 IP 封包送到 default gateway

3. 送出後即不理會

   送給 default gateway 後，主機就不理會之後的工作，default gateway 也會耕具上述流程分析自己的路由資訊，繼續傳輸給目的主機

### 路由的指令

我們也可以用指令來觀察本機的 route table，基本的指令使用方式如下：

```bash
route [-n]
```

* `OPTIONS`
  * `-n` -  將主機名稱以 IP 方式顯示

#### Example

```
$ route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref  Use Iface
192.168.0.0     *               255.255.255.0   U     0      0      0 eth0
127.0.0.0       *               255.0.0.0       U     0      0      0 lo
default         192.168.0.254   0.0.0.0         UG    0      0      0 eth0

$ route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref  Use Iface
192.168.0.0     0.0.0.0         255.255.255.0   U     0      0      0 eth0
127.0.0.0       0.0.0.0         255.0.0.0       U     0      0      0 lo
0.0.0.0         192.168.0.254   0.0.0.0         UG    0      0      0 eth0

# Destination ：Network IP
# Gateway     ：Gateway IP, 若為 0.0.0.0 表示不需要額外的 IP；
# Genmask     ：Netmask
# Flags       ：表示該網域或主機代表的意義：
#               U：路由可用；
#               G：網域需要經由 Gateway 來幫忙轉遞；
#               H：路由為一部主機，而非一整個網域；
# Iface       ：Interface, 介面
```

上面的例子表示有三個路由，若 Gateway 為 * 時，表示不用透過 Gateway，直接使用後面的網路介面傳送即可，若不再以上的規則，則會將封包傳送到 default 這個路由規則，基本上每個主機都有一個 default gateway 來處理非網域內的封包。

## Reference

* [鳥哥 - 基礎網路概念](http://linux.vbird.org/linux_server/0110network_basic.php#whatisnetwork_tcpip)