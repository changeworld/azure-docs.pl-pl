---
title: OutOfMemoryError wyjątki dla Platformy Spark Apache w usłudze Azure HDInsight
description: Różne wyjątki OutOfMemoryError dla klastra Apache Spark w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/15/2019
ms.openlocfilehash: 31cdef281b1cb26d01a4690c815e3d3621e2c053
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271969"
---
# <a name="outofmemoryerror-exceptions-for-apache-spark-in-azure-hdinsight"></a>OutOfMemoryError wyjątki dla Platformy Spark Apache w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas korzystania ze składników Platformy Apache Spark w klastrach usługi Azure HDInsight.

## <a name="scenario-outofmemoryerror-exception-for-apache-spark"></a>Scenariusz: OutOfMemoryError wyjątek dla Apache Spark

### <a name="issue"></a>Problem

Aplikacja Apache Spark nie powiodła się z nieobsługiwałem wyjątek OutOfMemoryError. Może pojawić się komunikat o błędzie podobny do:

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

### <a name="cause"></a>Przyczyna

Najbardziej prawdopodobną przyczyną tego wyjątku jest to, że za mało pamięci sterty jest przydzielany do maszyn wirtualnych Java (JVM). Te JVM są uruchamiane jako executors lub sterowniki w ramach aplikacji Apache Spark.

### <a name="resolution"></a>Rozwiązanie

1. Określ maksymalny rozmiar danych obsługiwanych przez aplikację aparatu Spark. Dokonaj oszacowania rozmiaru na podstawie maksymalnego rozmiaru danych wejściowych, danych pośrednich wytwarzanych przez przekształcenie danych wejściowych i danych wyjściowych, które spowodowały dalszą transformację danych pośrednich. Jeśli początkowe oszacowanie nie jest wystarczające, należy nieznacznie zwiększyć rozmiar i iteracji, aż błędy pamięci ustępują.

