---
title: Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight — najlepsze rozwiązania infrastruktury
description: Poznaj najlepsze rozwiązania infrastruktury dotyczące migrowania lokalnych klastrów Hadoop do usługi Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 6c57b62d63be55abc51b85327957afffa5dd3a42
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360201"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---infrastructure-best-practices"></a>Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight — najlepsze rozwiązania infrastruktury

Ten artykuł zawiera zalecenia dotyczące zarządzania infrastrukturą klastrów Azure HDInsight. Jest częścią serii, która zapewnia najlepsze rozwiązania w celu ułatwienia migrowania lokalnych systemów Apache Hadoop do usługi Azure HDInsight.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="plan-for-hdinsight-cluster-capacity"></a>Planowanie pojemności klastra HDInsight

Kluczowe wyborów Planowanie pojemności klastra HDInsight są następujące:

- **Wybierz region** — region platformy Azure Określa, gdzie fizycznie zainicjowaniu obsługi klastra. Aby zminimalizować opóźnienie operacji odczytu i zapisu, klaster musi należeć do tego samego regionu danych.
- **Wybierz lokalizację magazynu i rozmiar** — domyślny magazyn musi być w tym samym regionie co klaster. 48-węzeł klastra zaleca się mieć 4 do 8 kont magazynu. Mimo że może już być wystarczające, całkowita ilość miejsca, każde konto magazynu zapewnia dodatkową przepustowość sieci dla węzłów obliczeniowych. W przypadku wielu kont magazynu, należy użyć nazwy losowe dla każdego konta magazynu bez prefiksu. Celem losowych nazw jest zmniejszenie prawdopodobieństwo wąskich gardeł pamięci masowej (ograniczanie przepustowości) lub tryb typowych błędów dla wszystkich kont. Lepszą wydajność należy użyć tylko jednego kontenera na koncie magazynu.
- **Wybierz rozmiar maszyny Wirtualnej i typ (obsługuje teraz serii G)** — każdy typ klastra zawiera zbiór typy węzłów, a każdy typ węzła zawiera szczegółowe opcje ich rozmiar maszyny Wirtualnej i typu. Rozmiar maszyny Wirtualnej i typ jest określany przez wykorzystanie Procesora mocy obliczeniowej, pamięci RAM i opóźnienie sieci. Symulowane obciążenie może służyć do określenia optymalnego rozmiaru maszyny Wirtualnej i typ dla każdego typu węzła.
- **Wybierz liczbę węzłów procesu roboczego** — początkowa liczba węzłów procesu roboczego można ustalić przy użyciu symulowanych obciążeń. Klastra mogą być skalowane później, dodając większą liczbę węzłów procesu roboczego do obsłużenia szczytowego zapotrzebowania obciążenia. Później można skalować klastra, ponownie, gdy nie są wymagane dodatkowe węzły procesów roboczych.

Aby uzyskać więcej informacji, zobacz artykuł [planowania pojemności dla klastrów HDInsight](../hdinsight-capacity-planning.md).

## <a name="use-recommended-virtual-machine-type-for-cluster"></a>Użyj typu maszyn wirtualnych zalecane dla klastra

Zobacz [domyślne rozmiary maszyn wirtualnych i konfiguracja węzła klastrów](../hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) zaleca się typów maszyn wirtualnych dla każdego typu klastra HDInsight.

## <a name="check-hadoop-components-availability-in-hdinsight"></a>Sprawdzanie dostępności składników usługi Hadoop w HDInsight

Każda wersja HDInsight jest dystrybucją chmury wersji Hortonworks Data Platform (HDP) i składa się z zestawu składników ekosystemu Hadoop. Zobacz [HDInsight Component Versioning](../hdinsight-component-versioning.md) szczegółowe informacje dotyczące wszystkich składników HDInsight i ich bieżącej wersji.

Aby sprawdzić składniki i wersje w HDInsight, można użyć interfejsu użytkownika programu Apache Ambari lub interfejs API REST Ambari.

Aplikacje lub składniki, które były dostępne w klastrach w środowisku lokalnym, ale nie są częścią klastrów HDInsight można dodać na węźle krawędzi lub na maszynie Wirtualnej w tej samej podsieci co klaster HDInsight. Można zainstalować aplikacji platformy Hadoop innych firm, które nie są dostępne w usłudze Azure HDInsight przy użyciu opcji "Aplikacje" w klastrze HDInsight. Niestandardowych aplikacji platformy Hadoop można zainstalować w klastrze HDInsight przy użyciu "skrypt akcje". W poniższej tabeli wymieniono niektóre typowe aplikacje i ich opcji integracji HDInsight:

