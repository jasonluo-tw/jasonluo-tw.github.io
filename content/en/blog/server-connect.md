---
author: "Jason Luo"
title: "設置可以遠端連線的Linux Server"
description: "A guideline to set up the connection to my server"
tags: ["system", "tech"]
date: 2022-02-03
math: true
thumbnail: https://images.unsplash.com/photo-1488229297570-58520851e868?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1169&q=80
---
&emsp;最近重新開始把自己家裡的桌電當成小型server，所以就有了遠端連線的需求，還記得兩三年前的時候曾經有弄過一次，但這次要弄的時候卻發現忘了很多東西，所以這次就把過程記錄下來，除了分享之外，以後有需要也有文章可以參考。雖然在這個AWS、GCP等雲端服務橫行的時代裡，直接使用他們的服務非常的方便，但仍有自己的桌機server還是一件很酷的事😎，而且也不需要花錢才能使用服務，同時在架設的過程中也可以學到一些事情。

> ## 廢話不多說，就趕快進入正題吧！
-----

## 設定固定IP
&emsp;首先，家用網路通常是浮動IP，因此我們需要先設定一組固定IP，這樣才能用同一組IP做登入，不然每次登入時的IP會變來變去。我家的網路是`ADSL光世代`，中華電信有提供一組固定IP給客戶使用，因此先到[中華電信的網站](https://csapp.cht.com.tw/CustService/WEB/staticip-web.html)去做設定，設定完之後應該可以看到以下的畫面
<img src="https://i.imgur.com/9Qn6UEF.png" style="width: 400px">
> 設定時間
> [time=Thu, Jan 27, 2022 8:58 PM] 


## 設定PPPoE網路連線
設定完之後，接下來有兩條路可以走：
1. 網路線直接連到桌機
2. 網路數據機先接WIFI分享器後，再從分享器接到桌機

> 前面的方法比較簡單，只要在Ubuntu裡設定PPPoE之後就結束了，後面那一種則是要在WIFI分享器裡做設定

### 先說明第一種 Ubuntu設定PPPoE
1. 如果沒有PPPoE工具，先安裝
    ```bash
    sudo apt install net-tools
    sudo apt-get install pppoeconf
    ```
2. 設定PPPoE
    ```bash
    sudo pppoeconf
    ```
    下了這個指令之後會跳出一個視窗，除了帳號和密碼之外，其他都按YES
    在輸入帳號和密碼的地方，要輸入**電信公司給的帳號和密碼**
    這邊以中華電信為例:
    > 帳號: [帳號]@ip.hinet.net
    密碼: [中華電信的密碼]
3. 撥接(開啟固定IP服務)
    ```bash
    sudo pon dsl-provider
    ```
    > 撥接成功訊息
    > Plugin rp-pppoe.so loaed
4. 之後在command line打`ip addr show`，在最下面應該可以看到電信公司給的固定IP，如有看到即完成設定。

5. 如要中斷連線
    ```bash
    sudo poff -a
    ```
- 另外在`/etc/ppp`目錄下，`chap-secrets`和`pap-secrets`兩個檔案裡，可以查到撥接的帳號、密碼。
- 設定完之後，`/etc/network/interfaces`的檔案會被更動

### 另一個種是設定wifi分享器
&emsp;除了桌電要接網路之外，同時也想要有WIFI讓其他裝置可以連網，這時候就需要用到WIFI分享器。而當桌機的網路是從WIFI分享器來的時候，設置桌機linux的PPPoE就沒有用了，此時要去設定WIFI分享器的連線方式才行。
&emsp;這個時候我們可以把wifi分享器比擬成一個公司大樓的總機，裡面各個不同的連接阜想成是大樓裡不同的辦公室，所以當網路訊號(信件)送來時，會先傳到WIFI分享器(總機)，再藉由分享器(總機)來傳遞到正確的辦公室的人的手上。所以網路訊號送過來時，最先接觸到的是WIFI分享器，因此我們要設置分享器中的PPPoE，才會有固定IP。

#### WIFI分享器的設定
每個裝置的設定頁面可能會不一樣，而我是用D-Link 819的WIFI分享器，所以以下是D-Link 819分享器的裝置設定:
1. 打開網頁打入192.168.0.1連到分享器的設定網頁
2. 找到網路連線的設定畫面選擇PPPoE並輸入帳號、密碼(中華電信給的:XXXXXXXX@ip.hinet.net)，完成設定。
3. 以上步驟完成之後即完成固定IP的設置，此時，不管哪個裝置連線之後，顯示的IP位址都會是固定IP的位址。
4. 接下來需要把連接阜設定到特定的內網IP，這樣在連線的時候才會連到特定的內網IP
    設定如下圖，公用和私用連接阜都設定成22，22為ssh連線時默認的連接阜，通訊協定選擇TCP或是TCP+UDP都可以

    <img src="https://i.imgur.com/3WubrL6.png" style="width:600px">


5. 再來，我們要設定DHCP保留位址，這樣下次重新啟動WIFI分享器，重新連線的時候，桌機被分配到的內網才會相同，不然內網IP變了，連線的時候就會無法連線。

    <img src="https://i.imgur.com/X55PQG4.png" style="width:600px">
<br>

> ### 到此，固定IP就設定好了，接下來要設定SSH的連線🙂

## SSH網路連線的設定
&emsp;接下來要到Linux Ubuntu上設定SSH連線
1. 先檢查Ubuntu是否開啟openssh-server的服務，使用`ps aux|grep ssh`檢查是否有啟動sshd，如有啟動sshd服務可以跳過2, 3, 4步
**openssh-server是連到你的電腦需要的 
  openssh-client則是連出去需要用到的**
2. 檢查是否有安裝openssh-server 
    `dpkg -l | grep ssh`
    如果沒有就要下載
    `sudo apt-get install openssh-server`
3. 下載完之後啟動服務
    `sudo service ssh start`
4. 檢查服務是否有啟動
    `sudo service ssh status`
5. 設定`/etc/host.allow`和`/etc/host.deny`限制可以連線的IP位址，hosts.allow的優先順序高於hosts.deny
    e.g.
    ```
    # daemon_list : client_list [: command]
    sshd : 192.168.1.100
    ALL: 140.112.*
    vsftpd : 192.168.1.*  # ftp連線設定
    ```
6. SSH密鑰的設置
    原先一開始連線是用密碼來做登入，如果換成使用SSH密鑰登入的話，更可以增加安全性
    原本就有ssh key的話，只要把public key加到server的`~/.ssh/authorized_keys`中即可
    **沒有ssh key的話就要先用`ssh-keygen`產生一組publick key和private key**
7. 修改`/etc/ssh/sshd_config`檔案，使其登入方式改變成用SSH key登入
    把檔案中的`PasswordAuthentication`改成no，`PubkeyAuthentication`改成yes
    ```
    PasswordAuthentication no
    PubkeyAuthentication yes
    ```
8. 重新啟動sshd服務
    `service sshd restart`
    
    
> ## 最後，試試看使用筆電登入server，能登入的話就成功了🥳


## Some Reference:
- [openssh-server](https://andy6804tw.github.io/2019/01/23/ubuntu-ssh-remote/)
- [ssh公私鑰設定、連線](https://blog.gtwang.org/linux/linux-ssh-public-key-authentication/)
- [遠端連線回Server](https://medium.com/@fortunatemaker2603/ssh-%E9%81%A0%E7%AB%AF%E9%80%A3%E7%B7%9A%E5%9B%9E%E5%AE%B6%E4%B8%AD%E7%9A%84-windows-%E9%9B%BB%E8%85%A6-7e5267ae1e93)
- [Ubuntu PPPoE連線設定](https://stevenitlife.blogspot.com/2020/05/ubuntu-pppoe.html)
