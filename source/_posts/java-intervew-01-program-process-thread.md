---
title: Java面試01 - Program、Process和Thread
date: 2016-09-13 20:53:37
categories:
- Technique
- Java
tags:
- interview
- java_interview
---

{% asset_img process-and-thread.PNG multithreaded process  %}

### 請簡介 Program、Process 和 Thread 及其差別
這一題倒是比較少考到，不過為了後面講解多執行緒 (Multi-Thread) 以及 JVM Stack/Heap 方便，所以還是必須要提一下。

<!-- more -->

----

# Program 程式
**還沒有被執行的程式，或是說可以驅動/命令電腦的指令集合**，它可能是指令、可讀的程式碼、編譯過的位元碼 (bytecode) 或是機器碼。

----

# Process 程序
**當 Program 被執行時，所產生的執行個體**，Program 被執行就會產生 Process，所以如果同時執行同一個 Program 十次，就會產生至少十個 Process。要切記一點，一顆 CPU 同時只能做一個運算，多工作業系統 (Multitasking Operating System) 可以同時運行多個 Process 是因為它將 Process 進行排程 (Scheduling)，令 CPU 在各 Process 切換，因為速度很快，所以才會有同時處裡好多個 Process 的錯覺。

Process 包含以下兩個部分：
#### 1. Memory Space 記憶體空間
所有此Process的共享資料、資源放置於此，同一個 Process 底下的所有 Thread 皆可存取，但是不同 Process 的 Memory Space 不同，Process 彼此之間無法存取對方的 Memory Space。
#### 2. Thread 執行緒

----

# Thread 執行緒
**CPU 實際運算的部分**，Thread 包含兩個部分：
#### 1. Stack 堆疊
儲存從起點開始 (例如`main`)，到目前為止所有函數呼叫路徑，以及這些呼叫路徑上所用到的區域變數。
#### 2. CPU 狀態
存放 CPU 暫存器 (如 Program Counter, Stack Pointer, Program Status Word...) 等狀態，以便在 CPU 再次回來執行時能知道上次執行狀況。

--

一個 Process 包含一個以上的 Thread，以 Java SE 為例，應用程式從起始的入口`main`開始執行，這就是主執行緒 (Main Thread)，如果程式內沒有創建其他 Thread，則稱為單一執行緒；若是程式內有使用類似以下方式去主動創建 Thread，則稱為多執行緒 (Multithreading)。

```java
public class ThreadExample1 extends Thread {
    @Override
    public void run() {
        System.out.println("Custom Thread");
    }

    public static void main(String[] args) {
        Thread t = new ThreadExample1();  // 創建 Thread
        t.start();  // 執行 Thread
        System.out.println("Main Thread");
    }
}
```

```java
public class ThreadExample2 implements Runnable {
    @Override
    public void run() {
        System.out.println("Custom Thread");
    }

    public static void main(String[] args) {
        Thread t = new Thread(new ThreadExample2());  // 創建 Thread
        t.start();  // 執行 Thread
        System.out.println("Main Thread");
    }
}
```

前面說到 CPU 會在 Process 之間做切換，其實 CPU 被分配給 Process 時主要是執行 Thread 運算，當 CPU 要處理的 Process 底下有多個 Thread，CPU 會依照 Thread 優先順序以及 CPU 使用時間去分配，在 Thread 之間做切換，讓每個 Thread 都有被執行到的機會，達到多個 Thread 同時運算的效果。

# 總結
1. Program 是還沒被執行的指令或程式集合
2. Process 是 Program 執行時產生的執行個體
3. Process 下有 Memory Space 和一個以上的 Thread
4. Memory Space 是同 Process 下所有 Thread 共用，主要用來存放共享的資料及資源
5. Thread 有自己的 Stack 來儲存函數路徑及區域變數，所以不同 Thread 之間的區域變數是互相獨立的
6. CPU 會在 Process 之間快速切換來達到多工的目的
7. CPU 也會根據 Thread 優先權以及 CPU 使用時間，在 Thread 之間切換，讓每個 Thread 都有被執行的機會