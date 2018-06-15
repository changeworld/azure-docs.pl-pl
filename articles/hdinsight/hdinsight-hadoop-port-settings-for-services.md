---
title: Porty używane przez usługi Hadoop w usłudze HDInsight - Azure | Dokumentacja firmy Microsoft
description: Lista portów używanych przez usługi Hadoop działające w usłudze HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: dd14aed9-ec25-4bb3-a20c-e29562735a7d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: larryfr
ms.openlocfilehash: 4490ac9bccb406bd2e882fc8afcbaf05aa8ddfab
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31797318"
---
# <a name="ports-used-by-hadoop-services-on-hdinsight"></a>Porty używane przez usługi Hadoop w usłudze HDInsight

Ten dokument zawiera listę portów używane przez usługi Hadoop uruchamianych w klastrach usługi HDInsight opartej na systemie Linux. Zawiera także informacje o portach używanych do nawiązania połączenia z klastrem przy użyciu protokołu SSH.

## <a name="public-ports-vs-non-public-ports"></a>Porty publiczny a niepublicznych portów

Klastry usługi HDInsight opartej na systemie Linux udostępniają tylko trzy porty publicznie w Internecie. 22, 23 i 443. Te porty są używane do bezpiecznego dostępu do klastra przy użyciu protokołu SSH i usługi udostępniane za pośrednictwem bezpiecznego protokołu HTTPS.

Wewnętrznie, HDInsight jest implementowany przez kilka maszyn wirtualnych platformy Azure (węzłów w klastrze) uruchomione na sieć wirtualną platformy Azure. W ramach sieci wirtualnej, a dostępne porty nie są widoczne w Internecie. Na przykład jeśli łączysz się do jednego z węzłów głównych przy użyciu protokołu SSH, z węzła głównego można następnie bezpośrednio uzyskać dostęp usługi uruchomione w węzłach klastra.

> [!IMPORTANT]
> Nie określaj sieci wirtualnej platformy Azure jako opcji konfiguracji dla usługi HDInsight, co jest tworzone automatycznie. Jednak inne maszyny (np. inne maszyny wirtualne Azure lub komputerze klienckim Programowanie) nie można połączyć z tą siecią wirtualną.


Aby przyłączyć dodatkowe maszyny do sieci wirtualnej, musi najpierw utworzyć sieci wirtualnej, a następnie określ, podczas tworzenia klastra usługi HDInsight. Aby uzyskać więcej informacji, zobacz [możliwości rozszerzania HDInsight przy użyciu sieci wirtualnej platformy Azure](hdinsight-extend-hadoop-virtual-network.md)

## <a name="public-ports"></a>Porty publiczny

Wszystkie węzły w klastrze HDInsight znajdują się w sieci wirtualnej platformy Azure, a nie są bezpośrednio dostępne z Internetu. Publiczny brama zapewnia dostęp do Internetu dla następujących portów, które są wspólne dla wszystkich typów klastra usługi HDInsight.

