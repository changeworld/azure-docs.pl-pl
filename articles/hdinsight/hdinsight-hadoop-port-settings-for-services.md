---
title: Porty używane przez usługi Hadoop w HDInsight — Azure
description: Lista portów używanych przez usług Hadoop działających w HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: hrasheed
ms.openlocfilehash: 16041d2b7a971c9ba479c133261930b38d130792
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66252777"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>Porty używane przez usługi Apache Hadoop w HDInsight

Ten dokument zawiera listę portów używanych przez usługi Apache Hadoop w klastrach HDInsight opartych na systemie Linux. Zawiera również informacje dotyczące portów używanych do łączenia z klastrem przy użyciu protokołu SSH.

## <a name="public-ports-vs-non-public-ports"></a>Publicznych portach, a porty niepublicznych

Klastry HDInsight opartych na systemie Linux udostępniają tylko trzy porty publicznie w Internecie. 22 i 23, 443. Te porty są używane do bezpiecznego dostępu do klastra przy użyciu protokołu SSH i usług udostępnianych za pośrednictwem bezpiecznego protokołu HTTPS.

Wewnętrznie HDInsight jest implementowany przez kilka maszyn wirtualnych platformy Azure (węzłów w klastrze) uruchomiona w usłudze Azure Virtual Network. Z w ramach sieci wirtualnej są dostępne porty nie są udostępniane za pośrednictwem Internetu. Na przykład jeśli łączysz się do jednego z węzłów głównych przy użyciu protokołu SSH z węzłem głównym następnie bezpośrednio dostępne usługi uruchomione w węzłach klastra.

> [!IMPORTANT]  
> Jeśli nie określisz z siecią wirtualną platformy Azure jako opcji konfiguracji dla HDInsight, utworzony automatycznie. Jednak nie można dołączyć do innych komputerów (na przykład innych maszyn wirtualnych platformy Azure lub komputerze deweloperskim klienta) z tą siecią wirtualną.

Aby przyłączyć dodatkowe maszyny do sieci wirtualnej, należy najpierw utworzyć sieć wirtualną, a następnie je wprowadzić podczas tworzenia klastra usługi HDInsight. Aby uzyskać więcej informacji, zobacz [HDInsight rozszerzanie możliwości za pomocą usługi Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md)

## <a name="public-ports"></a>Porty publiczne

Wszystkie węzły w klastrze usługi HDInsight znajdują się w usłudze Azure Virtual Network, a nie są bezpośrednio dostępne z Internetu. Publiczne brama zapewnia dostęp do następujących portów, które są wspólne dla wszystkich typów klastrów HDInsight przez internet.

