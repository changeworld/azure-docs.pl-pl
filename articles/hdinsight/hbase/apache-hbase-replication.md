---
title: Konfigurowanie replikacji klastra HBase w sieciach wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie skonfigurowania replikacji bazy danych HBase z jednej wersji usługi HDInsight do innego równoważenia obciążenia, wysokiej dostępności, zero przestoju migracji i aktualizacji i odzyskiwania po awarii.
services: hdinsight,virtual-network
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: c28c48b5842deec9d9c3898c5742c3d4d473094e
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="set-up-hbase-cluster-replication-in-azure-virtual-networks"></a>Konfigurowanie replikacji klastra HBase w sieciach wirtualnych platformy Azure

Informacje o sposobie skonfigurowania replikacji bazy danych HBase w ramach sieci wirtualnej lub między dwiema sieciami wirtualnymi na platformie Azure.

Replikacja klastra używa metodologii wypychania źródła. Klaster HBase można źródła lub miejsca docelowego, lub jego jednocześnie spełnić obie role. Replikacja jest asynchroniczne. Celem replikacji jest spójność ostateczna. Gdy źródło odbiera Edytuj, aby rodziny kolumn po włączeniu replikacji, Edycja jest propagowana do wszystkich klastrów docelowych. Podczas replikacji danych z jednego klastra do innego klastra źródłowego i wszystkich klastrów, które mają już używane dane są śledzone, aby zapobiec pętli replikacji.

