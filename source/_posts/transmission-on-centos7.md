---
title: CentOS 7架設Transmission
date: 2016-06-24 15:12:07
categories:
- Technique
- Linux
tags:
- centos
---

{% asset_img jeff-beck.jpg Jeff Beck %}

希望能夠在任意地點經由網頁上傳torrent，Server會自動下載檔案。

<!-- more -->

```bash 安裝Transmission
yum install transmission transmission-daemon
```

```bash 啟動再關閉，系統會自行產生設定檔
systemctl start transmission-daemon
systemctl stop transmission-daemon
```

```bash 設為開機即啟動
systemctl enable transmission-daemon
```

```bash 修改設定檔
vi /var/lib/transmission/.config/transmission-daemon/settings.json
```

```bash 設定檔內修改登入資訊
"rpc-authentication-required": true,
"rpc-password": "密碼",
"rpc-username": "帳號",
```

使用Apache Proxy導向Transmission所在的port(可參考[Apache mod_proxy](http://blog.marksylee.com/2016/06/24/apache-proxy-forward-reverse/))。

```bash 修改Apache設定
vi /etc/httpd/conf/httpd.conf
```

```bash 在port 80的VirtualHost內加入以下內容
RewriteRule /transmission[/]?$ /transmission/web [R=permanent]
ProxyPass /transmission http://localhost:9091/transmission
ProxyPassReverse /transmission http://localhost:9091/transmission
```

```bash 重啟服務
systemctl start transmission-daemon
systemctl start httpd.service
```

連線至`http://localhost/transmission`即可登入使用。