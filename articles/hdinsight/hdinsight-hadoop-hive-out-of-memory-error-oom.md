---
title: Naprawianie błędu gałęzi z pamięci w usłudze Azure HDInsight
description: Napraw błąd gałęzi z pamięci w hdinsight. Scenariusz klienta jest kwerendą w wielu dużych tabelach.
keywords: brak błędu pamięci, OOM, ustawienia gałęzi
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive
ms.date: 11/28/2019
ms.openlocfilehash: add55c29bb93d8dce9ad69bd9850a1db02ea5afe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687766"
---
# <a name="fix-an-apache-hive-out-of-memory-error-in-azure-hdinsight"></a>Naprawianie błędu gałęzi apache z pamięci w usłudze Azure HDInsight

Dowiedz się, jak naprawić błąd gałęzi apache poza pamięcią (OOM) podczas przetwarzania dużych tabel, konfigurując ustawienia pamięci hive.

## <a name="run-apache-hive-query-against-large-tables"></a>Uruchamianie kwerendy gałęzi Apache względem dużych tabel

Klient uruchomił kwerendę hive:

```sql
SELECT
    COUNT (T1.COLUMN1) as DisplayColumn1,
    …
    …
    ….
FROM
    TABLE1 T1,
    TABLE2 T2,
    TABLE3 T3,
    TABLE5 T4,
    TABLE6 T5,
    TABLE7 T6
where (T1.KEY1 = T2.KEY1….
    …
    …
```

Niektóre niuanse tego zapytania:

* T1 jest aliasem do dużej tabeli TABLE1, która ma wiele typów kolumn STRING.
* Inne tabele nie są tak duże, ale mają wiele kolumn.
* Wszystkie tabele łączą się ze sobą, w niektórych przypadkach z wieloma kolumnami w TABLE1 i innych.

Badanie gałęzi trwało 26 minut, aby zakończyć na 24 węzła A3 HDInsight klastra. Klient zauważył następujące komunikaty ostrzegawcze:

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Za pomocą apache Tez aparat wykonywania. To samo zapytanie działało przez 15 minut, a następnie wrzuciło następujący błąd:

    Status: Failed
    Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
    org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
    org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
    Caused by: java.lang.OutOfMemoryError: Java heap space

Błąd pozostaje podczas korzystania z większej maszyny wirtualnej (na przykład D12).

## <a name="debug-the-out-of-memory-error"></a>Debugowanie błędu braku pamięci

Nasze zespoły wsparcia i inżynierii razem znaleźć jeden z problemów powodujących błąd braku pamięci był [znany problem opisany w Apache JIRA:](https://issues.apache.org/jira/browse/HIVE-8306)

"Kiedy hive.auto.convert.join.noconditionalzak / true sprawdzamy noconditionaltask.size i jeśli suma rozmiarów tabel w sprzęcie mapy jest mniejsza niż noconditionaltask.size plan wygenerowałby sprzężenie mapy, problem polega na tym, że obliczenia nie biorą pod uwagę obciążenie wprowadzone przez różne hashTable implementacji jako wyniki, jeśli suma rozmiarów wejściowych jest mniejsza niż rozmiar noconditionalzak przez małe zapytania margines trafi OOM."

**Ul.auto.convert.join.noconditionaltask** w pliku hive-site.xml został ustawiony na **true:**

```xml
<property>
    <name>hive.auto.convert.join.noconditionaltask</name>
    <value>true</value>
    <description>
            Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
            If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
            specified size, the join is directly converted to a mapjoin (there is no conditional task).
    </description>
</property>
```

Prawdopodobnie sprzężenie mapy było przyczyną błędu pamięci obszaru sterty java. Jak wyjaśniono w blogu [Hadoop Ustawienia pamięci przędzy w HDInsight](https://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx), gdy używany jest aparat wykonywania Tez, użyte miejsce na stercie faktycznie należy do kontenera Tez. Zobacz poniższą ilustrację opisującą pamięć kontenera Tez.

![Diagram pamięci kontenera Tez: Błąd po braku pamięci](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)

Jak sugeruje wpis w blogu, następujące dwa ustawienia pamięci definiują pamięć kontenera dla sterty: **hive.tez.container.size** i **hive.tez.java.opts**. Z naszego doświadczenia wyjątku braku pamięci nie oznacza, że rozmiar kontenera jest zbyt mały. Oznacza to, że rozmiar sterty Java (hive.tez.java.opts) jest zbyt mały. Więc gdy widzisz z pamięci, można spróbować zwiększyć **hive.tez.java.opts**. W razie potrzeby może być konieczne zwiększenie **hive.tez.container.size**. Ustawienie **java.opts** powinno wynosić około 80% **pliku container.size.**

> [!NOTE]  
> Ustawienie **hive.tez.java.opts** musi być zawsze mniejsze niż **hive.tez.container.size**.

Ponieważ komputer D12 ma 28 GB pamięci, zdecydowaliśmy się użyć kontenera o rozmiarze 10 GB (10240 MB) i przypisać 80% do java.opts:

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

Dzięki nowym ustawieniom kwerenda została pomyślnie uruchomiony w mniej niż 10 minut.

## <a name="next-steps"></a>Następne kroki

Uzyskanie błędu OOM nie musi oznaczać, że rozmiar kontenera jest zbyt mały. Zamiast tego należy skonfigurować ustawienia pamięci, tak aby rozmiar sterty jest zwiększona i jest co najmniej 80% rozmiaru pamięci kontenera. Aby zoptymalizować kwerendy hive, zobacz [Optymalizowanie zapytań hive Apache dla Apache Hadoop w udziale HDInsight](hdinsight-hadoop-optimize-hive-query.md).
