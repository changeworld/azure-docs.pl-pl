---
title: HDInsight nawiązać połączenia z siecią lokalną - Azure HDInsight | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia klastra usługi HDInsight w sieci wirtualnej platformy Azure, a następnie połącz go z sieci lokalnej. Dowiedz się, jak skonfigurować rozpoznawanie nazw między HDInsight i siecią lokalną przy użyciu niestandardowego serwera DNS.
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: larryfr
ms.openlocfilehash: 51307e1bdb31d902636787790d1c4f1248f3886b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361924"
---
# <a name="connect-hdinsight-to-your-on-premise-network"></a>HDInsight nawiązać połączenie z siecią lokalną

Dowiedz się, jak HDInsight połączyć się z sieci lokalnej przy użyciu sieci wirtualnych Azure i bramy sieci VPN. Ten dokument zawiera informacje na temat planowania na:

* Za pomocą usługi HDInsight w sieci wirtualnej Azure, który łączy się z siecią lokalną.

* Konfigurowanie rozpoznawania nazw DNS między sieci wirtualnej i sieci lokalnej.

* Konfigurowanie grup zabezpieczeń sieci, aby ograniczyć dostęp do Internetu do usługi HDInsight.

* Porty dostarczanych z usługą HDInsight w sieci wirtualnej.

## <a name="create-the-virtual-network-configuration"></a>Utwórz konfigurację sieci wirtualnej

Aby dowiedzieć się, jak utworzyć sieci wirtualnej Azure, która jest połączona z siecią lokalną, należy użyć następujących dokumentach:
    
