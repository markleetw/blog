---
title: Apache mod_proxy (Forward/Reverse)
date: 2016-06-24 11:43:11
categories:
- Technique
- Linux
tags:
- apache
redirect: https://blog.marklee.tw/apache-proxy-forward-reverse
---

{% asset_img eric-clapton.jpg Eric Clapton %}

為了物盡其用，我們總是在一台機器上架設很多網路服務，而這些服務通常都四散在各個Port上，例如在 80上有Apache HTTPd、Tomcat占用著8080、RStudio Server又裝在8787上，用起來十分不方便。

<!-- more -->

# 問題

**如何將不同Port上的服務整合到同一個Port上？**

舉例來說，我在CentOS 7上安裝了一套RStudio Server，這是一款網頁式的R語言IDE，該服務預設的Port是8787，而我的Port 80上也已經在跑WordPress等服務，但是我希望能夠在不改Port的情況下用URL來區別兩個服務，像是用`http://marksylee.com/rstudio`可以連到RStuidio Server，而不會影響到本來就正常運行的`http://marksylee.com/wordpress`。

# 基礎知識

容我發懶一下，TonyQ大大已經很好心地把文件翻譯出來，請看[[心得] apache mod_proxy介紹](https://www.ptt.cc/bbs/Web_Design/M.1226750420.A.3EB.html)。

# 解決方法

基本上，CentOS 7的Apache已經內建安裝了mod_proxy，可以在`/etc/httpd/conf/httpd.conf`或`/etc/httpd/conf.modules.d/` 內找到，如果沒有，那可能需要安裝或啟用，請自行估狗。

首先，執行以下指令找找看是否有`<IfModule mod_proxy.c>`的標籤，如果有，請把標籤的內容反註解掉。

```bash
vi /etc/httpd/conf/httpd.conf
```

如果沒有相關的內容，請在文件結尾加入以下內容：

```
<IfModule mod_proxy.c>
 
    <VirtualHost *:80>
 
        <Proxy *>
            Allow from localhost
        </Proxy>
 
        ProxyPass /rstudio/ http://localhost:8787/
        ProxyPassReverse /rstudio/ http://localhost:8787/
        RedirectMatch permanent ^/rstudio$ /rstudio/
 
    </VirtualHost>
 
</IfModule>
```

```bash 完成後重啟 Apache
systemctl restart httpd.service
```

然後連進網址`http://ip-address/rstudio`看看有沒有通，如果出現`503 Error (Service Unavailable)`請不要驚慌，可能是SELinux在搞鬼，可參考[Apache ProxyPass 出现503 Service Temporarily Unavailable 的解决方案](http://babyhe.blog.51cto.com/1104064/636602/)。