| Usługa | Port | Protokół | Opis |
| --- | --- | --- | --- | --- |
| sshd |22 |Protokół SSH |Łączy się sshd na głównej headnode z klientów. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |22 |Protokół SSH |Łączy klientów sshd węzła krawędzi. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |23 |Protokół SSH |Łączy się sshd na dodatkowej headnode z klientów. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Ambari |443 |HTTPS |Ambari web UI. Zobacz [HDInsight zarządzać za pomocą interfejsu użytkownika sieci Web Ambari](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |Interfejs API REST Ambari. Zobacz [Zarządzanie HDInsight przy użyciu interfejsu API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |HCatalog interfejsu API REST. Zobacz [korzystanie z programu Hive z Curl](hadoop/apache-hadoop-use-pig-curl.md), [korzystanie z języka Pig z Curl](hadoop/apache-hadoop-use-pig-curl.md), [korzystać z usługi MapReduce z Curl](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |Nawiązanie połączenia Hive za pośrednictwem sterownika ODBC. Zobacz [łączenie programu Excel do usługi HDInsight za pomocą sterownika Microsoft ODBC](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |Nawiązanie połączenia przy użyciu JDBC Hive. Zobacz [Połącz z Hive w usłudze HDInsight przy użyciu sterownik Hive JDBC](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

Poniżej przedstawiono dostępne dla określonego klastra typów:

| Usługa | Port | Protokół | Typ klastra | Opis |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |HBase |Interfejs API REST HBase. Zobacz [rozpocząć korzystanie z bazy danych HBase](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |platforma Spark |Interfejs API REST platformy Spark. Zobacz [Spark przesyłania zadania zdalnie przy użyciu programu Livy](spark/apache-spark-livy-rest-interface.md) |
| Serwera Spark Thrift |443 |HTTPS |platforma Spark |Używany do przesyłania zapytań programu Hive serwera Spark Thrift. Zobacz [Beeline za pomocą Hive w usłudze HDInsight](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |STORM interfejsu użytkownika sieci web. Zobacz [wdrażanie i zarządzanie topologiami Storm w usłudze HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md) |

### <a name="authentication"></a>Authentication

Wszystkie usługi udostępniane publicznie w Internecie, musi zostać uwierzytelniony:

| Port | Poświadczenia |
| --- | --- |
| 22 i 23 |Podane poświadczenia użytkownika SSH podczas tworzenia klastra |
| 443 |Nazwa logowania (domyślne: admin) i hasło, które zostały określone podczas tworzenia klastra |

## <a name="non-public-ports"></a>Niepubliczne portów

> [!NOTE]
> Niektóre usługi są dostępne tylko dla typów konkretnego klastra. Na przykład baza danych HBase jest dostępna tylko na typy klastrów HBase.

> [!IMPORTANT]
> Niektóre usługi uruchomić tylko jedną headnode naraz. Jeśli próbujesz połączyć się z usługą w głównej headnode i wystąpi błąd, spróbuj ponownie, używając headnode dodatkowej.

### <a name="ambari"></a>Ambari

| Usługa | Węzły | Port | Ścieżka adresu URL | Protokół | 
| --- | --- | --- | --- | --- |
| Interfejs użytkownika sieci web Ambari | HEAD węzłów | 8080 | / | HTTP |
| Interfejs API REST Ambari | HEAD węzłów | 8080 | / api/v1 | HTTP |

Przykłady:

* Interfejs API REST Ambari: `curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>System plików HDFS portów

| Usługa | Węzły | Port | Protokół | Opis |
| --- | --- | --- | --- | --- |
| NameNode interfejsu użytkownika sieci web |HEAD węzłów |30070 |HTTPS |Interfejs użytkownika, aby wyświetlić stan sieci Web |
| NameNode metadanych usługi |HEAD węzłów |8020 |IPC |Metadanych systemu plików |
| DataNode |Wszystkich węzłów procesu roboczego |30075 |HTTPS |Interfejs użytkownika sieci Web do stanu widoku, dzienniki itp. |
| DataNode |Wszystkich węzłów procesu roboczego |30010 |&nbsp; |Transfer danych |
| DataNode |Wszystkich węzłów procesu roboczego |30020 |IPC |Operacji na metadanych |
| NameNode dodatkowej |HEAD węzłów |50090 |HTTP |Punkt kontrolny NameNode metadanych |

### <a name="yarn-ports"></a>YARN portów

| Usługa | Węzły | Port | Protokół | Opis |
| --- | --- | --- | --- | --- |
| Menedżer zasobów interfejsu użytkownika sieci web |HEAD węzłów |8088 |HTTP |Interfejs użytkownika dla Menedżera zasobów sieci Web |
| Menedżer zasobów interfejsu użytkownika sieci web |HEAD węzłów |8090 |HTTPS |Interfejs użytkownika dla Menedżera zasobów sieci Web |
| Interfejs administracyjny Menedżera zasobów |HEAD węzłów |8141 |IPC |Dla aplikacji przesyłania (Hive, Pig, Hive serwera itp.) |
| Menedżer zasobów harmonogramu |HEAD węzłów |8030 |HTTP |Interfejs administracyjny |
| Interfejs aplikacji Menedżera zasobów |HEAD węzłów |8050 |HTTP |Adres interfejsu Menedżera aplikacji |
| NodeManager |Wszystkich węzłów procesu roboczego |30050 |&nbsp; |Adres menedżera kontenera |
| NodeManager interfejsu użytkownika sieci web |Wszystkich węzłów procesu roboczego |30060 |HTTP |Interfejs Menedżera zasobów |
| Adres osi czasu |HEAD węzłów |10200 |RPC |Usługa RPC usługi osi czasu. |
| Oś czasu interfejsu użytkownika sieci web |HEAD węzłów |8181 |HTTP |Interfejs użytkownika sieci web usługi osi czasu |

### <a name="hive-ports"></a>Porty gałęzi

| Usługa | Węzły | Port | Protokół | Opis |
| --- | --- | --- | --- | --- |
| HiveServer2 |HEAD węzłów |10001 |Thrift |Usługi w celu nawiązania gałąź (Thrift/JDBC) |
| Potrzeby magazynu metadanych hive |HEAD węzłów |9083 |Thrift |Usługi w celu nawiązania metadanych Hive (Thrift/JDBC) |

### <a name="webhcat-ports"></a>Porty WebHCat

| Usługa | Węzły | Port | Protokół | Opis |
| --- | --- | --- | --- | --- |
| Serwer usługi WebHCat |HEAD węzłów |30111 |HTTP |Interfejs API sieci Web na podstawie innych usług Hadoop i HCatalog |

### <a name="mapreduce-ports"></a>Porty MapReduce

| Usługa | Węzły | Port | Protokół | Opis |
| --- | --- | --- | --- | --- |
| JobHistory |HEAD węzłów |19888 |HTTP |MapReduce JobHistory interfejsu użytkownika sieci web |
| JobHistory |HEAD węzłów |10020 |&nbsp; |MapReduce JobHistory serwera |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |Pośredni mapy transferów danych wyjściowych z żądaniem reduktory |

### <a name="oozie"></a>Oozie

| Usługa | Węzły | Port | Protokół | Opis |
| --- | --- | --- | --- | --- |
| Oozie serwera |HEAD węzłów |11000 |HTTP |Adres URL usługi Oozie |
| Oozie serwera |HEAD węzłów |11001 |HTTP |Port programu Oozie administratora |

### <a name="ambari-metrics"></a>Metryki Ambari

| Usługa | Węzły | Port | Protokół | Opis |
| --- | --- | --- | --- | --- |
| Oś czasu (Historia aplikacji) |HEAD węzłów |6188 |HTTP |Interfejs użytkownika sieci web usługi osi czasu |
| Oś czasu (Historia aplikacji) |HEAD węzłów |30200 |RPC |Interfejs użytkownika sieci web usługi osi czasu |

### <a name="hbase-ports"></a>Porty HBase

| Usługa | Węzły | Port | Protokół | Opis |
| --- | --- | --- | --- | --- |
| HMaster |HEAD węzłów |16000 |&nbsp; |&nbsp; |
| HMaster informacje o interfejsie użytkownika sieci Web |HEAD węzłów |16010 |HTTP |Numer portu interfejsu użytkownika sieci web głównego HBase |
| Region serwera |Wszystkich węzłów procesu roboczego |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |Portu używanego przez klientów do nawiązania połączenia dozorcy |

### <a name="kafka-ports"></a>Porty Kafka

| Usługa | Węzły | Port | Protokół | Opis |
| --- | --- | --- | --- | --- |
| Broker |Węzłów procesu roboczego |9092 |[Kafka Wire Protocol](http://kafka.apache.org/protocol.html) |Używany do komunikacji z klientem |
| &nbsp; |Węzły dozorcy |2181 |&nbsp; |Portu używanego przez klientów do nawiązania połączenia dozorcy |

### <a name="spark-ports"></a>Porty Spark

| Usługa | Węzły | Port | Protokół | Ścieżka adresu URL | Opis |
| --- | --- | --- | --- | --- | --- |
| Serwery Spark Thrift |HEAD węzłów |10002 |Thrift | &nbsp; | Usługi w celu nawiązania Spark SQL (Thrift/JDBC) |
| Serwer programu Livy | HEAD węzłów | 8998 | HTTP | &nbsp; | Usługa uruchamiania instrukcje, zadania i aplikacji |
| Notesu Jupyter | HEAD węzłów | 8001 | HTTP | &nbsp; | Witryny sieci Web notesu Jupyter |

Przykłady:

* Livy: `curl -u admin -G "http://10.0.0.11:8998/"`. W tym przykładzie `10.0.0.11` jest adresem IP headnode, obsługującego usługę Livy.
