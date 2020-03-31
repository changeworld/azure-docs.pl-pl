---
title: Porty używane przez usługi Hadoop w usłudze HDInsight — Azure
description: Ten artykuł zawiera listę portów używanych przez usługi Apache Hadoop działające w usłudze Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 67cafbb7934381cd4c2936d6e6dfe7fb19d70735
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314695"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>Porty używane przez usługi Apache Hadoop w programie HDInsight

Ten dokument zawiera listę portów używanych przez usługi Apache Hadoop uruchomione w klastrach HDInsight. Zawiera również informacje o portach używanych do łączenia się z klastrem przy użyciu funkcji SSH.

## <a name="public-ports-vs-non-public-ports"></a>Porty publiczne a porty niepubliczne

Klastry HDInsight oparte na systemie Linux udostępniają tylko trzy porty publicznie w Internecie; 22, 23 i 443. Porty te są używane do bezpiecznego dostępu do klastra przy użyciu protokołu SSH i usług ujawnionych za pośrednictwem bezpiecznego protokołu HTTPS.

Wewnętrznie usługa HDInsight jest implementowana przez kilka maszyn wirtualnych platformy Azure (węzłów w klastrze) uruchomionych w sieci wirtualnej platformy Azure. Z poziomu sieci wirtualnej można uzyskać dostęp do portów nieumieszczonych przez Internet. Na przykład jeśli połączysz się z jednym z węzłów głównego przy użyciu SSH, z węzła głównego można następnie bezpośrednio uzyskać dostęp do usług uruchomionych w węzłach klastra.

> [!IMPORTANT]  
> Jeśli nie określisz sieci wirtualnej platformy Azure jako opcji konfiguracji dla usługi HDInsight, zostanie utworzona automatycznie. Jednak nie można dołączyć do innych maszyn (takich jak inne maszyny wirtualne platformy Azure lub maszyny deweloperskiej klienta) do tej sieci wirtualnej.

