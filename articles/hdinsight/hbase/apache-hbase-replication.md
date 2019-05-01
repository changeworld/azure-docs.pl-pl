---
title: Konfigurowanie replikacji klaster bazy danych HBase w sieciach wirtualnych platformy Azure — Azure HDInsight
description: Informacje o sposobie konfigurowania replikacji bazy danych HBase z jednej wersji HDInsight do innego dla równoważenia obciążenia, wysoką dostępność, bez jakichkolwiek przestojów migracji i aktualizacji i odzyskiwania po awarii.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/15/2018
ms.openlocfilehash: 95a1055df283765b24322f6f8efe3efcb9b19022
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64707974"
---
# <a name="set-up-apache-hbase-cluster-replication-in-azure-virtual-networks"></a>Konfigurowanie replikacji klaster bazy danych Apache HBase w sieci wirtualnej platformy Azure

Dowiedz się, jak skonfigurować [bazy danych Apache HBase](https://hbase.apache.org/) replikacji w sieci wirtualnej lub między dwiema sieciami wirtualnymi na platformie Azure.

Replikacja klastra używa metodologii wypychania źródła. Klaster bazy danych HBase może być źródła lub miejsca docelowego lub go wykonać obie te role jednocześnie. Replikacja jest asynchroniczne. Celem replikacji jest spójność ostateczną. Źródło odbiera zmianę do rodziny kolumn, po włączeniu replikacji, Edycja jest propagowany do wszystkich klastrów docelowych. Podczas replikacji danych z jednego klastra do innego klastra źródłowego i wszystkich klastrów, które już zostały wykorzystane danych są śledzone, aby zapobiec pętli replikacji.

W tym samouczku konfigurowania replikacji źródłowego i docelowego. Dla innych topologiach klastra [podręczniku bazy danych Apache HBase](https://hbase.apache.org/book.html#_cluster_replication).

Przypadki użycia replikacji bazy danych HBase w jednej sieci wirtualnej są następujące:

* Równoważenie obciążenia. Na przykład można uruchomić skanowania lub zadań MapReduce w docelowym klastrze i pozyskiwania danych w klastrze źródłowym.
* Dodawanie wysokiej dostępności.
* Migrowanie danych z jednego klastra HBase do innego.
* Uaktualnianie klastra usługi Azure HDInsight z jednej wersji do innego.

Przypadki użycia replikacji bazy danych HBase, dwie sieci wirtualne są następujące:

* Konfigurowanie odzyskiwania po awarii.
* Równoważenie obciążenia i partycjonowanie aplikacji.
* Dodawanie wysokiej dostępności.

Klastry można replikować przy użyciu [skryptu akcji](../hdinsight-hadoop-customize-cluster-linux.md) skryptów z [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem tego samouczka musisz dysponować subskrypcją platformy Azure. Zobacz [uzyskać bezpłatna wersja próbna platformy](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="set-up-the-environments"></a>Konfigurowanie środowiska

Masz trzy opcje konfiguracji:

- Dwa klastry Apache HBase w jednej sieci wirtualnej platformy Azure.
- Dwa klastry Apache HBase w dwóch różnych sieciach wirtualnych w tym samym regionie.
- Dwa klastry Apache HBase w dwóch różnych sieciach wirtualnych w dwóch różnych regionach (replikacja geograficzna).

W tym artykule opisano scenariusz replikacji geograficznej.

Ułatwiające konfigurowanie środowisk, utworzyliśmy niektóre [szablonów usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Jeśli wolisz skonfigurować tych środowisk przy użyciu innych metod, zobacz:

- [Tworzenie klastrów usługi Apache Hadoop w HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Tworzenie klastrów Apache HBase w usłudze Azure Virtual Network](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Konfigurowanie dwiema sieciami wirtualnymi w dwóch różnych regionach

Aby użyć szablonu, który tworzy dwie sieci wirtualne w dwóch różnych regionach i połączenia sieci VPN między sieciami wirtualnymi, należy wybrać następujące opcje **Wdróż na platformie Azure** przycisku. Definicja szablonu jest przechowywana w [magazynu obiektów blob publicznych](https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

Część zakodowanych wartości w szablonie:

**Sieć wirtualna 1**

| Właściwość | Wartość |
|----------|-------|
| Lokalizacja | Zachodnie stany USA |
| Nazwa sieci wirtualnej | &lt;ClusterNamePrevix>-vnet1 |
| Prefiks przestrzeni adresowej | 10.1.0.0/16 |
| Nazwa podsieci | Podsieć 1 |
| Prefiks podsieci | 10.1.0.0/24 |
| Nazwa podsieci (bramy) | GatewaySubnet (nie można zmienić) |
| Prefiks podsieci (bramy) | 10.1.255.0/27 |
| Nazwa bramy | vnet1gw |
| Typ bramy | Vpn |
| Typ sieci VPN bramy | RouteBased |
| Jednostka SKU bramy | Podstawowa |
| Brama IP | vnet1gwip |

**2 w sieci wirtualnej**

| Właściwość | Wartość |
|----------|-------|
| Lokalizacja | Wschodnie stany USA |
| Nazwa sieci wirtualnej | &lt;ClusterNamePrevix>-vnet2 |
| Prefiks przestrzeni adresowej | 10.2.0.0/16 |
| Nazwa podsieci | Podsieć 1 |
| Prefiks podsieci | 10.2.0.0/24 |
| Nazwa podsieci (bramy) | GatewaySubnet (nie można zmienić) |
| Prefiks podsieci (bramy) | 10.2.255.0/27 |
| Nazwa bramy | vnet2gw |
| Typ bramy | Vpn |
| Typ sieci VPN bramy | RouteBased |
| Jednostka SKU bramy | Podstawowa |
| Brama IP | vnet1gwip |

## <a name="setup-dns"></a>Ustawienia DNS

W ostatniej sekcji ten szablon tworzy maszynę wirtualną Ubuntu w każdej z dwiema sieciami wirtualnymi.  W tej sekcji Zainstaluj powiązania na dwie maszyny wirtualne DNS, a następnie skonfiguruj przesyłania dalej DNS na dwie maszyny wirtualne.

Aby zainstalować powiązania, yon, należy znaleźć publiczny adres IP z dwóch maszyn wirtualnych DNS.

1. Otwórz [portal Azure](https://portal.azure.com).
2. Otwórz DNS maszyny wirtualnej, wybierając **grupy zasobów > [Nazwa grupy zasobów] > [vnet1DNS]**.  Nazwa grupy zasobów jest ten, który zostanie utworzony w poprzedniej procedurze. Domyślne nazwy maszyny wirtualnej DNS są *vnet1DNS* i *vnet2NDS*.
3. Wybierz **właściwości** aby otworzyć stronę właściwości sieci wirtualnej.
4. Zapisz **publiczny adres IP**, a także sprawdzić **prywatny adres IP**.  Prywatny adres IP jest **10.1.0.4** dla vnet1DNS i **10.2.0.4** dla vnet2DNS.  
5. Zmień serwery DNS dla obu sieci wirtualnych do korzystania z serwerów domyślne (DNS platformy Azure) umożliwia dostęp ruchu przychodzącego i wychodzącego pobrać pakiety do zainstalowania powiązania w poniższych krokach.

Aby zainstalować Bind, użyj następującej procedury:

1. Używanie protokołu SSH, aby nawiązać połączenie __publiczny adres IP__ DNS maszyny wirtualnej. Poniższy przykład łączy się z maszyną wirtualną w 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Zastąp `sshuser` przy użyciu konta użytkownika SSH można określić podczas tworzenia maszyny wirtualnej DNS.

    > [!NOTE]  
    > Istnieją różne sposoby uzyskania `ssh` narzędzia. W systemie Linux, Unix i z systemem macOS jest ona udostępniana jako część systemu operacyjnego. Jeśli używasz Windows należy rozważyć użycie jednej z następujących opcji:
    >
    > * [Azure Cloud Shell](../../cloud-shell/quickstart.md)
    > * [Bash on Ubuntu on Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Aby zainstalować Bind, użyj następujących poleceń w sesji SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Konfigurowanie powiązania do przekazywania żądań rozpoznawania nazw do na lokalny serwer DNS. Aby to zrobić, skorzystaj z poniższego tekstu jako zawartość `/etc/bind/named.conf.options` pliku:

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
            168.63.129.16; #This is the Azure DNS server
        };

        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
    };
    ```
    
    > [!IMPORTANT]  
    > Zastąp wartości w `goodclients` części dwiema sieciami wirtualnymi przy użyciu zakresu adresów IP. Ta sekcja definiuje adresy, które ten serwer DNS odbiera z.

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

    Muszą również znaleźć się sufiks DNS z innego serwera DNS. Należy go w następnym kroku.

5. Aby skonfigurować powiązania do rozpoznawania nazw DNS dla zasobów w sieci wirtualnej, skorzystaj z poniższego tekstu jako zawartość `/etc/bind/named.conf.local` pliku:

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > Należy zastąpić `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` z sufiksem DNS innej sieci wirtualnej. I adres IP usługi przesyłania dalej prywatny adres IP serwera DNS w sieci wirtualnej.

    Aby edytować ten plik, użyj następującego polecenia:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Aby zapisać plik, użyj __Ctrl + X__, __Y__, a następnie __Enter__.

6. Aby rozpocząć Bind, użyj następującego polecenia:

    ```bash
    sudo service bind9 restart
    ```

7. Aby zweryfikować, że powiązania stanie rozpoznawać nazwy zasobów w innej sieci wirtualnej, użyj następujących poleceń:

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    ```

    > [!IMPORTANT]  
    > Zastąp `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` z w pełni kwalifikowana nazwa domeny (FQDN) DNS maszyny wirtualnej w innej sieci.
    >
    > Zastąp `10.2.0.4` z __wewnętrzny adres IP__ niestandardowego serwera DNS w sieci wirtualnej.

    Odpowiedź jest wyświetlana podobny do następującego tekstu:

    ```
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    Do tej pory nie może wyszukać adres IP z sieci bez określonego adresu IP serwera DNS.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Konfigurowanie sieci wirtualnej do użycia niestandardowego serwera DNS

Aby skonfigurować sieci wirtualnej, aby użyć niestandardowego serwera DNS zamiast mechanizmu rozpoznawania cyklicznego platformy Azure, użyj następujących czynności:

1. W [witryny Azure portal](https://portal.azure.com), wybierz sieć wirtualną, a następnie wybierz __serwerów DNS__.

2. Wybierz __niestandardowe__, a następnie wprowadź __wewnętrzny adres IP__ niestandardowego serwera DNS. Na koniec wybierz pozycję __Zapisz__.

6. Otwieranie maszyny wirtualnej serwera DNS w sieci vnet1, a następnie kliknij przycisk **ponowne uruchomienie**.  W sieci wirtualnej umożliwiają konfigurację DNS zaczęły obowiązywać, należy ponownie uruchomić wszystkich maszyn wirtualnych.
7. Powtórz kroki konfigurowania niestandardowego serwera DNS dla sieci vnet2.

Aby przetestować konfigurację systemu DNS, nawiązać połączenie z dwóch maszyn wirtualnych DNS przy użyciu protokołu SSH i wykonać polecenie ping serwer DNS innej sieci wirtualnej przy użyciu nazwy hosta. Jeśli to nie zadziała, użyj następującego polecenia, aby sprawdzić stan usługi DNS:

```bash
sudo service bind9 status
```

## <a name="create-apache-hbase-clusters"></a>Tworzenie klastrów Apache HBase

Tworzenie [bazy danych Apache HBase](https://hbase.apache.org/) klastrowania w każdym z dwiema sieciami wirtualnymi przy użyciu następującej konfiguracji:

- **Nazwa grupy zasobów**: Użyj tej samej nazwy grupy zasobów, podczas tworzenia sieci wirtualnych.
- **Typ klastra**: HBase
- **Wersja**: HBase 1.1.2 (HDI 3.6)
- **Lokalizacja**: Użyj tej samej lokalizacji co sieć wirtualna.  Domyślnie jest vnet1 *zachodnie stany USA*, a sieć vnet2 *wschodnie stany USA*.
- **Magazyn**: Utwórz nowe konto magazynu dla klastra.
- **Sieć wirtualna** (z ustawień zaawansowanych w portalu): Wybierz sieć vnet1, utworzony w poprzedniej procedurze.
- **Podsieć**: Domyślna nazwa używana w szablonie jest **subnet1**.

Aby upewnić się, że środowisko jest skonfigurowane prawidłowo, musi być odpytywać FQDN węzła głównego między dwoma klastrami.

## <a name="load-test-data"></a>Ładowanie danych testowych

W przypadku replikacji klastra należy określić tabel, które mają być replikowane. W tej sekcji możesz załadować dane do klastra źródłowego. W następnej sekcji spowoduje włączenie replikacji między dwoma klastrami.

Aby utworzyć **kontakty** tabeli i wstawić dane w tabeli, postępuj zgodnie z instrukcjami w artykule [samouczek bazy danych Apache HBase: Rozpoczęcie korzystania z bazy danych Apache HBase w HDInsight](apache-hbase-tutorial-get-started-linux.md).

## <a name="enable-replication"></a>Włączanie replikacji

Poniżej opisano sposób wywoływania akcji skryptu skrypt z witryny Azure portal. Aby dowiedzieć się, jak uruchamianie akcji skryptu za pomocą programu Azure PowerShell i klasycznego wiersza polecenia platformy Azure, zobacz [HDInsight Dostosowywanie klastrów za pomocą akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md).

**Aby włączyć replikację bazy danych HBase w witrynie Azure portal**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Otwórz źródło klastra HBase.
3. Wybierz z menu klastra **akcji skryptu**.
4. W górnej części strony wybierz **Prześlij nową**.
5. Wybierz lub wprowadź następujące informacje:

   1. **Nazwa**: Wprowadź **włączyć replikację**.
   2. **Adres URL skryptu programu bash**: Wprowadź **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**.
   3. **HEAD**: Upewnij się, że ta opcja jest zaznaczona. Wyczyść typy węzłów.
   4. **Parametry**: Następujące przykładowe parametry włączyć replikację dla wszystkich istniejących tabel, a następnie skopiuj wszystkie dane z klastra źródłowego do klastra docelowego:

          -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
      > [!NOTE]
      > Użyj nazwy hosta zamiast nazwy FQDN dla nazwy DNS klastra źródłowego i docelowego.

6. Wybierz pozycję **Utwórz**. Skrypt może wymagać trochę czasu, aby uruchomić, szczególnie w przypadku, gdy używasz **- copydata** argumentu.

Wymagane argumenty:

|Name (Nazwa)|Opis|
|----|-----------|
|-s, --src-cluster | Określa nazwę DNS klastra HBase źródła. Na przykład: hbsrccluster -s, klaster — src = hbsrccluster |
|-d, --dst-cluster | Określa nazwę DNS klastra HBase docelowego (repliki). Na przykład: dsthbcluster -s, klaster — src = dsthbcluster |
|-sp, w src — hasło ambari | Określa hasło administratora dla Ambari w klaster HBase źródła. |
|— punkt dystrybucji, w czasu letniego — hasło ambari | Określa hasło administratora dla Ambari w docelowym klastrze bazy danych HBase.|

Argumenty opcjonalne:

|Name (Nazwa)|Opis|
|----|-----------|
|-su, --src-ambari-user | Określa nazwę użytkownika administratora Ambari w klaster HBase źródła. Wartość domyślna to **administratora**. |
|— jednostka bazy danych, czasu letniego — ambari użytkownika | Określa nazwę użytkownika administratora Ambari w docelowym klastrze bazy danych HBase. Wartość domyślna to **administratora**. |
|-t,--listy tabel | Określa tabele, które mają być replikowane. Na przykład:--listy tabel = "Tabela1; table2; Tabela3". Jeśli nie określisz tabele są replikowane wszystkich istniejących tabel HBase.|
|-m,--maszyny | Określa węzeł główny, w którym jest uruchamiany akcji skryptu. Wartość to **hn0** lub **hn1** i powinna być wybrana oparta na który jest aktywnym węzłem głównym. Użyj tej opcji, po uruchomieniu skryptu $0 jako akcja skryptu HDInsight portal lub programu Azure PowerShell.|
|-cp, -copydata | Umożliwia migrację istniejących danych w tabelach, w którym replikacja jest włączona. |
|-rpm, -replicate-phoenix-meta | Umożliwia replikację dla rozwiązania Phoenix tabel systemowych. <br><br>*Użyj tej opcji z rozwagą.* Firma Microsoft zaleca, możesz ponownie utworzyć tabele Phoenix w klastrach repliki przed użyciem tego skryptu. |
|-h, — pomoc | Wyświetla informacje o użyciu. |

`print_usage()` Części [skryptu](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) zawiera szczegółowy opis parametrów.

Po pomyślnym wdrożeniu akcji skryptu umożliwia protokołu SSH Połącz się z klastrem HBase docelowego, a następnie sprawdź, czy replikowane dane.

### <a name="replication-scenarios"></a>Scenariusze replikacji

Na poniższej liście przedstawiono niektóre przypadki użycia ogólne i ich ustawienia parametru:

- **Włącz replikację dla wszystkich tabel między dwoma klastrami**. Ten scenariusz nie wymaga kopiowania lub Migrowanie istniejących danych w tabelach, a nie używa Phoenix tabel. Należy użyć następujących parametrów:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Włącz replikację na określonych tabel**. Aby włączyć replikację na table1, table2 i Tabela3, należy użyć następujących parametrów:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Włącz replikację na określonych tabel, a następnie skopiuj istniejących danych**. Aby włączyć replikację na table1, table2 i Tabela3, należy użyć następujących parametrów:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Włącz replikację dla wszystkich tabel i replikacji metadanych Phoenix ze źródła do miejsca docelowego**. Phoenix metadanych replikacji nie jest doskonały. Jej używać z rozwagą. Należy użyć następujących parametrów:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Skopiuj i przeprowadzić migrację danych

Dostępne są dwa skrypty akcji skryptu oddzielne kopiowania lub migracji danych, po włączeniu replikacji:

- [Skrypt małe tabele](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (tabel, które są kilku gigabajtów, rozmiar i ogólny kopii oczekuje się, aby zakończyć w mniej niż godzinę)

- [Skrypt dla dużych tabel](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (tabele, które powinny trwać dłużej niż godzinę, aby skopiować)

Możesz wykonać tę samą procedurę opisaną w [włączyć replikację](#enable-replication) do wywoływania akcji skryptu. Należy użyć następujących parametrów:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

`print_usage()` Części [skryptu](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) zawiera szczegółowy opis parametrów.

### <a name="scenarios"></a>Scenariusze

- **Skopiuj określonych tabel (test1 test2 i test3) we wszystkich wierszach edytować do tej pory (bieżąca sygnatura czasowa)**:

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  lub:

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Skopiuj określonych tabel z określonego zakresu czasu**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>Wyłączanie replikacji

Aby wyłączyć replikację, użyj innego skryptu akcji skryptu [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Możesz wykonać tę samą procedurę opisaną w [włączyć replikację](#enable-replication) do wywoływania akcji skryptu. Należy użyć następujących parametrów:

    -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

`print_usage()` Części [skryptu](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) zawiera szczegółowy opis parametrów.

### <a name="scenarios"></a>Scenariusze

- **Wyłącz replikację na wszystkich tabelach**:

        -m hn1 -s <source hbase cluster name> -sp Mypassword\!789 -all
  lub

        --src-cluster=<source hbase cluster name> --dst-cluster=<destination hbase cluster name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **Wyłącz replikację na określonych tabel (table1, table2 i Tabela3)**:

        -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób konfigurowania replikacji bazy danych Apache HBase w sieci wirtualnej lub między dwiema sieciami wirtualnymi. Aby dowiedzieć się więcej na temat HDInsight i bazy danych Apache HBase, zobacz następujące artykuły:

* [Rozpoczynanie pracy z usługą Apache HBase w HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Omówienie bazy danych Apache HDInsight HBase](./apache-hbase-overview.md)
* [Tworzenie klastrów Apache HBase w usłudze Azure Virtual Network](./apache-hbase-provision-vnet.md)

