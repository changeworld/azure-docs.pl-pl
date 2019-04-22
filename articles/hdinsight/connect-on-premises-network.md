---
title: Łączenie usługi Azure HDInsight z siecią lokalną
description: Dowiedz się, jak utworzyć klaster usługi HDInsight w usłudze Azure Virtual Network, a następnie połącz go z sieci lokalnej. Dowiedz się, jak skonfigurować rozpoznawanie nazw między HDInsight i siecią lokalną za pomocą niestandardowego serwera DNS.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/04/2019
ms.openlocfilehash: 52fe8c05101f9647549acec276f0bdb9fa52d1c7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59256808"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>Łączenie usługi HDInsight z siecią lokalną

Dowiedz się, jak połączyć HDInsight z siecią lokalną za pomocą usługi Azure Virtual Networks i bramy sieci VPN. Ten dokument zawiera informacje na temat planowania na:

* Używanie HDInsight w sieci wirtualnej platformy Azure, który nawiązuje połączenie z siecią lokalną.
* Konfigurowanie rozpoznawania nazw DNS między sieć wirtualną i siecią lokalną.
* Konfigurowanie grup zabezpieczeń sieci, aby ograniczyć dostęp do Internetu do HDInsight.
* Porty udostępnianego przez usługę HDInsight na sieć wirtualną.

## <a name="overview"></a>Omówienie

Aby umożliwić HDInsight i zasobów w dołączonym do sieci do komunikowania się według nazwy, należy wykonać następujące czynności:

* Tworzenie sieci wirtualnej platformy Azure.
* Tworzenie niestandardowego serwera DNS w usłudze Azure Virtual Network.
* Konfigurowanie sieci wirtualnej, aby użyć niestandardowego serwera DNS zamiast domyślnej Azure cyklicznego programu rozpoznawania nazw.
* Konfigurowanie składnika przesyłanie dalej między niestandardowego serwera DNS i serwerem DNS w środowisku lokalnym.

Ta konfiguracja umożliwia następujące zachowanie:

* Żądania dla w pełni kwalifikowanych nazw domen, które mają sufiks DNS __dla sieci wirtualnej__ są przekazywane do niestandardowego serwera DNS. Niestandardowy serwer DNS przekazuje następnie te żądania na platformie Azure cyklicznego programu rozpoznawania nazw, która zwraca adres IP.
* Wszystkie inne żądania są przekazywane do lokalnego serwera DNS. Nawet żądania publicznych zasobów internetowych, np. microsoft.com są przekazywane do lokalnego serwera DNS do rozpoznawania nazw.

Na poniższym diagramie zielony wiersze są żądania dotyczące zasobów, które kończą się na sufiks DNS w sieci wirtualnej. Niebieskie linie są żądania dotyczące zasobów w sieci lokalnej lub w publicznym Internecie.

