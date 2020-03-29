---
title: Łączenie usługi Azure HDInsight z siecią lokalną
description: Dowiedz się, jak utworzyć klaster HDInsight w sieci wirtualnej platformy Azure, a następnie połączyć go z siecią lokalną. Dowiedz się, jak skonfigurować rozpoznawanie nazw między programem HDInsight a siecią lokalną przy użyciu niestandardowego serwera DNS.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/04/2020
ms.openlocfilehash: 2ed7a5b9c81d1b50f80f379a88688b69c49ed382
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78897917"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>Łączenie usługi HDInsight z siecią lokalną

Dowiedz się, jak połączyć usługę HDInsight z siecią lokalną przy użyciu sieci wirtualnych platformy Azure i bramy sieci VPN. Niniejszy dokument zawiera informacje dotyczące planowania:

* Korzystanie z usługi HDInsight w sieci wirtualnej platformy Azure, która łączy się z siecią lokalną.
* Konfigurowanie rozpoznawania nazw DNS między siecią wirtualną a siecią lokalną.
* Konfigurowanie sieciowych grup zabezpieczeń w celu ograniczenia dostępu do Internetu do usługi HDInsight.
* Porty dostarczane przez HDInsight w sieci wirtualnej.

## <a name="overview"></a>Omówienie

Aby umożliwić programowi HDInsight i zasobom w sieci połączonej komunikowanie się według nazwy, należy wykonać następujące akcje:

1. Utwórz sieć wirtualną platformy Azure.
1. Utwórz niestandardowy serwer DNS w sieci wirtualnej platformy Azure.
1. Skonfiguruj sieć wirtualną tak, aby używała niestandardowego serwera DNS zamiast domyślnego programu rozpoznawania nazw cyklicznych platformy Azure.
1. Skonfiguruj przekazywanie dalej między niestandardowym serwerem DNS a lokalnym serwerem DNS.

Te konfiguracje umożliwiają następujące zachowanie:

* Żądania w pełni kwalifikowanych nazw domen z sufiksem DNS __dla sieci wirtualnej__ są przekazywane do niestandardowego serwera DNS. Niestandardowy serwer DNS następnie przekazuje te żądania do programu Azure Recursive Resolver, który zwraca adres IP.
* Wszystkie inne żądania są przekazywane do lokalnego serwera DNS. Nawet żądania publicznych zasobów internetowych, takich jak microsoft.com są przekazywane do lokalnego serwera DNS w celu rozpoznawania nazw.

Na poniższym diagramie zielone linie są żądaniami zasobów, które kończą się sufiksem DNS sieci wirtualnej. Niebieskie linie to żądania dotyczące zasobów w sieci lokalnej lub w publicznym internecie.

