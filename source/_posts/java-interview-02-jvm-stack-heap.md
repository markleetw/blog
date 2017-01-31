---
title: Java 面試 02 - JVM 的 Stack 和 Heap
date: 2016-09-14 00:08:46
categories:
- Technique
- Java
tags:
- interview
- java_interview
---

{% asset_img stack-heap-example1.PNG %}

### 請比較 JVM 記憶體的 Stack 和 Heap
這題非常容易考到，而且翻譯十分混亂，盡量用原文去表達比較好，Stack 繁中為**堆疊**、簡中為**棧**，Heap 繁中為**堆積**、簡中為**堆**。

要特別注意的是，這邊的 Stack 和 Heap 並不是在指資料結構，而是指 **JVM 記憶體管理**的部分。

<!-- more -->

> **聲明：**
>
> 這些內容比較簡略、粗糙，忽略了很多細節，只揀選能回答到問題的部份去解說，並不是百分之百準確，除非每一題都有兩個小時以上的回答時間，否則這些內容應該足夠回答到面試官想聽的點。如果你真的很在乎每一個部份的細節內容，你不該讀我的筆記，而是去讀文件，自己寫自己的筆記。當然，我會隨著越講越深入而帶入更多細節的部份，但是我也需要考量文章篇幅以及流暢度。
>
> **若有任何錯誤或遺漏之處，請在底下用力鞭我，不用客氣，隱瞞缺失比直指痛處更讓人害怕。**

----

首先，Java 檔經過編譯後所產生的 Class 檔 (bytecode)，只能運行在 JVM 上，而 JVM 在運算時，如同一般電腦一樣，需要記憶體儲存運算所需的資料及指令，而 JVM 記憶體中的 **Stack** 和 **Heap** 最常被拿來做比較 (記憶體中當然還有其他部份，包括 Method Area、PC Register、Native Method Stack，但不在本篇討論範圍)。

**JVM 記憶體的 Stack 和 Heap 絕對不要和其他同名的東西搞混，在這裡是做為 JVM 儲存資料或指令的區域，和其他同名的東西無關，請把它當作平行宇宙看待。**

----

# Java 中的資料型態

分為兩種：

#### 1. Primitive Type 基本型態
* 共有 8 種：***int***、***short***、***long***、***byte***、***float***、***double***、***boolean***、***char***。
* 這種類型是通過如 `int a = 3; long b = 123L;`的方式宣告。
* 長度及生命週期都為可知 (程式碼區塊執行完就扔掉)。
* 運算速度快，但長度與內容受限。

#### 2. Class Type / Reference Type 類別型態 / 參考型態
* 其他大都屬於此類別，如 ***Integer***、***String***、***Long*** 以及自行定義的類別 (ex. ***User***) 等。
* 這種類型通常都需要用 ***new*** 去創建，如 `User user = new User("Mark");`。
* 因為是在執行時才動態創建，所以長度及生命週期都不可預知。
* 靈活但運算較耗時。

----

# Stack 堆疊、棧
複習一下 [**前一章**](http://blog.marksylee.com/2016/09/13/java-interview-01-program-process-thread/) 所提到的：**每一個 Thread 擁有自己的 Stack，Stack 是用來儲存函數路徑及區域變數，Thread 之間的 Stack 互相獨立。**

Stack 是一種**後進先出 (FILO)** 的容器，具有存取速度快和管理簡單的特點。由於 Java 中變數跟函式的生命週期都為**後進先出**，也就是越晚產生的會越先被回收或銷毀，所以 Stack 非常適合做為這種可預測性的資料，如 **區域變數、函式參數與函式返回位址**等等，的儲存容器。

因為 Stack 中的資料生命週期都是**規律**的，所以由系統自行去產生和回收空間即可，程式設計師不需要介入。

----

# Heap 堆積、堆
還記得 [**前一章**](http://blog.marksylee.com/2016/09/13/java-interview-01-program-process-thread/) 提到 **Process 的 Memory Space 是專門儲存共享資料/資源**的嗎？Heap 就屬於這種共享資料。

Class Type 在創建**實例 (Instance)** 時 (ex. `new User("Mark");`)，就是把資料放置於 Heap 中，系統會在 Heap 內找一塊區域放置此 User 實例的**屬性資料**，也因為 Heap 存放的是共享資料，所以不論該實例是由哪一個 Thread 所創建的，同 Process 底下的其他 Thread 也可以進行存取。

因為實例的生命週期是不可預知的，系統較難以自行去回收空間，所幸，Java 的特色之一就是 **Garbage Collection 機制 (簡稱 GC)**，會自動去清理 Heap 內已經**沒有被參考 (Reference)** 的資料，當然，程式設計師可以撰寫程式去控制 GC 的行動，不過大多數情況下都**不建議**如此，過多的介入反而可能造成資源的浪費，未來~~可能~~會再寫一篇簡述 GC 是如何判斷哪些資料應該被清理。

----

# 範例
可能有人看到這邊仍是霧煞煞，以下我用簡單的程式來講解資料儲存實際上是如何運作。

```java
int a = 123;
long b = 456L;
User c = new User("Mark", Gender.MALE);
```

以上程式碼的資料儲存順序大致上可以視為以下流程圖 *(在此先不考慮 Constant Pool)*。

{% asset_img stack-heap-example2.PNG example %}

前兩行指令都是 Primitive Type，會在 Stack 內建立變數 ***a*** 和 ***b***，並將實際值 ***123*** 和 ***456L*** 存放於變數內。

第三行指令要切分成兩個部份，等號右邊及左邊，首先是右邊的部份，先以 ***name*** 和 ***gender*** 參數創建一個 User 實例，此時會在 Heap 中的一個位址 (ex. ***0x1234***) 存放實例的屬性資料 (ex. ***name***、***gender***、***address***...)，並將實例所在的記憶體位址 (***0x1234***) 指定給變數 ***c*** 參考。

> 這篇是以變數為例子去比較兩者儲存方式的不同，至於「Stack 如何儲存函式」、「常數池 (Constant Pool) 是什麼」、「實例的 method 存放在哪」等等問題，留待後面文章再做解答。

從以上的例子可以發現，Stack 內的變數值分為兩種類型，一種為 **Value Type 實質類別**，儲存的是**實際的值** (ex. ***123***、***456L***)；另一種為 **Reference Type 參考類別**，儲存的是**資料在 Heap 中的記憶體位址** (ex. ***0x1234***)。

----

# 總結
1. Stack 和 Heap 是 JVM 記憶體儲存資料或指令的區域。
2. Java 的資料類型分為兩種：Primitive Type 和 Class Type。
3. Stack 存取速度快，但資料長度及生命週期必須是預知的，用來儲存區域變數、函式參數與函式返回位址等資料。
4. Heap 是 Class Type 創建實例時存放資料的地方，在 Heap 創建完成後會回傳所在的記憶體位址。
5. 變數可能是 Primitive Type 或 Class / Reference Type 。若是 Primitive Type，則在 Stack 內的變數值為實際值；若是 Class / Reference Type，實例資料會儲存在 Heap 中，Stack 內的變數值為實例在 Heap 中的記憶體位址。
6. `User user = new User("Mark");` 的行為簡單來說是：
	i.	創建並儲存 **User 實例 (*name = Mark*)** 於 Heap 中。
	ii.	儲存完成後，回傳資料所在的記憶體位址。
	iii.在 Stack 中新增變量 ***user***。
	iv.	將 ii. 所傳回的記憶體位址指定給 ***user*** 參考。