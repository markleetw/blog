---
title: ADSL固定IP及數據機硬體撥接
date: 2016-06-23 11:52:48
categories:
- Technique
- Misc
tags:
- adsl
---

{% asset_img yngwie-johann-malmsteen.jpg Yngwie Johann Malmsteen %}

現在ISP(網路服務供應商)基本上都能夠申請固定IP，以Hinet ADSL為例，通常一組ADSL帳號同時可以使用8個浮動IP或是一個固定IP加上七個浮動IP，也就是說，可以有八台電腦使用同一個帳號，各自使用PPPoE撥號連線上網路。

<!-- more -->

# 向ISP申請固定IP

中華電信有提供這個服務，詳情請去[網站](http://service.hinet.net/2004/adslstaticip.php)看看，填一下資料，馬上就會給你一組固定IP，但要注意，這組IP有可能因為維修或其他原因暫時停止或變換，他都會用email通知。

# 由數據機使用固定IP自動連線

首先把電腦設定改成：
* IP - **192.168.1.2**
* 子網路遮罩 - **255.255.255.0**
* 預設閘道 - **192.168.1.1**
* 慣用DNS伺服器位址 - **192.168.1.1** (或google的**8.8.8.8**)

將電腦接上數據機，在瀏覽器打入**192.168.1.1**，會彈出帳號密碼視窗，輸入後即可進入D-link畫面。

* 光世代數據機：帳號**cht**密碼**chtnvdsl**(北區)、**chtcvdsl**(中區)、**chtsvdsl**(南區)
* ADSL數據機：帳號**cht**密碼**chtnadsl**(北區)、**chtcadsl**(中區)、**chtsadsl**(南區)

上方選單點選**Setup→WAN Setup→Add→PPPoE**，下方輸入上網的帳號密碼(Hinet給的帳號卡)，要特別注意的是，如果你想使用浮動IP連線的話，直接照著卡上輸入即可，但是，如果你想使用剛剛申請的固定IP來連線，就需要把後方的**hinet.net**改成**ip.hinet.net**。

# 其他設定

進入**LAN Setup**啟用**DHCP**，建議**Start IP Address**要預留幾個，例如設定從**192.168.1.100**開始。

WHY？例如你目前使用的這台電腦，IP已經設定為192.168.1.2，其實就可以放著不用再改回自動取得，這樣以後這台電腦在區網的IP就是這個，在區網內的其他電腦想遠端這台電腦也不用每次都去查IP。但是啟用DHCP的話，數據機會自動給那些沒有設定的電腦一組IP，你如果沒有把192.168.1.2先預留下來，可能就會被衝到。

如果有WiFi AP還要再進入你WiFi AP的設定畫面，選擇**Setup→WAN Setup**，把原本用PPPoE連線改成固定IP(Static IP)連線，否則WiFi AP會再撥號一次，那就不會在同一個區域內了。