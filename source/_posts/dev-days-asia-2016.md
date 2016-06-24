---
title: 2016 亞太開發人員技術年會‬
date: 2016-06-24 17:51:41
categories:
- Technique
- Misc
tags:

---

{% asset_img steve-vai.jpg Steve Vai %}

大部分都在展示產品功能(行銷研討會的味道)，會議主要分為兩個部分Office和Microsoft Azure。

[活動網址](https://www.microsoft.com/taiwan/events/devdays/)

<!-- more -->

# Office

### Office Add-ins

[Office Add-ins Dev Doc](https://dev.office.com/docs/add-ins/overview/office-add-ins)

支援使用JavaScript開發Office Add-ins，降低開發門檻和提升開發效率，提供API(但沒有大概介紹有哪些，主要還是要去看doc) 滿足使用者需求等等，然後Demo幾個範例，例如Word Add-in可以讓使用者直接用Word抓取外部資料和樣板，用拖拉的方式去完成一份文件；或是用Excel Add-in把Excel做得像是記帳軟體等等。

### Office 365 RESTful API

Office 365提供RESTful API讓開發者可以使用各種語言/應用去結合Office 365，這其中順便幫Office 365新的服務Connector打廣告，Connector想要做的是一個訊息集散的應用，可以整合GitHub、Trello、Twitter等等，作為企業內部溝通的平台，有一點Slack的感覺。

因為Office 365提倡的是雲端協同辦公室的概念，一切都交給微軟雲端解決，所以整合就是個很大的賣點，微軟將Office 365的所有應用服務API整合在單一窗口，稱為Microsoft Graph，透過Microsoft Graph提供的API可以對Office 365所有應用進行操作，至於操作可以到什麼程度並沒有說明，Demo主要都是針對獲取資料的部分，例如取得文件作者、時間、權限資料，拿資料還做視覺化處理或是產報表等等。

# Microsoft Azure

### Cortana and other tools

一開始稍微帶過Cortana以及背後的Machine Learning，展示了一些和Machine Learning相關的工具以及API，現場展示的有臉部辨識的應用，例如從相片或即時影像中找出人臉，並辨識年齡/性別/情緒等等，準確度滿高的，這些都有API可以使用，詳情可參考官網。

### Microsoft Azure VM/Database/DataWarehouse/DataLake and Power BI

感覺得出微軟非常想推Azure和Power BI，光是Demo如何在Azure上建Data Lake和用Power BI取出資料分析並建立報表就花了不少時間(跟其他比起來)，中間一度花很多時間在看講者操作Power BI拖拉產生各種報表展示有多方便、強大、產出來的圖片有多漂亮。

### Microsoft Azure Machine Learning

展示如何使用拖拉功能就能設計好演算法，甚至有不少經典的模型已經在裡面讓你直接套用，你要做的就是依你的需求做調整，並餵training data和testing data進去，model完成之後，未來想使用就直接call這個model的API就行了。當然，提供許多module，擁有彈性和易於開發，更可以跟Office Add-ins做結合。



大致上是這樣，當然實際上會有更細的解說、更多的展示和功能操作。

~~聽其他與會者有在 murmur 這些東西去年就講過了。~~