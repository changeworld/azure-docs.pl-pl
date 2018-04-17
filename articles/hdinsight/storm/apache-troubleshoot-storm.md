---
title: Rozwiązywanie problemów z Storm przy użyciu usługi Azure HDInsight | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące przy użyciu platformy Apache Storm w usłudze Azure HDInsight.
keywords: Azure HDInsight, Storm, często zadawane pytania, przewodnik, typowe problemy rozwiązywania problemów
services: Azure HDInsight
documentationcenter: na
author: raviperi
manager: ''
editor: ''
ms.assetid: 74E51183-3EF4-4C67-AA60-6E12FAC999B5
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: raviperi
ms.openlocfilehash: 46f07a1512435fd8ad5cae4df1858f948fe017e1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="troubleshoot-storm-by-using-azure-hdinsight"></a>Rozwiązywanie problemów z Storm przy użyciu usługi Azure HDInsight

Więcej informacji na temat Najważniejsze problemy i ich rozwiązania do pracy z ładunków Apache Storm w Apache Ambari.

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Jak uzyskać dostęp do interfejsu użytkownika platformy Storm w klastrze?
Masz dwie opcje do uzyskiwania dostępu do interfejsu użytkownika platformy Storm w przeglądarce:

### <a name="ambari-ui"></a>Interfejs użytkownika narzędzia Ambari
1. Przejdź do pulpitu nawigacyjnego narzędzia Ambari.
2. Na liście usług wybierz **Storm**.
3. W **szybkie linki** menu, wybierz opcję **interfejsu użytkownika platformy Storm**.

### <a name="direct-link"></a>Link bezpośredni
Aby dostęp do interfejsu użytkownika platformy Storm z następującego adresu URL:

https://\<nazwę klastra DNS\>/stormui

Przykład:

 https://stormcluster.azurehdinsight.net/stormui

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Jak przenieść informacje dotyczące punktu kontrolnego spout Centrum zdarzeń Storm od jednego topologii do innego

Podczas opracowywania topologii, które zapoznały się z usługi Azure Event Hubs przy użyciu Centrum zdarzeń HDInsight Storm spout pliku JAR, należy wdrożyć topologię, która ma taką samą nazwę w nowym klastrze. Należy jednak przechowywać dane punktu kontrolnego, które zostało przekazane do Apache ZooKeeper w starym klastrze.

### <a name="where-checkpoint-data-is-stored"></a>Gdzie są przechowywane dane punktu kontrolnego
Dane punktu kontrolnego dla przesunięcia jest przechowywany przez spout Centrum zdarzeń w dozorcy w dwie ścieżki katalogu głównego:
- Punkty kontrolne spout nietransakcyjne są przechowywane w /eventhubspout.
- Dane punktu kontrolnego transakcyjne spout jest przechowywany / transakcyjnych.

