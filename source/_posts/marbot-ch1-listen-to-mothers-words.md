---
title: MarBot Ch. 1 - Listen To Mother's Words
date: 2017-12-09 22:10:45
categories:
- MarBot
tags:
- chatbot
- linebot
- heroku
- python
---

{% asset_img orianthi-panagaris.jpg Orianthi Panagaris %}

你是否有在通訊軟體 LINE 上被長輩騷擾的經驗呢？

隨著智慧型手機的普及，長輩們使用通訊軟體、社群網站也越來越常見，但是隨之而來的各種假新聞分享、早安圖、午安圖、晚安圖、宵夜圖、感恩圖以及已讀不回躁鬱症，是否讓你深感困擾？

接下來一系列的文章，你將學會如何自製一套媽寶機器人，讓你能夠輕鬆地開發出強勁的功能，如極速秒讀、智能敷衍回訊、定時問候語（問候圖），促使家庭氛圍和樂，解決以上提到的所有煩惱，更重要的是，不需要花到你一毛錢！（如果有很多個媽媽則另當別論）

*媽寶的原名是 MarBot，也就是 Mark's Bot 的簡寫啦！*

**這一章節將介紹如何建立自己的機器人，並讓它成為長輩的應聲蟲。**

> 請注意！因為篇幅有限，此系列無法針對每一項技術做詳盡的說明，僅能介紹開發過程中必備的知識。

<!-- more -->

---

> 先來首 BGM 吧！
> {% youtube 9CRaXkB1EaE %}

## 在開始之前，必須要做一些事前準備：

1. 註冊 LINE@ 帳號
1. 建立開發和部署環境
    - Python
    - Heroku & Git

---

## 註冊 LINE＠ 帳號

其實 LINE 機器人的基本概念並不複雜，簡而言之就是使用 LINE@ 帳號所提供的 Message API 來做到自動接受和發送訊息的功能。當 LINE@ 帳號接收到好友傳來的訊息，Message API 便會將訊息傳送至你所設定的網路服務（機器人）位址，而機器人就可以針對訊息的內容，來決定該如何回覆。

LINE@ 就像是粉絲團、公眾號，你能夠透過 LINE@ 帳號與使用者交談、主動發送訊息，以誘騙年長者加好友送貼圖為最常見的應用。而 LINE@ 帳號的申請幾乎可以說是沒有門檻，只要你有 LINE 帳號就行了。

