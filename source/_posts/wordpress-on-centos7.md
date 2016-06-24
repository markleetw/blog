---
title: CentOS 7架設WordPress
date: 2016-06-23 17:04:17
categories:
- Technique
- Linux
tags:
- centos
- wordpress
---

{% asset_img carlos-santana.jpg Carlos Santana %}

網上已有非常豐富的WordPress架設教學，本篇稍微不同的是用MariaDB取代原先的MySQL。

<!-- more -->

# MariaDB

```bash 安裝MariaDB
yum -y install mariadb-server mariadb
```

```bash 開啟MariaDB且設定開機啟動
systemctl start mariadb.service
systemctl enable mariadb.service
```

```bash MariaDB基本設定
mysql_secure_installation
```

```bash 登入MariaDB
mysql -u root -p
```
 
```sql 建立wp資料庫
CREATE DATABASE wp;
```
 
```sql 建立使用者wpuser
CREATE USER wpuser@localhost;
```
 
```sql 設定密碼
SET PASSWORD FOR wpuser@localhost= PASSWORD('密碼');  
```

```sql wpuser具有wp資料庫的完整權限
GRANT ALL PRIVILEGES ON wp.* TO wpuser@localhost IDENTIFIED BY 'password';
```

# Apache HTTPd

```bash 安裝Apache HTTPd
yum -y install httpd
```

```bash 開啟Apache且設為開機啟動
systemctl start httpd.service
systemctl enable httpd.service
```

```bash 防火牆打開http和https的port
firewall-cmd --permanent --zone=public --add-service=http
firewall-cmd --permanent --zone=public --add-service=https
firewall-cmd --reload
```

此時可以試試看`http://ip`，看到Apache預設畫面表示成功。

# PHP

```bash 安裝PHP
yum -y install php
```

```bash 重啟Apache
systemctl restart httpd.service
```

```bash 寫一支info來看看PHP的狀況
vi /var/www/html/info.php
```

```php 檔案內容如下
<?php
phpinfo();
?>
```

試試看`http://ip/info.php`，看到畫面表示成功，從頁面上可以看到，並未安裝MySQL模組。

```bash 安裝php的mysql模組(mysql模組跟mariadb相容)
yum -y install php-mysql
```

```bash 重啟Apache
systemctl restart httpd.service
```

# phpMyAdmin

```bash 安裝
yum install phpMyAdmin
```

```bash 編輯phpMyAdmin設定
vi /etc/httpd/conf.d/phpMyAdmin.conf
```

```bash 把設定改成以下
[...]
Alias /phpMyAdmin /usr/share/phpMyAdmin
Alias /phpmyadmin /usr/share/phpMyAdmin
 
#<Directory /usr/share/phpMyAdmin/>
#   <IfModule mod_authz_core.c>
#     # Apache 2.4
#     <RequireAny>
#       Require ip 127.0.0.1
#       Require ip ::1
#     </RequireAny>
#   </IfModule>
#   <IfModule !mod_authz_core.c>
#     # Apache 2.2
#     Order Deny,Allow
#     Deny from All
#     Allow from 127.0.0.1
#     Allow from ::1
#   </IfModule>
#</Directory>
 
 
<Directory /usr/share/phpMyAdmin/>
        Options none
        AllowOverride Limit
        Require all granted
</Directory>
 
[...]
```

```bash 修改phpMyAdmin認證方式
vi /etc/phpMyAdmin/config.inc.php
```

將原本的`$cfg['Servers'][$i]['auth_type']='cookie';`改成`$cfg['Servers'][$i]['auth_type']='http';`。

```bash 重啟Apache
systemctl restart  httpd.service
```

進入`http://ip/phpmyadmin`輸入帳號密碼，看到畫面表示成功，進入wp資料庫，把編碼改為`utf8_unicode_ci`。

# WordPress

```bash 下載及安裝
cd /var/www/html
wget https://tw.wordpress.org/wordpress-4.5.2-zh_TW.tar.gz
tar -xzvf wordpress-4.5.2-zh_TW.tar.gz
```

```bash 設定擁有者與權限並重啟Apache
chown -R apache:apache /var/www/html/WordPress
chmod -R 755 /var/www/html/WordPress
systemctl restart httpd.service
```

試試看`http://ip/WordPress`能否連入，如果可以就能開始設定你的WordPress了！

```bash 如果無法從網站寫入wp_config.php，可以手動寫入
cd /var/www/html/WordPress/
cp wp-config-sample.php wp-config.php
vi wp-config.php
```

```php 設定如下
// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define('DB_NAME', '資料庫名稱'); // wp
 
/** MySQL database username */
define('DB_USER', '資料庫登入帳號'); // wpuser
 
/** MySQL database password */
define('DB_PASSWORD', '資料庫登入密碼'); // 密碼
```

WordPress安裝完後，可以將`phpMyAdmin.conf`改回預設值，這樣非本機就不能登入phpMyAdmin，以提升安全性。

```bash 將WordPress資料夾給Apache，並賦予權限
chrown apache:apache /your/WordPress/folder/
find /your/WordPress/folder/ -type d -exec chmod 755 {}
find /your/WordPress/folder/ -type f -exec chmod 644 {}
```

若發生`not found 404`，可參考[Where to set AllowOverride All for .htacess?](http://forums.cpanel.net/f5/where-set-allowoverride-all-htacess-222882.html)，應該是Apache沒有打開override功能。

其他安全相關設定，可參考[9 個提升 WordPress 網站安全性的方法](http://free.com.tw/improve-wordpress-security/)跟[11 個強化 WordPress 網站安全的 .Htaccess 設定技巧](http://free.com.tw/wordpress-htaccess-tips-and-tricks/)。