---
title: 'Infrastruktura: Lokalna usługa Apache Hadoop do usługi Azure HDInsight'
description: Poznaj najlepsze rozwiązania dotyczące infrastruktury podczas migracji lokalnych klastrów Hadoop do usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: d7ee8ae121e3cbb9760a87c95d12109a9b05e0c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74951517"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---infrastructure-best-practices"></a>Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight — najważniejsze wskazówki dotyczące infrastruktury

Ten artykuł zawiera zalecenia dotyczące zarządzania infrastrukturą klastrów usługi Azure HDInsight. Jest to część serii, która zapewnia najlepsze rozwiązania ułatwiające migrację lokalnych systemów Apache Hadoop do usługi Azure HDInsight.

## <a name="plan-for-hdinsight-cluster-capacity"></a>Planowanie pojemności klastra usługi HDInsight

Kluczowe opcje wyboru dla planowania pojemności klastra HDInsight są następujące:

**Region**  
Region platformy Azure określa, gdzie klaster jest fizycznie aprowizacji. Aby zminimalizować opóźnienie odczytów i zapisów, klaster powinien znajdować się w tym samym regionie co dane.

**Lokalizacja i rozmiar magazynu**  
Domyślny magazyn musi znajdować się w tym samym regionie co klaster.W przypadku klastra z 48 węzłami zaleca się mieć od 4 do 8 kont magazynu. Chociaż może już być wystarczająca całkowita ilość miejsca do magazynowania, każde konto magazynu zapewnia dodatkową przepustowość sieci dla węzłów obliczeniowych. Jeśli istnieje wiele kont magazynu, użyj losowej nazwy dla każdego konta magazynu, bez prefiksu. Celem losowego nazewnictwa jest zmniejszenie prawdopodobieństwo wąskich gardeł magazynu (ograniczanie przepustowości) lub błędów trybu wspólnego na wszystkich kontach. Aby uzyskać lepszą wydajność, należy używać tylko jednego kontenera na konto magazynu.

**Rozmiar i typ maszyny Wirtualnej (teraz obsługuje serię G)**  
Każdy typ klastra ma zestaw typów węzłów, a każdy typ węzła ma określone opcje dla ich rozmiaru i typu maszyny Wirtualnej. Rozmiar i typ maszyny Wirtualnej zależy od mocy przetwarzania procesora CPU, rozmiaru pamięci RAM i opóźnienia sieci. Symulowane obciążenie może służyć do określenia optymalnego rozmiaru i typu maszyny Wirtualnej dla każdego typu węzła.

**Liczba węzłów procesu roboczego**  
Początkową liczbę węzłów procesu roboczego można określić za pomocą symulowanych obciążeń. Klaster można skalować później, dodając więcej węzłów procesu roboczego w celu spełnienia zapotrzebowania na szczytowe obciążenie. Klaster można później skalować z powrotem, gdy dodatkowe węzły procesu roboczego nie są wymagane.

Aby uzyskać więcej informacji, zobacz artykuł [Planowanie pojemności klastrów HDInsight](../hdinsight-capacity-planning.md).

## <a name="use-recommended-virtual-machine-type-for-cluster"></a>Użyj zalecanego typu maszyny wirtualnej dla klastra

Zobacz [Domyślna konfiguracja węzłów i rozmiary maszyn wirtualnych dla klastrów](../hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) dla zalecanych typów maszyn wirtualnych dla każdego typu klastra HDInsight.

## <a name="check-hadoop-components-availability-in-hdinsight"></a>Sprawdź dostępność komponentów Hadoop w hdinsight

Każda wersja HDInsight jest rozkładem chmur zestawu komponentów ekosystemu Hadoop. Szczegółowe informacje na temat wszystkich składników HDInsight i ich aktualnych wersji można znaleźć w części [Wersja składników HDInsight.](../hdinsight-component-versioning.md)

Można również użyć Apache Ambari UI lub Ambari REST API, aby sprawdzić składniki i wersje Hadoop w HDInsight.

