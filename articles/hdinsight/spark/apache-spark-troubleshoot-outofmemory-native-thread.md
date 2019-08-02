---
title: Nie można uruchomić serwera usługi Livy na klastrze Apache Spark w usłudze Azure HDInsight
description: Nie można uruchomić serwera usługi Livy na klastrze Apache Spark w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 540b5d2a20764daf2a7f762ac7381f72ce4aabcd
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668913"
---
# <a name="scenario-livy-server-fails-to-start-on-apache-spark-cluster-in-azure-hdinsight"></a>Scenariusz: Nie można uruchomić serwera usługi Livy na klastrze Apache Spark w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku używania składników Apache Spark w klastrach usługi Azure HDInsight.

## <a name="issue"></a>Problem

Nie można uruchomić serwera usługi Livy na Apache Spark [(Spark 2,1 w systemie Linux (HDI 3,6)]. Podjęto próbę ponownego uruchomienia w następujący stosie błędów z dzienników usługi Livy:

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

## <a name="cause"></a>Przyczyna

`java.lang.OutOfMemoryError: unable to create new native thread`Najważniejsze elementy systemu operacyjnego nie mogą przypisywać więcej natywnych wątków do JVMs. Potwierdzenie, że ten wyjątek jest spowodowany przez naruszenie limitu liczby wątków dla procesu.

Po nieoczekiwanym zakończeniu działania serwera usługi Livy wszystkie połączenia z klastrami Spark są również przerywane, co oznacza, że wszystkie zadania i powiązane dane zostaną utracone. W ramach mechanizmu odzyskiwania sesji HDP 2,6 wprowadzono usługi Livy szczegóły sesji w dozorcy do odzyskania po zakończeniu działania serwera usługi Livy.

W przypadku przesyłania dużej liczby zadań za pośrednictwem usługi usługi Livy, w ramach wysokiej dostępności dla serwera usługi Livy są przechowywane te Stany sesji w ZK (w klastrach usługi HDInsight) i odzyskiwać te sesje po ponownym uruchomieniu usługi usługi Livy. Po ponownym uruchomieniu po nieoczekiwanym zakończeniu usługi Livy tworzy jeden wątek dla każdej sesji, co spowoduje zsumowanie określonej liczby sesji do odzyskiwania, co powoduje utworzenie zbyt wielu wątków.

## <a name="resolution"></a>Rozwiązanie

Usuń wszystkie wpisy, wykonując kroki opisane poniżej.

1. Pobierz adres IP węzłów dozorcy za pomocą polecenia

    ```bash
    grep -R zk /etc/hadoop/conf  
    ```

1. Powyższe polecenie na liście wszystkie dozorcami dla mojego klastra

    ```bash
    /etc/hadoop/conf/core-site.xml:      <value>zk1-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk2-      hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk4-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181</value>
    ```

1. Pobierz wszystkie adresy IP węzłów dozorcy za pomocą polecenia ping lub można także nawiązać połączenie z dozorcy z węzła głównego przy użyciu nazwy ZK

    ```bash
    /usr/hdp/current/zookeeper-client/bin/zkCli.sh -server zk2-hwxspa:2181
    ```

1. Po nawiązaniu połączenia z usługą dozorcy wykonaj następujące polecenie, aby wyświetlić listę wszystkich sesji, które próbujesz ponownie uruchomić.

    1. Większość przypadków może to być lista więcej niż 8000 sesji ####

        ```bash
        ls /livy/v1/batch
        ```

    1. Następujące polecenie służy do usuwania wszystkich odzyskiwanych sesji. #####

        ```bash
        rmr /livy/v1/batch
        ```

1. Poczekaj na zakończenie powyższego polecenia, a kursor ma zwrócić monit, a następnie uruchom ponownie usługę usługi Livy z Ambari, która powinna zakończyć się powodzeniem.

> [!NOTE]
> `DELETE`sesja usługi Livy po zakończeniu jego wykonywania. Sesje wsadowe usługi Livy nie zostaną usunięte automatycznie zaraz po zakończeniu aplikacji platformy Spark, która jest zaprojektowana. Sesja usługi Livy jest jednostką utworzoną przez żądanie POST na serwerze REST usługi Livy. Do usunięcia tej jednostki jest potrzebne wywołanie.`DELETE` W przeciwnym razie należy poczekać na zakończenie działania GC.

## <a name="next-steps"></a>Kolejne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) za pomocą — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
