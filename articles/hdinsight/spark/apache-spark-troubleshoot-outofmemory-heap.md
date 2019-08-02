---
title: OutOfMemoryError wyjątek dla Apache Spark w usłudze Azure HDInsight
description: OutOfMemoryError wyjątek dla Apache Spark w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/26/2019
ms.openlocfilehash: ac360312fdb3c4a238e6280872bc8c8b548e8544
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620989"
---
# <a name="scenario-outofmemoryerror-exception-for-apache-spark-in-azure-hdinsight"></a>Scenariusz: OutOfMemoryError wyjątek dla Apache Spark w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania składników Apache Spark w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Aplikacja Apache Sparka nie powiodła się z powodu nieobsługiwanego wyjątku OutOfMemoryError. Może zostać wyświetlony komunikat o błędzie podobny do:

```error
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439)

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.grow(Unknown Source)
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.write(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source)
    at java.io.ObjectOutputStream.writeObject0(Unknown Source)
    at java.io.ObjectOutputStream.writeObject(Unknown Source)
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44)
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101)
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source)
    at java.lang.Thread.run(Unknown Source)
```

```error
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main]

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    ...
```

## <a name="cause"></a>Przyczyna

Najbardziej prawdopodobną przyczyną tego wyjątku jest za mało pamięci sterty. Aplikacja platformy Spark wymaga wystarczającej ilości pamięci sterty języka Java Virtual Machines (JVM) podczas uruchamiania programu jako modułów wykonujących lub sterowników.

## <a name="resolution"></a>Rozwiązanie

1. Określ maksymalny rozmiar danych obsługiwanych przez aplikację aparatu Spark. Należy oszacować rozmiar na podstawie maksymalnego rozmiaru danych wejściowych, danych pośrednich generowanych przez transformacje danych wejściowych i danych wyjściowych, które wygenerowały dalsze Przekształcanie danych pośrednich. Jeśli wstępne oszacowanie nie jest wystarczające, Zwiększ rozmiar nieco i wykonaj iterację do momentu wystąpienia błędów pamięci.

1. Upewnij się, że klaster usługi HDInsight, który ma być używany, ma wystarczającą ilość zasobów pamięci i rdzeni, aby pomieścić aplikację aparatu Spark. Można to ustalić przez wyświetlenie sekcji metryki klastra w interfejsie użytkownika usługi YARN w celu uzyskania wartości używanej pamięci względem łącznej ilości pamięci oraz liczby używanych rdzeni wirtualnych względem łącznej liczby rdzeni wirtualnych.

    ![Widok pamięci podstawowej przędzy](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. Ustaw następujące konfiguracje platformy Spark na odpowiednie wartości. Równoważyć wymagania dotyczące aplikacji z dostępnymi zasobami w klastrze. Te wartości nie powinny przekraczać 90% dostępnej pamięci i rdzeni w postaci podanej przez PRZĘDZę i powinny również spełniać minimalne wymagania dotyczące pamięci aplikacji Spark:

    ```
    spark.executor.instances (Example: 8 for 8 executor count)
    spark.executor.memory (Example: 4g for 4 GB)
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB)
    spark.executor.cores (Example: 2 for 2 cores per executor)
    spark.driver.memory (Example: 8g for 8GB)
    spark.driver.cores (Example: 4 for 4 cores)
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB)
    ```

    Łączna ilość pamięci używanej przez wszystkie wykonawcy =

    ```
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```

    Całkowita ilość pamięci używanej przez sterownik =

    ```
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* [Zarządzanie pamięcią Spark — Omówienie](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Debugowanie aplikacji Spark w klastrach usługi HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) za pomocą — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