|**Aplikacja**|**Integracja**
|---|---|
|Powietrza|IaaS lub HDInsight węzłem krawędzi
|Alluxio|IaaS  
|Arcadia|IaaS 
|Atlas|Brak (tylko HDP)
|Datameer|Węzeł brzegowy HDInsight
|Usługa Datastax (Cassandra)|IaaS (bazy danych cosmos DB zamiast na platformie Azure)
|DataTorrent|IaaS 
|Przechodzenie do szczegółów|IaaS 
|Ignite|IaaS
|Jethro|IaaS 
|Mapador|IaaS 
|Mongo|IaaS (bazy danych cosmos DB zamiast na platformie Azure)
|NiFi|IaaS 
|Presto|IaaS lub HDInsight węzłem krawędzi
|Python 2|PaaS 
|Python 3|PaaS 
|R|PaaS 
|SAS|IaaS 
|Vertica|IaaS (SQLDW zamiast na platformie Azure)
|TABLEAU|IaaS 
|Wodnej|Węzeł brzegowy HDInsight
|StreamSets|HDInsight krawędzi 
|Palantir|IaaS 
|SailPoint|Iaas 

Aby uzyskać więcej informacji, zobacz artykuł [składniki platformy Apache Hadoop dostępne z różnymi wersjami HDInsight](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)

## <a name="customize-hdinsight-clusters-using-script-actions"></a>Dostosowywanie klastrów HDInsight za pomocą akcji skryptu

HDInsight zapewnia metodę konfiguracji klastra o nazwie **skryptu akcji**. Akcja skryptu jest skrypt powłoki Bash, jest uruchamiany w węzłach klastra usługi HDInsight, która może służyć do instalowania dodatkowych składników i zmianę ustawień konfiguracji.

Akcje skryptu muszą być przechowywane w identyfikatorze URI, który jest dostępny z klastra HDInsight. One może być używany podczas lub po utworzeniu klastra i mogą być ograniczone do uruchomienia tylko na niektóre typy węzłów.

Skrypt może być utrwalona lub wykonać jeden raz. Utrwalonych skryptów są używane do dostosowywania nowych węzłach procesów roboczych dodane do klastra za pośrednictwem operacji skalowania. Utrwalonego skryptu może mieć zastosowanie także zmiany na inny typ węzła, takich jak węzła głównego, po wystąpieniu operacji skalowania.

HDInsight udostępnia wstępnie napisane skrypty do zainstalowania następujących składników w klastrach HDInsight:

- Dodaj konto usługi Azure Storage
- Instalowanie rozwiązania Hue
- Zainstaluj skrypt Presto
- Zainstaluj program Solr
- Zainstaluj program Giraph
- Ładuj wstępnie biblioteki technologii Hive
- Instalowanie lub aktualizowanie środowiska Mono

> [!Note]  
> HDInsight nie zapewnia bezpośredniej pomocy technicznej dla składników hadoop niestandardowych lub składniki zainstalowane za pomocą akcji skryptu.

Akcje skryptu można także publikować w portalu Azure Marketplace jako aplikacji HDInsight.

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Instalowanie aplikacji platformy Hadoop Apache innych firm na HDInsight](../hdinsight-apps-install-applications.md)
- [Dostosowywanie klastrów HDInsight za pomocą akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md)
- [Publikowanie aplikacji HDInsight w witrynie Azure Marketplace](../hdinsight-apps-publish-applications.md)

## <a name="customize-hdinsight-configs-using-bootstrap"></a>Dostosowywanie configs HDInsight za pomocą narzędzia Bootstrap

Zmiany konfiguracji w plikach konfiguracji takich jak `core-site.xml`, `hive-site.xml` i `oozie-env.xml` jest możliwe za pomocą narzędzia Bootstrap. Poniższy skrypt jest przykładem dotyczącym używania programu Powershell:

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New—AzHDInsightClusterConfig '
    | Set—AzHDInsightDefaultStorage
    —StorageAccountName "$defaultStorageAccountName.blob. core.windows.net" `
    —StorageAccountKey "defaultStorageAccountKey " `
    | Add—AzHDInsightConfigValues `
        —HiveSite $hiveConfigValues

New—AzHDInsightCluster `
    —ResourceGroupName $existingResourceGroupName `
    —Cluster-Name $clusterName `
    —Location $location `
    —ClusterSizeInNodes $clusterSizeInNodes `
    —ClusterType Hadoop `
    —OSType Linux `
    —Version "3.6" `
    —HttpCredential $httpCredential `
    —Config $config