![Diagram przedstawiający sposób żądania DNS są rozpoznawane w konfiguracji używane w tym dokumencie](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).
* Jeśli przy użyciu programu PowerShell, konieczne będzie [modułu AZ](https://docs.microsoft.com/powershell/azure/overview).
* Jeśli chcą używać wiersza polecenia platformy Azure i nie został jeszcze zainstalowany go, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-virtual-network-configuration"></a>Utwórz konfigurację sieci wirtualnej

Aby dowiedzieć się, jak utworzyć sieci wirtualnej platformy Azure, która jest połączona z siecią lokalną, należy użyć następujących dokumentów:

* [Korzystanie z witryny Azure Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Korzystanie z programu Azure PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Korzystanie z interfejsu wiersza polecenia platformy Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="create-custom-dns-server"></a>Tworzenie niestandardowego serwera DNS

> [!IMPORTANT]  
> Należy utworzyć i skonfigurować serwer DNS, przed zainstalowaniem HDInsight w sieci wirtualnej.

Te kroki odnoszą się [witryny Azure portal](https://portal.azure.com) utworzyć maszynę wirtualną platformy Azure. Inne sposoby tworzenia maszyny wirtualnej, zobacz [Utwórz maszynę Wirtualną — interfejs wiersza polecenia Azure](../virtual-machines/linux/quick-create-cli.md) i [Utwórz maszynę Wirtualną — program Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md).  Do utworzenia maszyny Wirtualnej systemu Linux, który używa [powiązać](https://www.isc.org/downloads/bind/) oprogramowania DNS, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
  
2. W menu po lewej stronie przejdź do **+ Utwórz zasób** > **obliczenia** > **Ubuntu Server 18.04 LTS**.

    ![Tworzenie maszyny wirtualnej systemu Ubuntu](./media/connect-on-premises-network/create-ubuntu-vm.png)

3. Z __podstawy__ wprowadź następujące informacje:  
  
    | Pole | Wartość |
    | --- | --- |
    |Subskrypcja |Wybierz odpowiednią subskrypcję.|
    |Grupa zasobów |Wybierz grupę zasobów, która zawiera utworzonej wcześniej sieci wirtualnej.|
    |Nazwa maszyny wirtualnej | Wprowadź przyjazną nazwę, która identyfikuje tę maszynę wirtualną. W tym przykładzie użyto **DNSProxy**.|
    |Region | Wybierz tego samego regionu co sieć wirtualna została utworzona wcześniej.  Nie wszystkie rozmiary maszyn wirtualnych są dostępne we wszystkich regionach.  |
    |Opcje dostępności |  Wybierz żądany poziom dostępności.  Platforma Azure oferuje szeroką gamę opcji zarządzania, dostępność oraz odporność aplikacji.  Architektury rozwiązania na potrzeby replikowanych maszyn wirtualnych w strefach dostępności lub zestawach dostępności, aby chronić aplikacje i dane przed awariami centrum danych i zdarzenia konserwacji. W tym przykładzie użyto **nie nadmiarowości infrastruktury wymagane**. |
    |Image (Obraz) | Pozostaw **LTS Ubuntu Server 18.04**. |
    |Typ uwierzytelniania | __Hasło__ lub __klucz publiczny SSH__: Metoda uwierzytelniania dla konta SSH. Zalecamy używanie kluczy publicznych, ponieważ są one bardziej bezpieczne. W tym przykładzie użyto **hasło**.  Aby uzyskać więcej informacji, zobacz [tworzenia i używania kluczy SSH dla maszyn wirtualnych systemu Linux](../virtual-machines/linux/mac-create-ssh-keys.md) dokumentu.|
    |Nazwa użytkownika |Wprowadź nazwę użytkownika administratora dla maszyny Wirtualnej.  W tym przykładzie użyto **sshuser**.|
    |Klucz publiczny hasła lub protokołu SSH | Dostępne pola zależy od wybranego dla **typ uwierzytelniania**.  Wprowadź odpowiednią wartość.|
    |Publiczne porty wejściowe|Wybierz pozycję **Zezwalaj na wybrane porty**. Następnie wybierz pozycję **SSH (22)** z **wybierz porty dla ruchu przychodzącego** listy rozwijanej.|

    ![Podstawowa konfiguracja maszyny wirtualnej](./media/connect-on-premises-network/vm-basics.png)

    Pozostaw inne wpisy wartości domyślne, a następnie wybierz pozycję **sieć** kartę.

4. Z **sieć** wprowadź następujące informacje:

    | Pole | Wartość |
    | --- | --- |
    |Sieć wirtualna | Wybierz sieć wirtualną, która została utworzona wcześniej.|
    |Podsieć | Wybierz podsieć domyślna dla sieci wirtualnej, która została utworzona wcześniej. Czy __nie__ Wybierz podsieć, używany przez bramę sieci VPN.|
    |Publiczny adres IP | Użyj wartości wypełnianie automatycznie.  |

    ![Ustawienia sieci wirtualnej](./media/connect-on-premises-network/virtual-network-settings.png)

    Pozostaw inne wpisy wartości domyślne, a następnie wybierz pozycję **przeglądu + Utwórz**.

5. Z **przeglądu + Utwórz** zaznacz **Utwórz** do utworzenia maszyny wirtualnej.

### <a name="review-ip-addresses"></a>Przejrzyj adresów IP
Po utworzeniu maszyny wirtualnej zostanie wyświetlony **wdrażanie zakończyło się pomyślnie** powiadomienia o **przejdź do zasobu** przycisku.  Wybierz **przejdź do zasobu** można przejść do swojej nowej maszyny wirtualnej.  W widoku domyślnego z nową maszyną wirtualną wykonaj następujące kroki, aby zidentyfikować skojarzony adresy IP:

1. Z **ustawienia**, wybierz opcję **właściwości**.

2. Zwróć uwagę na wartości dla **publicznych etykieta nazwy DNS/adres IP** i **prywatny adres IP** do późniejszego użycia.

   ![Publiczne i prywatne adresy IP](./media/connect-on-premises-network/vm-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Instalowanie i konfigurowanie powiązania (oprogramowanie DNS)

1. Używanie protokołu SSH, aby nawiązać połączenie __publiczny adres IP__ maszyny wirtualnej. Zastąp `sshuser` przy użyciu konta użytkownika SSH można określić podczas tworzenia maszyny Wirtualnej. Poniższy przykład łączy się z maszyną wirtualną w 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

2. Aby zainstalować Bind, użyj następujących poleceń w sesji SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Aby skonfigurować powiązania do przekazywania żądań rozpoznawania nazw do na lokalny serwer DNS, skorzystaj z poniższego tekstu jako zawartość `/etc/bind/named.conf.options` pliku:

        acl goodclients {
            10.0.0.0/16; # Replace with the IP address range of the virtual network
            10.1.0.0/16; # Replace with the IP address range of the on-premises network
            localhost;
            localnets;
        };

        options {
                directory "/var/cache/bind";

                recursion yes;

                allow-query { goodclients; };

                forwarders {
                192.168.0.1; # Replace with the IP address of the on-premises DNS server
                };

                dnssec-validation auto;

                auth-nxdomain no;    # conform to RFC1035
                listen-on { any; };
        };

    > [!IMPORTANT]  
    > Zastąp wartości w `goodclients` części z zakresem adresów IP sieci wirtualnej i siecią lokalną. Ta sekcja definiuje adresy, które ten serwer DNS odbiera z.
    >
    > Zastąp `192.168.0.1` wpis `forwarders` sekcji przy użyciu adresu IP serwera DNS w środowisku lokalnym. Ten wpis kieruje żądania DNS do serwera DNS w środowisku lokalnym do rozpoznawania.

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

    ```output
    dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` Tekst jest __sufiks DNS__ dla tej sieci wirtualnej. Zapisz tę wartość, ponieważ jest używana później.

5. Aby skonfigurować powiązania do rozpoznawania nazw DNS dla zasobów w sieci wirtualnej, skorzystaj z poniższego tekstu jako zawartość `/etc/bind/named.conf.local` pliku:

        // Replace the following with the DNS suffix for your virtual network
        zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
            type forward;
            forwarders {168.63.129.16;}; # The Azure recursive resolver
        };

    > [!IMPORTANT]  
    > Należy zastąpić `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` sufiksu DNS, pobranym wcześniej.

    Aby edytować ten plik, użyj następującego polecenia:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Aby zapisać plik, użyj __Ctrl + X__, __Y__, a następnie __Enter__.

6. Aby rozpocząć Bind, użyj następującego polecenia:

    ```bash
    sudo service bind9 restart
    ```

7. Aby zweryfikować tego powiązania stanie rozpoznawać nazwy zasobów w sieci lokalnej, użyj następujących poleceń:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]  
    > Zastąp `dns.mynetwork.net` z w pełni kwalifikowana nazwa domeny (FQDN) zasobów w sieci lokalnej.
    >
    > Zastąp `10.0.0.4` z __wewnętrzny adres IP__ niestandardowego serwera DNS w sieci wirtualnej.

    Odpowiedź jest wyświetlana podobny do następującego tekstu:

    ```output
    Server:         10.0.0.4
    Address:        10.0.0.4#53

    Non-authoritative answer:
    Name:   dns.mynetwork.net
    Address: 192.168.0.4
    ```

## <a name="configure-virtual-network-to-use-the-custom-dns-server"></a>Konfigurowanie sieci wirtualnej do użycia niestandardowego serwera DNS

Aby skonfigurować sieci wirtualnej, aby użyć niestandardowego serwera DNS zamiast mechanizmu rozpoznawania cyklicznego platformy Azure, wykonaj następujące kroki z [witryny Azure portal](https://portal.azure.com):

1. W menu po lewej stronie przejdź do **wszystkich usług** > **sieć** > **sieci wirtualne**.

2. Wybierz sieć wirtualną z listy, który zostanie otwarty widok domyślny dla sieci wirtualnej.  

3. W widoku domyślnego w obszarze **ustawienia**, wybierz opcję **serwerów DNS**.  

4. Wybierz __niestandardowe__, a następnie wprowadź **prywatny adres IP** niestandardowego serwera DNS.   

5. Wybierz pozycję __Zapisz__.  <br />  

    ![Ustawianie niestandardowego serwera DNS dla sieci](./media/connect-on-premises-network/configure-custom-dns.png)

## <a name="configure-on-premises-dns-server"></a>Konfigurowanie serwera DNS w środowisku lokalnym

W poprzedniej sekcji należy skonfigurować niestandardowego serwera DNS do przekazywania żądań do serwera DNS w środowisku lokalnym. Następnie należy skonfigurować serwer DNS w środowisku lokalnym, aby przekazywać żądania do niestandardowego serwera DNS.

Aby poznać konkretne kroki dotyczące sposobu konfigurowania serwera DNS zajrzyj do dokumentacji oprogramowania serwera DNS. Sprawdź, aby uzyskać instrukcje dotyczące sposobu konfigurowania __warunkowego przesyłania dalej__.

Warunkowe do przodu tylko przekazuje żądania dotyczące określonego sufiksu DNS. W takim przypadku należy skonfigurować usługę przesyłania dalej dla sufiksu DNS w sieci wirtualnej. Żądania dotyczące ten sufiks powinien być przekazywany do adresu IP, niestandardowego serwera DNS. 

Poniższy tekst znajduje się przykład konfiguracji warunkowego przesyłania dalej dla **powiązać** oprogramowania DNS:

    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
    };

Instrukcje dotyczące korzystania z systemu DNS **systemu Windows Server 2016**, zobacz [DnsServerConditionalForwarderZone Dodaj](https://technet.microsoft.com/itpro/powershell/windows/dnsserver/add-dnsserverconditionalforwarderzone) dokumentacji...

Po skonfigurowaniu serwera DNS w środowisku lokalnym, możesz użyć `nslookup` z siecią lokalną, aby zweryfikować, że może rozpoznać nazwy w sieci wirtualnej. Poniższy przykład 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

W tym przykładzie używa lokalnego serwera DNS w 196.168.0.4 rozpoznać nazwę niestandardowego serwera DNS. Zastąp adres IP dla serwera DNS w środowisku lokalnym. Zastąp `dnsproxy` adresu za pomocą w pełni kwalifikowana nazwa domeny niestandardowego serwera DNS.

## <a name="optional-control-network-traffic"></a>Opcjonalnie: Kontrola ruchu sieciowego

Sieciowe grupy zabezpieczeń (NSG) lub trasy zdefiniowane przez użytkownika (UDR) umożliwia sterowanie ruchem sieciowym. Sieciowe grupy zabezpieczeń umożliwiają filtrowanie ruchu przychodzącego i wychodzącego ruchu sieciowego i blokują lub zezwalają na ruch. Tras zdefiniowanych przez użytkownika umożliwiają kontrolowanie, przepływ ruchu między zasobami w sieci wirtualnej, Internetu i siecią lokalną.

> [!WARNING]  
> HDInsight wymaga dostępu przychodzącego z określonych adresów IP w chmurze platformy Azure i nieograniczony dostęp ruchu wychodzącego. Za pomocą sieciowych grup zabezpieczeń lub tras zdefiniowanych przez użytkownika kontroli ruchu, należy wykonać następujące czynności:

1. Znajdowanie adresów IP dla lokalizacji, która zawiera sieci wirtualnej. Aby uzyskać listę wymaganych adresów IP według lokalizacji, zobacz [adresy IP wymagane](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip).

2. Adresy IP w kroku 1, zezwalać na ruch przychodzący z tego adresu IP adresów.

   * Jeśli używasz __sieciowej grupy zabezpieczeń__: Zezwalaj na __dla ruchu przychodzącego__ ruch na porcie __443__ adresów IP.
   * Jeśli używasz __trasy zdefiniowanej przez użytkownika__: Ustaw __następnego skoku__ typ trasy do __Internet__ adresów IP.

Aby uzyskać przykład tworzenia sieciowych grup zabezpieczeń za pomocą programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, zobacz [rozszerzyć HDInsight z usługą Azure Virtual Networks](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-nsg) dokumentu.

## <a name="create-the-hdinsight-cluster"></a>Tworzenie klastra HDInsight

> [!WARNING]  
> Niestandardowego serwera DNS należy skonfigurować przed zainstalowaniem HDInsight w sieci wirtualnej.

Użyj kroków w [Tworzenie klastra usługi HDInsight przy użyciu witryny Azure portal](./hdinsight-hadoop-create-linux-clusters-portal.md) dokumentu, aby utworzyć klaster usługi HDInsight.

> [!WARNING]  
> * Podczas tworzenia klastra musisz wybrać lokalizację zawierającą sieci wirtualnej.
> * W __Zaawansowane ustawienia__ część konfiguracji, należy wybrać sieć wirtualną i podsieć, która została utworzona wcześniej.

## <a name="connecting-to-hdinsight"></a>Nawiązywanie połączenia z HDInsight

Większość dokumentacji w HDInsight przyjęto założenie, że masz dostęp do klastra za pośrednictwem Internetu. Na przykład możesz połączyć się z klastrem pod adresem `https://CLUSTERNAME.azurehdinsight.net`. Ten adres używa publicznej bramy, która nie jest dostępna, jeśli używano sieciowych grup zabezpieczeń lub tras zdefiniowanych przez użytkownika do ograniczania dostępu z Internetu.

Odwołujący się dokumentacji `headnodehost` podczas nawiązywania połączenia z klastrem w sesji SSH. Ten adres jest dostępny tylko w węzłach w klastrze, a nie jest używany na komputerach klienckich połączonych za pośrednictwem sieci wirtualnej.

Na bezpośrednie łączenie się z HDInsight za pośrednictwem sieci wirtualnej, wykonaj następujące kroki:

1. Aby odnaleźć w wewnętrznej w pełni kwalifikowanych nazw domen węzłów klastra HDInsight, użyj jednej z następujących metod:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

2. Aby określić numer portu, którego usługa jest dostępna w, zobacz [porty używane przez usługi Apache Hadoop w HDInsight](./hdinsight-hadoop-port-settings-for-services.md) dokumentu.

    > [!IMPORTANT]  
    > Niektóre usługi do węzłów głównych w serwisie tylko są aktywne na jednym węźle naraz. Jeśli zostanie podjęta, uzyskiwanie dostępu do usługi w jednym węźle głównym i zakończy się niepowodzeniem, przełącz się do innego węzła głównego.
    >
    > Na przykład Apache Ambari służy tylko aktywne na jednego węzła głównego w danym momencie. Jeśli zostanie podjęta, uzyskiwanie dostępu do systemu Ambari na jednego węzła głównego i zwraca błąd 404, następnie działa on w innym węźle głównym.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji na temat używania HDInsight w sieci wirtualnej, zobacz [rozszerzyć HDInsight przy użyciu usługi Azure Virtual Networks](./hdinsight-extend-hadoop-virtual-network.md).

* Aby uzyskać więcej informacji na temat sieci wirtualnych platformy Azure, zobacz [Omówienie usługi Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

* Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md).

* Aby uzyskać więcej informacji na temat trasy zdefiniowane przez użytkownika, zobacz [trasy zdefiniowane przez użytkownika i przesyłaniu dalej IP](../virtual-network/virtual-networks-udr-overview.md).
