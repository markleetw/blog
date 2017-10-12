---
title: Shebang：*nix 的使用預設程式執行
date: 2017-10-13 00:36:09
categories:
- Technique
- Linux
tags:
- python
---

{% asset_img tosin-abasi.jpg Tosin Abasi %}

在 Windows 的世界，我們可以針對不同的檔案類型選擇預設的執行程式，例如 CSV 檔可以設定自動使用 Microsoft Office Excel 開啟，一旦設定好，只需要對著檔案連點兩下就會自動打開 Excel 讀取檔案。

在[類 Unix 系統](https://zh.wikipedia.org/wiki/%E7%B1%BBUnix%E7%B3%BB%E7%BB%9F)裡（或稱 *nix，包含 Linux、macOS 等等）其實也有類似的功能，稱為 [Shebang](https://zh.wikipedia.org/wiki/Shebang)（或稱 Hashbang），不同的是，Shebang 是針對各個檔案來設定，而非特定檔案類型。

<!-- more -->

Shebang 通常寫在檔案的第一行，以 `#!` 為開頭，加上希望用來開啟此檔案的指令（絕對路徑）。

> Unix 術語中，井號通常稱為 sharp、hash 或 mesh，驚嘆號則為 bang，Shebang 這個名稱也是由此而來。

舉例來說，假設你在 Python 檔案的第一行加上這樣的 Shebang：

```python
#!/usr/bin/python
```

此時在命令行輸入 `./test.py` 會等價於 `/usr/bin/python test.py`。

當然，如果你有特別想要指定的 Python 直譯器或是其他用來開啟檔案的指令，可以將 Shebang 的路徑改成：

- 用指定的 Python 直譯器執行

```python
#!/where/is/your/python
```

- 用 [vi](https://zh.wikipedia.org/zh-tw/Vi) 編輯器開啟

```python
#!/where/is/your/vi
```

- 一般來說，最通用的 Shebang 為（以 `python` 為例）

```python
#!/usr/bin/env python
```

> Shebang 僅會在直接開啟檔案時作用，有指名開啟程式的情況下是沒有用的，例如 `python test.py` 或 `vi test.py`。

### 為什麼是 /usr/bin/env ？

由於每個人的安裝習慣不同、不同平台的慣例也不同，就像有的人喜歡把軟體裝在 C 槽、把影片放在 D 槽，甚至有的人還會根據類型分門別類地放在不同資料夾底下，我們很難保證 Python 會被安裝在某個固定的位置，如果我們在 Shebang 內將路徑寫死，那會變得很沒彈性。

`env` 是一套環境變數相關工具（絕對位置是 `/usr/bin/env`），在 CLI 僅輸入 `env` 時會列出當下所有環境變數，而輸入 `env command` 時會執行在 `PATH` 內找到的**第一個** `command` 。

也就是說，在 Shebang 設定為 `#!/usr/bin/env python` 的情況下，無論使用者將 Python 安裝在什麼地方，都會預設使用 `PATH` 內第一個 `python` 作為執行指令。

但是這樣做真的好嗎？

確實，這個做法可以很輕易地做到跨平台、使用者，不需要去理會各種 Python 安裝路徑，但是缺點也是顯而易見的：首先，我們無法保證 `PATH` 內的第一個 `python` 指令就是該使用者慣用或期望使用的指令，或許他常用的 `python` 直譯器是在 `PATH` 內的第二或是第三順位，甚至是根本不在 `PATH` 內；其次，使用 `env python` 就無法對 Python 直譯器下參數，不過一般來說，這問題對於 Python 這個語言影響並不大。

其實 Shebang 並非必要的，僅是一個方便的選擇，知道以上的優缺點後，要不要加 Shebang？加什麼 Shebang？就要由開發者自行斟酌囉！
