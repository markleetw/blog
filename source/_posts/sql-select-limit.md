---
title: 限制SQL的查詢資料筆數
date: 2016-06-22 15:51:55
categories:
- Technique
- Database
tags:
- SQL
---


{% asset_img les-paul.jpg Gibson - Les Paul %}

常常在SELECT資料庫時，因為資料量龐大或是某些特殊原因，只想查詢特定筆數的資料，但因為各家資料庫的語法不盡相同，避免每次都要重新查詢一次，所以就整理起來。

<!-- more -->

# Oracle
關鍵字**ROWNUM**，放在**WHERE**後方即可，範例語法如下：

``` sql 上述語法的作用是查詢前3筆資料
SELECT * FROM TABLE_NAME WHERE ROWNUM <= 3;
```

需特別注意的地方有：

1. ROWNUM會比ORDER BY, GROUP BY還要早執行，所以如果有使用這兩個子句的話，會發現顯示結果可能跟預期不同，建議用子查詢先包起來做ORDER BY, GROUP BY後再使用。
2. ROWNUM就像一個序列，把資料一筆一筆放進去，當放進第一筆資料時，其值為1，並且判斷是否符合條件式。若符合，則留下資料；不符合的話，就將該筆資料刪除，放進下一筆資料，ROWNUM從1開始。

``` sql 用此語法會查無資料，因為永遠不會出現大於3的ROWNUM
SELECT * FROM TABLE_NAME WHERE ROWNUM > 3;
```

``` sql 查詢第3筆以後的資料
SELECT * FROM
(SELECT TABLE_NAME.*, ROWNUM AS NUM FROM TABLE_NAME)
WHERE NUM > 3;
```

``` sql 取得第2、3筆資料
SELECT * FROM
(SELECT TABLE_NAME.*, ROWNUM AS NUM FROM TABLE_NAME)
WHERE NUM >=2 AND NUM <= 3;
```

# MySQL
關鍵字**LIMIT**，可以接受一或兩個參數，範例如下：

``` sql 查詢前2筆資料
SELECT * FROM TABLE_NAME LIMIT 2;
SELECT * FROM TABLE_NAME LIMIT 0, 2;
```

``` sql 查詢第2到第4筆資料
SELECT * FROM TABLE_NAME LIMIT 1, 3;
```
# SQL Server
在SQL Server中，查詢前N筆資料的語法為**TOP**，範例如下：

``` sql 查詢前3筆資料
SELECT TOP 3 * FROM TABLE_NAME;
```

但若要在SQL Server中取出第x筆到第y筆資料，方法就有非常多種：

* **ROW_NUMBER 函式法：**此函式會根據參數OVER內的ORDER BY順序給予資料列序號，再用這些序號當作條件去篩選，只適用於SQL Server 2005以上版本
``` sql 查詢出第2、3筆資料
SELECT * FROM (
  SELECT *, ROW_NUMBER() OVER (ORDER BY ID) as ROWNUM
  FROM TABLE_NAME
 ) a WHERE ROWNUM >= 2 and ROWNUM <= 3;
```

* **TOP相減法：**將兩個TOP查詢結果相減來取得區間資料(TOP 3 - TOP 1 = 第2、3筆)
``` sql
SELECT TOP 3 * FROM TABLE_NAME
MINUS
SELECT TOP 1 * FROM TABLE_NAME;
```

* **大TOP包小TOP法：**先找出TOP 3再倒序後找出其TOP 2
``` sql
SELECT TOP 2 * FROM (
  SELECT TOP 3 * FROM TABLE_NAME ORDER BY ID
) a ORDER BY ID DESC;
```

使用這類功能時，切記要多注意ORDER BY和GROUP BY的使用，這會大大地影響回傳的資料內容！