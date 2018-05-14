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
ms.date: 05/11/2018
ms.author: jgao
ms.openlocfilehash: 56b2b5ae9d3e4a0e682ec3dd47cd5cc30ebf6d58
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
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

W tym artykule opisano scenariusz replikację geograficzną.

Ułatwiają konfigurowanie środowisk, utworzyliśmy niektóre [szablonów usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Jeśli wolisz skonfigurować tych środowisk przy użyciu innych metod, zobacz:

- [Tworzenie klastrów Hadoop w usłudze HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Tworzenie klastrów HBase w sieci wirtualnej platformy Azure](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Skonfigurować dwie sieci wirtualne w dwóch różnych regionach.

Aby utworzyć dwie sieci wirtualne w dwóch różnych regionach i połączenia sieci VPN między sieciami wirtualnymi, wybierz poniższy obraz, aby utworzyć. Szablon jest przechowywany w [magazynu obiektów blob publicznego]] (https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

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

## <a name="setup-dns"></a>Konfiguracja DNS

W ostatniej sekcji szablon tworzy maszyny wirtualnej systemu Ubuntu w każdym z dwóch sieci wirtualnych.  W tej sekcji Zainstaluj Bind w przypadku dwóch maszyn wirtualnych DNS, a następnie skonfiguruj przesyłania dalej DNS w przypadku dwóch maszyn wirtualnych.

Aby zainstalować Bind, yon musi znaleźć publiczny adres IP dwóch maszyn wirtualnych DNS.

1. Otwórz [portal Azure](https://portal.azure.com).
2. Otwórz DNS maszynę wirtualną, wybierając **grup zasobów > [Nazwa grupy zasobów] > [vnet1DNS]**.  Nazwa grupy zasobów jest tworzonym w poprzedniej procedurze. Domyślne nazwy maszyny wirtualnej DNS są *vnet1DNS* i *vnet2NDS*.
3. Wybierz **właściwości** aby otworzyć stronę właściwości sieci wirtualnej.
4. Zapisz **publicznego adresu IP**, a także sprawdź **prywatny adres IP**.  Jest prywatny adres IP **10.1.0.4** dla vnet1DNS i **10.2.0.4** dla vnet2DNS.  

Aby zainstalować Bind, użyj następującej procedury:

1. Używanie protokołu SSH do nawiązania połączenia __publicznego adresu IP__ DNS maszyny wirtualnej. Poniższy przykład nawiązuje połączenie z maszyną wirtualną na 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Zastąp `sshuser` przy użyciu konta użytkownika SSH określone podczas tworzenia maszyny wirtualnej DNS.

    > [!NOTE]
    > Istnieje wiele sposobów, aby uzyskać `ssh` narzędzia. W systemie Linux, Unix i macOS jest podana jako część systemu operacyjnego. Jeśli korzystasz z systemu Windows, weź pod uwagę jedną z następujących opcji:
    >
    > * [Powłoka w chmurze Azure](../../cloud-shell/quickstart.md)
    > * [Bash na Ubuntu w systemie Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH)https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Aby zainstalować Bind, użyj następujących poleceń w sesji SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Aby skonfigurować powiązania do przekazywania żądań rozpoznawania nazw do lokalnego serwera DNS, użyj następującego tekstu zawartość, `/etc/bind/named.conf.options` pliku:

    ```
    acl goodclients {
        10.1.0.0/16; # Replace with the IP address range of the virtual network 1
        10.2.0.0/16; # Replace with the IP address range of the virtual network 2
        localhost;
        localhost;
    };
    
    options {
        directory "/var/cache/bind";
        recursion yes;
        allow-query { goodclients; };

        forwarders {
            168.63.129.16 #This is the Azure DNS server
        };

        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
    };
    ```
    
    > [!IMPORTANT]
    > Zastąp wartości w `goodclients` części dwie sieci wirtualnej z zakresem adresów IP. Ta sekcja definiuje adresów, które ten serwer DNS odbiera z.

    Aby edytować ten plik, użyj następującego polecenia:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Aby zapisać plik, użyj __Ctrl + X__, __Y__, a następnie __Enter__.

4. W sesji SSH Użyj następującego polecenia:

    ```bash
    hostname -f
    ```

    To polecenie zwraca wartość podobny do następującego tekstu:

        vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` Tekst jest __sufiks DNS__ dla tej sieci wirtualnej. Zapisz tę wartość, ponieważ jest używana później.

    Należy także sprawdzić sufiks DNS z innego serwera DNS. Należy go w następnym kroku.

5. Aby skonfigurować powiązania do rozpoznawania nazw DNS dla zasobów w sieci wirtualnej, użyj następującego tekstu jako zawartość `/etc/bind/named.conf.local` pliku:

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]
    > Należy zastąpić `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` sufiksu DNS, innych sieci wirtualnej. I adres IP usługi przesyłania dalej jest prywatny adres IP serwera DNS w sieci wirtualnej.

    Aby edytować ten plik, użyj następującego polecenia:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Aby zapisać plik, użyj __Ctrl + X__, __Y__, a następnie __Enter__.

6. Aby uruchomić Bind, użyj następującego polecenia:

    ```bash
    sudo service bind9 restart
    ```

7. Aby zweryfikować tego powiązania mogły rozpoznawać nazwy zasobów w sieci wirtualnej, użyj następujących poleceń:

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net 10.2.0.4
    ```

    > [!IMPORTANT]
    > Zastąp `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` z w pełni kwalifikowaną nazwę (FQDN) DNS maszyny wirtualnej w innej sieci.
    >
    > Zastąp `10.2.0.4` z __wewnętrzny adres IP__ niestandardowego serwera DNS w sieci wirtualnej.

    Odpowiedź jest podobny do następującego tekstu:

    ```
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    Do tej pory nie można odszukać adresu IP z sieci bez określony adres IP serwera DNS.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Skonfiguruj sieć wirtualną do używania niestandardowych serwera DNS

Aby skonfigurować sieci wirtualnej, aby użyć niestandardowego serwera DNS zamiast Azure cyklicznego programu rozpoznawania nazw, należy użyć następujących czynności:

1. W [portalu Azure](https://portal.azure.com), wybierz sieć wirtualną, a następnie wybierz __serwerów DNS__.

2. Wybierz __niestandardowych__, a następnie wprowadź __wewnętrzny adres IP__ niestandardowego serwera DNS. Na koniec wybierz __zapisać__.

6. Otwórz w vnet1 maszyny wirtualnej serwera DNS, a następnie kliknij przycisk **ponownego uruchomienia**.  W sieci wirtualnej, aby konfiguracja DNS zaczęły obowiązywać, należy ponownie uruchomić wszystkich maszyn wirtualnych.
7. Powtórz kroki konfiguracji niestandardowej serwera DNS vnet2.

Aby przetestować konfigurację systemu DNS, nawiązać dwie maszyny wirtualne DNS przy użyciu protokołu SSH i zbadaj serwer DNS w sieci wirtualnej za pomocą jego nazwy hosta. Jeśli ta funkcja nie działa, użyj następującego polecenia, aby sprawdzić stan usługi DNS:

```bash
sudo service bind9 status
```

## <a name="create-hbase-clusters"></a>Tworzenie klastrów HBase

Utworzyć klaster HBase w każdym z dwóch sieci wirtualnej przy użyciu następującej konfiguracji:

- **Nazwa grupy zasobów**: Użyj tej samej nazwy grupy zasobów, tworzenia sieci wirtualnych.
- **Typ klastra**: HBase
- **Wersja**: HBase 1.1.2 (HDI 3,6)
- **Lokalizacja**: Użyj tej samej lokalizacji co sieć wirtualna.  Domyślnie jest vnet1 *zachodnie stany USA*, i jest vnet2 *wschodnie stany USA*.
- **Magazyn**: Utwórz nowe konto magazynu dla klastra.
- **Sieć wirtualna** (za pomocą zaawansowanych ustawień portalu): Wybierz vnet1 utworzony w poprzedniej procedurze.
- **Podsieci**: domyślna nazwa używana w szablonie jest **podsieć1**.

Aby zapewnić, że środowisko jest skonfigurowane poprawnie, musi mieć możliwość ping FQDN headnode między dwoma klastrami.

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

