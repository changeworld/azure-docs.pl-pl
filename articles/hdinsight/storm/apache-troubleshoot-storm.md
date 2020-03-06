---
title: Rozwiązywanie problemów z systemu Storm przy użyciu usługi Azure HDInsight
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące korzystania z platformy Apache Storm przy użyciu usługi Azure HDInsight.
keywords: Usługa Azure HDInsight, Storm, często zadawane pytania, przewodnik, typowych problemów dotyczących rozwiązywania problemów
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: b51b2c21fd9256c93f6947386a48336af2b75d88
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395188"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Rozwiązywanie problemów z platformą Apache Storm przy użyciu usługi Azure HDInsight

Poznaj najważniejsze problemy i ich rozwiązania umożliwiające pracę z [Apache Storm](https://storm.apache.org/) ładunkiami w programie [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Jak uzyskać dostęp do interfejsu użytkownika platformy Storm w klastrze?

Masz dwie opcje do uzyskiwania dostępu do interfejsu użytkownika platformy Storm z przeglądarki:

### <a name="apache-ambari-ui"></a>Interfejs użytkownika Apache Ambari

1. Przejdź do pulpitu nawigacyjnego systemu Ambari.
2. Na liście usług wybierz pozycję **burza**.
3. W menu **szybkie linki** wybierz pozycję **interfejs użytkownika burzy**.

### <a name="direct-link"></a>Link bezpośredni

Aby uzyskać dostęp interfejsu użytkownika platformy Storm następującego adresu URL:

`https://<cluster DNS name>/stormui`

Przykład: `https://stormcluster.azurehdinsight.net/stormui`

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Jak przenieść informacji dotyczących punktu kontrolnego spout Centrum zdarzeń platformy Storm między do innej?

Podczas opracowywania topologii odczytujących z usługi Azure Event Hubs za pomocą Centrum zdarzeń HDInsight Storm elementu spout plik JAR, musisz wdrożyć topologię, która ma taką samą nazwę w nowym klastrze. Należy jednak zachować dane punktów kontrolnych, które zostały zatwierdzone do [Apache ZooKeeper](https://zookeeper.apache.org/) w starym klastrze.

### <a name="where-checkpoint-data-is-stored"></a>Gdzie są przechowywane dane punktu kontrolnego

Dane punktu kontrolnego dla przesunięcia jest przechowywany przez spout Centrum zdarzeń w dozorcy w dwie ścieżki katalogu głównego:

- Nietransakcyjne punkty kontrolne elementu Spout są przechowywane w `/eventhubspout`.

- Dane transakcyjnego punktu kontrolnego elementu Spout są przechowywane w `/transactional`.

### <a name="how-to-restore"></a>Jak przywrócić

Aby pobrać skrypty i biblioteki, których używasz do eksportowania danych z programu dozorcy, a następnie zaimportuj dane z powrotem do dozorcy z nową nazwą, zobacz [przykłady burzy](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0)w usłudze HDInsight.

Folder biblioteki zawiera pliki JAR, zawierające implementację dla operacji eksportu/importu. Bash folder zawiera przykładowy skrypt, który demonstruje sposób eksportowania danych z serwera dozorcy w starym klastrze, a następnie zaimportuj go ponownie do serwera dozorcy w nowym klastrze.

Uruchom skrypt [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) z węzłów dozorcy, aby wyeksportować i zaimportować dane. Zaktualizuj skrypt do poprawnej wersji Hortonworks Data Platform (HDP). (Pracujemy nad ogólnego w HDInsight te skrypty. Ogólny umożliwia uruchamianie skryptów z poziomu każdego węzła w klastrze bez modyfikacji przez użytkownika.)

Polecenie eksportu zapisuje metadane w ścieżce rozproszony system plików Apache Hadoop (HDFS) (na platformie Azure Blob Storage lub Azure Data Lake Storage) w ustawionej lokalizacji.

### <a name="examples"></a>Przykłady

#### <a name="export-offset-metadata"></a>Eksportowanie metadanych przesunięcia

1. Przejdź do klastra dozorcy w klastrze, z którego punkt kontrolny przesunięcie musi być eksportowane za pomocą protokołu SSH.
2. Uruchom następujące polecenie (po zaktualizowaniu ciągu wersji HDP), aby wyeksportować dane przesunięcia dozorcy do ścieżki `/stormmetadta/zkdata` HDFS:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Importuj metadane przesunięcia

1. Korzystanie z protokołu SSH, aby przejść do klastra dozorcy w klastrze, z którego punkt kontrolny przesunięcie musi zostać zaimportowane.
2. Uruchom następujące polecenie (po zaktualizowaniu ciągu wersji HDP), aby zaimportować dane przesunięcia dozorcy z ścieżki systemu plików HDFS `/stormmetadata/zkdata` do serwera dozorcy w klastrze docelowym:

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

Dane binarne burzy dla bieżącego stosu HDP znajdują się w `/usr/hdp/current/storm-client`. Lokalizacja jest taka sama zarówno węzły główne i węzły procesu roboczego.

Może istnieć wiele plików binarnych dla określonych wersji HDP w/usr/HDP (na przykład `/usr/hdp/2.5.0.1233/storm`). Folder `/usr/hdp/current/storm-client` jest symlinked do najnowszej wersji działającej w klastrze.

Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z klastrem usługi HDInsight przy użyciu protokołu SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) i [Apache Storm](https://storm.apache.org/).

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

[Przetwarzanie zdarzeń z usługi Azure Event Hubs przy użyciu Apache Storm w usłudze HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C# — na podstawie topologii (Mono w klastrach HDInsight 3.4 + Linux Storm)

[Przetwarzaj zdarzenia z usługi Azure Event Hubs przy użyciu Apache StormC#w usłudze HDInsight ()](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)

### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Pliki binarne dla klastrów HDInsight 3.5 + Linux Storm elementów spout Centrum zdarzeń w usłudze najnowsze platformy Apache Storm

Aby dowiedzieć się, jak używać najnowszej elementu Spout centrum zdarzeń burzy, które współpracuje z klastrami burzy usługi HDInsight 3.5 + Linux, zobacz [plik Readme MVN-repo](https://github.com/hdinsight/mvn-repo/blob/master/README.md).

### <a name="source-code-examples"></a>Przykłady kodu źródłowego

Zapoznaj się z [przykładami](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) dotyczącymi odczytywania i zapisywania z usługi Azure Event Hub przy użyciu topologii Apache Storm (napisanych w języku Java) w klastrze usługi Azure HDInsight.

## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>Jak znaleźć pliki konfiguracji 2 mechanizmu Log4J systemu Storm w klastrach?

Aby zidentyfikować pliki konfiguracji [Apache Log4J 2](https://logging.apache.org/log4j/2.x/) dla usług burzy.

### <a name="on-head-nodes"></a>Na węzły główne

Konfiguracja Nimbus Log4J jest odczytana z `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`.

### <a name="on-worker-nodes"></a>Na węzłach procesu roboczego

Konfiguracja Log4J opiekuna jest odczytywana z `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml`.

Plik konfiguracji Log4J procesu roboczego jest odczytywany z `/usr/hdp/\<HDP version>/storm/log4j2/worker.xml`.

Przykłady: `/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml`
`/usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml`

---

## <a name="not-a-leader-exception"></a>To nie jest wyjątek lidera

Podczas przesyłania topologii użytkownik może otrzymać komunikat o błędzie podobny do: `Topology submission exception, cause not a leader, the current leader is NimbusInfo`.

Aby rozwiązać ten problem, użytkownik może potrzebować zaplikować bilet w celu ponownego uruchomienia lub ponownego uruchomienia węzłów. Aby uzyskać więcej informacji, zobacz [https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html](https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html).

---

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

- Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

- Połącz się z [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego Microsoft Azure konta, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

- Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