Aby dołączyć do dodatkowych maszyn do sieci wirtualnej, należy najpierw utworzyć sieć wirtualną, a następnie określić ją podczas tworzenia klastra HDInsight. Aby uzyskać więcej informacji, zobacz [Planowanie sieci wirtualnej dla usługi HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="public-ports"></a>Porty publiczne

Wszystkie węzły w klastrze HDInsight znajdują się w sieci wirtualnej platformy Azure i nie można uzyskać do nich bezpośredniego dostępu z Internetu. Brama publiczna zapewnia dostęp do Internetu do następujących portów, które są wspólne dla wszystkich typów klastrów HDInsight.

| Usługa | Port | Protocol (Protokół) | Opis |
| --- | --- | --- | --- |
| Sshd |22 |Protokół SSH |Łączy klientów z sshd na głównym głównym. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Sshd |22 |Protokół SSH |Łączy klientów z sshd w węźle krawędzi. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Sshd |23 |Protokół SSH |Łączy klientów z sshd na drugorzędnym centrali. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Ambari |443 |HTTPS |Interfejs użytkownika sieci Ambari. Zobacz [Zarządzanie programem HDInsight przy użyciu interfejsu użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |Ambari REST API. Zobacz [Zarządzanie programem HDInsight za pomocą interfejsu API Apache Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |Interfejs API ODC. Zobacz [Korzystanie z mapNawiększanie z curl](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| Serwer HiveServer2 |443 |ODBC |Łączy się z gałęzi za pomocą ODBC. Zobacz [Łączenie programu Excel z programem HDInsight ze sterownikiem Microsoft ODBC](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| Serwer HiveServer2 |443 |JDBC |Łączy się z ApacheHive za pomocą JDBC. Zobacz [Łączenie się z gałęzią apache w programie HDInsight przy użyciu sterownika Hive JDBC](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

Dla określonych typów klastrów dostępne są następujące elementy:

| Usługa | Port | Protocol (Protokół) | Typ klastra | Opis |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |HBase |Interfejs API REST bazy HBase. Zobacz [Wprowadzenie do korzystania z apache HBase](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |platforma Spark |Interfejs API spark REST. Zobacz [Przekazywanie zadań Apache Spark zdalnie przy użyciu Apache Livy](spark/apache-spark-livy-rest-interface.md) |
| Serwer Spark Thrift |443 |HTTPS |platforma Spark |Serwer Spark Thrift używany do przesyłania zapytań hive. Zobacz [Korzystanie z beeline z ula Apache na HDInsight](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |Interfejs użytkownika sieci Storm. Zobacz [Wdrażanie i zarządzanie topologią apache Storm w programie HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md) |
| Kafka Rest proxy |443 |HTTPS |Kafka |Interfejs API REST platformy Kafka. Zobacz [Interakcja z klastrami platformy Apache w usłudze Azure HDInsight przy użyciu serwera proxy REST](kafka/rest-proxy.md) |

### <a name="authentication"></a>Uwierzytelnianie

Wszystkie usługi publicznie udostępniane w Internecie muszą być uwierzytelnione:

| Port | Poświadczenia |
| --- | --- |
| 22 lub 23 |Poświadczenia użytkownika SSH określone podczas tworzenia klastra |
| 443 |Nazwa logowania (domyślnie: administrator) i hasło ustawione podczas tworzenia klastra |

## <a name="non-public-ports"></a>Porty niepubliczne

> [!NOTE]  
> Niektóre usługi są dostępne tylko dla określonych typów klastra. Na przykład HBase jest dostępna tylko w typach klastra HBase.

> [!IMPORTANT]  
> Niektóre usługi działają tylko na jednym headnode na raz. Jeśli spróbujesz połączyć się z usługą na głównym głównąno i otrzymasz błąd, ponów próbę użycia pomocniczego pliku głównego.

### <a name="ambari"></a>Ambari

| Usługa | Węzły | Port | Ścieżka adresu URL | Protocol (Protokół) |
| --- | --- | --- | --- | --- |
| Interfejs użytkownika sieci Ambari | Węzły głowy | 8080 | / | HTTP |
| Ambari REST API | Węzły głowy | 8080 | /api/v1 | HTTP |

Przykłady:

* Ambari REST API:`curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>Porty HDFS

| Usługa | Węzły | Port | Protocol (Protokół) | Opis |
| --- | --- | --- | --- | --- |
| Interfejs użytkownika sieci NameNode web |Węzły głowy |30070 |HTTPS |Interfejs użytkownika sieci Web do wyświetlania stanu |
| NameNode usługa metadanych |węzły głowy |8020 |Ipc |Metadane systemu plików |
| DataNode (Nie ma) |Wszystkie węzły procesu roboczego |30075 |HTTPS |Interfejs użytkownika sieci Web do wyświetlania stanu, dzienników itp. |
| DataNode (Nie ma) |Wszystkie węzły procesu roboczego |30010 |&nbsp; |Transfer danych |
| DataNode (Nie ma) |Wszystkie węzły procesu roboczego |30020 |Ipc |Operacje metadanych |
| Nazwa pomocniczaNode |Węzły głowy |50090 |HTTP |Punkt kontrolny dla metadanych NameNode |

### <a name="yarn-ports"></a>Porty YARN

| Usługa | Węzły | Port | Protocol (Protokół) | Opis |
| --- | --- | --- | --- | --- |
| Interfejs użytkownika sieci Web Menedżera zasobów |Węzły głowy |8088 |HTTP |Interfejs użytkownika sieci Web dla Menedżera zasobów |
| Interfejs użytkownika sieci Web Menedżera zasobów |Węzły głowy |8090 |HTTPS |Interfejs użytkownika sieci Web dla Menedżera zasobów |
| Interfejs administratora Menedżera zasobów |węzły głowy |8141 |Ipc |Do składania wniosków (Hive, Hive server, Pig, itp.) |
| Harmonogram Menedżera zasobów |węzły głowy |8030 |HTTP |Interfejs administracyjny |
| Interfejs aplikacji Menedżera zasobów |węzły głowy |8050 |HTTP |Adres interfejsu menedżera aplikacji |
| Menedżer węzłów |Wszystkie węzły procesu roboczego |30050 |&nbsp; |Adres menedżera kontenerów |
| Interfejs użytkownika sieci Web nodemanager |Wszystkie węzły procesu roboczego |30060 |HTTP |Interfejs Menedżera zasobów |
| Adres osi czasu |Węzły głowy |10200 |Zdalne wywołanie procedury |Usługa RPC usługi osi czasu. |
| Interfejs użytkownika sieci Web osi czasu |Węzły głowy |8188 |HTTP |Interfejs użytkownika usługi osi czasu w sieci Web |

### <a name="hive-ports"></a>Porty hive

| Usługa | Węzły | Port | Protocol (Protokół) | Opis |
| --- | --- | --- | --- | --- |
| Serwer HiveServer2 |Węzły głowy |10001 |Oszczędności |Usługa łączenia się z hive (Thrift/JDBC) |
| Magazyn metadanych Hive |Węzły głowy |9083 |Oszczędności |Usługa łączenia się z metadanymi hive (Thrift/JDBC) |

### <a name="webhcat-ports"></a>Porty WebHCat

| Usługa | Węzły | Port | Protocol (Protokół) | Opis |
| --- | --- | --- | --- | --- |
| Serwer WebHCat |Węzły głowy |30111 |HTTP |Internetowy INTERFEJS API na szczycie HCatalog i innych usług Hadoop |

### <a name="mapreduce-ports"></a>Porty MapReduce

| Usługa | Węzły | Port | Protocol (Protokół) | Opis |
| --- | --- | --- | --- | --- |
| JobHistory (Historia zadania) |Węzły głowy |19888 |HTTP |Interfejs użytkownika sieci Web MapReduce JobHistory |
| JobHistory (Historia zadania) |Węzły głowy |10020 |&nbsp; |Serwer MapReduce JobHistory |
| ShuffleHandler ( ShuffleHandler ) |&nbsp; |13562 |&nbsp; |Przenosi pośrednie dane wyjściowe mapy do żądających reduktorów |

### <a name="oozie"></a>Oozie

| Usługa | Węzły | Port | Protocol (Protokół) | Opis |
| --- | --- | --- | --- | --- |
| Serwer Oozie |Węzły głowy |11000 |HTTP |Adres URL usługi Oozie |
| Serwer Oozie |Węzły głowy |11001 |HTTP |Port dla administratora Oozie |

### <a name="ambari-metrics"></a>Metryki systemu Ambari

| Usługa | Węzły | Port | Protocol (Protokół) | Opis |
| --- | --- | --- | --- | --- |
| Linia czasu (historia aplikacji) |Węzły głowy |6188 |HTTP |Interfejs użytkownika usługi TimeLine w sieci Web |
| Linia czasu (historia aplikacji) |Węzły głowy |30200 |Zdalne wywołanie procedury |Interfejs użytkownika usługi TimeLine w sieci Web |

### <a name="hbase-ports"></a>Porty HBase

| Usługa | Węzły | Port | Protocol (Protokół) | Opis |
| --- | --- | --- | --- | --- |
| Okręg wyborczy HMaster |Węzły głowy |16000 |&nbsp; |&nbsp; |
| HMaster info Web UI |Węzły głowy |16010 |HTTP |Port interfejsu użytkownika sieci Web wzorca bazy danych |
| Serwer regionu |Wszystkie węzły procesu roboczego |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |Port używany przez klientów do łączenia się z urządzeniem ZooKeeper |

### <a name="kafka-ports"></a>Porty Kafka

| Usługa | Węzły | Port | Protocol (Protokół) | Opis |
| --- | --- | --- | --- | --- |
| Broker |Węzły procesu roboczego |9092 |[Protokół Kafka Wire](https://kafka.apache.org/protocol.html) |Używany do komunikacji z klientem |
| &nbsp; |Węzły zookeeper |2181 |&nbsp; |Port używany przez klientów do łączenia się z zookeeperem |
| Serwer proxy REST | Węzły zarządzania platformą Kafka |9400 |HTTPS |[Specyfikacja Kafka REST](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/) |

### <a name="spark-ports"></a>Porty Iskry

| Usługa | Węzły | Port | Protocol (Protokół) | Ścieżka adresu URL | Opis |
| --- | --- | --- | --- | --- | --- |
| Serwery Spark Thrift |Węzły głowy |10002 |Oszczędności | &nbsp; | Usługa łączenia się z Spark SQL (Thrift/JDBC) |
| Serwer Livy | Węzły głowy | 8998 | HTTP | &nbsp; | Usługa uruchamiania instrukcji, zadań i aplikacji |
| Notes Jupyter | Węzły głowy | 8001 | HTTP | &nbsp; | Witryna internetowa firmy Jupyter notebook |

Przykłady:

* Livy: `curl -u admin -G "http://10.0.0.11:8998/"`. W tym `10.0.0.11` przykładzie jest adres IP headnode, który obsługuje usługę Livy.
