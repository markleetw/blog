---
title: CentOS 7初始化設定
date: 2016-06-23 12:24:52
categories:
- Technique
- Linux
tags:
- centos
---

{% asset_img tak-matsumoto.jpg Tak Matsumoto %}

CentOS 7安裝方式很簡單，所以就不再贅述，如果想要教學指南可參考[CentOS 7安裝](http://blog.xuite.net/tolarku/blog/231550320-CentOS+7+%E5%AE%89%E8%A3%9D)，本篇只介紹安裝完後設定的指令。

<!-- more -->

# 帳號及密碼

### 變更root密碼：

如果你在圖形化介面安裝時CentOS時已經有設定root密碼，其實可以跳過這個步驟，但是如果你的root密碼是使用預設，請務必、一定要在登入的第一時間修改密碼。

```bash 修改root密碼
passwd
```

### 新增使用者帳號：

因為安全性考量，在玩弄CentOS時非常不建議使用root帳號，非常容易出人命，所以新建一個帳號來玩耍，如果有需要root權限，再使用sudo就好了。

```bash 新增使用者(user01)
adduser user01
```

```bash 設定密碼
passwd user01
```

# 安裝及設定套件

### 檢查yum是否安裝

yum套件非常強大，專司package的安裝、更新、移除等工作。

```shell 檢查 yum 是否已安裝
rpm -qa | grep yum
```

```bash 如果有類似以下的內容表示已有安裝yum
yum-metadata-parser-1.1.4-10.el7.x86_64
yum-cron-3.4.3-118.el7.centos.noarch
yum-3.4.3-118.el7.centos.noarch
yum-plugin-fastestmirror-1.1.31-25.el7_0.noarch
```

### 安裝sudo

權限管理套件，能暫時得到root權限。

```bash 檢查是否已安裝
yum list installed | grep sudo
```

```bash 安裝sudo
yum install sudo
```

```bash 修改sudo設定
vi /etc/sudoers
```

在`root ALL=(ALL) ALL`下面複製一樣的內容，並把`root`改為帳號，例如

```bash
user01 ALL=(ALL) ALL
```

或是希望使用sudo時免輸入密碼

```bash
user01 ALL=(ALL) NOPASSWD:ALL
```

*以下所有動作都應該使用sudo或是先切換成root身分操作喔！*

### 禁止root使用ssh登入

避免有心人士得到root密碼後進去搞破壞，所以乾脆不要打開root的ssh。

```bash 修改ssh設定
vi /etc/ssh/sshd_config
```

找到`#PermitRootLogin yes`取消註解，並把`yes`改為`no`。

### 變更ssh port

ssh的port預設為**22**，很常成為攻擊的目標，避免不必要的麻煩可以把它改掉。

```bash 修改 ssh 設定
vi /etc/ssh/sshd_config
```

找到`#Port 22`取消註解並改成你想要的阜號(1024 – 65535，為避免衝突請先參考[這裡](https://en.wikipedia.org/wiki/List_of_TCP_and_UDP_port_numbers))

```bash 重啟 ssh
service sshd restart
```

### 安裝及設定 firewalld

firewalld是CentOS 7預設的防火牆使用者介面，安裝後可以查看防火牆狀態，雖然介面是firewalld，底層仍然是iptables。

```bash 使用 yum 來安裝
yum install firewalld
```

```bash 修改設定
vi /etc/firewalld/services/ssh.xml
```

讓新的ssh port可以通過防火牆，把原本的`port="22″`改為你設定的阜號

```bash 重載
firewall-cmd --reload
```

### 安裝及設定 fail2ban

有些無聊人士會使用暴力破解法去猜登入密碼，為了避免這種問題，所以使用fail2ban來讓短時間登入失敗次數過多的ip被封鎖。

```bash 用yum安裝套件
yum install fail2ban
```

```bash 如果失敗，可能因為套件不在套件褲內，先執行以下指令，再用yum安裝
rpm -Uvh http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
```

```bash 修改log設定
vi /etc/fail2ban/fail2ban.conf
```

把`#logtarget = SYSLOG`取消註解並更改路徑為`/var/log/fail2ban.log`

```bash 設定封鎖規則
cp /etc/fail2ban/jail.conf  /etc/fail2ban/jail.local
vi /etc/fail2ban/jail.local
```

找到`[sshd]`，在他前面新增規則如下

```bash 登入失敗連續3+1次，封鎖43200秒
[ssh-iptables]
enabled = true
filter = sshd
logpath = /var/log/secure
maxretry = 3
bantime = 43200
```

```bash 在任何提示模式自動啟動服務
chkconfig fail2ban on
```

```bash 啟動服務
service fail2ban start
```

```bash 如果沒有作用請執行
systemctl restart fail2ban.service
```

```bash 檢查 ssh 的封鎖狀態
fail2ban-client status sshd
```

### 誤鎖救援方式

```bash 查看規則，應該可以找到名為f2b-ssh-iptables的chain
iptables -L
```

```bash 查看被f2b-ssh-iptables規則所封鎖的IP
iptables f2b-ssh-iptables -L
```

```bash 解除封鎖，結尾的N表示第幾個IP
iptables -D f2b-ssh-iptables N
```