---
title: Gradle/Ant合併檔案編碼問題
date: 2016-06-25 11:41:12
categories:
- Technique
- Java
tags:
- gradle
- ant
---

{% asset_img joe-satriani.jpg Joe Satriani %}

使用Ant或Gradle合併多個檔案時，因為檔案內含中文字導致編碼問題。

<!-- more -->

# 問題

我有一支合併檔案的task如下：

```gradle 將data下的SQL檔合併成output內的result.sql
task concatsql(type: SourceTask) {
    delete 'output/result.sql'
    source 'data'
    include '*.sql'
    outputs.file new File("output", "result.sql")
    doLast {
        ant.concat(destfile: outputs.files.singleFile) {
            source.addToAntBuilder(ant, 'fileset', FileCollection.AntType.FileSet)
        }
    }
}
```

因為SQL檔內容包含中文字，導致輸出的內容會有亂碼，而這支Gradle，其實底層是使用Ant的cancat功能，所以同理，使用Ant去跑也會出現一樣的問題。

# 基礎知識

好像也沒什麼基礎知識，就是encoding的問題。

Ant可以加入`encoding`跟`outputencoding`標籤，請參考[How to encode Java files in UTF-8 using Apache Ant?](http://stackoverflow.com/questions/12374324/how-to-encode-java-files-in-utf-8-using-apache-ant)。

# 解決方法

首先將原始檔案儲存為`UTF-8`編碼，並在呼叫`ant.concat`時，加入`encoding`和`outputencoding`設定。

```gradle
task concatsql(type: SourceTask) {
    delete 'output/result.sql'
    source 'data'
    include '*.sql'
    outputs.file new File("output", "result.sql")
    doLast {
        ant.concat(destfile: outputs.files.singleFile, encoding:"UTF-8",
                outputencoding: "UTF-8") {
            source.addToAntBuilder(ant, 'fileset', FileCollection.AntType.FileSet)
        }
    }
}
```