![Diagram rozpoznawania żądań DNS w konfiguracji](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).
* Jeśli korzystasz z programu PowerShell, potrzebny jest [moduł AZ](https://docs.microsoft.com/powershell/azure/overview).
* Jeśli chcesz korzystać z interfejsu wiersza polecenia platformy Azure i nie zainstalowano go jeszcze, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-virtual-network-configuration"></a>Tworzenie konfiguracji sieci wirtualnej

Skorzystaj z następujących dokumentów, aby dowiedzieć się, jak utworzyć sieć wirtualną platformy Azure, która jest połączona z siecią lokalną:

* [Korzystanie z witryny Azure portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Korzystanie z programu Azure PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Korzystanie z interfejsu wiersza polecenia platformy Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="create-custom-dns-server"></a>Tworzenie niestandardowego serwera DNS

> [!IMPORTANT]  
> Przed zainstalowaniem programu HDInsight w sieci wirtualnej należy utworzyć i skonfigurować serwer DNS.

Te kroki używają [witryny Azure portal](https://portal.azure.com) do tworzenia maszyny wirtualnej platformy Azure. Aby uzyskać inne sposoby tworzenia maszyny wirtualnej, zobacz [Tworzenie maszyny wirtualnej — interfejsu wiersza polecenia platformy Azure](../virtual-machines/linux/quick-create-cli.md) i tworzenie maszyny [wirtualnej — Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md).  Aby utworzyć maszynę wirtualną z systemem Linux, która korzysta z oprogramowania [Bind](https://www.isc.org/downloads/bind/) DNS, należy wykonać następujące czynności:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
  
1. Z górnego menu wybierz pozycję **+ Utwórz zasób**.

    ![Tworzenie maszyny wirtualnej Ubuntu](./media/connect-on-premises-network/azure-portal-create-resource.png)

1. Wybierz **opcję Oblicz maszynę** > **wirtualną,** aby przejść do strony Tworzenie maszyny **wirtualnej.**

1. Na karcie __Podstawy__ wprowadź następujące informacje:  
  
    | Pole | Wartość |
    | --- | --- |
    |Subskrypcja |Wybierz odpowiednią subskrypcję.|
    |Grupa zasobów |Wybierz grupę zasobów zawierającą sieć wirtualną utworzoną wcześniej.|
    |Nazwa maszyny wirtualnej | Wprowadź przyjazną nazwę identyfikującą tę maszynę wirtualną. W tym przykładzie użyto **programu DNSProxy**.|
    |Region | Wybierz ten sam region co sieć wirtualna utworzona wcześniej.  Nie wszystkie rozmiary maszyn wirtualnych są dostępne we wszystkich regionach.  |
    |Opcje dostępności |  Wybierz żądany poziom dostępności.  Platforma Azure oferuje szereg opcji zarządzania dostępnością i odpornością aplikacji.  Twórz rozwiązanie do używania replikowanych maszyn wirtualnych w strefach dostępności lub zestawach dostępności w celu ochrony aplikacji i danych przed awariami i zdarzeniami konserwacji w centrum danych. W tym przykładzie użyto **nadmiarowości infrastruktury.** |
    |Image (Obraz) | Wyjdź na **Ubuntu Server 18.04 LTS**. |
    |Typ uwierzytelniania | __Hasło__ lub __klucz publiczny SSH__: Metoda uwierzytelniania dla konta SSH. Zalecamy korzystanie z kluczy publicznych, ponieważ są one bardziej bezpieczne. W tym przykładzie użyto **hasła**.  Aby uzyskać więcej informacji, zobacz [tworzenie i używanie kluczy SSH dla maszyn wirtualnych z systemem Linux](../virtual-machines/linux/mac-create-ssh-keys.md) dokumentu.|
    |Nazwa użytkownika |Wprowadź nazwę użytkownika administratora maszyny Wirtualnej.  W tym przykładzie użyto **sshuser**.|
    |Hasło lub klucz publiczny SSH | Dostępne pole zależy od wyboru **typu uwierzytelniania.**  Wprowadź odpowiednią wartość.|
    |Publiczne porty wejściowe|Wybierz pozycję **Zezwalaj na wybrane porty**. Następnie wybierz **SSH (22)** z listy rozwijanej **Wybierz porty przychodzące.**|

    ![Podstawowa konfiguracja maszyny wirtualnej](./media/connect-on-premises-network/virtual-machine-basics.png)

    Pozostaw inne wpisy przy wartościach domyślnych, a następnie wybierz kartę **Sieć.**

4. Na karcie **Sieć** wprowadź następujące informacje:

    | Pole | Wartość |
    | --- | --- |
    |Sieć wirtualna | Wybierz sieć wirtualną utworzoną wcześniej.|
    |Podsieć | Wybierz domyślną podsieć dla sieci wirtualnej utworzonej wcześniej. __Nie__ należy wybierać podsieci używanej przez bramę sieci VPN.|
    |Publiczny adres IP | Użyj wartości autopopulowanej.  |

    ![Ustawienia sieci wirtualnej usługi HDInsight](./media/connect-on-premises-network/virtual-network-settings.png)

    Pozostaw inne wpisy przy wartościach domyślnych, a następnie wybierz **opcję Przejrzyj + utwórz**.

5. Na karcie **Recenzja + utwórz** wybierz pozycję **Utwórz,** aby utworzyć maszynę wirtualną.

### <a name="review-ip-addresses"></a>Przeglądanie adresów IP

Po utworzeniu maszyny wirtualnej otrzymasz powiadomienie **o pomyślnym wdrożeniu** za pomocą przycisku **Przejdź do zasobu.**  Wybierz **pozycję Przejdź do zasobu,** aby przejść do nowej maszyny wirtualnej.  W widoku domyślnym nowej maszyny wirtualnej wykonaj następujące kroki, aby zidentyfikować skojarzone adresy IP:

1. W **obszarze Ustawienia**wybierz pozycję **Właściwości**.

2. Zanotuj wartości **dla PUBLICZNEGO ADRESU IP/DNS NAME LABEL** i PRYWATNEGO ADRESU **IP** do późniejszego użycia.

   ![Publiczne i prywatne adresy IP](./media/connect-on-premises-network/virtual-machine-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Instalowanie i konfigurowanie powiązania (oprogramowanie DNS)

1. Użyj protokołu SSH, aby połączyć się z __publicznym adresem IP__ maszyny wirtualnej. Zamień `sshuser` je na konto użytkownika SSH określone podczas tworzenia maszyny Wirtualnej. Poniższy przykład łączy się z maszyną wirtualną w 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

2. Aby zainstalować binda, użyj następujących poleceń z sesji SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Aby skonfigurować bind do przesyłania dalej żądań rozpoznawania nazw do lokalnego serwera `/etc/bind/named.conf.options` DNS, użyj następującego tekstu jako zawartości pliku:

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
    > Zastąp `goodclients` wartości w sekcji zakresem adresów IP sieci wirtualnej i sieci lokalnej. W tej sekcji zdefiniowano adresy, z których ten serwer DNS akceptuje żądania.
    >
    > Zastąp `192.168.0.1` `forwarders` wpis w sekcji adresem IP lokalnego serwera DNS. Ten wpis kieruje żądania DNS do lokalnego serwera DNS w celu rozwiązania.

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

    ```output
    dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    Tekst `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` jest __sufiksem DNS__ dla tej sieci wirtualnej. Zapisz tę wartość, ponieważ jest używana później.

5. Aby skonfigurować powiązanie do rozpoznawania nazw DNS zasobów w sieci wirtualnej, użyj następującego tekstu jako zawartości `/etc/bind/named.conf.local` pliku:

        // Replace the following with the DNS suffix for your virtual network
        zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
            type forward;
            forwarders {168.63.129.16;}; # The Azure recursive resolver
        };

    > [!IMPORTANT]  
    > Należy zastąpić `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` sufiks DNS pobrany wcześniej.

    Aby edytować ten plik, użyj następującego polecenia:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Aby zapisać plik, użyj __klawiszy Ctrl+X__, __Y__, a następnie __wprowadź__.

6. Aby uruchomić binda, użyj następującego polecenia:

    ```bash
    sudo service bind9 restart
    ```

7. Aby sprawdzić, czy powiązanie może rozpoznać nazwy zasobów w sieci lokalnej, należy użyć następujących poleceń:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]  
    > Zastąp `dns.mynetwork.net` w pełni kwalifikowaną nazwą domeny (FQDN) zasobu w sieci lokalnej.
    >
    > Zamień `10.0.0.4` na __wewnętrzny adres IP__ niestandardowego serwera DNS w sieci wirtualnej.

    Odpowiedź wygląda podobnie do następującego tekstu:

    ```output
    Server:         10.0.0.4
    Address:        10.0.0.4#53

    Non-authoritative answer:
    Name:   dns.mynetwork.net
    Address: 192.168.0.4
    ```

## <a name="configure-virtual-network-to-use-the-custom-dns-server"></a>Konfigurowanie sieci wirtualnej do używania niestandardowego serwera DNS

Aby skonfigurować sieć wirtualną do używania niestandardowego serwera DNS zamiast cyklicznego programu rozpoznawania nazw platformy Azure, należy wykonać następujące kroki z [witryny Azure portal:](https://portal.azure.com)

1. W menu po lewej stronie przejdź do **pozycji Wszystkie usługi** > **Sieci** > **sieci wirtualne**.

2. Wybierz sieć wirtualną z listy, która otworzy widok domyślny dla sieci wirtualnej.  

3. W widoku domyślnym w obszarze **Ustawienia**wybierz pozycję **Serwery DNS**.  

4. Wybierz __opcję Niestandardowy__i wprowadź **prywatny adres IP** niestandardowego serwera DNS.

5. Wybierz __pozycję Zapisz__.  <br />  

    ![Ustawianie niestandardowego serwera DNS dla sieci](./media/connect-on-premises-network/configure-custom-dns.png)

## <a name="configure-on-premises-dns-server"></a>Konfigurowanie lokalnego serwera DNS

W poprzedniej sekcji skonfigurowano niestandardowy serwer DNS do przesyłania dalej żądań do lokalnego serwera DNS. Następnie należy skonfigurować lokalny serwer DNS do przesyłania dalej żądań do niestandardowego serwera DNS.

Aby uzyskać szczegółowe instrukcje dotyczące konfigurowania serwera DNS, zapoznaj się z dokumentacją oprogramowania serwera DNS. Poszukaj kroków, jak skonfigurować __warunkową przesyłanie dalej__.

Warunkowe przesyłanie dalej tylko przekazuje żądania dla określonego sufiksu DNS. W takim przypadku należy skonfigurować usługę przesyłania dalej dla sufiksu DNS sieci wirtualnej. Żądania tego sufiksu powinny być przekazywane na adres IP niestandardowego serwera DNS. 

Poniższy tekst jest przykładem warunkowej konfiguracji usługi przesyłania dalej dla oprogramowania **Bind** DNS:

    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
    };

Aby uzyskać informacje na temat korzystania z systemu DNS w **systemie Windows Server 2016,** zobacz dokumentację [Add-DnsServerConditionalForwarderZone...](https://technet.microsoft.com/itpro/powershell/windows/dnsserver/add-dnsserverconditionalforwarderzone)

Po skonfigurowaniu lokalnego serwera DNS można użyć `nslookup` z sieci lokalnej, aby sprawdzić, czy można rozpoznać nazwy w sieci wirtualnej. Poniższy przykład 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

W tym przykładzie użyto lokalnego serwera DNS o godzinie 196.168.0.4 do rozpoznania nazwy niestandardowego serwera DNS. Zastąp adres IP adresem lokalnym serwerem DNS. Zastąp `dnsproxy` adres w pełni kwalifikowaną nazwą domeny niestandardowego serwera DNS.

## <a name="optional-control-network-traffic"></a>Opcjonalnie: sterowanie ruchem sieciowym

Do kontrolowania ruchu sieciowego można użyć sieciowych grup zabezpieczeń (NSG) lub tras zdefiniowanych przez użytkownika (UDR). NsGs umożliwiają filtrowanie ruchu przychodzącego i wychodzącego oraz zezwalają lub odmawiają ruchu. UDR umożliwiają kontrolowanie przepływu ruchu między zasobami w sieci wirtualnej, Internecie i sieci lokalnej.

> [!WARNING]  
> Usługa HDInsight wymaga dostępu przychodzącego z określonych adresów IP w chmurze platformy Azure i nieograniczonego dostępu wychodzącego. W przypadku używania grup zabezpieczeń lub udr do kontrolowania ruchu należy wykonać następujące kroki:

1. Znajdź adresy IP lokalizacji zawierającej sieć wirtualną. Aby uzyskać listę wymaganych adresów IP według lokalizacji, zobacz [Wymagane adresy IP](./hdinsight-management-ip-addresses.md).

2. W przypadku adresów IP zidentyfikowanych w kroku 1 zezwalaj na ruch przychodzący z tych adresów IP.

   * Jeśli używasz __sieciowej sieciowej sieciowej__: Zezwalaj na ruch __przychodzący__ na porcie __443__ dla adresów IP.
   * Jeśli używasz __UDR:__ Ustaw następny __przeskok__ trasy do __Internetu__ dla adresów IP.

Na przykład przy użyciu programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure do tworzenia sieciowych zabezpieczeń, zobacz [rozszerzenie hdinsight z sieciami wirtualnymi platformy Azure](hdinsight-create-virtual-network.md#hdinsight-nsg) dokumentu.

## <a name="create-the-hdinsight-cluster"></a>Tworzenie klastra USŁUGI HDInsight

> [!WARNING]  
> Przed zainstalowaniem usługi HDInsight w sieci wirtualnej należy skonfigurować niestandardowy serwer DNS.

Aby utworzyć klaster usługi HDInsight, należy wykonać kroki opisane w [klastrze Tworzenie usługi HDInsight przy użyciu](./hdinsight-hadoop-create-linux-clusters-portal.md) dokumentu portalu Azure.

> [!WARNING]  
> * Podczas tworzenia klastra należy wybrać lokalizację zawierającą sieć wirtualną.
> * W części __Ustawienia zaawansowane__ konfiguracji należy wybrać sieć wirtualną i podsieć, które zostały utworzone wcześniej.

## <a name="connecting-to-hdinsight"></a>Podłączanie do urządzenia HDInsight

Większość dokumentacji w programie HDInsight zakłada, że masz dostęp do klastra przez Internet. Na przykład możesz połączyć się z klastrem pod adresem `https://CLUSTERNAME.azurehdinsight.net`. Ten adres używa bramy publicznej, która nie jest dostępna, jeśli używano nsg lub UDR do ograniczania dostępu z Internetu.

Niektóre dokumentacja `headnodehost` odwołuje się również podczas łączenia się z klastrem z sesji SSH. Ten adres jest dostępny tylko w węzłach w klastrze i nie jest użyteczny dla klientów połączonych za pośrednictwem sieci wirtualnej.

Aby bezpośrednio połączyć się z usługą HDInsight za pośrednictwem sieci wirtualnej, należy wykonać następujące czynności:

1. Aby odnajdować wewnętrzne, w pełni kwalifikowane nazwy domen węzłów klastra USŁUGI HDInsight, należy użyć jednej z następujących metod:

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

2. Aby określić port, na który jest dostępna usługa, zobacz [Porty używane przez usługi Apache Hadoop w dokumencie HDInsight.](./hdinsight-hadoop-port-settings-for-services.md)

    > [!IMPORTANT]  
    > Niektóre usługi hostowane w węzłach głównego są aktywne tylko w jednym węźle naraz. Jeśli spróbujesz uzyskać dostęp do usługi w jednym węźle głównym i zakończy się niepowodzeniem, przełącz się do drugiego węzła głównego.
    >
    > Na przykład Apache Ambari jest aktywny tylko w jednym węźle głównym naraz. Jeśli spróbujesz uzyskać dostęp do Ambari w jednym węźle głównym i zwraca błąd 404, to jest uruchomiony na drugim węźle głównym.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat korzystania z usługi HDInsight w sieci wirtualnej, zobacz [Planowanie wdrożenia sieci wirtualnej dla klastrów usługi Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md).

* Aby uzyskać więcej informacji na temat sieci wirtualnych platformy Azure, zobacz [omówienie usługi Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

* Aby uzyskać więcej informacji na temat grup zabezpieczeń sieci, zobacz [Sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md).

* Aby uzyskać więcej informacji na temat tras zdefiniowanych przez użytkownika, zobacz [Trasy zdefiniowane przez użytkownika i przekierowanie IP](../virtual-network/virtual-networks-udr-overview.md).
