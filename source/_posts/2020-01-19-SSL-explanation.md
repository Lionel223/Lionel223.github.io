---
title:  "SSL簡易說明"
date: 2020-01-19 00:00:00
tags: ["security", "SSL"]
categories: security
---

SSL就是Secure Socket Layer的縮寫，是一種安全協定，目的是為網際網路通訊提供安全及資料完整性保障，以及防止兩個應用/系統間發送的資料不會被讀取或修改任何傳輸的資訊。底下就來簡易說明 ssl 的原理。

<!-- more -->

SSL使用上被排在OSI model(Open Systems Interconnection model)中的Session層以及Application層，示意如下

1. Physical layer (network cable / wifi)
2. Data link layer (ethernet)
3. Network layer (IPv4)
4. Transport layer (TCP)
5. **Session layer (SSL)**
6. Presentation layer (none in this case)
7. **Application layer (HTTP)**

## 如何運作
一般來說，會進行SSL Handshake，也就是交換雙方的加密演算法來認證伺服器的身份，流程如下:
1. Client利用訊息將本身支援的SSL版本、加密演算法、演算法等資訊發送給Server。
2. Server收到Client訊息並確定本次通訊採用的SSL版本和加密套件後，將訊息回覆給Client。
3. Server將利用Certificate訊息將本身公鑰的數位憑證傳給Client。
4. Server發送Done訊息，通知Client版本和加密套件協商結束，並開始進行密鑰交換。
5. 當Client驗證Server的證書合法後，利用Server的證書中之公鑰加密SSL用戶端隨機生成的Premaster Secret(這是一個用在對稱加密密鑰產生中的46位元組的亂數字)，並透過Client Key Exchange消息發送給Server。
6. Client發送Change Cipher Spec消息，通知Server後續報文將採用協商好的密鑰和加密套件進行加密。
7. Client計算已交互的握手消息的Hash值，利用協商好的密鑰和加密演算法處理Hash值，並透過Finished消息發送給SSL伺服器。SSL伺服器利用同樣的方法計算已交互的握手消息的Hash值，並與Finished消息的解密結果比較，如果兩者相同，則證明密鑰和加密套件協商成功。
8. Server發送Change Cipher Spec訊息，通知Client後續傳輸將採用協商好的密鑰和加密套件進行加密。
9. Server計算已交互的握手消息的Hash值，利用協商好的密鑰和加密套件處理Hash值，並透過Finished消息發送給Client。Client利用同樣的方法計算已交互的握手消息的Hash值，並與Finished消息的解密結果比較，如果兩者相同，且MAC值驗證成功，則證明密鑰和加密套件協商成功。在Client接收到Server發送的Finished消息後，如果解密成功，則可以判斷SSL伺服器是數位證書的擁有者，即SSL伺服器身分驗證成功。這是因為只有擁有私鑰的Server才能從Client Key Exchange消息中解密得到Premaster Secret，從而間接地實現了Client對Server的身分驗證。

## Certificate的作用
通常server的加密都是使用public key與private key，提供使用者對資訊加密、解密，private key會留在server，public key則會公開，在https時，client會取得此server的public key，使用public key來加密，加密的資訊傳過來，server會使用自己的private key解密，從而得到資訊。

然而，這種只是最簡單的模型，實際情況時，client取得public key時，會無法確定提供public key的server是否是正確的server，也就是說，這個public key沒有被**認證**過。

為了達到這個目的，所以出現了第三方公正機關CA(Certificate Authority)來發放、管理數位憑證，引入CA的概念後，前面的流程就會稍微改變：
1. server生成public key與private key，private key留在server，public key經過加工後公開，並送給憑證中心驗證(需放入public key以及網頁的基本資訊)
2. 憑証中心簽署完畢後的public key，又可以稱之為ceriticate。Certificate自憑証中心取回之後，要安裝入server，作為SSL的public key來使用
3. 當client使用網頁時就會取得server的certificate，browser預先安裝好的憑證中心就會認證此certificate有無問題，若無問題則會使用其來加密

CA有分兩種，分別是Self-Signed CA和Signed CA，剛剛說的都是Signed CA，需被上層的CA認證，其中最上層的就是Self-Signed CA，他不需上層CA的認證，是用自行認證的方式認證，最上層的商業CA以及自行架設內部認證用的CA都屬於此類，其關係如下圖