> LINE@ 帳號跟一般的 LINE 帳號是完全分開的，但因為 LINE@ 帳號並不需要設定密碼，而是透過 LINE 帳號作為管理員進行登入，所以在此才使用自己的 LINE 帳號來進行申請。
>
> 如果你還想知道更多關於 LINE@ 的資訊，請前往[官方網站](http://at.line.me/tw/)。

1. 首先進入 [LINE@ 註冊頁面](https://entry-at.line.me/) 點選**開始使用一般帳號**
1. 登入你個人的 LINE 帳號
1. 跟著步驟填入所需的資料

    > - 請不用擔心，本文所介紹的 LINE 機器人製作方式是完全免費的（**Developer Trial**或**免費版**），當然你可以隨時根據自己的需求更改為付費方案，但是基本上，進階的付費功能主要是提供給商業用戶使用，你可以查看[LINE@方案介紹](http://at.line.me/tw/plan)了解更多詳情。
    >
    > - 因為帳號申請的過程全都有中文說明，照理說應不會遇到困難，但若是需要更詳盡的申請教學，可以參閱 [LINE 官方部落格的教學文章](http://at-blog.line.me/tw/archives/58236627.html)。

1. 申請完 LINE@ 帳號後，便可以從[官網](https://entry-at.line.me/)點選右上方的**管理後台**，使用私人 LINE 帳號登入。

    {% asset_img line@-panel.png LINE@ 管理後台 %}

1. 進入 LINE@ 後台管理介面後，可以在下方的**帳號一覽**中，看到剛剛所申請的 LINE@ 帳號，點選該帳號後，就可以開始對它進行設定。

    {% asset_img line@-dashboard.png LINE@ 帳號一覽 %}

1. 為了取信長輩，必須讓 LINE@ 帳號看起來像是個普通私人帳號，所以請先至「帳號管理 > 基本設定」修改帳號名稱、顯示圖片、狀態等等，讓 LINE@ 帳號看起來像是你的私人帳號。

    {% asset_img line@-general-settings.png 基本設定 %}

至此，LINE@ 的設定暫時告一段落。

---

## 建立開發和部署環境

### Python

本系列介紹的機器人開發，是使用 Python 作為主要開發語言，因本篇的主旨並非 Python 教學，所以我只會快速帶過，不再做詳盡的解說，以介紹步驟為主。

- **Windows**

    首先前往 [Python 官網](https://www.python.org/downloads/windows/) ，根據你目前使用的 OS，選擇 **Windows x86 executable installer**（32bit） 或 **Windows x86-64 executable installer**（64bit） 進行下載和安裝。**請特別注意**，在安裝時請勾選 **Add Python to PATH** 這個選項。

    *進階：可以使用 MIT 的 Python 版本管理工具 [SNAFU](https://gist.github.com/uranusjr/91969cb2826d11d8e9e8f636b9ac1b97)。*

- **macOS**

    在 macOS 上，我會建議使用 [Homebrew](https://brew.sh/index_zh-tw.html) 來安裝 Python，可以方便、快速的安裝、升級、刪除任何你所需要的套件、工具。

    官方網站已有詳細的[中文教學](https://brew.sh/index_zh-tw.html)，根據教學步驟安裝完之後，請在終端機輸入

    `brew install python3`

    *進階：可以使用 Python 管理工具 [pyenv](https://github.com/pyenv/pyenv) 或 [pipenv](https://github.com/pypa/pipenv)。*

- **Linux**

    Linux 的用戶應該不需要我教如何安裝 Python 了（懶）。

> 環境建置是一個非常枯燥、佔用篇幅且意外狀況很多的部分，所以我不打算教得太深入，以能夠解決大多數狀況為主，所以如果你是 Python 新手，在沒有人指導或是資訊基礎的情況下，不建議直接使用**進階**的工具，很容易因為不熟悉而產生錯誤引起挫敗感。如果在過程中有遇到任何問題，歡迎在下方留言，我會盡力幫忙。

至此，Python 應該已經安裝完成了，為了驗證是否可以使用 Python，請打開**終端機**或**命令提示字元**，輸入

`python3 --version`

如果有回覆

`Python 3.6.3`

恭喜你，可以進行下一步了。

> Windows 用戶請注意，安裝任何軟體（包含 Python 和後面會介紹的所有工具），安裝完後都必須要重新開啟命令提示字元，指令才能正常生效喔！

### Heroku & Git

Heroku 是一套 PaaS 雲端服務平台，讓使用者可以快速部署服務（程式）到雲端上，除了簡單易用之外，更重要的是，它所提供的免費方案完全能夠滿足媽寶機器人的需求。

1. 前往 [Heroku 的官網](https://signup.heroku.com/)註冊帳號，註冊方式非常簡單，只需要填寫基本資料就可以了，完全不需要填寫信用卡等財務相關資訊。

    {% asset_img heroku-register.png Heroku Sign up %}

1. 送出註冊表格後，應會收到系統發出的認證信，認證後便完成了帳號註冊。

1. 接下來就可以建立一套屬於自己的服務。首先前往 [Heroku](https://dashboard.heroku.com/apps) 登入剛剛所申請的帳號，在首次登入時，因為我們未曾建立過任何服務，所以會看到以下的畫面。

    {% asset_img heroku-onboarding.png Heroku Onboarding %}

1. 點選 **Create New App**，並依照提示輸入 App name 和選擇 US 後點選 **Create app** 送出。

    {% asset_img heroku-create-new-app.png Create New App %}

1. 就這麼簡單，我們已經建立好一個空服務了！網頁會自動將你導向簡易的說明頁面，建議你可以快速地閱讀它，我知道你可能會看得一頭霧水，別擔心，繼續跟著後面的步驟做吧！

    {% asset_img heroku-deploy-page.png Heroku Deploy Page %}

1. 接下來要安裝 Heroku Cli，它是一套 Heroku 管理工具，簡單來說是可以讓你可以下指令去命令電腦把服務（機器人）部署上 Heroku、查看服務狀態等等。

    根據系統不同而有不同的安裝方式，詳情請參閱[官方說明](https://devcenter.heroku.com/articles/heroku-cli)。基本上類似於 Python 安裝方式，Ｗindows [在此選擇](https://devcenter.heroku.com/articles/heroku-cli#windows) 32bit 或 64bit 安裝檔下載安裝。而 macOS 則是[建議](https://devcenter.heroku.com/articles/heroku-cli#macos)使用 Homebrew 來進行安裝，指令如下：

    `brew install heroku/brew/heroku`

1. 安裝完 Heroku Cli 之後，便可以登入剛剛申請的帳號，只要在**終端機**或**命令提示字元**輸入 `heroku login` 並根據提示輸入帳號密碼即可。

1. 最後，需要安裝開發者必備的版本控管工具 [Git](https://git-scm.com/)。在此安裝 Git 的目的並非只是為了版本控管，而是 Heroku Cli 在部分操作上需要與 Git 配合，後面會再解說。

    安裝的步驟就不再贅述，Windows 一樣前往[頁面](https://git-scm.com/download/win)選擇對應的版本來下載安裝(如果看不懂可以先按 Next 到底）；而 macOS 用戶則使用 Homebrew（知道它有多方便了吧！），指令我想你應該也猜得出來了。

    `brew install git`

恭喜你，環境建置終於結束啦！

---

## 正式進入開發

> 將以下指令內的 `your-app-name` 改為 Heroku 上的 App name

1. 首先找個風水寶地將我們先前建立的 Heroku 服務下載下來

    - **Windows**

        ```
        C:\Users\mark> D:
        D:\> heroku git:clone --app your-app-name
        ```

    - **macOS / Linux**
 
        ```
        ~$ cd ~/Documents
        ~/Document$ heroku git:clone --app you-app-name
        ```

1. 執行完後，此時你的目錄下會多出一個 `your-app-name` 的資料夾

1. 在資料夾下建立四個檔案，分別是

    - runtime.txt
    - requirements.txt
    - Procfile
    - your-app-name.py

1. 此時你的檔案結構應該會**類似**這樣

    ```
    D:\marbot-demo （或 ~/Documents/marbot-demo）
        - runtime.txt
        - requirements.txt
        - marbot-demo.py
        - Procfile
    ```

1. 編輯 **runtime.txt**，使其內容如下：

    ```
    python-3.6.3
    ```

  這邊應該不難理解，就是讓 Heroku 知道你的程式是用 Python 3.6.3 版所撰寫，Heroku 會在部署時將 Python 環境準備好。

1. 編輯 **requiremenst.txt**，使其內容如下：

    ```
    Flask==0.12
    gunicorn==19.6.0
    line-bot-sdk==1.5.0
    ```

  這邊是列出機器人所需要的三個套件，前兩個建立 Python 網路服務的基本套件，Flask 是一套輕量級的 WEB 框架，而 gunicorn 則是 Python WSGI HTTP Server，第三項則是 LINE 官方所提供的 Python 開發套件（看不懂沒關係，不影響開發，未來有機會再介紹）。

1. 編輯 **Procfile**，使其內容如下：

    ```
    web gunicorn your-app-name:app
    ```

    這邊是告訴 Heroku，你建立的網路服務是跑在 gunicorn Server 上，且主程式為 `your-app-name`。請注意，必須要跟 **your-app-name.py** 同檔名。

1. 最後，就是我們的主角**your-app-name.py**啦！

    因為篇幅較長，我將簡單的說明寫在註解內，如果看不懂也可以直接跳過閱讀，先讓機器人上線再說。

    {% gistit marksylee marbot-demo blob/ch1-listen-to-mothers-words/marbot-demo.py %}

> 如果對於這四支檔案還有任何疑惑，請至[這裡](https://github.com/marksylee/marbot-demo/tree/ch1-listen-to-mothers-words)觀看實際範例，或是在下方留言詢問。

---

## 部署 Heroku 以及串接 Message API

1. 首先，請回到 [LINE@ 後台管理頁面](https://admin-official.line.me)並點選 LINE@ 帳號，進入「帳號設定 > Message API設定」。由於你的 LINE@ 帳號尚未開通 Message API，請先按照網頁指示啟用後，點擊進入最下方的 **LINE Developers**。

    {% asset_img line@-message-api-settings.png Message API 設定 %}

1. 進入 LINE Developers，會看到一系列的 Channel Settings 設定。請依照指示填入資料，但其中有幾個部分特別重要：

    {% asset_img line@-channel-settings.png Channel Settings %}
  
    - **Plan**
        有 Developer Trial 和 Free 可供選擇，它們之間的差異在網頁上已有說明。Developer Trial 可以主動發送訊息給使用者，但最多只能擁有 50 位好友；Free 方案可以有無限多的好友，但無法主動發送訊息給使用者（換句話說：只能回嘴，不能主動問候）。如果僅是自娛娛人，建議使用 Developer Trial 可以享受到比較豐富的功能。

    - **Channel secret**
        這是機器人是否能**接收並解讀** Message API 資料的關鍵，請將它妥善保存並將它取代 **your-app-name.py** 內的 `LINEBOT_CHANNEL_SECRET`。

    - **Channel access token**
        這是機器人用來**發送、回覆訊息**的密鑰，請按下右方的 **ISSUE** 申請一組新密鑰，並選擇是否讓它定時過期（過期就必須重新申請），取得後請妥善保存並將它取代 **your-app-name.py** 內的 `LINEBOT_CHANNEL_ACCESS_TOKEN`。

    - **Use webhooks**
        當 LINE@ 收到訊息時，是否將訊息傳送至指定服務（機器人），請修改設定為 **Enabled**。

    - **Webhook URL**
        此設定與上一個相關，當 **Use webhooks** 啟動時，Message API 會將訊息傳送至 **Webhook URL**，請填入
        `https://your-app-name.herokuapp.com/callback`
        （切記將 your-app-name 改為你在 Heroku 所設定的 App name）

    - **Allow bot to join group chats**
        媽寶機器人是否可以被加入群組聊天，請依需求自行決定是否要啟動。

    - **Auto-reply messages** 和 **Greeting messages**
        兩者皆設定為 **Disabled**。

    - **QR code**
        顧名思義就是 LINE@ 的 QR code，掃描後就可以加入好友。

1. 修改完資料後，請先別急著關閉頁面。回到終端機或命令提示字元，將路徑移動至專案路徑下

    - Windows
  
        ```
        C:\Users\mark> D:
        D:\> cd your-app-name
        ```

    - macOS / Linux

        ```
        ~$ cd ~/Documents/your-app-name
        ```

1. 依序執行以下指令，將機器人部署至 Heroku

    ```
    git add .
    git commit -m 'first commit'
    git push heroku master:master
    ```

1. 等待 Heroku 部署完後，回到剛剛的 LINE Developers 頁面，按下 **Webhook URL** 旁的 **Verify**。如果機器人有部署成功，應會出現 **Success** 字樣。

1. 將 LINE@ QR code 分享給他人（也可以自己使用），並開始享受清靜的時光吧！

{% asset_img marbot-demo.png DEMO %}

### 真是...真是太棒了...

---

> - 第一次寫如此長篇的教學，可能有所遺漏，請不吝指教！
>
> - 應該沒有人會真的拿這個機器人去唬弄長輩吧...
