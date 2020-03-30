---
title: Rozwiązywanie problemów z usługą Storm przy użyciu usługi Azure HDInsight
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące korzystania z usługi Apache Storm w usłudze Azure HDInsight.
keywords: Usługa Azure HDInsight, burza, często zadawane pytania, przewodnik po rozwiązywaniu problemów, typowe problemy
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: b51b2c21fd9256c93f6947386a48336af2b75d88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271930"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Rozwiązywanie problemów z usługą Apache Storm przy użyciu usługi Azure HDInsight

Dowiedz się więcej o najważniejszych problemach i ich rozwiązaniach dotyczących pracy z ładunkami [Apache Storm](https://storm.apache.org/) w [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Jak uzyskać dostęp do interfejsu użytkownika burzy w klastrze?

Masz dwie opcje uzyskiwania dostępu do interfejsu użytkownika burzy z przeglądarki:

### <a name="apache-ambari-ui"></a>Interfejs apache Ambari

1. Przejdź do pulpitu nawigacyjnego Ambari.
2. Na liście usług wybierz pozycję **Burza**.
3. W menu **Szybkie łącza** wybierz polecenie **Interfejs burzy**.

### <a name="direct-link"></a>Bezpośredni link

Dostęp do interfejsu użytkownika burzy można uzyskać pod następującym adresem URL:

`https://<cluster DNS name>/stormui`

Przykład: `https://stormcluster.azurehdinsight.net/stormui`

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Jak przenieść informacje o punkcie kontrolnym centrum zdarzeń Storm z jednej topologii do drugiej?

Podczas opracowywania topologii, które są odczytywane z usługi Azure Event Hubs przy użyciu pliku .jar centrum zdarzeń usługi HDInsight Storm, należy wdrożyć topologię o tej samej nazwie w nowym klastrze. Jednak należy zachować dane punktu kontrolnego, który został zatwierdzony do [Apache ZooKeeper](https://zookeeper.apache.org/) na starym klastrze.

### <a name="where-checkpoint-data-is-stored"></a>Gdzie przechowywane są dane punktu kontrolnego

Dane punktu kontrolnego dla przesunięć są przechowywane przez wylewka centrum zdarzeń w ZooKeeper w dwóch ścieżkach głównych:

- Nietransakcyjne punkty kontrolne wylewki są przechowywane w `/eventhubspout`pliku .

- Dane punktu kontrolnego wylewki transakcyjnej są przechowywane w `/transactional`pliku .

### <a name="how-to-restore"></a>Jak przywrócić

Aby uzyskać skrypty i biblioteki używane do eksportowania danych z zookeeper, a następnie zaimportować dane z powrotem do ZooKeeper o nowej nazwie, zobacz [przykłady BURZY HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

Folder lib zawiera pliki jar, które zawierają implementację operacji eksportowania/importowania. Folder bash zawiera przykładowy skrypt, który pokazuje, jak wyeksportować dane z serwera ZooKeeper w starym klastrze, a następnie zaimportować go z powrotem do serwera ZooKeeper w nowym klastrze.

Uruchom skrypt [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) z węzłów ZooKeeper do wyeksportowania, a następnie zaimportuj dane. Zaktualizuj skrypt do poprawnej wersji platformy danych Hortonworks (HDP). (Pracujemy nad tym, aby te skrypty były ogólne w HDInsight. Skrypty ogólne można uruchamiać z dowolnego węzła w klastrze bez modyfikacji przez użytkownika.)

Polecenie eksportu zapisuje metadane do ścieżki apache Hadoop Distributed File System (HDFS) (w usłudze Azure Blob Storage lub Azure Data Lake Storage) w ustawionej lokalizacji.

### <a name="examples"></a>Przykłady

#### <a name="export-offset-metadata"></a>Eksportowanie metadanych przesunięcia

1. Użyj SSH, aby przejść do klastra ZooKeeper w klastrze, z którego musi zostać wyeksportowane przesunięcie punktu kontrolnego.
2. Uruchom następujące polecenie (po zaktualizowaniu ciągu wersji HDP), aby `/stormmetadta/zkdata` wyeksportować dane odsunięcia ZooKeeper do ścieżki HDFS:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Importowanie metadanych przesunięcia

1. Użyj SSH, aby przejść do klastra ZooKeeper w klastrze, z którego należy zaimportować przesunięcie punktu kontrolnego.
2. Uruchom następujące polecenie (po zaktualizowaniu ciągu wersji HDP), aby zaimportować dane odsunięcia Programu ZooKeeper ze ścieżki `/stormmetadata/zkdata` HDFS do serwera ZooKeeper w klastrze docelowym:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```

#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Usuń metadane offsetowe, aby topologie mogły rozpocząć przetwarzanie danych od początku lub z sygnatury czasowej, którą użytkownik wybierze

1. Użyj SSH, aby przejść do klastra ZooKeeper w klastrze, z którego należy usunąć przesunięcie punktu kontrolnego.
2. Uruchom następujące polecenie (po zaktualizowaniu ciągu wersji HDP), aby usunąć wszystkie dane odsunięcia Programu ZooKeeper w bieżącym klastrze:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Jak zlokalizować pliki binarne burzy w klastrze?

Pliki binarne burzowe dla `/usr/hdp/current/storm-client`bieżącego stosu HDP znajdują się w pliku . Lokalizacja jest taka sama zarówno dla węzłów głównego, jak i węzłów procesu roboczego.

Może istnieć wiele plików binarnych dla określonych wersji HDP `/usr/hdp/2.5.0.1233/storm`w /usr/hdp (na przykład ). Folder `/usr/hdp/current/storm-client` jest połączony z najnowszą wersją, która jest uruchomiona w klastrze.

Aby uzyskać więcej informacji, zobacz [Łączenie się z klastrem HDInsight przy użyciu funkcji SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) i [Apache Storm](https://storm.apache.org/).

## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Jak określić topologię wdrażania klastra Storm?

Najpierw należy zidentyfikować wszystkie składniki, które są zainstalowane z HDInsight Storm. Klaster Storm składa się z czterech kategorii węzłów:

* Węzły bramy
* Węzły głowy
* Węzły ZooKeeper
* Węzły procesu roboczego

### <a name="gateway-nodes"></a>Węzły bramy

Węzeł bramy jest usługą bramy i odwrotnego serwera proxy, która umożliwia publiczny dostęp do aktywnej usługi zarządzania Ambari. Zajmuje się również wyborami lidera Ambari.

### <a name="head-nodes"></a>Węzły głowy

Węzły head storm uruchamiają następujące usługi:
* Nimbus
* Serwer Ambari
* Serwer Ambari Metrics
* Ambari Metryk Collector
 
### <a name="zookeeper-nodes"></a>Węzły ZooKeeper

HDInsight jest wyposażony w trzywęzłowe kworum ZooKeeper. Rozmiar kworum jest stały i nie można go ponownie skonfigurować.

Usługi burzowe w klastrze są skonfigurowane do automatycznego używania kworum ZooKeeper.

### <a name="worker-nodes"></a>Węzły procesu roboczego

Węzły procesu roboczego burzy uruchamiają następujące usługi:
* Nadzorca
* Maszyny wirtualne języka Java (JVM) dla uruchamiania topologii
* Agent Ambari

## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Jak zlokalizować pliki binarne centrum zdarzeń Storm w celu opracowania?

Aby uzyskać więcej informacji na temat korzystania z plików .jar centrum zdarzeń burzy z topologią, zobacz następujące zasoby.

### <a name="java-based-topology"></a>Topologia oparta na języku Java

[Przetwarzanie zdarzeń z usługi Azure Event Hubs za pomocą usługi Apache Storm w usłudze HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>Topologia oparta na języku C #(Mono na klastrach Burzań 3.4+ Linux)

[Przetwarzanie zdarzeń z usługi Azure Event Hubs za pomocą usługi Apache Storm w programie HDInsight (C#)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)

### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Najnowsze pliki binarne centrum zdarzeń Apache Storm dla klastrów Burzak 3.5+ Linux

Aby dowiedzieć się, jak korzystać z najnowszej wylewki centrum zdarzeń Storm, która współpracuje z klastrami HDInsight 3.5+ Linux Storm, zobacz [plik readme mvn-repo](https://github.com/hdinsight/mvn-repo/blob/master/README.md).

### <a name="source-code-examples"></a>Przykłady kodu źródłowego

Zobacz [przykłady](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) sposobu odczytywania i pisania z usługi Azure Event Hub przy użyciu topologii usługi Apache Storm (napisanej w języku Java) w klastrze usługi Azure HDInsight.

## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>Jak zlokalizować pliki konfiguracyjne Storm Log4J 2 w klastrach?

Aby zidentyfikować [apache Log4j 2](https://logging.apache.org/log4j/2.x/) pliki konfiguracyjne dla usług Storm.

### <a name="on-head-nodes"></a>Na węzłach głowy

Konfiguracja Nimbus Log4J `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`jest odczytywana z .

### <a name="on-worker-nodes"></a>W węzłach procesu roboczego

Konfiguracja log4J nadzorcy jest odczytywana z `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`.

Plik konfiguracyjny log4J `/usr/hdp/\<HDP version>/storm/log4j2/worker.xml`procesu roboczego jest odczytywany z pliku .

Przykłady:`/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml`
`/usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml`

---

## <a name="not-a-leader-exception"></a>Nie jest to wyjątek od lidera

Podczas przesyłania topologii użytkownik może otrzymać komunikat o `Topology submission exception, cause not a leader, the current leader is NimbusInfo`błędzie podobny do: .

Aby rozwiązać problem, użytkownik może być konieczne złożyć bilet, aby węzły ponownie/ponownie uruchomić. Aby uzyskać więcej [https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html](https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html)informacji, zobacz .

---

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

- Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

- Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

- Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