W tym samouczku skonfigurowaniu replikacji źródłowego i docelowego. Dla innych topologiach klastra [Podręcznik bazy danych Apache HBase](http://hbase.apache.org/book.html#_cluster_replication).

Przypadki użycia replikacji bazy danych HBase pojedynczej sieci wirtualnych są następujące:

* Równoważenie obciążenia. Na przykład można uruchomić zadania MapReduce lub skanowania w docelowym klastrze i pozyskiwania danych w klastrze źródłowym.
* Dodawanie wysokiej dostępności.
* Migrowanie danych z jednego klastra HBase na inny.
* Uaktualnianie klastra usługi Azure HDInsight z jednej wersji do innego.

Przypadków użycia replikacji bazy danych HBase dla dwie sieci wirtualne są następujące:

* Konfigurowanie odzyskiwania po awarii.
* Równoważenie obciążenia i partycjonowanie aplikacji.
* Dodawanie wysokiej dostępności.

Można replikować klastrów za pomocą [akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md) skrypty z [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem tego samouczka musisz dysponować subskrypcją platformy Azure. Zobacz [uzyskać Azure bezpłatnej wersji próbnej](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="set-up-the-environments"></a>Konfigurowanie środowisk

Dostępne są trzy opcje konfiguracji:

- Dwoma klastrami HBase w jednej sieci wirtualnej platformy Azure.
- Dwoma klastrami HBase w dwóch różnych sieciach wirtualnych w tym samym regionie.
- Dwoma klastrami HBase w dwóch różnych sieciach wirtualnych w dwóch różnych regionach (replikacja geograficzna).

Ułatwiają konfigurowanie środowisk, utworzyliśmy niektóre [szablonów usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Jeśli wolisz skonfigurować tych środowisk przy użyciu innych metod, zobacz:

- [Tworzenie klastrów Hadoop w usłudze HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Tworzenie klastrów HBase w sieci wirtualnej platformy Azure](apache-hbase-provision-vnet.md)

### <a name="set-up-one-virtual-network"></a>Skonfiguruj jedną sieć wirtualną

Do utworzenia dwóch klastrów HBase w tej samej sieci wirtualnej, wybierz poniższy obraz. Szablon jest przechowywany w [szablonów Szybki Start Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-one-vnet/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-one-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

### <a name="set-up-two-virtual-networks-in-the-same-region"></a>Skonfigurować dwie sieci wirtualne w tym samym regionie

Aby utworzyć dwie sieci wirtualne sieci wirtualnej komunikacji równorzędnej i dwoma klastrami HBase w tym samym regionie, wybierz poniższy obraz. Szablon jest przechowywany w [szablonów Szybki Start Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-two-vnets-same-region/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-two-vnets-same-region%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>



Ten scenariusz wymaga [sieci wirtualnej komunikacji równorzędnej](../../virtual-network/virtual-network-peering-overview.md). Ten szablon umożliwia sieci wirtualnej komunikacji równorzędnej.   

Adresy IP maszyn wirtualnych dozorcy korzysta z replikacji bazy danych HBase. Należy zdefiniować statyczne adresy IP dla docelowej bazy danych HBase dozorcy węzłów.

**Aby skonfigurować statyczne adresy IP**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie wybierz **grup zasobów**.
3. Wybierz grupę zasobów, która ma miejsce docelowe klastra HBase. Jest to grupa zasobów, określoną w przypadku używania szablonu usługi Resource Manager do tworzenia środowiska. Można użyć filtru, aby zawęzić listę. Można wyświetlić listę zasobów, które zawierają dwie sieci wirtualne.
4. Wybierz sieć wirtualną, która zawiera klastra HBase docelowego. Na przykład wybierz **xxxx vnet2**. Trzy urządzeń o nazwach rozpoczynających się od **kart-zookeepermode -** są wyświetlane. Te urządzenia są trzy dozorcy maszyn wirtualnych.
5. Wybierz jedną z dozorcy maszyn wirtualnych.
6. Wybierz **konfiguracje adresów IP**.
7. Na liście wybierz **ipConfig1**.
8. Wybierz **statycznych**, skopiuj i Zapisz adres IP rzeczywistego. Po uruchomieniu akcji skryptu, aby włączyć replikację jest wymagany adres IP.

  ![Replikacja bazy danych HDInsight HBase dozorcy statycznego adresu IP](./media/apache-hbase-replication/hdinsight-hbase-replication-zookeeper-static-ip.png)

9. Powtórz krok 6, aby ustawić statyczny adres IP dla innych dwa węzły dozorcy.

W scenariuszu sieci-virtual, należy użyć **- ip** przełącznika podczas wywoływania `hdi_enable_replication.sh` akcji skryptu.

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Skonfigurować dwie sieci wirtualne w dwóch różnych regionach.

Aby utworzyć dwie sieci wirtualne w dwóch różnych regionach i połączenia sieci VPN między sieciami wirtualnymi, kliknij poniższy obraz. Szablon jest przechowywany w [szablonów Szybki Start Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-geo%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

Niektóre wartości ustalony w szablonie:

**Sieć wirtualna 1**

| Właściwość | Wartość |
|----------|-------|
| Lokalizacja | Zachodnie stany USA |
| Nazwa sieci wirtualnej | &lt;ClusterNamePrevix >-vnet1 |
| Prefiks przestrzeni adresów | 10.1.0.0/16 |
| Nazwa podsieci | Podsieć 1 |
| Prefiks podsieci | 10.1.0.0/24 |
| Nazwa podsieci (bramy) | GatewaySubnet (nie można zmienić) |
| Prefiks podsieci (bramy) | 10.1.255.0/27 |
| Nazwa bramy | vnet1gw |
| Typ bramy | Vpn |
| Typ sieci VPN dla bramy | RouteBased |
| Jednostka SKU bramy | Podstawowa |
| brama IP | vnet1gwip |
| Nazwa klastra | &lt;ClusterNamePrefix > 1 |
| Wersja klastra | 3.6 |
| Typ klastra | Baza danych hbase |
| Liczba węzłów procesu roboczego klastra | 2 |


**Sieci wirtualnej 2**

| Właściwość | Wartość |
|----------|-------|
| Lokalizacja | Wschodnie stany USA |
| Nazwa sieci wirtualnej | &lt;ClusterNamePrevix >-vnet2 |
| Prefiks przestrzeni adresów | 10.2.0.0/16 |
| Nazwa podsieci | Podsieć 1 |
| Prefiks podsieci | 10.2.0.0/24 |
| Nazwa podsieci (bramy) | GatewaySubnet (nie można zmienić) |
| Prefiks podsieci (bramy) | 10.2.255.0/27 |
| Nazwa bramy | vnet2gw |
| Typ bramy | Vpn |
| Typ sieci VPN dla bramy | RouteBased |
| Jednostka SKU bramy | Podstawowa |
| brama IP | vnet1gwip |
| Nazwa klastra | &lt;ClusterNamePrefix > 2 |
| Wersja klastra | 3.6 |
| Typ klastra | Baza danych hbase |
| Liczba węzłów procesu roboczego klastra | 2 |

Adresy IP maszyn wirtualnych dozorcy korzysta z replikacji bazy danych HBase. Należy zdefiniować statyczne adresy IP dla docelowej bazy danych HBase dozorcy węzłów. Aby skonfigurować statyczny adres IP, zobacz [skonfigurować dwie sieci wirtualne w tym samym regionie](#set-up-two-virtual-networks-in-the-same-region) w tym artykule.

W scenariuszu sieci-virtual, należy użyć **- ip** przełącznika podczas wywoływania `hdi_enable_replication.sh` akcji skryptu.

## <a name="load-test-data"></a>Danych testu obciążenia

Podczas replikowania klastra, należy określić tabel, które mają być replikowane. W tej sekcji w źródłowym klastrze ładowania niektórych danych. W następnej sekcji spowoduje włączenie replikacji między dwoma klastrami.

Aby utworzyć **kontaktów** tabeli i wstaw dowolne dane w tabeli, postępuj zgodnie z instrukcjami w [samouczek HBase: rozpoczęcie pracy z bazy danych Apache HBase w usłudze HDInsight](apache-hbase-tutorial-get-started-linux.md).

## <a name="enable-replication"></a>Włączanie replikacji

W poniższych krokach opisano sposób wywoływania skryptu akcji skryptu z portalu Azure. Aby uzyskać informacje o uruchamianiu akcji skryptu za pomocą programu Azure PowerShell i narzędzie wiersza polecenia platformy Azure (Azure CLI), zobacz [Dostosowywanie klastrów usługi HDInsight przy użyciu akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md).

**Aby włączyć replikację bazy danych HBase z portalu Azure**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Otwórz klaster HBase źródła.
3. Wybierz z menu klastra **akcji skryptu**.
4. W górnej części strony, wybierz **przesłać nowe**.
5. Wybierz lub wprowadź następujące informacje:

  1. **Nazwa**: wprowadź **włączyć replikację**.
  2. **Adres URL skryptu bash**: wprowadź **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**.
  3.  **HEAD**: Upewnij się, ta opcja jest zaznaczona. Usuń zaznaczenie innych typów węzłów.
  4. **Parametry**: następujące parametry przykładowe włączyć replikację dla wszystkich istniejących tabel, a następnie skopiuj wszystkie dane z klastra źródłowego do klastra docelowego:

          -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
    >[!note]
    >
    > Używa nazwy hosta zamiast nazwy FQDN dla źródłowego i docelowego nazwę klastra DNS.

6. Wybierz pozycję **Utwórz**. Skrypt może potrwać dłuższy czas, szczególnie w przypadku, gdy używasz **COPYDATA: -** argumentu.

Wymagane argumenty:

|Name (Nazwa)|Opis|
|----|-----------|
|-s, - src-klaster | Określa nazwę DNS klastra HBase źródła. Na przykład: hbsrccluster -s, klaster — src = hbsrccluster |
|-d, klaster — czasu letniego | Określa nazwę DNS klastra HBase docelowego (repliki). Na przykład: dsthbcluster -s, klaster — src = dsthbcluster |
|-sp, w--src hasła narzędzia ambari | Określa hasło administratora dla Ambari w klastrze źródłowym HBase. |
|— punkt dystrybucji, w czasu letniego — hasła narzędzia ambari | Określa hasło administratora dla Ambari w docelowym klastrze HBase.|

Argumenty opcjonalne:

|Name (Nazwa)|Opis|
|----|-----------|
|-su,--src-ambari-user | Określa nazwę użytkownika administratora dla Ambari w klastrze źródłowym HBase. Wartość domyślna to **admin**. |
|-du, czasu letniego — ambari użytkownika | Określa nazwę użytkownika administratora dla Ambari w docelowym klastrze HBase. Wartość domyślna to **admin**. |
|-t,--lista tabeli | Określa tabele, które mają być replikowane. Na przykład:--lista tabeli = "Tabela1; table2 Tabela3". Jeśli nie określisz tabel, replikacja wszystkich istniejących tabel HBase.|
|-m,--maszyny | Określa węzła głównego, w którym jest uruchomiona akcja skryptu. Wartość to **hn1** lub **hn0**. Ponieważ **hn0** węzła głównego zazwyczaj jest coraz bardziej zajęty, zaleca się używanie **hn1**. Użyj tej opcji, po uruchomieniu skryptu $0 jako akcja skryptu HDInsight portalu lub Azure PowerShell.|
|-ip | Wymagana, gdy w przypadku włączenia replikacji między dwiema sieciami wirtualnymi. Ten argument działa jako przełącznika do używania statycznych adresów IP dozorcy węzłów z klastrów repliki zamiast nazwy FQDN. Przed włączeniem replikacji, należy wstępnie skonfigurować statyczne adresy IP. |
|-cp, - COPYDATA: | Umożliwia migrację istniejących danych w tabelach, w którym replikacja jest włączona. |
|-obr. / min, - replikacja-phoenix-meta | Włącza replikację na Phoenix tabel systemowych. <br><br>*Użyj tej opcji z rozwagą.* Firma Microsoft zaleca, ponownie utwórz tabele Phoenix w klastrach repliki przed skorzystaniem z tego skryptu. |
|-h, — pomoc | Wyświetla informacje o użyciu. |

`print_usage()` Sekcji [skryptu](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) zawiera szczegółowy opis parametrów.

Po pomyślnym wdrożeniu akcji skryptu służy SSH do nawiązania połączenia klastra HBase docelowego, a następnie sprawdź, czy replikowane dane.

### <a name="replication-scenarios"></a>Scenariusze replikacji

Na poniższej liście przedstawiono niektóre przypadki użycia ogólne i ich ustawienia parametru:

- **Włącz replikację dla wszystkich tabel między dwoma klastrami**. Ten scenariusz nie wymaga się kopiowania lub Migrowanie istniejących danych w tabelach, a nie używa Phoenix tabel. Użyj następujących parametrów:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Włącz replikację na określonych tabel**. Aby włączyć replikację na table1 i table2, Tabela3, należy użyć poniższych parametrów:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Włącz replikację na określonych tabel i skopiuj istniejące dane**. Aby włączyć replikację na table1 i table2, Tabela3, należy użyć poniższych parametrów:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Włącz replikację dla wszystkich tabel i replikacji metadanych Phoenix ze źródła do miejsca docelowego**. Phoenix metadanych replikacji nie jest idealne. Użyj go z rozwagą. Użyj następujących parametrów:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Skopiuj i przeprowadzić migrację danych

Dostępne są dwa skrypty akcji skryptu oddzielne kopiowania lub migracji danych, po włączeniu replikacji:

- [Skrypt dla małych tabel](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (tabel, które są kilku gigabajtów w rozmiarze i ogólnej kopii oczekuje na zakończenie w mniej niż godzinę)

- [Skrypt dla dużych tabel](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (tabel, które powinny trwać dłużej niż godzinę, aby skopiować)

Można wykonać tę samą procedurę opisaną w [włączyć replikację](#enable-replication) do wywoływania akcji skryptu. Użyj następujących parametrów:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

`print_usage()` Sekcji [skryptu](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) zawiera szczegółowy opis parametrów.

### <a name="scenarios"></a>Scenariusze

- **Skopiuj określonych tabel (test1 test2 i test3) dla wszystkich wierszy edytować do tej pory (bieżąca sygnatura czasowa)**:

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  Lub:

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Skopiuj określonych tabel z określonego przedziału czasu**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>Wyłączanie replikacji

Aby wyłączyć replikacji, użyj innego skryptu akcji skryptu z [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Można wykonać tę samą procedurę opisaną w [włączyć replikację](#enable-replication) do wywoływania akcji skryptu. Użyj następujących parametrów:

    -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

`print_usage()` Sekcji [skryptu](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) zawiera szczegółowy opis parametrów.

### <a name="scenarios"></a>Scenariusze

- **Wyłącz replikację dla wszystkich tabel**:

        -m hn1 -s <source cluster DNS name> -sp Mypassword\!789 -all
  lub

        --src-cluster=<source cluster DNS name> --dst-cluster=<destination cluster DNS name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **Wyłącz replikację w określonych tabel (Tabela1 table2 i Tabela3)**:

        -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób skonfigurowania replikacji bazy danych HBase w ramach sieci wirtualnej lub między dwiema sieciami wirtualnymi. Aby dowiedzieć się więcej na temat usługi HDInsight i HBase, zobacz następujące artykuły:

* [Rozpoczynanie pracy z bazy danych Apache HBase w usłudze HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [HDInsight HBase — omówienie](./apache-hbase-overview.md)
* [Tworzenie klastrów HBase w sieci wirtualnej platformy Azure](./apache-hbase-provision-vnet.md)