| Usługa | Port | Protocol | Opis |
| --- | --- | --- | --- |
| sshd |22 |Protokół SSH |Klientom nawiązanie połączenia sshd na podstawowym węzłem głównym. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |22 |Protokół SSH |Klientom nawiązanie połączenia sshd na węźle krawędzi. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |23 |Protokół SSH |Łączy się sshd na dodatkowy węzeł główny z klientów. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Ambari |443 |HTTPS |Ambari web UI. Zobacz [Zarządzanie HDInsight przy użyciu Interfejsu sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |Interfejs API REST Ambari. Zobacz [Zarządzanie HDInsight przy użyciu interfejsu API Apache Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |HCatalog interfejsu API REST. Zobacz [Apache Hive za pomocą programu Curl](hadoop/apache-hadoop-use-pig-curl.md), [Apache Pig za pomocą programu Curl](hadoop/apache-hadoop-use-pig-curl.md), [używanie technologii MapReduce z programu Curl](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |Nawiązywanie połączeń za pośrednictwem sterownika ODBC programu Hive. Zobacz [Connect Excel to HDInsight przy użyciu sterownika Microsoft ODBC](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |Nawiązanie połączenia przy użyciu sterownika JDBC ApacheHive. Zobacz [nawiązywanie połączenia z programu Apache Hive HDInsight przy użyciu sterownika JDBC technologii Hive](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

Poniżej przedstawiono dostępne dla określone typy klastrów:

| Usługa | Port | Protocol | Typ klastra | Opis |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |HBase |Interfejs API REST HBase. Zobacz [rozpoczęcie korzystania z bazy danych Apache HBase](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |platforma Spark |Interfejs API REST platformy Spark. Zobacz [zadania przesyłania Apache Spark zdalnie przy użyciu programu Livy Apache](spark/apache-spark-livy-rest-interface.md) |
| Spark Thrift server |443 |HTTPS |platforma Spark |Serwer Spark Thrift, używane do przesyłania zapytań programu Hive. Zobacz [korzystanie z usługi Beeline z gałęzi Apache na HDInsight](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |STORM interfejsu użytkownika sieci web. Zobacz [wdrażanie i zarządzanie topologiami Apache Storm na HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md) |

### <a name="authentication"></a>Authentication

Wszystkie usługi publicznie dostępne w Internecie, musi zostać uwierzytelniony:

| Port | Poświadczenia |
| --- | --- |
| 22 i 23 |Określone podczas tworzenia klastra poświadczeń użytkownika SSH |
| 443 |Nazwa logowania (domyślne: admin) i hasło, które zostały ustawione podczas tworzenia klastra |

## <a name="non-public-ports"></a>Niepubliczne portów

> [!NOTE]  
> Niektóre usługi są dostępne tylko na określone typy klastrów. Na przykład baza danych HBase jest dostępna tylko dla typów klastra HBase.

> [!IMPORTANT]  
> Niektóre usługi działać tylko na jednym węzłem głównym w danym momencie. Jeśli spróbujesz nawiązać połączenie z usługą na podstawowym węzłem głównym i wystąpi błąd, spróbuj ponownie, używając dodatkowy węzeł główny.

### <a name="ambari"></a>Ambari

| Usługa | Węzły | Port | Ścieżka adresu URL | Protocol | 
| --- | --- | --- | --- | --- |
| Ambari web UI | Węzły główne | 8080 | / | HTTP |
| Interfejs API REST systemu Ambari | Węzły główne | 8080 | /api/v1 | HTTP |

Przykłady:

* Interfejs API REST Ambari: `curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>Porty systemu plików HDFS

| Usługa | Węzły | Port | Protocol | Opis |
| --- | --- | --- | --- | --- |
| NameNode interfejsu użytkownika sieci web |Węzły główne |30070 |HTTPS |Internetowy interfejs użytkownika, aby wyświetlić stan |
| NameNode metadanych usługi |Węzły główne |8020 |IPC |Metadanych systemu plików |
| DataNode |Wszystkich węzłów procesu roboczego |30075 |HTTPS |Interfejs użytkownika sieci Web do stanu widoku, dzienników itp. |
| DataNode |Wszystkich węzłów procesu roboczego |30010 |&nbsp; |Transfer danych |
| DataNode |Wszystkich węzłów procesu roboczego |30020 |IPC |Operacje dotyczące metadanych |
| Pomocniczy NameNode |Węzły główne |50090 |HTTP |Punkt kontrolny NameNode metadanych |

### <a name="yarn-ports"></a>Porty YARN

| Usługa | Węzły | Port | Protocol | Opis |
| --- | --- | --- | --- | --- |
| Interfejs użytkownika sieci web usługi Resource Manager |Węzły główne |8088 |HTTP |Internetowy interfejs użytkownika dla usługi Resource Manager |
| Interfejs użytkownika sieci web usługi Resource Manager |Węzły główne |8090 |HTTPS |Internetowy interfejs użytkownika dla usługi Resource Manager |
| Interfejs administracyjny usługi Resource Manager |Węzły główne |8141 |IPC |Dla przesyłania aplikacji (Hive, Hive server, Pig, itp.) |
| Harmonogram usługi Resource Manager |Węzły główne |8030 |HTTP |Interfejs administracyjny |
| Interfejs aplikacji usługi Resource Manager |Węzły główne |8050 |HTTP |Adres interfejsu Menedżera aplikacji |
| NodeManager |Wszystkich węzłów procesu roboczego |30050 |&nbsp; |Adres menedżera kontenera |
| NodeManager interfejsu użytkownika sieci web |Wszystkich węzłów procesu roboczego |30060 |HTTP |Interfejs Menedżera zasobów |
| Adres osi czasu |Węzły główne |10200 |RPC |Usługa RPC osi czasu usługi. |
| Interfejs użytkownika sieci web osi czasu |Węzły główne |8181 |HTTP |Interfejs użytkownika sieci web usługi osi czasu |

### <a name="hive-ports"></a>Porty programu hive

| Usługa | Węzły | Port | Protocol | Opis |
| --- | --- | --- | --- | --- |
| HiveServer2 |Węzły główne |10001 |Thrift |Usługa do łączenia się z programu Hive (Thrift/JDBC) |
| Magazyn metadanych Hive |Węzły główne |9083 |Thrift |Usługa do łączenia się z metadanych programu Hive (Thrift/JDBC) |

### <a name="webhcat-ports"></a>Porty usługi WebHCat

| Usługa | Węzły | Port | Protocol | Opis |
| --- | --- | --- | --- | --- |
| Serwer usługi WebHCat |Węzły główne |30111 |HTTP |Interfejs API sieci Web na podstawie HCatalog i inne usługi Hadoop |

### <a name="mapreduce-ports"></a>Porty MapReduce

| Usługa | Węzły | Port | Protocol | Opis |
| --- | --- | --- | --- | --- |
| JobHistory |Węzły główne |19888 |HTTP |Interfejs użytkownika sieci web MapReduce JobHistory |
| JobHistory |Węzły główne |10020 |&nbsp; |Serwer MapReduce JobHistory |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |Mapa pośrednich transferów danych wyjściowych z żądaniem reduktorów |

### <a name="oozie"></a>Oozie

| Usługa | Węzły | Port | Protocol | Opis |
| --- | --- | --- | --- | --- |
| Serwer programu Oozie |Węzły główne |11000 |HTTP |Adres URL dla usługi Oozie |
| Serwer programu Oozie |Węzły główne |11001 |HTTP |Port dla administratora programu Oozie |

### <a name="ambari-metrics"></a>Metryki systemu Ambari

| Usługa | Węzły | Port | Protocol | Opis |
| --- | --- | --- | --- | --- |
| Oś czasu (Historia aplikacji) |Węzły główne |6188 |HTTP |Interfejs użytkownika sieci web usługi osi czasu |
| Oś czasu (Historia aplikacji) |Węzły główne |30200 |RPC |Interfejs użytkownika sieci web usługi osi czasu |

### <a name="hbase-ports"></a>Porty bazy danych HBase

| Usługa | Węzły | Port | Protocol | Opis |
| --- | --- | --- | --- | --- |
| HMaster |Węzły główne |16000 |&nbsp; |&nbsp; |
| Informacje o serwera HMaster interfejs użytkownika sieci Web |Węzły główne |16010 |HTTP |Port dla interfejsu użytkownika sieci web głównego interfejsu użytkownika HBase |
| Serwer regionu |Wszystkich węzłów procesu roboczego |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |Port, którego klienci używają nawiązać dozorcy |

### <a name="kafka-ports"></a>Porty platformy Kafka

| Usługa | Węzły | Port | Protocol | Opis |
| --- | --- | --- | --- | --- |
| Broker |Węzły procesu roboczego |9092 |[Kafka Wire Protocol](https://kafka.apache.org/protocol.html) |Używany do komunikacji z klientem |
| &nbsp; |Węzły usługi Zookeeper |2181 |&nbsp; |Port, którego klienci używają nawiązać dozorcy |

### <a name="spark-ports"></a>Porty platformy Spark

| Usługa | Węzły | Port | Protocol | Ścieżka adresu URL | Opis |
| --- | --- | --- | --- | --- | --- |
| Serwery Spark Thrift |Węzły główne |10002 |Thrift | &nbsp; | Usługa do łączenia się z programu Spark SQL (Thrift/JDBC) |
| Serwer usługi Livy | Węzły główne | 8998 | HTTP | &nbsp; | Usługa do uruchamiania instrukcji, zadań i aplikacji |
| Notes Jupyter | Węzły główne | 8001 | HTTP | &nbsp; | Notes Jupyter witryny sieci Web |

Przykłady:

* Usługi Livy: `curl -u admin -G "http://10.0.0.11:8998/"`. W tym przykładzie `10.0.0.11` jest adres IP węzła głównego, który jest hostem usługi Livy.