1. Upewnij się, że klaster usługi HDInsight, który ma być używany, ma wystarczającą ilość zasobów pamięci i rdzeni, aby pomieścić aplikację aparatu Spark. Można to ustalić, przeglądając sekcję Metryki klastra w interfejsie użytkownika YARN klastra dla wartości **używanej pamięci** vs **suma pamięci** i **kami wirtualne używane** w porównaniu z całkowitą **całkowitej liczby podstawowych.**

    ![widok pamięci rdzenia przędzy](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. Ustaw następujące konfiguracje platformy Spark na odpowiednie wartości. Równoważ wymagania aplikacji z dostępnymi zasobami w klastrze. Wartości te nie powinny przekraczać 90% dostępnej pamięci i rdzeni, które są wyświetlane przez YARN, a także powinny spełniać minimalne wymagania dotyczące pamięci aplikacji Spark:

    ```
    spark.executor.instances (Example: 8 for 8 executor count)
    spark.executor.memory (Example: 4g for 4 GB)
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB)
    spark.executor.cores (Example: 2 for 2 cores per executor)
    spark.driver.memory (Example: 8g for 8GB)
    spark.driver.cores (Example: 4 for 4 cores)
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB)
    ```

    Całkowita pamięć używana przez wszystkich wykonawców =

    ```
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```

    Całkowita pamięć używana przez sterownik =

    ```
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

---

## <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server"></a>Scenariusz: Błąd miejsca na stercie java podczas próby otwarcia serwera historii platformy Apache Spark

### <a name="issue"></a>Problem

Podczas otwierania zdarzeń na serwerze Spark History pojawia się następujący błąd:

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

### <a name="cause"></a>Przyczyna

Ten problem jest często spowodowane przez brak zasobów podczas otwierania dużych plików spark-event. Rozmiar sterty platformy Spark jest domyślnie ustawiony na 1 GB, ale duże pliki zdarzeń platformy Spark mogą wymagać więcej niż to.

Jeśli chcesz sprawdzić rozmiar plików, które próbujesz załadować, możesz wykonać następujące polecenia:

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

### <a name="resolution"></a>Rozwiązanie

Można zwiększyć pamięć serwera historii platformy `SPARK_DAEMON_MEMORY` Spark, edytując właściwość w konfiguracji platformy Spark i uruchamiając ponownie wszystkie usługi.

Można to zrobić z poziomu interfejsu użytkownika przeglądarki Ambari, wybierając sekcję Spark2/Config/Advanced spark2-env.

![Zaawansowana sekcja spark2-env](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image01.png)

Dodaj następującą właściwość, aby zmienić pamięć serwera historii `SPARK_DAEMON_MEMORY=4g`platformy Spark z 1g na 4g: .

![Właściwość Spark](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image02.png)

Upewnij się, że ponownie uruchomić wszystkie usługi, których dotyczy problem z Ambari.

---

## <a name="scenario-livy-server-fails-to-start-on-apache-spark-cluster"></a>Scenariusz: Nie można uruchomić serwera Livy server w klastrze Apache Spark

### <a name="issue"></a>Problem

Nie można uruchomić serwera Livy na platformie Apache Spark [(Spark 2.1 w systemie Linux (HDI 3.6)]. Próba ponownego uruchomienia powoduje następujące stos błędów z dzienników Livy:

```log
17/07/27 17:52:50 INFO CuratorFrameworkImpl: Starting
17/07/27 17:52:50 INFO ZooKeeper: Client environment:zookeeper.version=3.4.6-29--1, built on 05/15/2017 17:55 GMT
17/07/27 17:52:50 INFO ZooKeeper: Client environment:host.name=10.0.0.66
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.version=1.8.0_131
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.vendor=Oracle Corporation
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.home=/usr/lib/jvm/java-8-openjdk-amd64/jre
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.class.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.library.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.io.tmpdir=/tmp
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.compiler=<NA>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.name=Linux
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.arch=amd64
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.version=4.4.0-81-generic
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.name=livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.home=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.dir=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Initiating client connection, connectString=zk2-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk3-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk6-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181 sessionTimeout=60000 watcher=org.apache.curator.ConnectionState@25fb8912
17/07/27 17:52:50 INFO StateStore$: Using ZooKeeperStateStore for recovery.
17/07/27 17:52:50 INFO ClientCnxn: Opening socket connection to server 10.0.0.61/10.0.0.61:2181. Will not attempt to authenticate using SASL (unknown error)
17/07/27 17:52:50 INFO ClientCnxn: Socket connection established to 10.0.0.61/10.0.0.61:2181, initiating session
17/07/27 17:52:50 INFO ClientCnxn: Session establishment complete on server 10.0.0.61/10.0.0.61:2181, sessionid = 0x25d666f311d00b3, negotiated timeout = 60000
17/07/27 17:52:50 INFO ConnectionStateManager: State change: CONNECTED
17/07/27 17:52:50 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
17/07/27 17:52:50 INFO AHSProxy: Connecting to Application History server at headnodehost/10.0.0.67:10200
Exception in thread "main" java.lang.OutOfMemoryError: unable to create new native thread
  at java.lang.Thread.start0(Native Method)
  at java.lang.Thread.start(Thread.java:717)
  at com.cloudera.livy.Utils$.startDaemonThread(Utils.scala:98)
  at com.cloudera.livy.utils.SparkYarnApp.<init>(SparkYarnApp.scala:232)
  at com.cloudera.livy.utils.SparkApp$.create(SparkApp.scala:93)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:117)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:116)
  at com.cloudera.livy.server.batch.BatchSession.<init>(BatchSession.scala:137)
  at com.cloudera.livy.server.batch.BatchSession$.recover(BatchSession.scala:108)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.mutable.ResizableArray$class.foreach(ResizableArray.scala:59)
  at scala.collection.mutable.ArrayBuffer.foreach(ArrayBuffer.scala:47)
  at scala.collection.TraversableLike$class.map(TraversableLike.scala:244)
  at scala.collection.AbstractTraversable.map(Traversable.scala:105)
  at com.cloudera.livy.sessions.SessionManager.com$cloudera$livy$sessions$SessionManager$$recover(SessionManager.scala:150)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at scala.Option.getOrElse(Option.scala:120)
  at com.cloudera.livy.sessions.SessionManager.<init>(SessionManager.scala:82)
  at com.cloudera.livy.sessions.BatchSessionManager.<init>(SessionManager.scala:42)
  at com.cloudera.livy.server.LivyServer.start(LivyServer.scala:99)
  at com.cloudera.livy.server.LivyServer$.main(LivyServer.scala:302)
  at com.cloudera.livy.server.LivyServer.main(LivyServer.scala)
  
  ## using "vmstat" found  we had enough free memory