Aplikacje lub składniki, które były dostępne w klastrach lokalnych, ale nie są częścią klastrów HDInsight, można dodać do węzła brzegowego lub na maszynie Wirtualnej w tej samej sieci wirtualnej co klaster HDInsight. Aplikację Hadoop innej firmy, która nie jest dostępna w usłudze Azure HDInsight, można zainstalować przy użyciu opcji "Aplikacje" w klastrze HDInsight. Niestandardowe aplikacje Hadoop mogą być instalowane w klastrze HDInsight za pomocą "akcji skryptów". W poniższej tabeli wymieniono niektóre typowe aplikacje i ich opcje integracji HDInsight:

|**Aplikacja**|**Integracja**
|---|---|
|Przepływ powietrza|Węzeł krawędzi IaaS lub HDInsight
|Alluxio ( Alluxio )|IaaS  
|Arcadia|IaaS 
|Atlas|Brak (tylko HDP)
|Datameer (właso)|Węzeł krawędzi HDInsight
|Datastax (Cassandra)|IaaS (CosmosDB alternatywą na platformie Azure)
|DataTorrent (DataTorrent)|IaaS 
|Przechodzenie do szczegółów|IaaS 
|Ignite|IaaS
|Jethro|IaaS 
|Mapador|IaaS 
|Mongo|IaaS (CosmosDB alternatywą na platformie Azure)
|NiFi (NiFi)|IaaS 
|Presto|Węzeł krawędzi IaaS lub HDInsight
|Python 2|PaaS 
|Python 3|PaaS 
|R|PaaS 
|Sas|IaaS 
|Vertica|IaaS (SQLDW alternatywą na platformie Azure)
|Tableau|IaaS 
|Wodnej|Węzeł krawędzi HDInsight
|Zestawy strumieni|Krawędź HDInsight 
|Palantir ( Palantir )|IaaS 
|Punkt żaglowy|Iaas 

Aby uzyskać więcej informacji, zobacz artykuł [Apache Hadoop komponenty dostępne w różnych wersjach HDInsight](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)

## <a name="customize-hdinsight-clusters-using-script-actions"></a>Dostosowywanie klastrów usługi HDInsight przy użyciu akcji skryptów

Usługa HDInsight udostępnia metodę konfiguracji klastra zwaną **akcją skryptu**. Akcja skryptu to skrypt Bash, który działa w węzłach w klastrze HDInsight i może służyć do instalowania dodatkowych składników i zmiany ustawień konfiguracji.

Akcje skryptu muszą być przechowywane w identyfikatorze URI, który jest dostępny z klastra HDInsight. Mogą być używane podczas lub po tworzeniu klastra, a także mogą być ograniczone do uruchamiania tylko na niektórych typach węzłów.

Skrypt może być utrwalony lub wykonany jeden raz. Utrwalone skrypty są używane do dostosowywania nowych węzłów procesu roboczego dodanych do klastra za pomocą operacji skalowania. Utrwalony skrypt może również zastosować zmiany do innego typu węzła, takiego jak węzeł główny, podczas skalowania operacji.

Usługa HDInsight udostępnia wstępnie napisane skrypty w celu zainstalowania następujących składników w klastrach usługi HDInsight:

- Dodawanie konta usługi Azure Storage
- Zainstaluj barwę
- Zainstaluj Presto
- Instalowanie Solr
- Zainstaluj Giraph
- Wstępne ładowanie bibliotek gałęzi
- Instalowanie lub aktualizowanie środowiska Mono

> [!Note]  
> Usługa HDInsight nie zapewnia bezpośredniej obsługi niestandardowych składników lub składników hadoop zainstalowanych przy użyciu akcji skryptów.

