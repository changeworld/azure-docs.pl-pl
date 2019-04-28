---
title: Rozwiązywanie problemów z systemu Storm przy użyciu usługi Azure HDInsight
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące korzystania z platformy Apache Storm przy użyciu usługi Azure HDInsight.
keywords: Usługa Azure HDInsight, Storm, często zadawane pytania, przewodnik, typowych problemów dotyczących rozwiązywania problemów
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 3866e25cc3c87f569e84b2d5639d25aa9386cc78
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62116452"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Rozwiązywanie problemów z platformą Apache Storm przy użyciu usługi Azure HDInsight

Dowiedz się więcej o najważniejszych problemach i ich rozwiązania do pracy z [Apache Storm](https://storm.apache.org/) ładunków w [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Jak uzyskać dostęp do interfejsu użytkownika platformy Storm w klastrze?
Masz dwie opcje do uzyskiwania dostępu do interfejsu użytkownika platformy Storm z przeglądarki:

### <a name="ambari-ui"></a>Interfejs użytkownika systemu Ambari
1. Przejdź do pulpitu nawigacyjnego systemu Ambari.
2. Na liście usług wybierz **Storm**.
3. W **szybkich łączy** menu, wybierz opcję **interfejs użytkownika platformy Storm**.

### <a name="direct-link"></a>Link bezpośredni
Aby uzyskać dostęp interfejsu użytkownika platformy Storm następującego adresu URL:

https://\<nazwy DNS klastra\>/stormui

Przykład:

 https://stormcluster.azurehdinsight.net/stormui

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Jak przenieść informacji dotyczących punktu kontrolnego spout Centrum zdarzeń platformy Storm między do innej?

Podczas opracowywania topologii odczytujących z usługi Azure Event Hubs za pomocą Centrum zdarzeń HDInsight Storm elementu spout plik JAR, musisz wdrożyć topologię, która ma taką samą nazwę w nowym klastrze. Jednak użytkownik musi zachować dane punktu kontrolnego, która została zatwierdzona [Apache ZooKeeper](https://zookeeper.apache.org/) w starym klastrze.

### <a name="where-checkpoint-data-is-stored"></a>Gdzie są przechowywane dane punktu kontrolnego
Dane punktu kontrolnego dla przesunięcia jest przechowywany przez spout Centrum zdarzeń w dozorcy w dwie ścieżki katalogu głównego:
- Spout nietransakcyjnych w punkty kontrolne są przechowywane w /eventhubspout.
- Spout transakcyjnych dane punktu kontrolnego są przechowywane w pionie i w transakcji.

### <a name="how-to-restore"></a>Jak przywrócić
Aby uzyskać skrypty i bibliotek, których używasz do wyeksportowania danych poza dozorcy, a następnie zaimportować dane z powrotem do dozorcy pod nową nazwą, zobacz [przykłady HDInsight Storm](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

Folder biblioteki zawiera pliki JAR, zawierające implementację dla operacji eksportu/importu. Bash folder zawiera przykładowy skrypt, który demonstruje sposób eksportowania danych z serwera dozorcy w starym klastrze, a następnie zaimportuj go ponownie do serwera dozorcy w nowym klastrze.

Uruchom [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) skrypt z węzłów dozorcy w celu wyeksportowania i następnie zaimportowania danych. Zaktualizuj skrypt do poprawnej wersji Hortonworks Data Platform (HDP). (Pracujemy nad ogólnego w HDInsight te skrypty. Ogólny umożliwia uruchamianie skryptów z poziomu każdego węzła w klastrze bez modyfikacji przez użytkownika.)

Polecenie export zapisuje metadane do ścieżki Apache Hadoop Distributed pliku System (HDFS) (w usłudze Azure Blob Storage lub usługi Azure Data Lake Storage) w lokalizacji, który został ustawiony.

### <a name="examples"></a>Przykłady

#### <a name="export-offset-metadata"></a>Eksportowanie metadanych przesunięcia
1. Przejdź do klastra dozorcy w klastrze, z którego punkt kontrolny przesunięcie musi być eksportowane za pomocą protokołu SSH.
2. Uruchom następujące polecenie (po zaktualizowaniu ciąg wersji HDP), aby wyeksportować dane przesunięcia dozorcy do ścieżki systemu plików HDFS /stormmetadta/zkdata:

    ```apache   
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Importuj metadane przesunięcia
1. Korzystanie z protokołu SSH, aby przejść do klastra dozorcy w klastrze, z którego punkt kontrolny przesunięcie musi zostać zaimportowane.
2. Uruchom następujące polecenie (po zaktualizowaniu ciąg wersji HDP), do importowania danych przesunięcia dozorcy z /stormmetadata/zkdata ścieżka systemu plików HDFS do serwera dozorcy w klastrze docelowym:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```
   
#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Usuwanie metadanych przesunięcia, tak, aby uruchomić topologii przetwarzania danych z początkiem lub sygnaturę czasową, gdy użytkownik wybierze
1. Korzystanie z protokołu SSH, aby przejść do klastra dozorcy w klastrze, z którego punkt kontrolny przesunięcie musi zostać usunięta.
2. Uruchom następujące polecenie (po zaktualizowaniu ciąg wersji HDP), aby usunąć wszystkie dane przesunięcia dozorcy w bieżącym klastrze:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Jak znaleźć pliki binarne systemu Storm w klastrze?
Pliki binarne systemu STORM dla bieżącego stosu HDP znajdują się w /usr/hdp/current/storm-client. Lokalizacja jest taka sama zarówno węzły główne i węzły procesu roboczego.
 
Może istnieć wiele plików binarnych dla określonych wersji HDP w /usr/hdp (na przykład /usr/hdp/2.5.0.1233/storm). /Usr/hdp/current/storm-client folder jest symlinked do najnowszej wersji, która jest uruchomiona w klastrze.

Aby uzyskać więcej informacji, zobacz [łączenie z klastrem usługi HDInsight przy użyciu protokołu SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) i [Apache Storm](https://storm.apache.org/).
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Jak ustalić topologii wdrażania klastra Storm?
Najpierw Zidentyfikuj wszystkie składniki, które są zainstalowane w systemie HDInsight Storm. Klaster Storm składa się z czterech węzłów kategorii:

* Węzły bramy
* Węzły główne
* Węzły dozorcy
* Węzły procesu roboczego
 
### <a name="gateway-nodes"></a>Węzły bramy
Węzeł bramy jest bramy i usługa zwrotnego serwera proxy, która umożliwia dostęp publiczny do usługi zarządzania active Ambari. Obsługuje ona również wybór lidera Ambari.
 
### <a name="head-nodes"></a>Węzły główne
Węzły główne systemu STORM, uruchom następujące usługi:
* Nimbus
* Serwer Ambari
* Ambari metryk serwera
* Moduł zbierający metryk systemu Ambari
 
### <a name="zookeeper-nodes"></a>Węzły dozorcy
HDInsight jest dostarczany z trzema węzłami dozorcy kworum. Rozmiar kworum jest stała i nie można ponownie skonfigurować.
 
Usługi STORM w klastrze są skonfigurowane automatyczne stosowanie kworum dozorcy.
 
### <a name="worker-nodes"></a>Węzły procesu roboczego
Węzły procesu roboczego systemu STORM, uruchom następujące usługi:
* Nadzorca
* Proces roboczy maszyn wirtualnych Oracle Java (JVMs) na działające topologie
* Agent systemu Ambari
 
## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Jak znaleźć pliki binarne spout Centrum zdarzeń systemu Storm do tworzenia aplikacji?
 
Aby uzyskać więcej informacji o używaniu pliki JAR spout Centrum zdarzeń platformy Storm z topologii zobacz następujące zasoby.
 
### <a name="java-based-topology"></a>Topologii opartych na języku Java
[Przetwarzanie zdarzeń z usługi Azure Event Hubs przy użyciu platformy Apache Storm w HDInsight (Java)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology)
 
### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C# — na podstawie topologii (Mono w klastrach HDInsight 3.4 + Linux Storm)
[Przetwarzania zdarzeń pochodzących z usługi Azure Event Hubs przy użyciu platformy Apache Storm w HDInsight (C#)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)
 
### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Pliki binarne dla klastrów HDInsight 3.5 + Linux Storm elementów spout Centrum zdarzeń w usłudze najnowsze platformy Apache Storm
Aby dowiedzieć się, jak używać najnowszych współdziałającego z klastrów HDInsight 3.5 + Linux Storm spout Centrum zdarzeń platformy Storm, zobacz repozytorium mvn [pliku readme](https://github.com/hdinsight/mvn-repo/blob/master/README.md).
 
### <a name="source-code-examples"></a>Przykłady kodu źródłowego
Zobacz [przykłady](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) działania do odczytu i zapisu z usługi Azure Event Hub przy użyciu topologii Apache Storm (napisanego w języku Java) w klastrze usługi Azure HDInsight.
 
## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>Jak znaleźć pliki konfiguracji 2 mechanizmu Log4J systemu Storm w klastrach?
 
Aby zidentyfikować [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) pliki konfiguracji dla usługi Storm.
 
### <a name="on-head-nodes"></a>Na węzły główne
Konfiguracja mechanizmu Log4J Nimbus są odczytywane z/usr/hdp/\<wersji HDP\>/storm/log4j2/cluster.xml.
 
### <a name="on-worker-nodes"></a>Na węzłach procesu roboczego
Konfiguracja nadzorca Log4J są odczytywane z/usr/hdp/\<wersji HDP\>/storm/log4j2/cluster.xml.
 
Plik konfiguracyjny procesu roboczego Log4J są odczytywane z/usr/hdp/\<wersji HDP\>/storm/log4j2/worker.xml.
 
Przykłady: /usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml

### <a name="see-also"></a>Zobacz też
[Rozwiązywanie problemów przy użyciu usługi Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)