* [Korzystanie z witryny Azure Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

* [Korzystanie z programu Azure PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

* [Korzystanie z interfejsu wiersza polecenia platformy Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="configure-name-resolution"></a>Konfigurowanie rozpoznawania nazw

Aby umożliwić HDInsight i zasoby przyłączone do sieci w celu komunikacji według nazwy, należy wykonać następujące czynności:

* Utwórz niestandardowy serwer DNS w sieci wirtualnej platformy Azure.

* Skonfiguruj sieć wirtualną do użycia niestandardowego serwera DNS, zamiast domyślnej Azure cyklicznego programu rozpoznawania nazw.

* Konfiguruj przekazywanie między niestandardowy serwer DNS i serwer DNS lokalnie.

Ta konfiguracja zapewnia następujące działania:

* Żądania dla nazwy FQDN, które mają sufiks DNS __dla sieci wirtualnej__ są przekazywane do niestandardowego serwera DNS. Niestandardowy serwer DNS przekazuje następnie te żądania do usługi Azure cyklicznego programu rozpoznawania nazw, która zwraca adres IP.

* Wszystkie inne żądania są przekazywane do lokalnego serwera DNS. Nawet żądania publicznego zasobów internetowych, takich jak microsoft.com są przekazywane do lokalnego serwera DNS do rozpoznawania nazw.

Na poniższym diagramie zielony wiersze są żądania dotyczące zasobów, które kończą się sufiksem DNS w sieci wirtualnej. Niebieski wiersze są żądania dotyczące zasobów w sieci lokalnej lub w publicznej sieci internet.

![Diagram przedstawiający sposób żądania DNS są rozpoznawane w konfiguracji używane w tym dokumencie](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

### <a name="create-a-custom-dns-server"></a>Utwórz niestandardowy serwer DNS

> [!IMPORTANT]
> Należy utworzyć i skonfigurować serwer DNS, przed zainstalowaniem usługi HDInsight w sieci wirtualnej.

Do utworzenia maszyny Wirtualnej systemu Linux, który używa [powiązać](https://www.isc.org/downloads/bind/) oprogramowania DNS, wykonaj następujące kroki:

> [!NOTE]
> Następujące kroki użyj [portalu Azure](https://portal.azure.com) do utworzenia maszyny wirtualnej platformy Azure. Inne sposoby tworzenia maszyny wirtualnej można znaleźć w następujących dokumentach:
>
> * [Tworzenie maszyny wirtualnej — interfejs wiersza polecenia platformy Azure](../virtual-machines/linux/quick-create-cli.md)
> * [Tworzenie maszyny Wirtualnej — program Azure PowerShell](../virtual-machines/linux/quick-create-portal.md)

1. Z [portalu Azure](https://portal.azure.com), wybierz pozycję __+__, __obliczeniowe__, i __Ubuntu Server 16.04 LTS__.

    ![Tworzenie maszyny wirtualnej systemu Ubuntu](./media/connect-on-premises-network/create-ubuntu-vm.png)

2. W sekcji __Podstawowe__ wprowadź następujące informacje:

    * __Nazwa__: przyjazną nazwę identyfikującą tej maszyny wirtualnej. Na przykład __DNSProxy__.
    * __Nazwa użytkownika__: Nazwa konta SSH.
    * __Klucz publiczny SSH__ lub __hasło__: metodę uwierzytelniania dla konta SSH. Zalecamy używanie kluczy publicznych, ponieważ są one bardziej bezpieczne. Aby uzyskać więcej informacji, zobacz [tworzenia i używania kluczy SSH dla maszyn wirtualnych systemu Linux](../virtual-machines/linux/mac-create-ssh-keys.md) dokumentu.
    * __Grupa zasobów__: Wybierz __Użyj istniejącego__, a następnie wybierz grupę zasobów, która zawiera utworzony wcześniej sieci wirtualnej.
    * __Lokalizacja__: Wybierz z tej samej lokalizacji co sieć wirtualna.

    ![Maszyny wirtualnej konfiguracji podstawowej](./media/connect-on-premises-network/vm-basics.png)

    Pozostaw innych pozycji na wartości domyślne, a następnie wybierz __OK__.

3. Z __wybierz rozmiar__ wybierz rozmiar maszyny Wirtualnej. W tym samouczku wybierz opcję najmniejszą i najniższy koszt. Aby kontynuować, należy użyć __wybierz__ przycisku.

4. Z __ustawienia__ sekcji, wprowadź następujące informacje:

    * __Sieć wirtualna__: Wybierz sieć wirtualną, który został utworzony wcześniej.

    * __Podsieci__: Wybierz domyślną podsieć sieci wirtualnej. Czy __nie__ wybierz w podsieci używanej przez bramę sieci VPN.

    * __Konto magazynu diagnostyki__: Wybierz istniejące konto magazynu lub Utwórz nową.

    ![Ustawienia sieci wirtualnej](./media/connect-on-premises-network/virtual-network-settings.png)

    Pozostaw wartość domyślną innych pozycji, a następnie wybierz __OK__ aby kontynuować.

5. Z __zakupu__ zaznacz __zakupu__ przycisk, aby utworzyć maszynę wirtualną.

6. Po utworzeniu maszyny wirtualnej, jego __omówienie__ sekcja jest wyświetlana. Wybierz z listy po lewej stronie __właściwości__. Zapisz __publicznego adresu IP__ i __prywatny adres IP__ wartości. Będzie używany w następnej sekcji.

    ![Publiczne i prywatne adresy IP](./media/connect-on-premises-network/vm-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Instalowanie i konfigurowanie Bind (oprogramowania DNS)

1. Używanie protokołu SSH do nawiązania połączenia __publicznego adresu IP__ maszyny wirtualnej. Poniższy przykład nawiązuje połączenie z maszyną wirtualną na 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Zastąp `sshuser` przy użyciu konta użytkownika SSH określone podczas tworzenia klastra.

    > [!NOTE]
    > Istnieje wiele sposobów, aby uzyskać `ssh` narzędzia. W systemie Linux, Unix i macOS jest podana jako część systemu operacyjnego. Jeśli korzystasz z systemu Windows, weź pod uwagę jedną z następujących opcji:
    >
    > * [Powłoka w chmurze Azure](../cloud-shell/quickstart.md)
    > * [Bash na Ubuntu w systemie Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH)https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Aby zainstalować Bind, użyj następujących poleceń w sesji SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Aby skonfigurować powiązania do przekazywania żądań rozpoznawania nazw do lokalnego serwera DNS, użyj następującego tekstu zawartość, `/etc/bind/named.conf.options` pliku:

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
    > Zastąp wartości w `goodclients` sekcji z zakresem adresów IP sieci wirtualnej i sieci lokalnej. Ta sekcja definiuje adresów, które ten serwer DNS odbiera z.
    >
    > Zastąp `192.168.0.1` wpis w `forwarders` sekcji przy użyciu adresu IP serwera DNS lokalnie. Ten wpis kieruje żądania DNS do lokalnego serwera DNS do rozpoznawania.

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

        dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` Tekst jest __sufiks DNS__ dla tej sieci wirtualnej. Zapisz tę wartość, ponieważ jest używana później.

5. Aby skonfigurować powiązania do rozpoznawania nazw DNS dla zasobów w sieci wirtualnej, użyj następującego tekstu jako zawartość `/etc/bind/named.conf.local` pliku:

        // Replace the following with the DNS suffix for your virtual network
        zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
            type forward;
            forwarders {168.63.129.16;}; # The Azure recursive resolver
        };

    > [!IMPORTANT]
    > Należy zastąpić `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` sufiksu DNS został wcześniej pobrany.

    Aby edytować ten plik, użyj następującego polecenia:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Aby zapisać plik, użyj __Ctrl + X__, __Y__, a następnie __Enter__.

6. Aby uruchomić Bind, użyj następującego polecenia:

    ```bash
    sudo service bind9 restart
    ```

7. Aby zweryfikować tego powiązania mogły rozpoznawać nazwy zasobów w sieci lokalnej, użyj następujących poleceń:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]
    > Zastąp `dns.mynetwork.net` z w pełni kwalifikowaną nazwę (FQDN) zasobu w sieci lokalnej.
    >
    > Zastąp `10.0.0.4` z __wewnętrzny adres IP__ niestandardowego serwera DNS w sieci wirtualnej.

    Odpowiedź jest podobny do następującego tekstu:

        Server:         10.0.0.4
        Address:        10.0.0.4#53

        Non-authoritative answer:
        Name:   dns.mynetwork.net
        Address: 192.168.0.4

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Skonfiguruj sieć wirtualną do używania niestandardowych serwera DNS

Aby skonfigurować sieci wirtualnej, aby użyć niestandardowego serwera DNS zamiast Azure cyklicznego programu rozpoznawania nazw, należy użyć następujących czynności:

1. W [portalu Azure](https://portal.azure.com), wybierz sieć wirtualną, a następnie wybierz __serwerów DNS__.

2. Wybierz __niestandardowych__, a następnie wprowadź __wewnętrzny adres IP__ niestandardowego serwera DNS. Na koniec wybierz __zapisać__.

    ![Ustaw niestandardowy serwer DNS w sieci](./media/connect-on-premises-network/configure-custom-dns.png)

### <a name="configure-the-on-premises-dns-server"></a>Konfigurowanie lokalnego serwera DNS

W poprzedniej sekcji należy skonfigurować niestandardowy serwer DNS do przekazywania żądań do lokalnego serwera DNS. Następnie skonfiguruj lokalny serwer DNS do przekazywania żądań do niestandardowego serwera DNS.

Aby poznać konkretne kroki dotyczące sposobu konfigurowania serwera DNS zajrzyj do dokumentacji oprogramowania serwera DNS. Szukaj, aby uzyskać instrukcje dotyczące sposobu konfigurowania __warunkowego przesyłania dalej__.

Warunkowe do przodu przekazuje tylko żądania dotyczące określonego sufiksu DNS. W takim przypadku należy skonfigurować usługę przesyłania dalej dla sufiksu DNS w sieci wirtualnej. Adres IP serwera DNS, niestandardowe powinny zostać przekazane żądania dla tego sufiksu. 

Następujący tekst jest przykładem konfiguracji warunkowego przesyłania dalej dla **powiązać** oprogramowania DNS:

    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
    };

Aby uzyskać informacje dotyczące korzystania z systemu DNS **systemu Windows Server 2016**, zobacz [DnsServerConditionalForwarderZone Dodaj](https://technet.microsoft.com/itpro/powershell/windows/dnsserver/add-dnsserverconditionalforwarderzone) dokumentacji...

Po skonfigurowaniu lokalnego serwera DNS, możesz użyć `nslookup` z sieci lokalnej, aby sprawdzić, czy można rozwiązać nazwy w sieci wirtualnej. Poniższy przykład 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

W tym przykładzie używane lokalnego serwera DNS na 196.168.0.4 do rozpoznania nazwy niestandardowego serwera DNS. Zamień adres IP dla lokalnego serwera DNS. Zastąp `dnsproxy` adresu z w pełni kwalifikowaną nazwę niestandardowego serwera DNS.

## <a name="optional-control-network-traffic"></a>Opcjonalne: Ruch sieciowy kontroli

Grupy zabezpieczeń sieci (NSG) lub trasy zdefiniowane przez użytkownika (przez) służy do kontroli ruchu sieciowego. Grupy NSG umożliwiają filtrowanie ruchu przychodzącego i wychodzącego, a akceptować lub odrzucać ruchu. Udr pozwala na kontrolowanie, jak przepływa ruch między zasobami w sieci wirtualnej, internet oraz sieć lokalną.

> [!WARNING]
> HDInsight wymaga dostępu ruchu przychodzącego z określonych adresów IP w chmurze Azure i nieograniczony dostęp ruchu wychodzącego. Za pomocą grup NSG lub Udr kontroli ruchu, należy wykonać następujące czynności:

1. Znajdowanie adresów IP do lokalizacji zawierającej sieci wirtualnej. Aby uzyskać listę wymaganych adresów IP według lokalizacji, zobacz [adresów IP wymagana](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip).

2. Dla adresów IP w kroku 1, Zezwalaj na ruch przychodzący z tego adresu IP adresów.

   * Jeśli używasz __NSG__: Zezwalaj na __przychodzących__ ruch na porcie __443__ dla adresu IP adresów.
   * Jeśli używasz __przez__: Ustaw __następnego przeskoku__ typu trasy do __Internet__ dla adresu IP adresów.

Na przykład do tworzenia grup NSG za pomocą programu Azure PowerShell lub interfejsu wiersza polecenia Azure, zobacz [rozszerzenie usługi HDInsight za pomocą sieci wirtualnych Azure](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-nsg) dokumentu.

## <a name="create-the-hdinsight-cluster"></a>Tworzenie klastra usługi HDInsight

> [!WARNING]
> Niestandardowy serwer DNS należy skonfigurować przed zainstalowaniem usługi HDInsight w sieci wirtualnej.

Wykonaj kroki w [tworzenia klastra usługi HDInsight przy użyciu portalu Azure](./hdinsight-hadoop-create-linux-clusters-portal.md) dokumentu, aby utworzyć klaster usługi HDInsight.

> [!WARNING]
> * Podczas tworzenia klastra musisz wybrać lokalizację, który zawiera sieci wirtualnej.
>
> * W __Zaawansowane ustawienia__ część konfiguracji, należy wybrać sieć wirtualna i podsieć, który został utworzony wcześniej.

## <a name="connecting-to-hdinsight"></a>Nawiązywanie połączenia z usługi HDInsight

Większość dokumentacji w usłudze HDInsight założono, że dostęp do klastra za pośrednictwem Internetu. Na przykład możesz połączyć się z klastrem pod adresem https://CLUSTERNAME.azurehdinsight.net. Ten adres używa publicznego brama nie jest dostępna w przypadku użycia grup NSG lub Udr ograniczyć dostęp z Internetu.

Odwołujący się części dokumentacji `headnodehost` podczas nawiązywania połączenia z klastrem w sesji SSH. Ten adres jest dostępna wyłącznie z węzłów w klastrze, a nie jest używany na klientów połączonych za pośrednictwem sieci wirtualnej.

Bezpośrednio z usługi HDInsight za pośrednictwem sieci wirtualnej, wykonaj następujące kroki:

1. Aby odnaleźć wewnętrzny pełni kwalifikowane nazwy domen z węzłów klastra usługi HDInsight, użyj jednej z następujących metod:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

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

2. Aby ustalić port, który usługa jest dostępna na, zobacz [porty używane przez usługi Hadoop w usłudze HDInsight](./hdinsight-hadoop-port-settings-for-services.md) dokumentu.

    > [!IMPORTANT]
    > Niektóre usługi hostowanej o węzłach głównych są tylko aktywne na jednym węźle naraz. Jeśli nie powiedzie się próby uzyskiwania dostępu do usługi na jednym węźle głównym, Przełącz do innego węzła głównego.
    >
    > Na przykład Ambari jest tylko aktywna na jednym węźle głównym naraz. Jeśli dostęp do narzędzia Ambari w jednym węźle głównym zwraca błąd 404, następnie uruchomiona w innym węźle głównym.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji na temat używania usługi HDInsight w sieci wirtualnej, zobacz [rozszerzyć HDInsight przy użyciu sieci wirtualnych Azure](./hdinsight-extend-hadoop-virtual-network.md).

* Aby uzyskać więcej informacji o sieci wirtualnych platformy Azure, zobacz [Omówienie usługi Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

* Aby uzyskać więcej informacji dotyczących grup zabezpieczeń sieci, zobacz [sieciowej grupy zabezpieczeń](../virtual-network/security-overview.md).

* Aby uzyskać więcej informacji dotyczących tras zdefiniowanych przez użytkownika, zobacz [trasy zdefiniowane przez użytkownika i przesyłanie dalej IP](../virtual-network/virtual-networks-udr-overview.md).