Akcje skryptu mogą być również publikowane w portalu Azure Marketplace jako aplikacja HDInsight.

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Instalowanie aplikacji Apache Hadoop innych firm w programie HDInsight](../hdinsight-apps-install-applications.md)
- [Dostosowywanie klastrów usługi HDInsight przy użyciu akcji skryptów](../hdinsight-hadoop-customize-cluster-linux.md)
- [Publikowanie aplikacji USŁUGI HDInsight w portalu Azure Marketplace](../hdinsight-apps-publish-applications.md)

## <a name="customize-hdinsight-configs-using-bootstrap"></a>Dostosowywanie konfiguracji HDInsight za pomocą Bootstrap

Zmiany w configs w plikach `core-site.xml` `hive-site.xml` konfiguracyjnych, takich jak , i `oozie-env.xml` mogą być dokonywane za pomocą Bootstrap. Poniższy skrypt jest przykładem przy użyciu polecenia cmdlet [modułu Powershell AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) [New-AzHDInsightClusterConfig:](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster)

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

Aby uzyskać więcej informacji, zobacz artykuł [Dostosowywanie klastrów HDInsight przy użyciu pliku Bootstrap](../hdinsight-hadoop-customize-cluster-bootstrap.md).  Zobacz też Zarządzanie [klastrami HDInsight za pomocą interfejsu API Apache Ambari REST](../hdinsight-hadoop-manage-ambari-rest-api.md).

## <a name="access-client-tools-from-hdinsight-hadoop-cluster-edge-nodes"></a>Dostęp do narzędzi klienckich z węzłów brzegowych klastra programu HDInsight Hadoop

Pusty węzeł krawędzi to maszyna wirtualna systemu Linux z tymi samymi narzędziami klienckimi zainstalowanymi i skonfigurowanym jak w węzłach head, ale bez uruchomionych usług Hadoop. Węzeł krawędzi może być używany do następujących celów:

- uzyskiwanie dostępu do klastra
- testowanie aplikacji klienckich
- hostowanie aplikacji klienckich

Węzły krawędzi mogą być tworzone i usuwane za pośrednictwem witryny Azure portal i mogą być używane podczas lub po utworzeniu klastra. Po utworzeniu węzła krawędzi można połączyć się z węzłem brzegowym za pomocą funkcji SSH i uruchomić narzędzia klienckie w celu uzyskania dostępu do klastra Hadoop w programie HDInsight. Punktem końcowym węzła `<EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22`krawędzi jest .


Aby uzyskać więcej informacji, zobacz artykuł [Używanie pustych węzłów krawędzi w klastrach Apache Hadoop w programie HDInsight](../hdinsight-apps-use-edge-node.md).


## <a name="use-scale-up-and-scale-down-feature-of-clusters"></a>Korzystanie z funkcji skalowania w górę i w dół klastrów

Usługa HDInsight zapewnia elastyczność, udostępniając możliwość skalowania w górę i skalowania w dół liczby węzłów procesu roboczego w klastrach. Ta funkcja umożliwia zmniejszanie klastra po godzinach lub w weekendy i rozszerzanie go w szczytowym okresie wymagań biznesowych. Aby uzyskać więcej informacji, zobacz:

