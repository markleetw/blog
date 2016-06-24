---
title: Pidgin Plugin For Line
date: 2016-06-24 17:23:03
categories:
- Technique
- Linux
tags:
- ubuntu
---

{% asset_img tony-lommi.jpg Tony Lommi %}

Pidgin是一套通訊軟體，整合了Facebook、Google Talk、IRC、Line等多達20種的即時通訊。可惜的是，Pidgin預設並未提供Line登入，好在神人很多，已經有人開發Line的plugin，但是只有Source Code，所以想裝就得自己編譯！

<!-- more -->

```bash 使用root身分
sudo su
```
 
```bash 安裝必要軟體
apt-get update 
apt-get install -y pidgin git build-essential
apt-get install -y libpurple-dev libboost-dev libboost-test-dev libboost-program-options-dev libevent-dev automake libtool flex bison pkg-config g++ libssl-dev libgcrypt11-dev
```

```bash 安裝Apache thirft
cd /tmp
mkdir purpleline
cd purpleline
git clone https://github.com/apache/thrift thrift
cd thrift
git checkout 0.9.2
./bootstrap.sh
./configure
make
make install
cd /usr/local/lib
mkdir thrift
mv libthrift* thrift
ln -s /usr/local/lib/thrift/libthrift-0.9.2.so /usr/lib/libthrift.so
cd /tmp/purpleline
```

```bash 安裝 purple_line 
git clone http://altrepo.eu/git/purple-line.git/ purple-line 
git clone http://altrepo.eu/git/line-protocol.git/ line-protocol 
cp line-protocol/line_main.thrift purple-line/ 
cp line-protocol/line.thrift purple-line/ 
cd purple-line 
make
make install
ldconfig
```

重新登入後即可使用！