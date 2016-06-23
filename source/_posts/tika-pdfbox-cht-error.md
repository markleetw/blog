---
title: Tika PDFBox 讀取繁體中文編碼錯誤
date: 2016-06-22 17:22:09
categories:
- Technique
- Java
tags:
- library
---

{% asset_img the-beatles-abbey-road.jpg The Beatles - Abbey Road %}

最近遇到個詭異的問題，就是當Tika去解析PDF文件時，如果文件有使用特定編碼(ETenms-B5)，就會解析出亂碼，Tika解析PDF的部分是使用PDFBox，而我遇到的問題也正好是存在已久的PDFBox bug。

<!-- more -->

# 問題
PDFBox在解析檔案時，若該PDF含有ETenms-B5-H編碼，則會解析出亂碼。

# 基礎知識

PDF的預設字集是Adobe公司自行編碼的字集「CID字集」，不是大家通用的萬國碼Unicode，所以在讀取內容時，會先透過一個叫做CMap的檔案，對映到CID字集，以便在顯示的時取得正確的字形。

舉例來說，ETenms-B5-H的CMap，裡面描述的是怎麼把ETenms-B5-H的編碼對應到CID編碼，這樣PDF檔案如果包含ETenms-B5-H的編碼，開啟檔案時就會透過CMap得到CID編碼並秀出正確的字。

ETenms-B5-H、ETenms-B5-V是倚天輸入法以BIG5為基礎發展的擴充集(V為垂直書寫、H為水平)，基本上使用細明體、新細明體、標楷體等都有可能會用到它，所以要多注意，這個編碼方式是繁體中文專用，所以基本上只有台灣等地會用到。

# 解決方法

我將PDFBox的jar檔解壓縮後，檢查裡面的CMap，發現PDFBox內確實有ETenms-B5-H的CMap對應檔(否則執行過程中會報錯)，但是內容殘缺不全，所以我就拿在同位址下另一個倚天的編碼ETen-B5-H內容複製過去把內文中的ETen-B5-H改成ETenms-B5-H，重新封裝jar檔，就能夠順利解析了。

註：ETen-B5-H和ETenms-B5-H一樣，但ETenms-B5-H將半寬的拉丁字符替換成比例字符。