```

### <a name="cause"></a>Przyczyna

`java.lang.OutOfMemoryError: unable to create new native thread`podkreśla, że system operacyjny nie może przypisać więcej wątków natywnych do JVM. Potwierdzono, że ten wyjątek jest spowodowany naruszeniem limitu liczby wątków na proces.

Gdy livy Server kończy się nieoczekiwanie, wszystkie połączenia z klastrami platformy Spark są również zakończone, co oznacza, że wszystkie zadania i powiązane dane zostaną utracone. W HDP 2.6 mechanizm odzyskiwania sesji został wprowadzony, Livy przechowuje szczegóły sesji w Zookeeper do odzyskania po Livy Server jest z powrotem.

Gdy duża liczba zadań są przesyłane za pośrednictwem livy, jako część Wysokiej dostępności dla Livy Server przechowuje te stany sesji w ZK (w klastrach HDInsight) i odzyskać te sesje po ponownym uruchomieniu usługi Livy. Po ponownym uruchomieniu po nieoczekiwanym zakończeniu Livy tworzy jeden wątek na sesję i to gromadzi pewną liczbę sesji do odzyskania, powodując tworzenie zbyt wielu wątków.

### <a name="resolution"></a>Rozwiązanie

Usuń wszystkie wpisy, wykonując czynności opisane poniżej.

1. Uzyskaj adres IP węzłów zookeeper za pomocą

    ```bash
    grep -R zk /etc/hadoop/conf  
    ```

1. Powyższe polecenie wymienione wszystkie zookeepers dla mojego klastra

    ```bash
    /etc/hadoop/conf/core-site.xml:      <value>zk1-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk2-      hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk4-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181</value>
    ```

1. Uzyskaj cały adres IP węzłów zookeeper za pomocą pingu Lub możesz również połączyć się z zookeeperem z headnode używając nazwy ZK

    ```bash
    /usr/hdp/current/zookeeper-client/bin/zkCli.sh -server zk2-hwxspa:2181
    ```

1. Po nawiązaniu połączenia z zookeeper wykonać następujące polecenie, aby wyświetlić listę wszystkich sesji, które są próbą ponownego uruchomienia.

    1. W większości przypadków może to być lista ponad 8000 sesji ####

        ```bash
        ls /livy/v1/batch
        ```

    1. Następujące polecenie jest usunięcie wszystkich sesji do odzyskania. #####

        ```bash
        rmr /livy/v1/batch
        ```

1. Poczekaj na zakończenie powyższego polecenia i kursor, aby zwrócić wiersz, a następnie uruchom ponownie usługę Livy z Ambari, co powinno zakończyć się pomyślnie.

> [!NOTE]
> `DELETE`po zakończeniu jego wykonania. Sesje wsadowe Livy nie zostaną usunięte automatycznie, gdy tylko aplikacja spark zostanie ukończona, co jest zgodnie z projektem. Sesja Livy to jednostka utworzona przez żądanie POST przeciwko serwerowi Livy Rest. Wywołanie `DELETE` jest potrzebne do usunięcia tej jednostki. Albo powinniśmy czekać na GC kopać w.

---

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* [Omówienie zarządzania pamięcią iskrzą](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Debugowanie aplikacji Spark w klastrach HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
