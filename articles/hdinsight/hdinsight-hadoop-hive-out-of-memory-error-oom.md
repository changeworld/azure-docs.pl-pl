---
title: Usuń gałąź błąd braku pamięci w usłudze Azure HDInsight
description: Usuń gałąź błąd braku pamięci w HDInsight. Scenariusz klienta jest zapytanie w wielu dużych tabel.
keywords: Brak ustawienia programu Hive błąd, za mało pamięci, pamięci
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: 2e7328b95aecc8e644d7b9e2ec407a62551fff79
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64712790"
---
# <a name="fix-an-apache-hive-out-of-memory-error-in-azure-hdinsight"></a>Napraw, Apache Hive błąd braku pamięci w usłudze Azure HDInsight

Dowiedz się, jak naprawić Apache Hive błąd braku pamięci (za mało pamięci), podczas przetwarzania dużych tabel, konfigurując ustawienia pamięci programu Hive.

## <a name="run-apache-hive-query-against-large-tables"></a>Uruchomienie zapytania usługi Apache Hive dużych tabel

Klient uruchomienia zapytania programu Hive:

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

Niektóre szczegóły skutecznego tego zapytania:

* T1 jest aliasem tabelę big Data, TABLE1, który ma wiele typów kolumn CIĄGÓW.
* Inne tabele są nie, big Data, ale mają wiele kolumn.
* Wszystkie tabele są dołączenie do siebie nawzajem w niektórych przypadkach z wieloma kolumnami TABLE1 i innych.

Zapytanie Hive trwało 26 minut na węźle klastra HDInsight A3 24. Klient zauważyć następujące komunikaty ostrzegawcze:

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Za pomocą aparatu wykonywania Apache Tez. Tego samego zapytania został uruchomiony przez 15 minut, a następnie wygenerował następujący błąd:

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

Błąd pozostają w przypadku używania większe maszyny wirtualnej (na przykład, D12).


## <a name="debug-the-out-of-memory-error"></a>Błąd braku pamięci debugowania

Nasze wsparcie i zespoły inżynierów razem ustalono na jeden z problemów, co powoduje błąd braku pamięci [znanego problemu opisanego w usłudze JIRA Apache](https://issues.apache.org/jira/browse/HIVE-8306):

    When hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum  of tables sizes in the map join is less than noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation doesn't take into account the overhead introduced by different HashTable implementation as results if the sum of input sizes is smaller than the noconditionaltask size by a small margin queries will hit OOM.

**Hive.auto.convert.join.noconditionaltask** w gałęzi site.xml pliku była ustawiona na **true**:

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

Prawdopodobnie sprzężenia mapy był przyczyną miejsca na stercie Java naszych błędu pamięci. Jak wyjaśniono w wpis w blogu [ustawienia pamięci usługi Yarn w usłudze Hadoop w HDInsight](https://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx), gdy jest aparat wykonywania platformy Tez użyta sterta miejsca faktycznie należy do kontenera aplikacji Tez. Zobacz poniższy obraz, opisujący pamięci kontenera aplikacji Tez.

![Diagram pamięci kontenera tez: Hive błąd braku pamięci](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)

Jak sugeruje wpis w blogu, następujące ustawienia dwóch pamięci definiują kontenera pamięć sterty: **hive.tez.container.size** i **hive.tez.java.opts**. Z naszych doświadczeń wynika wyjątku braku pamięci nie oznacza, że rozmiar kontenera jest za mały. Oznacza to, że rozmiar sterty środowiska Java (hive.tez.java.opts) jest za mały. Aby zawsze wtedy, gdy zostanie wyświetlony jest za mało pamięci, możesz spróbować zwiększyć **hive.tez.java.opts**. Jeśli to konieczne może być konieczne zwiększenie **hive.tez.container.size**. **Java.opts** ustawienie powinno być około 80% **container.size**.

> [!NOTE]  
> Ustawienie **hive.tez.java.opts** zawsze musi być mniejszy niż **hive.tez.container.size**.
> 
> 

Maszyny D12 jest 28GB pamięci, dlatego zdecydowaliśmy się użyć kontenera o rozmiarze 10 GB (10240MB) i przypisz 80% do java.opts:

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

Z nowymi ustawieniami zapytanie działa prawidłowo w mniej niż 10 minut.

## <a name="next-steps"></a>Kolejne kroki

Występuje błąd za mało pamięci nie musi oznaczać, że rozmiar kontenera jest za mały. Zamiast tego należy skonfigurować ustawienia pamięci, dzięki czemu rozmiar stosu zwiększa się i jest co najmniej 80% rozmiar pamięci kontenera. Do optymalizacji zapytań programu Hive, zobacz [zoptymalizować Apache Hive zapytania dla usługi Apache Hadoop w HDInsight](hdinsight-hadoop-optimize-hive-query.md).
