---
title: 筆記型電腦 Windows Ubuntu 雙系統
date: 2016-06-24 17:13:59
categories:
- Technique
- Linux
tags:
- ubuntu
---

{% asset_img jimmy-page.jpg Jimmy Page %}

明很簡單的東西，我硬要給人繞一大圈，真夠蠢的…

<!-- more -->

# 這是昨晚的流程

Windows Reset → D槽切一半 → 裝 Ubuntu → 裝 Grub 2 開機選單 → 但是選單的第一項是 Ubuntu，想改為 Windows → 修改 BIOS 的 boot option → 發現 boot option #1 如果是 Windows Boot Maganer 就不會跳出選單會直接進 Windows → 把 boot option #1 設為 Linux → 進 Ubuntu 用 boot-repair 重建 Grub 選單 → Windows 的 MBR (在C槽) 和 Ubuntu (在D槽) 合併，並存到 D 槽 → boot option #1 改回 Windows Boot Manager 依舊無法解決系統直接進入 Windows 問題，而且產生很多詭異的檔案 → 砍掉 boot-repair 以及產生的檔案 → 無法解決 → re-intall Ubuntu → 乾淨的 Ubuntu → boot option #1 設為 Linux → 開機選單 Windows 8.1 不見了 → Grub 重新設定並更新開機選單 → 選單出現 Windows → 進不去 → format Ubuntu → 電腦找不到任何 OS，直接進空蕩蕩的 grub command line → Windows 8 沒有 Recovery CD or Repair Disc → 使用 Ubuntu Live CD 找 tool 救回跟著 Ubuntu 一起被 format 的 MBR → 失敗 → 找另一台電腦燒修復磁碟 → 用修復磁碟 Reset Windows → 重開機仍舊直接進 grub command line → 使用修復磁碟進入 CMD rebuild MBR → 重開機仍舊直接進 grub command line → 進 BIOS 把開機選項第一個改為 Windows Boot Manager → Windows 啟動成功 → 進入乾淨的 Windows → 天亮了

# 發生什麼事？

* Windows Boot Manager 是 Windows 自帶的開機選單，有時候開機可以選安全模式或是其他版本的 Windows，那就是 Windows Boot Manager，如果 boot option #1 設定 Windows Boot Manager，他就會先去讀這套選單系統，因為 Ubuntu 是後裝的，所以 Windows Boot Manager 根本不知道有 Ubuntu (Windows 對其他 OS 不太友善，就算是先裝 Ubuntu 再裝 Windows，一樣會產生很多問題)，導致不會有選單就直接進去 Windows。
* BIOS 的 Boot Option #1 如果設定 Linux，就會使用 Grub 或 Syslinux 開機選單系統，因為這套選單系統是安裝 Ubuntu 時裝的，所以他知道此電腦內有 Windows，自然就可以挑選 Windows or Ubuntu 登入。
* 想要控制選單的內容並不是去修改 boot option，那是讓你挑要用哪一套開機選單，如果想要改 Grub 選單的選項順序、等待時間或是預設選項，要去修改 Grub 的 config 檔。
* 為什麼 Windows 回復原廠狀態及 rebuild MBR 都失敗？因為 boot option #1 是 Linux，他跑去讀 Grub 當然空空如也 (因為re-intall + format Ubuntu 導致 Grub 已經不知道變什麼鬼樣子)，當時如果把 boot option #1 改成 Windows Boot Manager 應該可以成功。
 

# 結論

切割磁區 → 光碟開機 → 安裝 Ubuntu → 進 BIOS 把 Boot Option #1 改成 Linux → 進 Ubuntu 調整 /boot/grub/grub.cfg 內的 OS 順序 → 結束

**如果做這種系統面的大變動，一定要先備份、建立還原點或是產生修復磁碟，可以省下非常多的時間，建立修復磁碟 (Windows Repair Media) 的功能躲在 控制台 > 系統及安全性 > 重要訊息中心 > 復原 > 建立修復磁碟機**