---
title: Usuń gałąź błędu braku pamięci w usłudze Azure HDInsight | Dokumentacja firmy Microsoft
description: Usuń gałąź błędu braku pamięci w usłudze HDInsight. Scenariusz klienta jest kwerenda w wielu dużych tabel.
keywords: Brak ustawienia Hive za mało pamięci, błąd pamięci
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 7bce3dff-9825-4fa0-a568-c52a9f7d1dad
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: f2ae83d259b7567a4b5c39e76ed7610e2ee426f8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="fix-a-hive-out-of-memory-error-in-azure-hdinsight"></a>Usuń gałąź błędu braku pamięci w usłudze Azure HDInsight

Dowiedz się, jak rozwiązać gałąź błędu braku pamięci podczas przetwarzania dużych tabel, konfigurując ustawienia pamięci Hive.

## <a name="run-hive-query-against-large-tables"></a>Uruchamianie zapytań Hive względem dużych tabel

Klient uruchomiono zapytań programu Hive:

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

Niektóre różnice tego zapytania:

* T1 jest aliasu wielką tabelę Tabela1, który ma wiele typów kolumn ciągu.
* Inne tabele że duży, którzy mają dużo kolumn.
* Wszystkie tabele się wzajemnie, w niektórych przypadkach z wieloma kolumnami TABLE1 i innych.

Zapytanie Hive trwało 26 minut na zakończenie na węźle klastra usługi HDInsight A3 24. Klient zauważyć następujące ostrzeżenia:

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Za pomocą aparat wykonywania platformy Tez. Tego samego zapytania został uruchomiony przez 15 minut, a następnie wygenerował następujący błąd:

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

Błąd pozostaje podczas używania większe maszyny wirtualnej (na przykład D12).


## <a name="debug-the-out-of-memory-error"></a>Debugowanie błąd braku pamięci

Technicznej i zespoły inżynierów razem ustalono na jeden z problemów, co powoduje błąd braku pamięci [znany problem opisany w Apache JIRA](https://issues.apache.org/jira/browse/HIVE-8306):

    When hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum  of tables sizes in the map join is less than noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation doesnt take into account the overhead introduced by different HashTable implementation as results if the sum of input sizes is smaller than the noconditionaltask size by a small margin queries will hit OOM.

**Hive.auto.convert.join.noconditionaltask** hive site.xml pliku zostało ustawione na **true**:

    <property>
        <name>hive.auto.convert.join.noconditionaltask</name>
        <value>true</value>
        <description>
              Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
              If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
              specified size, the join is directly converted to a mapjoin (there is no conditional task).
        </description>
      </property>

Prawdopodobnie mapy sprzężenia została przyczynę miejsca na stercie Java naszych błędu pamięci. Zgodnie z objaśnieniem w blogu [Hadoop Yarn ustawienia pamięci w usłudze HDInsight](http://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx), gdy Tez aparat wykonywania jest używana sterty miejsca używanego faktycznie należy do kontenera aplikacji Tez. Zobacz poniższy obraz opisujące pamięci kontenera aplikacji Tez.

![Diagram pamięci kontenera tez: Hive błąd braku pamięci](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)

Zgodnie z sugestią, wpis w blogu, następujące ustawienia pamięci dwóch Definiowanie pamięci kontenera na stercie: **hive.tez.container.size** i **hive.tez.java.opts**. Naszym doświadczeniu wyjątku braku pamięci oznacza to, że rozmiar kontenera jest za mały. Oznacza to, że rozmiar sterty Java (hive.tez.java.opts) jest za mały. Aby zawsze, gdy zostanie wyświetlony za mało pamięci, możesz zwiększyć **hive.tez.java.opts**. W razie potrzeby może zajść konieczność zwiększenia **hive.tez.container.size**. **Java.opts** ustawienie powinno być około 80% **container.size**.

> [!NOTE]
> Ustawienie **hive.tez.java.opts** zawsze musi być mniejsza niż **hive.tez.container.size**.
> 
> 

Ponieważ maszyny D12 ma 28GB pamięci RAM, zdecydowaliśmy się użyć kontenera o rozmiarze 10GB (10240MB) i przypisać 80% java.opts:

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

Przy użyciu nowych ustawień kwerendy bazy danych pomyślnie wykonał poniżej 10 minut.

## <a name="next-steps"></a>Kolejne kroki

Pobieranie za mało pamięci błąd nie musi oznaczać, że rozmiar kontenera jest za mały. Zamiast tego należy skonfigurować ustawienia pamięci, dzięki czemu rozmiar stosu zwiększa się i jest co najmniej 80% rozmiar pamięci kontenera. Do optymalizacji zapytań programu Hive, zobacz [zoptymalizować Hive zapytania dla platformy Hadoop w usłudze HDInsight](hdinsight-hadoop-optimize-hive-query.md).
