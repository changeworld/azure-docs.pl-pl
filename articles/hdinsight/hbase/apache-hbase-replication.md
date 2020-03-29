---
title: Replikacja klastra HBase w sieciach wirtualnych — Usługa Azure HDInsight
description: Dowiedz się, jak skonfigurować replikację HBase z jednej wersji USŁUGI HDInsight do innej w celu równoważenia obciążenia, wysokiej dostępności, migracji i aktualizacji bez przestojów oraz odzyskiwania po awarii.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 1e6465584dd4e67f736b94d2939678c1a69163bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435662"
---
# <a name="set-up-apache-hbase-cluster-replication-in-azure-virtual-networks"></a>Konfigurowanie replikacji klastra Apache HBase w sieciach wirtualnych platformy Azure

Dowiedz się, jak skonfigurować replikację [Apache HBase](https://hbase.apache.org/) w sieci wirtualnej lub między dwiema sieciami wirtualnymi na platformie Azure.

Replikacja klastra używa metodologii wypychania źródłowego. Klaster HBase może być źródłem lub miejscem docelowym lub może spełniać obie role jednocześnie. Replikacja jest asynchroniza. Celem replikacji jest spójność ostateczna. Gdy źródło odbiera edycję do rodziny kolumn, gdy replikacja jest włączona, edycja jest propagowana do wszystkich klastrów docelowych. Gdy dane są replikowane z jednego klastra do drugiego, klaster źródłowy i wszystkie klastry, które już zostały wykorzystane dane są śledzone, aby zapobiec pętli replikacji.

W tym artykule skonfigurować replikację docelową źródła. Aby zapoznać się z innymi topologiami klastra, zobacz [przewodnik apache HBase .](https://hbase.apache.org/book.html#_cluster_replication)

Poniżej przedstawiono przypadki użycia replikacji HBase dla pojedynczej sieci wirtualnej:

* Równoważenie obciążenia. Na przykład można uruchomić skanowanie lub MapReduce zadań w klastrze docelowym i pozyskiwania danych w klastrze źródłowym.
* Dodawanie wysokiej dostępności.
* Migrowanie danych z jednego klastra HBase do innego.
* Uaktualnianie klastra usługi Azure HDInsight z jednej wersji do drugiej.

Poniżej przedstawiono przypadki użycia replikacji HBase dla dwóch sieci wirtualnych:

* Konfigurowanie odzyskiwania po awarii.
* Równoważenie obciążenia i partycjonowanie aplikacji.
* Dodawanie wysokiej dostępności.

Klastry można replikować przy użyciu skryptów [akcji](../hdinsight-hadoop-customize-cluster-linux.md) ze strony [gitHub](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem tego artykułu musisz mieć subskrypcję platformy Azure. Zobacz [Pobierz bezpłatną wersję próbną platformy Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="set-up-the-environments"></a>Konfigurowanie środowisk

Dostępne są trzy opcje konfiguracji:

- Dwa klastry Apache HBase w jednej sieci wirtualnej platformy Azure.
- Dwa klastry Apache HBase w dwóch różnych sieciach wirtualnych w tym samym regionie.
- Dwa klastry Apache HBase w dwóch różnych sieciach wirtualnych w dwóch różnych regionach (replikacja geograficzna).

W tym artykule opisano scenariusz replikacji geograficznej.

Aby ułatwić konfigurowanie środowisk, utworzyliśmy niektóre [szablony usługi Azure Resource Manager](../../azure-resource-manager/management/overview.md). Jeśli wolisz skonfigurować środowiska przy użyciu innych metod, zobacz:

- [Tworzenie klastrów Apache Hadoop w udziale HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Tworzenie klastrów Apache HBase w sieci wirtualnej platformy Azure](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Konfigurowanie dwóch sieci wirtualnych w dwóch różnych regionach

Aby użyć szablonu, który tworzy dwie sieci wirtualne w dwóch różnych regionach i połączenia sieci VPN między sieciami wirtualnymi, wybierz następujący przycisk **Wdrażanie na platformie Azure.** Definicja szablonu jest przechowywana w [publicznym magazynie obiektów blob](https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

Niektóre wartości zakodowane na czas w szablonie:

**Wirtualna 1**

| Właściwość | Wartość |
|----------|-------|
| Lokalizacja | Zachodnie stany USA |
| Nazwa sieci wirtualnej | &lt;ClusterNamePrevix>-vnet1 |
| Prefiks przestrzeni adresowej | 10.1.0.0/16 |
| Nazwa podsieci | podsieć 1 |
| Prefiks podsieci | 10.1.0.0/24 |
| Nazwa podsieci (bramy) | GatewaySubnet (nie można zmienić) |
| Prefiks podsieci (bramy) | 10.1.255.0/27 |
| Nazwa bramy | vnet1gw |
| Typ bramy | Vpn |
| Typ sieci VPN bramy | RouteBased |
| Jednostka SKU bramy | Podstawowa (Basic) |
| Adres IP bramy | vnet1gwip |

**Wirtualna 2**

| Właściwość | Wartość |
|----------|-------|
| Lokalizacja | Wschodnie stany USA |
| Nazwa sieci wirtualnej | &lt;ClusterNamePrevix>-vnet2 |
| Prefiks przestrzeni adresowej | 10.2.0.0/16 |
| Nazwa podsieci | podsieć 1 |
| Prefiks podsieci | 10.2.0.0/24 |
| Nazwa podsieci (bramy) | GatewaySubnet (nie można zmienić) |
| Prefiks podsieci (bramy) | 10.2.255.0/27 |
| Nazwa bramy | vnet2gw |
| Typ bramy | Vpn |
| Typ sieci VPN bramy | RouteBased |
| Jednostka SKU bramy | Podstawowa (Basic) |
| Adres IP bramy | vnet1gwip |

## <a name="setup-dns"></a>Konfigurowanie systemu DNS

W ostatniej sekcji szablon tworzy maszynę wirtualną Ubuntu w każdej z dwóch sieci wirtualnych.  W tej sekcji należy zainstalować bind na dwóch maszynach wirtualnych DNS, a następnie skonfigurować przekazywanie DNS na dwóch maszynach wirtualnych.

Aby zainstalować binda, yon musi znaleźć publiczny adres IP dwóch maszyn wirtualnych DNS.

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).
2. Otwórz maszynę wirtualną DNS, wybierając **pozycję Grupy zasobów > [nazwa grupy zasobów] > [vnet1DNS]**.  Nazwa grupy zasobów jest nazwą utworzyć w ostatniej procedurze. Domyślne nazwy maszyn wirtualnych DNS to *vnet1DNS* i *vnet2NDS*.
3. Wybierz **polecenie Właściwości,** aby otworzyć stronę właściwości sieci wirtualnej.
4. Zapisz **publiczny adres IP,** a także sprawdź **prywatny adres IP**.  Prywatny adres IP wynosi **10.1.0.4** dla vnet1DNS i **10.2.0.4** dla vnet2DNS.  
5. Zmień serwery DNS dla obu sieci wirtualnych, aby używały domyślnych serwerów DNS (dostarczonych przez platformę Azure), aby umożliwić dostęp przychodzący i wychodzący do pobierania pakietów w celu zainstalowania powiązania w poniższych krokach.

Aby zainstalować binda, należy użyć następującej procedury:

1. Użyj protokołu SSH, aby połączyć się z __publicznym adresem IP__ maszyny wirtualnej DNS. Poniższy przykład łączy się z maszyną wirtualną w 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Zamień `sshuser` konto użytkownika SSH określone podczas tworzenia maszyny wirtualnej DNS.

    > [!NOTE]  
    > Istnieje wiele sposobów, aby uzyskać `ssh` narzędzie. W systemach Linux, Unix i macOS jest on dostarczany jako część systemu operacyjnego. Jeśli korzystasz z systemu Windows, rozważ jedną z następujących opcji:
    >
    > * [Azure Cloud Shell](../../cloud-shell/quickstart.md)
    > * [Bash na Ubuntu w systemie Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Aby zainstalować binda, użyj następujących poleceń z sesji SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Skonfiguruj binda do przesyłania dalej żądań rozpoznawania nazw do lokalnego serwera DNS. Aby to zrobić, użyj następującego tekstu `/etc/bind/named.conf.options` jako zawartości pliku:

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
    > Zastąp `goodclients` wartości w sekcji zakresem adresów IP dwóch sieci wirtualnych. W tej sekcji zdefiniowano adresy, z których ten serwer DNS akceptuje żądania.

    Aby edytować ten plik, użyj następującego polecenia:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Aby zapisać plik, użyj __klawiszy Ctrl+X__, __Y__, a następnie __wprowadź__.

4. W sesji SSH użyj następującego polecenia:

    ```bash
    hostname -f
    ```

    To polecenie zwraca wartość podobną do następującego tekstu:

        vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    Tekst `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` jest __sufiksem DNS__ dla tej sieci wirtualnej. Zapisz tę wartość, ponieważ jest używana później.

    Sufiks DNS należy również znaleźć z innego serwera DNS. Potrzebujesz go w następnym kroku.

5. Aby skonfigurować powiązanie do rozpoznawania nazw DNS zasobów w sieci wirtualnej, użyj następującego tekstu jako zawartości `/etc/bind/named.conf.local` pliku:

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > Należy zastąpić `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` sufiks DNS innej sieci wirtualnej. A adres IP usługi przesyłania dalej jest prywatnym adresem IP serwera DNS w innej sieci wirtualnej.

    Aby edytować ten plik, użyj następującego polecenia:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Aby zapisać plik, użyj __klawiszy Ctrl+X__, __Y__, a następnie __wprowadź__.

6. Aby uruchomić binda, użyj następującego polecenia:

    ```bash
    sudo service bind9 restart
    ```

7. Aby sprawdzić, czy powiązanie może rozpoznać nazwy zasobów w innej sieci wirtualnej, należy użyć następujących poleceń:

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    ```

    > [!IMPORTANT]  
    > Zastąp `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` w pełni kwalifikowaną nazwą domeny (FQDN) maszyny wirtualnej DNS w innej sieci.
    >
    > Zamień `10.2.0.4` na __wewnętrzny adres IP__ niestandardowego serwera DNS w innej sieci wirtualnej.

    Odpowiedź wygląda podobnie do następującego tekstu:

    ```
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    Do tej pory nie można wyszukać adresu IP z innej sieci bez określonego adresu IP serwera DNS.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Konfigurowanie sieci wirtualnej do używania niestandardowego serwera DNS

Aby skonfigurować sieć wirtualną do używania niestandardowego serwera DNS zamiast cyklicznego programu rozpoznawania nazw platformy Azure, należy wykonać następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com)wybierz sieć wirtualną, a następnie wybierz pozycję __Serwery DNS__.

2. Wybierz __opcję Niestandardowe__i wprowadź __wewnętrzny adres IP__ niestandardowego serwera DNS. Na koniec wybierz pozycję __Zapisz__.

6. Otwórz maszynę wirtualną serwera DNS w sieci wirtualnej1 i kliknij przycisk **Uruchom ponownie**.  Aby konfiguracja DNS została uwzględnić, należy ponownie uruchomić wszystkie maszyny wirtualne w sieci wirtualnej.
7. Powtórz kroki konfigurowania niestandardowego serwera DNS dla sieci wirtualnej2.

Aby przetestować konfigurację DNS, można połączyć się z dwoma maszynami wirtualnymi DNS przy użyciu funkcji SSH i przepauć serwer DNS innej sieci wirtualnej przy użyciu jej nazwy hosta. Jeśli to nie zadziała, użyj następującego polecenia, aby sprawdzić stan DNS:

```bash
sudo service bind9 status
```

## <a name="create-apache-hbase-clusters"></a>Tworzenie klastrów Apache HBase

Utwórz klaster [Apache HBase](https://hbase.apache.org/) w każdej z dwóch sieci wirtualnych z następującą konfiguracją:

- **Nazwa grupy zasobów:** użyj tej samej nazwy grupy zasobów, co sieci wirtualne.
- **Typ klastra**: HBase
- **Wersja**: HBase 1.1.2 (HDI 3.6)
- **Lokalizacja:** Użyj tej samej lokalizacji co sieć wirtualna.  Domyślnie vnet1 to *Zachodnie stany USA,* a vnet2 to *wschodnie stany USA.*
- **Magazyn:** Utwórz nowe konto magazynu dla klastra.
- **Sieć wirtualna** (z ustawień zaawansowanych w portalu): wybierz vnet1 utworzoną w ostatniej procedurze.
- **Podsieć**: Domyślną nazwą używaną w szablonie jest **podsieć1**.

Aby upewnić się, że środowisko jest poprawnie skonfigurowane, należy mieć możliwość pingu sieci FQDN węzła czołowego między dwoma klastrami.

## <a name="load-test-data"></a>Ładowanie danych testowych

Podczas replikowania klastra należy określić tabele, które mają być replikowane. W tej sekcji należy załadować niektóre dane do klastra źródłowego. W następnej sekcji włączysz replikację między dwoma klastrami.

Aby utworzyć tabelę **kontaktów** i wstawić niektóre dane do tabeli, postępuj zgodnie z instrukcjami [w apache HBase samouczek: Wprowadzenie do korzystania z Apache HBase w HDInsight](apache-hbase-tutorial-get-started-linux.md).

> [!NOTE]
> Jeśli chcesz replikować tabele z niestandardowego obszaru nazw, należy upewnić się, że odpowiednie niestandardowe przestrzenie nazw są również zdefiniowane w klastrze docelowym.
>

## <a name="enable-replication"></a>Włączanie replikacji

W poniższych krokach opisano sposób wywoływania skryptu akcji skryptu z witryny Azure portal. Aby uzyskać informacje dotyczące uruchamiania akcji skryptu przy użyciu programu Azure PowerShell i klasycznego interfejsu wiersza polecenia platformy Azure, zobacz [Dostosowywanie klastrów usługi HDInsight przy użyciu akcji skryptu](../hdinsight-hadoop-customize-cluster-linux.md).

**Aby włączyć replikację bazy danych HBase z witryny Azure portal**

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Otwórz źródłowy klaster HBase.
3. W menu klastra wybierz polecenie **Akcje skryptu**.
4. U góry strony wybierz pozycję **Prześlij nowy**.
5. Wybierz lub wprowadź następujące informacje:

   1. **Nazwa**: Wprowadź **włącz replikację**.
   2. **Adres URL skryptu bash:** Enter **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**.
   3. **Głowa**: Upewnij się, że jest zaznaczona. Wyczyść inne typy węzłów.
   4. **Parametry:** Następujące parametry przykładowe umożliwiają replikację dla wszystkich istniejących tabel, a następnie kopiują wszystkie dane z klastra źródłowego do klastra docelowego:

          -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
      > [!NOTE]
      > Nazwa hosta zamiast nazwy FQDN należy używać zarówno nazwy DNS klastra źródłowego, jak i docelowego.
      >
      > W tym instruktażu przyjęto założenie, że hn1 jest aktywnym headnode. Sprawdź klaster, aby zidentyfikować aktywny węzeł główny.

6. Wybierz **pozycję Utwórz**. Uruchomienie skryptu może zająć trochę czasu, szczególnie w przypadku użycia argumentu **-copydata.**

Wymagane argumenty:

|Nazwa|Opis|
|----|-----------|
|-s, --src-cluster | Określa nazwę DNS źródłowego klastra HBase. Na przykład: -s hbsrccluster, --src-cluster=hbsrccluster |
|-d, --dst-cluster | Określa nazwę DNS docelowego (repliki) klastra HBase. Na przykład: -s dsthbcluster, --src-cluster=dsthbcluster |
|-sp, --src-ambari-hasło | Określa hasło administratora ambari w źródłowym klastrze HBase. |
|-dp, --dst-ambari-hasło | Określa hasło administratora ambari w docelowym klastrze HBase.|

Argumenty opcjonalne:

|Nazwa|Opis|
|----|-----------|
|-su, --src-ambari-user | Określa nazwę użytkownika administratora ambari w źródłowym klastrze HBase. Wartością domyślną jest **admin**. |
|-du, --dst-ambari-user | Określa nazwę użytkownika administratora ambari w docelowym klastrze HBase. Wartością domyślną jest **admin**. |
|-t, --table-list | Określa tabele, które mają być replikowane. Na przykład: --table-list="table1;table2;table3". Jeśli nie określisz tabel, wszystkie istniejące tabele HBase są replikowane.|
|-m, --maszyna | Określa węzeł główny, w którym jest uruchamiana akcja skryptu. Wartość powinna być wybrana na podstawie tego, który jest aktywny węzeł głowy. Użyj tej opcji podczas uruchamiania skryptu $0 jako akcji skryptu z portalu HDInsight lub programu Azure PowerShell.|
|-cp, -copydata | Umożliwia migrację istniejących danych w tabelach, w których replikacja jest włączona. |
|-obrpm, -replicate-phoenix-meta | Włącza replikację w tabelach systemowych Phoenix. <br><br>*Użyj tej opcji ostrożnie.* Przed użyciem tego skryptu zaleca się ponowne utworzenie tabel phoenix w klastrach replik. |
|-h, --pomoc | Wyświetla informacje o użytkowaniu. |

Sekcja `print_usage()` [skryptu](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) zawiera szczegółowe wyjaśnienie parametrów.

Po pomyślnym wdrożeniu akcji skryptu można użyć funkcji SSH, aby połączyć się z docelowym klastrem HBase, a następnie sprawdzić, czy dane zostały zreplikowane.

### <a name="replication-scenarios"></a>Scenariusze replikacji

Na poniższej liście przedstawiono niektóre ogólne przypadki użycia i ich ustawienia parametrów:

- **Włącz replikację we wszystkich tabelach między dwoma klastrami**. Ten scenariusz nie wymaga kopiowania lub migracji istniejących danych w tabelach i nie używa tabel Phoenix. Użyj następujących parametrów:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Włącz replikację w określonych tabelach**. Aby włączyć replikację w tabelach1, table2 i table3, należy użyć następujących parametrów:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Włącz replikację w określonych tabelach i skopiuj istniejące dane**. Aby włączyć replikację w tabelach1, table2 i table3, należy użyć następujących parametrów:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Włącz replikację we wszystkich tabelach i zreplikuj metadane Phoenix ze źródła do miejsca docelowego**. Replikacja metadanych Phoenix nie jest doskonała. Używaj go ostrożnie. Użyj następujących parametrów:

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Kopiowanie i migrowanie danych

Istnieją dwa oddzielne skrypty akcji skrypt dostępne do kopiowania lub migracji danych po włączeniu replikacji:

- [Skrypt dla małych tabel](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (tabele o rozmiarze kilku gigabajtów i całkowita kopia ma zakończyć się w mniej niż godzinę)

- [Skrypt dla dużych tabel](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (kopiowanie tabel, których kopiowanie ma potrwać dłużej niż godzinę)

Można wykonać tę samą procedurę, która jest opisana w [Włącz replikację,](#enable-replication) aby wywołać akcję skryptu. Użyj następujących parametrów:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

Sekcja `print_usage()` [skryptu](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) zawiera szczegółowy opis parametrów.

### <a name="scenarios"></a>Scenariusze

- **Kopiowanie określonych tabel (test1, test2 i test3) dla wszystkich wierszy edytowanych do tej pory (bieżąca sygnatura czasowa):**

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  Lub:

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Kopiowanie określonych tabel o określonym zakresie czasu:**

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>Wyłączanie replikacji

Aby wyłączyć replikację, użyj innego skryptu akcji skryptu z [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Można wykonać tę samą procedurę, która jest opisana w [Włącz replikację,](#enable-replication) aby wywołać akcję skryptu. Użyj następujących parametrów:

    -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

Sekcja `print_usage()` [skryptu](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) zawiera szczegółowe wyjaśnienie parametrów.

### <a name="scenarios"></a>Scenariusze

- **Wyłącz replikację we wszystkich tabelach:**

        -m hn1 -s <source hbase cluster name> -sp Mypassword\!789 -all
  lub

        --src-cluster=<source hbase cluster name> --dst-cluster=<destination hbase cluster name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **Wyłącz replikację w określonych tabelach (table1, table2 i table3):**

        -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> -t "table1;table2;table3"

> [!NOTE]
> Jeśli zamierzasz usunąć klaster docelowy, upewnij się, że usunąłeś go z listy równorzędnej klastra źródłowego. Można to zrobić, uruchamiając polecenie remove_peer "1" w powłoce hbase w klastrze źródłowym. W przeciwnym razie klaster źródłowy może nie działać poprawnie.
>

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak skonfigurować replikację Apache HBase w sieci wirtualnej lub między dwiema sieciami wirtualnymi. Aby dowiedzieć się więcej o hdinsight i Apache HBase, zobacz następujące artykuły:

* [Wprowadzenie do Apache HBase w hdinsight](./apache-hbase-tutorial-get-started-linux.md)
* [Omówienie bazy danych HDInsight Apache HBase](./apache-hbase-overview.md)
* [Tworzenie klastrów Apache HBase w sieci wirtualnej platformy Azure](./apache-hbase-provision-vnet.md)