```

Aby uzyskać więcej informacji, zobacz artykuł [HDInsight Dostosowywanie klastrów za pomocą narzędzia Bootstrap](../hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="access-client-tools-from-hdinsight-hadoop-cluster-edge-nodes"></a>Węzły krawędzi klastra narzędzia klienta dostępu z usługi HDInsight Hadoop

Pustego węzła krawędzi jest maszyną wirtualną systemu Linux przy użyciu tych samych narzędzi klienta, które są zainstalowane i skonfigurowane jako o węzłach głównych, ale z nie usług Hadoop działających. W węźle brzegowym może służyć do następujących celów:

- Uzyskiwanie dostępu do klastra
- Testowanie aplikacji klienta
- hostowanie aplikacji klienta

Węzły brzegowe mogą być tworzone i usunąć za pomocą witryny Azure portal i może być używany podczas lub po tworzenie klastra. Po utworzeniu węzła krawędzi możesz nawiązać połączenie z węzłem krawędzi za pomocą protokołu SSH i uruchom narzędzia klienta, aby uzyskać dostęp do klastra usługi Hadoop w HDInsight. W węźle brzegowym ssh punkt końcowy jest `<EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22`.


Aby uzyskać więcej informacji, zobacz artykuł [używanie pustych węzłów brzegowych w klastrach usługi Apache Hadoop w HDInsight](../hdinsight-apps-use-edge-node.md).


## <a name="use-scale-up-and-scale-down-feature-of-clusters"></a>Funkcja skalowania w górę i w dół klastrów

HDInsight zapewnia elastyczność, oferując możliwość skalowania w górę i skalowania w dół liczbę węzłów procesu roboczego w klastrach. Ta funkcja umożliwia zmniejszania klastra po godzinach lub w weekendy i rozwiń go podczas szczytowego zapotrzebowania biznesowych.

Skalowanie klastra można zautomatyzować przy użyciu następujących metod:

### <a name="powershell-cmdlet"></a>Polecenia cmdlet programu PowerShell

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```powershell
azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
```

### <a name="azure-portal"></a>Azure Portal

Podczas dodawania węzłów do uruchomionego klastra HDInsight, nie będzie mieć wpływ na wszystkie oczekujące lub uruchomione zadania. Nowe zadania można bezpiecznie przesyłać proces skalowania jest uruchomiona. Jeśli operacje skalowania się nie powieść z jakiegokolwiek powodu, awarii bez problemu zmieniała odbywa się, pozostawiając klastra w stanie działać.

Jednak jeśli klaster skalowania, usuwając węzłów, wszystkie oczekujące lub uruchomione zadania zakończy się niepowodzeniem po zakończeniu operacji skalowania. Jest to spowodowane przez niektóre z tych usług, ponownego uruchamiania podczas procesu. Aby rozwiązać ten problem, możesz poczekać na ukończenie przed skalowaniem klaster, ręcznie zakończyć zadania, próby lub ponawiania przesyłania zadań po zakończył operację skalowania zadań.

Zmniejszania klastra w dół do co najmniej jednego procesu roboczego węzła systemu plików HDFS może stać się zatrzymany w trybie awaryjnym podczas stosowania poprawek lub od razu po wykonaniu operacji skalowania węzłów procesu roboczego są ponownie uruchamiane. Można wykonać następujące polecenie, aby przełączyć tryb awaryjny systemu plików HDFS:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

Po wyjściu z trybu awaryjnego, można ręcznie usunąć pliki tymczasowe lub poczekaj, aż gałęzi po pewnym czasie pora to oczyścić automatycznie.

Aby uzyskać więcej informacji, zobacz artykuł [klastrów HDInsight skalowania](../hdinsight-scaling-best-practices.md).

## <a name="use-hdinsight-with-azure-virtual-network"></a>HDInsight za pomocą sieci wirtualnej platformy Azure

Sieci wirtualne platformy Azure Włącz zasobów platformy Azure, takich jak Azure Virtual Machines do bezpiecznego komunikowania się ze sobą internetowego i sieciami lokalnymi przez filtrowaniem i routingiem ruchu sieciowego.

Za pomocą HDInsight przy użyciu usługi Azure Virtual Network umożliwia następujące scenariusze:

- Łączenie z HDInsight bezpośrednio z siecią lokalną.
- O łączeniu z danymi HDInsight są przechowywane w usłudze Azure Virtual network.
- Bezpośredni dostęp do usług Hadoop, które nie są dostępne publicznie w Internecie. Na przykład interfejsów API platformy Kafka lub interfejsu API języka Java bazy danych HBase.

HDInsight albo można dodać do nowej lub istniejącej Azure sieci wirtualnej. Jeśli HDInsight jest dodawany do istniejącej sieci wirtualnej, istniejące grupy zabezpieczeń sieci, a trasy zdefiniowane przez użytkownika muszą zostać zaktualizowane, aby zezwolić na nieograniczony dostęp do [kilka adresów IP](../hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) w centrum danych platformy Azure. Ponadto upewnij się, że nie blokuje ruchu skierowanego do [porty](../hdinsight-extend-hadoop-virtual-network.md#hdinsight-ports) które są używane przez usługi HDInsight.

> [!Note]  
> HDInsight aktualnie nie obsługuje wymuszonym tunelowaniem. Wymuszone tunelowanie ma ustawienia podsieci, która wymusza ruch wychodzący z Internetu na urządzeniu w celu przeprowadzenia inspekcji i rejestrowania. Usuń, wymuszonego tunelowania przed zainstalowaniem HDInsight w podsieci lub Utwórz nową podsieć dla HDInsight. HDInsight nie obsługuje również ograniczanie połączenia sieciowego ruchu wychodzącego.

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Azure — sieci — Omówienie wirtualnych](../../virtual-network/virtual-networks-overview.md)
- [Rozszerzenie Azure HDInsight przy użyciu sieci wirtualnej platformy Azure](../hdinsight-extend-hadoop-virtual-network.md)

## <a name="securely-connect-to-azure-services-with-azure-virtual-network-service-endpoints"></a>Bezpieczne łączenie z usługami platformy Azure z punktami końcowymi usługi Azure Virtual Network

HDInsight obsługuje [punkty końcowe usługi sieci wirtualnej](../../virtual-network/virtual-network-service-endpoints-overview.md) umożliwiają nawiązania bezpiecznego połączenia bazy danych usługi Azure Blob Storage, Azure Data Lake Storage Gen2, Cosmos DB i SQL. Po włączeniu punktu końcowego usługi dla usługi Azure HDInsight ruch odbywa się za pośrednictwem zabezpieczonej trasy z w obrębie centrum danych platformy Azure. Podwyższony poziom zabezpieczeń na poziomie warstwy sieci możesz zablokować kont magazynu danych big data do ich określonej sieci wirtualne (Vnet) i nadal używać klastrów HDInsight bezproblemowo uzyskać dostęp do przetwarzania tych danych.

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Punkty końcowe usługi sieci wirtualnej](../../virtual-network/virtual-network-service-endpoints-overview.md)
- [Podnieś poziom zabezpieczeń HDInsight z punktami końcowymi usługi](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/)

## <a name="connect-hdinsight-to-the-on-premises-network"></a>HDInsight nawiązać połączenie z siecią lokalną

HDInsight można połączyć w sieci lokalnej za pomocą usługi Azure Virtual Networks i bramy sieci VPN. Poniższe kroki, może służyć do ustanowienia połączenia:

- Użyj HDInsight w sieci wirtualnej platformy Azure, który jest połączony z siecią lokalną.
- Konfigurowanie rozpoznawania nazw DNS między siecią wirtualną i siecią lokalną.
- Skonfiguruj sieciowe grupy zabezpieczeń lub tras zdefiniowanych przez użytkownika (UDR) do kontrolowania ruchu sieciowego.

Aby uzyskać więcej informacji, zobacz artykuł [Connect HDInsight z siecią lokalną](../connect-on-premises-network.md)

## <a name="next-steps"></a>Kolejne kroki

Przeczytaj następny artykuł w tej serii:

- [Magazyn najlepsze rozwiązania dotyczące lokalnych do migracji usługi Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-storage.md)