![](https://www.netadmin.com.tw/upload/news/NP110614000811061414255001.png)

圖片來源：[網站SSL加密原理簡介](https://www.netadmin.com.tw/netadmin/zh-tw/technology/6F6D669EB83E4DC9BEA42F1C94636D46)

## One-Way SSL
在one-way SSL中，client會驗證server去確保是從預期的server收取資料，為了實踐one-way SSL，server需要分享自己的公鑰(public certificate)給clients，下面步驟即是client和server在one-way SSL中，建立連線以及傳送資料的步驟
1. Client對server發起request，要求一些保護的資料，此步驟開始SSL/TLS的握手機制(Client Hello)
2. Server回傳他的public certificate(Server Hello)
3. Client使用CA驗證取得的certificate
4. SSL/TLS client傳送隨機字串，使client與server使用此字串計算並加密訊息，此隨機字串是使用server的public key進行加密
5. 在驗證、同意這個Secret key後，client和Server使用這個key來加密、解密，進而交換彼此資料(handshake結束)
![](http://tutorialspedia.com/wp-content/uploads/2017/12/one-way-ssl.png)

圖片來源：[An Overview of One-Way SSL and Two-Way SSL](https://tutorialspedia.com/an-overview-of-one-way-ssl-and-two-way-ssl/)

## Two-Way SSL
two-way SSL中，client與server會驗證彼此，確保通訊是安全的，兩邊會分享他們的public certificate，並驗證彼此的通訊是基於此憑證之上的。下面步驟即是client和server在two-way SSL中，建立連線以及傳送資料的步驟
1. Client對server發起request，要求一些保護的資料，此步驟開始SSL/TLS的握手機制(Client Hello)
2. Server回傳他的public certificate(Server Hello)
3. Client使用CA驗證取得的certificate
4. 當client驗證成功，client也提供自己的public certificate給server
5. server使用CA驗證取得的certificate
6. 在驗證、同意這個Secret key後，client和Server使用這個key來加密、解密，進而交換彼此資料(handshake結束)
![](http://tutorialspedia.com/wp-content/uploads/2017/12/2-way-ssl.png)
圖片來源：[An Overview of One-Way SSL and Two-Way SSL](https://tutorialspedia.com/an-overview-of-one-way-ssl-and-two-way-ssl/)

## 總結
Two-way SSL要求server與client都有CA。One-way SSL則不需要client擁有CA證書，具體的過程相對於上面的步驟。只需將伺服器端驗證客戶證書的過程去掉，以及在協商對稱密碼方案，對稱通話密鑰時，伺服器發送給客戶的是沒有加過密的(這並不影響 SSL 過程的安全性)密碼方案。這樣，雙方具體的通訊內容，就是加過密的數據，如果有第三方攻擊，獲得的只是加密的數據，第三方要獲得有用的信息，就需要對加密的數據進行解密，這時候的安全就依賴於密碼方案的安全。

一般Web應用都是採用one-way SSL，因為有許多的用戶，且無需在通訊層對用戶身份進行驗證，一般都在應用邏輯層來保證用戶的合法登入。但如果是企業應用對接，情況就不一樣，可能會要求對客戶端(相對而言)做身份驗證。這時就需要做two-way SSL。

## Reference
* [網站SSL加密原理簡介](https://www.netadmin.com.tw/netadmin/zh-tw/technology/6F6D669EB83E4DC9BEA42F1C94636D46)
* [Where does SSL encryption take place?](https://security.stackexchange.com/questions/19681/where-does-ssl-encryption-take-place)
* [SSL Explaned](http://community.lightspeedsystems.com/courses/ssl-explained/)
* [SSL憑証(SSL certificate)的原理](http://support.unethost.com/knowledgebase.php?action=displayarticle&id=82)
* [Certificate Authority(CA) 憑證簡介](https://mistech.pixnet.net/blog/post/80751019-certificate-authority%28ca%29-%E6%86%91%E8%AD%89%E7%B0%A1%E4%BB%8B)
* [An Overview of One-Way SSL and Two-Way SSL](https://tutorialspedia.com/an-overview-of-one-way-ssl-and-two-way-ssl/)