### <a name="how-to-restore"></a>Jak przywrócić
Aby uzyskać skrypty i biblioteki, które umożliwia eksportowanie danych poza dozorcy, a następnie zaimportować dane z powrotem do dozorcy pod nową nazwą, zobacz [przykłady HDInsight Storm](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

Lib znajduje się w nim pliki JAR, które zawierają implementację dla operacji importu/eksportu. Bash folder zawiera przykładowy skrypt, który demonstruje sposób eksportowania danych z serwera dozorcy w starym klastrze, a następnie zaimportować go z powrotem do serwera dozorcy w nowym klastrze.

Uruchom [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) skryptu z węzły dozorcy do wyeksportowania, a następnie zaimportować dane. Zaktualizuj skrypt do prawidłowej wersji Hortonworks Data Platform (HDP). (Pracujemy nad wprowadzeniem tych skryptów ogólnego w usłudze HDInsight. Ogólny skrypty można uruchomić z dowolnego węzła w klastrze bez modyfikacji przez użytkownika.)

Polecenie export zapisuje metadanych na ścieżkę Apache Hadoop Distributed plików systemowych (HDFS) (w sklepie z magazynu obiektów Blob Azure lub usługi Azure Data Lake Store) w lokalizacji, które można ustawić.

### <a name="examples"></a>Przykłady

#### <a name="export-offset-metadata"></a>Eksportowanie metadanych przesunięcia
1. Używanie protokołu SSH, aby przejść do dozorcy klastra w klastrze, w którym punktu kontrolnego przesunięcie musi być eksportowany.
2. Uruchom następujące polecenie (po zaktualizowaniu ciąg wersji HDP), aby wyeksportować dane przesunięcia dozorcy do ścieżki systemu plików HDFS /stormmetadta/zkdata:

    ```apache   
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Importuj metadane przesunięcia
1. Używanie protokołu SSH, aby przejść do dozorcy klastra w klastrze, z którego punkt kontrolny przesunięcia musi zostać zaimportowane.
2. Uruchom następujące polecenie (po zaktualizowaniu ciąg wersji HDP) do importowania danych przesunięcia dozorcy z /stormmetadata/zkdata ścieżki systemu plików HDFS do serwera dozorcy w klastrze docelowym:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```
   
#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Usuwanie metadanych przesunięcia tak, aby uruchomić topologie przetwarzania danych z początku lub sygnatury czasowej przez użytkownika
1. Używanie protokołu SSH, aby przejść do dozorcy klastra w klastrze, w którym punktu kontrolnego przesunięcia musi zostać usunięte.
2. Uruchom następujące polecenie (po zaktualizowaniu ciąg wersji HDP), aby usunąć wszystkie dane przesunięcia dozorcy w bieżący klaster:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Jak znaleźć plików binarnych systemu Storm w klastrze?
Pliki binarne STORM dla bieżącego stosu HDP znajdują się w /usr/hdp/current/storm-client. Lokalizacja jest taka sama dla węzłów głównych i węzłów procesu roboczego.
 
Może istnieć wiele plików binarnych dla określonej wersji HDP w /usr/hdp (na przykład /usr/hdp/2.5.0.1233/storm). /Usr/hdp/current/storm-client folder jest symlinked do najnowszej wersji, która działa w klastrze.

Aby uzyskać więcej informacji, zobacz [Połącz z klastrem usługi HDInsight przy użyciu protokołu SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) i [Storm](http://storm.apache.org/).
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Określanie topologii wdrożenia klastra Storm?
Ustalenie wszystkie składniki, które są instalowane z HDInsight Storm. Klaster Storm składa się z czterech węzłów kategorii:

* Węzły bramy
* HEAD węzłów
* Węzły dozorcy
* Węzłów procesu roboczego
 
### <a name="gateway-nodes"></a>Węzły bramy
Węzeł bramy ma bramy i usłudze zwrotnego serwera proxy, która pozwala na publiczny dostęp do usługi zarządzania usługą active Ambari. Obsługuje ona również Ambari wiodące wyborów.
 
### <a name="head-nodes"></a>HEAD węzłów
Głównymi węzłami STORM uruchomienia następujących usług:
* Nimbus
* Serwer Ambari
* Metryki Ambari serwera
* Moduł zbierający metryki Ambari
 
### <a name="zookeeper-nodes"></a>Węzły dozorcy
Usługa HDInsight obejmuje trzy dozorcy kworum. Rozmiar kworum jest stała i nie można ponownie skonfigurować.
 
Usługi STORM w klastrze są skonfigurowane automatyczne stosowanie dozorcy kworum.
 
### <a name="worker-nodes"></a>Węzłów procesu roboczego
STORM węzłów procesu roboczego uruchomienia następujących usług:
* Nadzorca
* Proces roboczy języka Java maszyn wirtualnych (JVMs), do uruchamiania topologii
* Ambari agent
 
## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Jak znaleźć plików binarnych spout Centrum zdarzeń Storm do tworzenia aplikacji?
 
Aby uzyskać więcej informacji o korzystaniu z topologii Storm — pliki JAR spout Centrum zdarzeń zobacz następujące zasoby.
 
### <a name="java-based-topology"></a>Topologia opartych na języku Java
[Process events from Azure Event Hubs with Storm on HDInsight (Java)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology) (Przetwarzanie zdarzeń usługi Azure Event Hubs przy użyciu systemu Storm w usłudze HDInsight — Java)
 
### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C# — na podstawie topologii (Mono w klastrach HDInsight 3.4 + Linux Storm)
[Process events from Azure Event Hubs with Storm on HDInsight (C#)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology) (Przetwarzanie zdarzeń usługi Azure Event Hubs przy użyciu systemu Storm w usłudze HDInsight — C#)
 
### <a name="latest-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Pliki binarne w przypadku klastrów Linux Storm HDInsight 3.5 + elementów spout Centrum zdarzeń Storm najnowsze
Aby dowiedzieć się, jak używać najnowszej współpracujące z klastrami HDInsight 3.5 + Linux Storm spout Centrum zdarzeń Storm, zobacz repozytorium mvn [plik readme](https://github.com/hdinsight/mvn-repo/blob/master/README.md).
 
### <a name="source-code-examples"></a>Przykłady kodu źródłowego
Zobacz [przykłady](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) sposobu odczytywania i zapisywania z Centrum zdarzeń platformy Azure przy użyciu topologii Apache Storm (napisany w języku Java) w klastrze usługi HDInsight Azure.
 
## <a name="how-do-i-locate-storm-log4j-configuration-files-on-clusters"></a>Jak znaleźć pliki konfiguracji narzędzia Log4J Storm w klastrze?
 
Aby zidentyfikować pliki konfiguracji usługi Storm Apache Log4J.
 
### <a name="on-head-nodes"></a>W węzłach głównych
Konfiguracja narzędzia Log4J Nimbus są odczytywane z usr/hdp/\<wersji HDP\>/storm/log4j2/cluster.xml.
 
### <a name="on-worker-nodes"></a>Na węzłów procesu roboczego
Konfiguracja przełożonego Log4J są odczytywane z usr/hdp/\<wersji HDP\>/storm/log4j2/cluster.xml.
 
Plik konfiguracji procesu roboczego Log4J są odczytywane z usr/hdp/\<wersji HDP\>/storm/log4j2/worker.xml.
 
Przykłady: /usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml

### <a name="see-also"></a>Zobacz też
[Rozwiązywanie problemów przy użyciu usługi Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)