* [Skalowanie klastrów HDInsight](../hdinsight-scaling-best-practices.md).
* [Skalowanie klastrów](../hdinsight-administer-use-portal-linux.md#scale-clusters).

## <a name="use-hdinsight-with-azure-virtual-network"></a>Korzystanie z usługi HDInsight w sieci wirtualnej platformy Azure

Sieci wirtualne platformy Azure umożliwiają zasoby platformy Azure, takie jak maszyny wirtualne platformy Azure, aby bezpiecznie komunikować się ze sobą, Internet i sieci lokalne, filtrowanie i routingu ruchu sieciowego.

Korzystanie z usługi Azure Virtual Network z hdinsight umożliwia następujące scenariusze:

- Łączenie się z hdinsight bezpośrednio z sieci lokalnej.
- Łączenie usługi HDInsight z magazynami danych w sieci wirtualnej platformy Azure.
- Bezpośredni dostęp do usług Hadoop, które nie są dostępne publicznie przez Internet. Na przykład interfejsy API platformy Kafka lub interfejs API języka Java bazy HBase.

Usługa HDInsight można dodać do nowej lub istniejącej sieci wirtualnej platformy Azure. Jeśli usługa HDInsight jest dodawana do istniejącej sieci wirtualnej, istniejące sieciowe grupy zabezpieczeń i trasy zdefiniowane przez użytkownika muszą zostać zaktualizowane, aby umożliwić nieograniczony dostęp do [kilku adresów IP](../hdinsight-management-ip-addresses.md) w centrum danych platformy Azure. Upewnij się również, że nie można blokować ruchu do [portów,](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ports)które są używane przez usługi HDInsight.

> [!Note]  
> HDInsight obecnie nie obsługuje wymuszonego tunelowania. Wymuszone tunelowanie to ustawienie podsieci, które wymusza wychodzący ruch internetowy do urządzenia do inspekcji i rejestrowania. Usuń wymuszone tunelowanie przed zainstalowaniem usługi HDInsight w podsieci lub utwórz nową podsieć dla usługi HDInsight. Usługa HDInsight nie obsługuje również ograniczania wychodzącej łączności sieciowej.

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Omówienie sieci wirtualnych platformy Azure](../../virtual-network/virtual-networks-overview.md)
- [Rozszerzanie usługi Azure HDInsight przy użyciu sieci wirtualnej platformy Azure](../hdinsight-plan-virtual-network-deployment.md)

## <a name="securely-connect-to-azure-services-with-azure-virtual-network-service-endpoints"></a>Bezpieczne łączenie się z usługami platformy Azure za pomocą punktów końcowych usługi azure virtual network

Usługa HDInsight obsługuje [punkty końcowe usługi sieci wirtualnej,](../../virtual-network/virtual-network-service-endpoints-overview.md)które umożliwiają bezpieczne łączenie się z usługą Azure Blob Storage, azure data lake storage gen2, cosmos DB i bazami danych SQL. Po włączeniu punktu końcowego usługi dla usługi Azure HDInsight ruch przepływa przez zabezpieczoną trasę z poziomu centrum danych platformy Azure. Dzięki temu zwiększonemu poziomowi zabezpieczeń w warstwie sieci można zablokować konta magazynu dużych zbiorów danych w określonych sieciach wirtualnych (VNETs) i nadal bezproblemowo używać klastrów HDInsight do uzyskiwania dostępu do tych danych i przetwarzania ich.

Aby uzyskać więcej informacji zobacz następujące artykuły:

- [Punkty końcowe usługi dla sieci wirtualnej](../../virtual-network/virtual-network-service-endpoints-overview.md)
- [Zwiększ bezpieczeństwo usługi HDInsight dzięki punktom końcowym usługi](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/)

## <a name="connect-hdinsight-to-the-on-premises-network"></a>Podłączanie usługi HDInsight do sieci lokalnej

Usługa HDInsight może być połączona z siecią lokalną przy użyciu sieci wirtualnych platformy Azure i bramy sieci VPN. Do ustanowienia łączności można wykonać następujące kroki:

- Użyj usługi HDInsight w sieci wirtualnej platformy Azure, która ma łączność z siecią lokalną.
- Skonfiguruj rozpoznawanie nazw DNS między siecią wirtualną a siecią lokalną.
- Skonfiguruj sieciowe grupy zabezpieczeń lub trasy zdefiniowane przez użytkownika (UDR) w celu kontrolowania ruchu sieciowego.

Aby uzyskać więcej informacji, zobacz artykuł [Łączenie usługi HDInsight z siecią lokalną](../connect-on-premises-network.md)

## <a name="next-steps"></a>Następne kroki

Przeczytaj następny artykuł z tej serii: [Najważniejsze wskazówki dotyczące lokalnego przechowywania danych dotyczących migracji usługi Azure HDInsight Hadoop.](apache-hadoop-on-premises-migration-best-practices-storage.md)
