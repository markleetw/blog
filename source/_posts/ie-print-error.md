---
title: IE列印異常
date: 2016-06-24 17:40:39
categories:
- Technique
- HTML
tags:
- ie
---

{% asset_img les-paul.jpg Les Paul %}

最近接到個比較陌生的問題，客戶想要直接透過瀏覽器的列印功能，去印下系統頁面，當內容頁數超過一頁以上時，在IE的預覽列印只能顯示第一頁(當然也只會印第一頁)，而其他瀏覽器則沒有這個問題。

<!-- more -->

# 問題

使用IE打開網頁時，若是網頁內容超過一頁以上，使用IE的列印功能，只能夠印出或預覽列印第一頁。

# 參考資料

如果內容長度超出一個頁面的標籤，其style有position: absolute時，就會出現這個狀況，所以可以算是IE對於CSS上面的bug。

來源：[Internet Explorer sometimes only prints the first page](http://serverfault.com/questions/6998/internet-explorer-sometimes-only-prints-the-first-page)

# 解決方法

我的做法是用其他position取代absolute，但是記得要 check 一下版型有沒有跑掉。

```html 原為
<style>
#content {
    position: absolute;
    /* 其他 css */
}
</style>
 
<div id="content">
    一狗票好幾頁的內容
</div>
```

```html 改為
<style>
#content {
    position: relative;
    /* 其他 css */
}
</style>

<div id="content">
    一狗票好幾頁的內容
</div>
```