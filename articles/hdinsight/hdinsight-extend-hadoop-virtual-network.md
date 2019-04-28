---
title: Rozszerzanie HDInsight z usługą Virtual Network - Azure
description: Dowiedz się, jak połączyć z usługą HDInsight do innych zasobów w chmurze lub zasobów w centrum danych za pomocą usługi Azure Virtual Network
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/29/2019
ms.openlocfilehash: f97c07c522dfb22818aca84d41d30c023f564d84
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097341"
---
# <a name="extend-azure-hdinsight-using-an-azure-virtual-network"></a>Rozszerzenie usługi Azure HDInsight przy użyciu usługi Azure Virtual Network

Dowiedz się, jak używać HDInsight przy użyciu [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Korzystanie z usługi Azure Virtual Network umożliwia następujące scenariusze:

* Łączenie z HDInsight bezpośrednio z siecią lokalną.

* O łączeniu z danymi HDInsight są przechowywane w usłudze Azure Virtual network.

* Bezpośredni dostęp do [Apache Hadoop](https://hadoop.apache.org/) usług, które nie są dostępne publicznie w Internecie. Na przykład [platformy Apache Kafka](https://kafka.apache.org/) interfejsów API lub [bazy danych Apache HBase](https://hbase.apache.org/) interfejsu API języka Java.

> [!IMPORTANT]  
> Od 28 lutego 2019 roku zasobów sieciowych (takich jak karty sieciowe, modułów równoważenia obciążenia itp.) dla nowych klastrów utworzone w sieci Wirtualnej zostaną zainicjowane w tej samej grupie zasobów klastra HDInsight. Wcześniej te zasoby zostały aprowizowane w grupie zasobów sieci Wirtualnej. Nie ma zmian do bieżącego uruchamianie klastrów i tych klastrów, utworzone bez sieci Wirtualnej.

## <a name="prerequisites-for-code-samples-and-examples"></a>Wymagania wstępne dotyczące przykłady kodu i przykłady

* Opis sieci TCP/IP. Jeśli nie jesteś zaznajomiony z sieci TCP/IP, należy partnera z osobą, która jest przed wprowadzeniem zmian w sieciach produkcyjnych.
* Jeśli przy użyciu programu PowerShell, konieczne będzie [modułu AZ](https://docs.microsoft.com/powershell/azure/overview).
* Jeśli chcą używać wiersza polecenia platformy Azure i nie został jeszcze zainstalowany go, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

> [!IMPORTANT]  
> Jeśli szukasz wskazówki krok po kroku dotyczące łączenia HDInsight do Twojej sieci lokalnej sieci za pomocą usługi Azure Virtual Network, zobacz [Connect HDInsight z siecią lokalną](connect-on-premises-network.md) dokumentu.

## <a name="planning"></a>Planowanie

Poniżej przedstawiono pytania, na które musi odpowiedzieć podczas planowania instalacji HDInsight w sieci wirtualnej:

* Potrzebujesz instalować HDInsight w istniejącej sieci wirtualnej? Czy tworzysz nową sieć?

    Jeśli używasz istniejącej sieci wirtualnej może być konieczne modyfikowanie konfiguracji sieciowej, przed zainstalowaniem HDInsight. Aby uzyskać więcej informacji, zobacz [Dodaj HDInsight do istniejącej sieci wirtualnej](#existingvnet) sekcji.

* Czy chcesz połączyć sieć wirtualną, zawierający HDInsight do innej sieci wirtualnej lub sieci lokalnej?

    Aby można było łatwo pracować z zasobami w sieciach, może być konieczne utworzenie niestandardowego systemu DNS i skonfigurowanie przekazywania DNS. Aby uzyskać więcej informacji, zobacz [łączenie wielu sieci](#multinet) sekcji.

* Czy chcesz ograniczyć/przekierowania ruchu przychodzącego i wychodzącego do HDInsight?

    HDInsight musi mieć nieograniczony komunikacji z określonych adresów IP w centrum danych platformy Azure. Dostępne są także kilku portów, które muszą być dozwolone za pośrednictwem zapór do komunikacji z klientem. Aby uzyskać więcej informacji, zobacz [kontrolowanie ruchu sieciowego](#networktraffic) sekcji.

## <a id="existingvnet"></a>Dodaj HDInsight do istniejącej sieci wirtualnej

Wykonaj kroki w tej sekcji, aby dowiedzieć się, jak dodać nowe HDInsight do istniejącej sieci wirtualnej platformy Azure.

> [!NOTE]  
> Nie można dodać do istniejącego klastra HDInsight w sieci wirtualnej.

1. Czy używasz klasycznym lub modelu wdrażania usługi Resource Manager dla sieci wirtualnej?

    HDInsight 3.4 i nowszych wymaga sieci wirtualnej usługi Resource Manager. Wcześniejszych wersjach HDInsight wymagana klasycznej sieci wirtualnej.

    Istniejącej sieci w przypadku klasycznej sieci wirtualnej, należy utworzyć sieć wirtualną usługi Resource Manager i następnie połączyć dwa. [Łączenie klasycznych sieci wirtualnych z nowymi sieciami wirtualnymi](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    Gdy dołączony, HDInsight, zainstalowane w sieci usługi Resource Manager mogą wchodzić w interakcje z zasobami w klasycznej sieci.

2. Używasz wymuszone tunelowanie? Wymuszone tunelowanie ma ustawienia podsieci, która wymusza ruch wychodzący z Internetu na urządzeniu w celu przeprowadzenia inspekcji i rejestrowania. HDInsight nie obsługuje wymuszonym tunelowaniem. Usuń, wymuszonego tunelowania przed wdrożeniem HDInsight na istniejącą podsieć lub Utwórz nową podsieć z nie wymuszonego tunelowania dla HDInsight.

3. Czy używasz sieciowych grup zabezpieczeń, trasy zdefiniowane przez użytkownika lub wirtualnych urządzeń sieciowych do ograniczania ruchu do lub z sieci wirtualnej?

    Jako usługa zarządzana HDInsight wymaga nieograniczony dostęp do wielu adresów IP w centrum danych platformy Azure. Aby umożliwić komunikację z tych adresów IP, zaktualizuj istniejących grup zabezpieczeń sieci ani trasy zdefiniowane przez użytkownika.
    
    HDInsight obsługuje wiele usług, które korzystają z różnych portów. Nie blokuje ruchu skierowanego do tych portów. Aby uzyskać listę portów, aby umożliwić przez urządzenie wirtualne zapory Zobacz sekcję zabezpieczeń.
    
    Aby znaleźć istniejącej konfiguracji zabezpieczeń, użyj następujących poleceń programu Azure PowerShell lub wiersza polecenia platformy Azure:

    * Grupy zabezpieczeń sieci

        Zastąp `RESOURCEGROUP` nazwą grupy zasobów zawierającej sieć wirtualną, a następnie wpisz polecenie:
    
        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```
    
        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z sieciowymi grupami zabezpieczeń](../virtual-network/diagnose-network-traffic-filter-problem.md) dokumentu.

        > [!IMPORTANT]  
        > Reguły sieciowej grupy zabezpieczeń są stosowane w kolejności, w oparciu o priorytet reguły. Pierwszą regułę, która pasuje do wzorca ruch jest stosowany, a nie inne są stosowane dla tego ruchu. Kolejność reguł z najwyższych uprawnieniach do najniższych uprawnieniach. Aby uzyskać więcej informacji, zobacz [filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](../virtual-network/security-overview.md) dokumentu.

    * Trasy definiowane przez użytkownika

        Zastąp `RESOURCEGROUP` nazwą grupy zasobów zawierającej sieć wirtualną, a następnie wpisz polecenie:

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z trasami](../virtual-network/diagnose-network-routing-problem.md) dokumentu.

4. Tworzenie klastra usługi HDInsight, a następnie wybierz sieć wirtualną platformy Azure podczas konfiguracji. Aby zrozumieć ten proces tworzenia klastra, wykonaj kroki w następujących dokumentach:

    * [Create HDInsight using the Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md) (Tworzenie klastrów usługi HDInsight za pomocą usługi Azure Portal)
    * [Create HDInsight using Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) (Tworzenie klastrów usługi HDInsight przy użyciu programu Azure PowerShell)
    * [Tworzenie przy użyciu klasyczny interfejs wiersza polecenia usługi Azure HDInsight](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Utwórz HDInsight przy użyciu szablonu usługi Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > Dodawanie HDInsight z siecią wirtualną jest krokiem opcjonalnym. Pamiętaj wybrać sieć wirtualną, podczas konfigurowania klastra.

## <a id="multinet"></a>Łączenie wielu sieci

Największe wyzwanie z konfiguracją wielu sieci jest rozpoznawanie nazw między tymi sieciami.

Platforma Azure udostępnia rozpoznawanie nazw dla usług platformy Azure, które są zainstalowane w sieci wirtualnej. To rozpoznawanie nazw wbudowane umożliwia HDInsight połączyć się z następującymi zasobami przy użyciu w pełni kwalifikowaną nazwę domeny (FQDN):

* Zasób, który jest dostępny w Internecie. Na przykład microsoft.com, windowsupdate.com.

* Dowolnego zasobu, który znajduje się w tej samej sieci wirtualnej platformy Azure, używając __wewnętrzne nazwy DNS__ zasobu. Na przykład podczas rozpoznawania nazwy domyślnej, poniżej przedstawiono przykład przypisane do węzłów procesu roboczego HDInsight wewnętrzne nazwy DNS:

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Oba te węzły mogą komunikować się bezpośrednio ze sobą, a inne węzły w HDInsight, za pomocą wewnętrzne nazwy DNS.

Rozpoznawanie nazw domyślny jest __nie__ Zezwalaj HDInsight do rozpoznawania nazw zasobów w sieci, które są przyłączone do sieci wirtualnej. Na przykład jest wspólne dla Dołącz do sieci lokalnej do sieci wirtualnej. Z tylko domyślne rozpoznawania nazw HDInsight nie może uzyskiwać dostęp do zasobów w sieci lokalnej według nazwy. Odwrotny jest również ma wartość true, zasobów w sieci lokalnej nie może uzyskiwać dostęp do zasobów w sieci wirtualnej według nazwy.

> [!WARNING]  
> Należy utworzyć niestandardowego serwera DNS i skonfigurować sieci wirtualnej, aby użyć go przed utworzeniem klastra HDInsight.

Aby włączyć rozpoznawanie nazw między zasobami w dołączonym do sieci i sieci wirtualnej, należy wykonać następujące czynności:

1. Tworzenie niestandardowego serwera DNS w sieci wirtualnej platformy Azure, której planujesz zainstalować HDInsight.

2. Konfigurowanie sieci wirtualnej, aby użyć niestandardowego serwera DNS.

3. Znajdź Azure przypisane sufiks DNS dla sieci wirtualnej. Ta wartość jest podobny do `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`. Aby uzyskać informacje dotyczące znajdowania sufiks DNS, zobacz [przykładu: Niestandardowe DNS](#example-dns) sekcji.

4. Konfigurowanie składnika przesyłanie dalej między serwerami DNS. Konfiguracja zależy od rodzaju sieci zdalnej.

   * Jeśli zdalna sieć jest siecią lokalną, skonfiguruj DNS w następujący sposób:
        
     * __Niestandardowe DNS__ (w sieci wirtualnej):

         * Przesyłania żądań dla sufiksu DNS w sieci wirtualnej do mechanizmu rozpoznawania cyklicznego platformy Azure (168.63.129.16). Azure obsługuje żądania dotyczące zasobów w sieci wirtualnej

         * Przekazuje wszystkie żądania do serwera DNS w środowisku lokalnym. DNS w środowisku lokalnym obsługuje wszystkie inne żądania dotyczące rozpoznawania nazw, nawet żądania zasobów internetowych, np. Microsoft.com.

     * __W środowisku lokalnym DNS__: Przesyłania żądań dla sufiksu DNS sieci wirtualnej do niestandardowego serwera DNS. Niestandardowy serwer DNS przekazuje następnie do mechanizmu rozpoznawania cyklicznego platformy Azure.

       Tego żądania trasy konfiguracji dla w pełni kwalifikowane nazwy domeny, które zawierają sufiks DNS w sieci wirtualnej do niestandardowego serwera DNS. Wszystkie żądania (nawet w przypadku publicznych adresów internetowych) są obsługiwane przez serwer DNS w środowisku lokalnym.

   * Jeśli sieci zdalnej jest kolejną sieć wirtualną platformy Azure, skonfiguruj DNS w następujący sposób:

     * __Niestandardowe DNS__ (w każdej sieci wirtualnej):

         * Żądania dla sufiksu DNS w sieci wirtualnej są przekazywane do niestandardowych serwerów DNS. Serwer DNS w każdej sieci wirtualnej jest odpowiedzialny za rozpoznawanie zasobów w ramach jego sieci.

         * Przekazywać wszystkich innych żądaniach do mechanizmu rozpoznawania cyklicznego platformy Azure. Cyklicznego programu rozpoznawania nazw jest odpowiedzialny za rozpoznawania lokalnych i zasobów w Internecie.

       Serwer DNS dla każdej sieci przekazuje żądania do drugiej strony, na podstawie sufiksu DNS. Inne żądania są rozwiązane, używanie mechanizmu rozpoznawania cyklicznego platformy Azure.

     Na przykład konfiguracjami zobacz [przykładu: Niestandardowe DNS](#example-dns) sekcji.

Aby uzyskać więcej informacji, zobacz [rozpoznawania nazw dla maszyn wirtualnych i wystąpień roli](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) dokumentu.

## <a name="directly-connect-to-apache-hadoop-services"></a>Bezpośrednie łączenie z usługi Apache Hadoop

Możesz nawiązać połączenie klastrem pod adresem `https://CLUSTERNAME.azurehdinsight.net`. Ten adres używa publicznego adresu IP, może nie być dostępny, jeśli używano sieciowych grup zabezpieczeń, aby ograniczyć ruch przychodzący z Internetu. Ponadto w przypadku wdrażania klastra w sieci wirtualnej można do niego dostęp przy użyciu prywatnych punktów końcowych `https://CLUSTERNAME-int.azurehdinsight.net`. Ten punkt końcowy jest rozpoznawany jako prywatny adres IP w sieci wirtualnej, aby uzyskać dostęp do klastra.

Nawiązywanie Apache Ambari i stron sieci web za pośrednictwem sieci wirtualnej, użyj następujących kroków:

1. Aby odnaleźć w wewnętrznej w pełni kwalifikowanych nazw domen (FQDN) węzłów klastra HDInsight, użyj jednej z następujących metod:

    Zastąp `RESOURCEGROUP` nazwą grupy zasobów zawierającej sieć wirtualną, a następnie wpisz polecenie:

    ```powershell
    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP" | where-object {$_.Name -like "*node*"}

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
    az network nic list --resource-group RESOURCEGROUP --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    Na liście węzłów zwrócił znaleźć nazwę FQDN dla węzłów głównych i nawiązywanie Ambari i inne usługi sieci web za pomocą nazwy FQDN. Na przykład użyć `http://<headnode-fqdn>:8080` na dostęp do narzędzia Ambari.

    > [!IMPORTANT]  
    > Niektóre usługi do węzłów głównych w serwisie tylko są aktywne na jednym węźle naraz. Jeśli zostanie podjęta, uzyskiwanie dostępu do usługi w jednym węźle głównym i zwraca błąd 404, przełącz się do innego węzła głównego.

2. Aby określić węzeł i portów, które usługi są dostępne w, zobacz [porty używane przez usługi Hadoop w HDInsight](./hdinsight-hadoop-port-settings-for-services.md) dokumentu.

## <a id="networktraffic"></a> Kontrolowanie ruchu sieciowego

Ruch sieciowy w sieciach wirtualnych platformy Azure mogą być kontrolowane za pomocą następujących metod:

* **Sieciowe grupy zabezpieczeń** (NSG) umożliwiają filtrowanie ruchu przychodzącego i wychodzącego do sieci. Aby uzyskać więcej informacji, zobacz [filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](../virtual-network/security-overview.md) dokumentu.

    > [!WARNING]  
    > HDInsight nie obsługuje Ograniczanie ruchu wychodzącego. Powinien być dozwolony ruch wychodzący.

* **Trasy zdefiniowane przez użytkownika** (UDR) określać przepływ ruchu między zasobami w sieci. Aby uzyskać więcej informacji, zobacz [trasy zdefiniowane przez użytkownika i przesyłaniu dalej IP](../virtual-network/virtual-networks-udr-overview.md) dokumentu.

* **Sieciowych urządzeń wirtualnych** replikować z działaniem urządzeń, takich jak routery i zapory. Aby uzyskać więcej informacji, zobacz [urządzenia sieciowe](https://azure.microsoft.com/solutions/network-appliances) dokumentu.

Jako usługa zarządzana HDInsight wymaga nieograniczony dostęp do kondycji HDInsight i usług zarządzania, zarówno dla ruchu przychodzącego i wychodzącego z sieci Wirtualnej. Przy użyciu sieciowych grup zabezpieczeń i tras zdefiniowanych przez użytkownika, należy się upewnić, że te usługi nadal może komunikować się z klastrem HDInsight.

### <a id="hdinsight-ip"></a> HDInsight przy użyciu sieciowych grup zabezpieczeń i trasy zdefiniowane przez użytkownika

Jeśli planujesz użycie **sieciowe grupy zabezpieczeń** lub **trasy zdefiniowane przez użytkownika** do sterowania ruchem sieciowym, wykonaj następujące czynności przed zainstalowaniem HDInsight:

1. Określ region platformy Azure, które będą używane dla HDInsight.

2. Określ adresy IP wymagane przez HDInsight. Aby uzyskać więcej informacji, zobacz [adresy IP wymagane przez HDInsight](#hdinsight-ip) sekcji.

3. Utwórz lub zmodyfikuj sieciowych grup zabezpieczeń lub tras zdefiniowanych przez użytkownika dla podsieci, której planujesz zainstalować HDInsight do.

    * __Sieciowe grupy zabezpieczeń__: Zezwalaj na __dla ruchu przychodzącego__ ruch na porcie __443__ z adresu IP adresów. Pozwoli to zagwarantować, usługi zarządzania usługi HDI dotrzeć do klastra z zewnętrznej sieci Wirtualnej.
    * __Trasy zdefiniowane przez użytkownika__: Jeśli planujesz użyć tras zdefiniowanych przez użytkownika, tworzona jest trasa dla każdego adresu IP i ustawić __typu następnego przeskoku__ do __Internet__. Należy również zezwolić wszelki ruch wychodzący z sieci Wirtualnej bez żadnych ograniczeń. Na przykład mogą kierować cały pozostały ruch do platformy Azure zapory lub sieciowych urządzeń wirtualnych (hostowanego na platformie Azure) do celów monitorowania, ale ruch wychodzący nie powinien być blokowany.

Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń lub tras zdefiniowanych przez użytkownika dokumentacji:

* [Sieciowa grupa zabezpieczeń](../virtual-network/security-overview.md)

* [Trasy zdefiniowane przez użytkownika](../virtual-network/virtual-networks-udr-overview.md)

#### <a name="forced-tunneling-to-on-premise"></a>Wymuszonego tunelowania do środowiska lokalnego

Wymuszone tunelowanie jest zdefiniowane przez użytkownika konfiguracji routingu którym cały ruch z podsieci jest zmuszony do określonej sieci lub lokalizacji, takiej jak sieć lokalną. HDInsight jest __nie__ obsługi wymuszonego tunelowania do sieci lokalnej. Jeśli używasz zapory usługi Azure lub sieciowe urządzenie wirtualne hostowane na platformie Azure, można użyć tras zdefiniowanych przez użytkownika, przekierowywanie ruchu do konta magazynu do celów monitorowania i zezwolić na cały ruch wychodzący.

## <a id="hdinsight-ip"></a> Wymaganych adresów IP

> [!IMPORTANT]  
> Zarządzania i kondycji usług platformy Azure musi być w stanie nawiązać połączenia z usługą HDInsight. Jeśli używasz sieciowych grup zabezpieczeń lub tras zdefiniowanych przez użytkownika, należy zezwalać na ruch z adresów IP dla tych usług do osiągnięcia HDInsight.
>
> Jeśli nie używasz sieciowych grup zabezpieczeń lub tras zdefiniowanych przez użytkownika do kontroli ruchu, możesz zignorować tę sekcję.

Jeśli używasz grup zabezpieczeń sieci, muszą zezwalać na ruch z usługi kondycji i zarządzania platformy Azure do osiągnięcia klastry HDInsight na porcie 443. Musisz również zezwolić na ruch między maszynami wirtualnymi w tej podsieci. Aby znaleźć adresy IP, które muszą być dozwolone, wykonaj następujące kroki:

1. Zawsze muszą zezwalać na ruch z następujących adresów IP:

    | Źródłowy adres IP | Port docelowy | Direction |
    | ---- | ----- | ----- |
    | 168.61.49.99 | 443 | Przychodzący |
    | 23.99.5.239 | 443 | Przychodzący |
    | 168.61.48.131 | 443 | Przychodzący |
    | 138.91.141.162 | 443 | Przychodzący |

2. W przypadku klastra usługi HDInsight w jednym z następujących regionów, muszą zezwalać na ruch z adresów IP dla regionu na liście:

    > [!IMPORTANT]  
    > Jeśli region platformy Azure, którego używasz, nie jest wymieniony, następnie używać tylko cztery adresy IP z kroku 1.

    | Kraj | Region | Dozwolone adresy IP źródła | Dozwolone port docelowy | Direction |
    | ---- | ---- | ---- | ---- | ----- |
    | Azja | Azja Wschodnia | 23.102.235.122</br>52.175.38.134 | 443 | Przychodzący |
    | &nbsp; | Azja Południowo-Wschodnia | 13.76.245.160</br>13.76.136.249 | 443 | Przychodzący |
    | Australia | Australia Wschodnia | 104.210.84.115</br>13.75.152.195 | 443 | Przychodzący |
    | &nbsp; | Australia Południowo-Wschodnia | 13.77.2.56</br>13.77.2.94 | 443 | Przychodzący |
    | Brazylia | Brazylia Południowa | 191.235.84.104</br>191.235.87.113 | 443 | Przychodzący |
    | Kanada | Kanada Wschodnia | 52.229.127.96</br>52.229.123.172 | 443 | Przychodzący |
    | &nbsp; | Kanada Środkowa | 52.228.37.66</br>52.228.45.222 | 443 | Przychodzący |
    | Chiny | Chiny Północne | 42.159.96.170</br>139.217.2.219</br></br>42.159.198.178</br>42.159.234.157 | 443 | Przychodzący |
    | &nbsp; | Chiny Wschodnie | 42.159.198.178</br>42.159.234.157</br></br>42.159.96.170</br>139.217.2.219 | 443 | Przychodzący |
    | &nbsp; | Chiny Północne 2 | 40.73.37.141</br>40.73.38.172 | 443 | Przychodzący |
    | &nbsp; | Chiny Wschodnie 2 | 139.217.227.106</br>139.217.228.187 | 443 | Przychodzący |
    | Europa | Europa Północna | 52.164.210.96</br>13.74.153.132 | 443 | Przychodzący |
    | &nbsp; | Europa Zachodnia| 52.166.243.90</br>52.174.36.244 | 443 | Przychodzący |
    | Francja | Francja Środkowa| 20.188.39.64</br>40.89.157.135 | 443 | Przychodzący |
    | Niemcy | Niemcy Środkowe | 51.4.146.68</br>51.4.146.80 | 443 | Przychodzący |
    | &nbsp; | Niemcy Północno-Wschodnie | 51.5.150.132</br>51.5.144.101 | 443 | Przychodzący |
    | Indie | Indie Środkowe | 52.172.153.209</br>52.172.152.49 | 443 | Przychodzący |
    | &nbsp; | Indie Południowe | 104.211.223.67<br/>104.211.216.210 | 443 | Przychodzący |
    | Japonia | Japonia Wschodnia | 13.78.125.90</br>13.78.89.60 | 443 | Przychodzący |
    | &nbsp; | Japonia Zachodnia | 40.74.125.69</br>138.91.29.150 | 443 | Przychodzący |
    | Korea | Korea Środkowa | 52.231.39.142</br>52.231.36.209 | 433 | Przychodzący |
    | &nbsp; | Korea Południowa | 52.231.203.16</br>52.231.205.214 | 443 | Przychodzący
    | Zjednoczone Królestwo | Zachodnie Zjednoczone Królestwo | 51.141.13.110</br>51.141.7.20 | 443 | Przychodzący |
    | &nbsp; | Południowe Zjednoczone Królestwo | 51.140.47.39</br>51.140.52.16 | 443 | Przychodzący |
    | Stany Zjednoczone | Środkowe stany USA | 13.67.223.215</br>40.86.83.253 | 443 | Przychodzący |
    | &nbsp; | Wschodnie stany USA | 13.82.225.233</br>40.71.175.99 | 443 | Przychodzący |
    | &nbsp; | Środkowo-północne stany USA | 157.56.8.38</br>157.55.213.99 | 443 | Przychodzący |
    | &nbsp; | Środkowo-zachodnie stany USA | 52.161.23.15</br>52.161.10.167 | 443 | Przychodzący |
    | &nbsp; | Zachodnie stany USA | 13.64.254.98</br>23.101.196.19 | 443 | Przychodzący |
    | &nbsp; | Zachodnie stany USA 2 | 52.175.211.210</br>52.175.222.222 | 443 | Przychodzący |

    Aby uzyskać informacji na temat adresów IP do użycia dla platformy Azure Government, zobacz [platformy Azure dla instytucji rządowych w rozwiązania inteligentne + analiza](https://docs.microsoft.com/azure/azure-government/documentation-government-services-intelligenceandanalytics) dokumentu.

3. Należy także zezwolić na dostęp z __168.63.129.16__. Ten adres jest Azure cyklicznego programu rozpoznawania nazw. Aby uzyskać więcej informacji, zobacz [rozpoznawanie nazw dla maszyn wirtualnych i ról wystąpień](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) dokumentu.

Aby uzyskać więcej informacji, zobacz [kontrolowanie ruchu sieciowego](#networktraffic) sekcji.

Jeśli używasz routes(UDRs) zdefiniowanych przez użytkownika, możesz określić trasę i zezwolić na ruch wychodzący z sieci Wirtualnej do powyższych adresów IP z następnego przeskoku jest ustawiona na "Internet".
    
## <a id="hdinsight-ports"></a> Wymagane porty

Jeśli planujesz użycie **zapory** i uzyskać dostęp do klastra z poza na określonych portach, może być konieczne zezwolić na ruch na tych portach potrzebnych do danego scenariusza. Domyślnie nie specjalne umieszczania na białej liście portów potrzebny jest tak długo, jak opisano w poprzedniej sekcji ruch zarządzania platformy azure może dotrzeć klastra na porcie 443.

Aby uzyskać listę portów dla określonych usług, zobacz [porty używane przez usługi Apache Hadoop w HDInsight](hdinsight-hadoop-port-settings-for-services.md) dokumentu.

Aby uzyskać więcej informacji na temat reguł zapory dla urządzeń wirtualnych, zobacz [scenariusza urządzenie wirtualne](../virtual-network/virtual-network-scenario-udr-gw-nva.md) dokumentu.

## <a id="hdinsight-nsg"></a>Przykład: sieciowe grupy zabezpieczeń z HDInsight

Przykłady w tej sekcji pokazują, jak utworzyć zabezpieczeń sieci zasad grupy, umożliwiające HDInsight komunikować się z usługami zarządzania systemu Azure. Przed użyciem przykładów, Dostosuj adresy IP, aby dopasować te, dla regionu platformy Azure, którego używasz. Można znaleźć te informacje w [HDInsight przy użyciu sieciowych grup zabezpieczeń i trasy zdefiniowane przez użytkownika](#hdinsight-ip) sekcji.

### <a name="azure-resource-management-template"></a>Szablon usługi Azure Resource Management

Następujący szablon funkcji zarządzania zasobami tworzy sieć wirtualną, która ogranicza ruch przychodzący, ale zezwala na ruch z adresów IP związanej z HDInsight. Ten szablon utworzy również klaster usługi HDInsight w sieci wirtualnej.

* [Wdrażanie zabezpieczonej sieci wirtualnej platformy Azure oraz klaster usługi HDInsight Hadoop](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

> [!IMPORTANT]  
> Zmień adresy IP używane w tym przykładzie, aby dopasować regionu platformy Azure, którego używasz. Można znaleźć te informacje w [HDInsight przy użyciu sieciowych grup zabezpieczeń i trasy zdefiniowane przez użytkownika](#hdinsight-ip) sekcji.

### <a name="azure-powershell"></a>Azure PowerShell

Poniższy skrypt programu PowerShell umożliwia tworzenie sieci wirtualnej, która ogranicza ruch przychodzący i zezwala na ruch z adresów IP w regionie Europa Północna.

> [!IMPORTANT]  
> Zmień adresy IP używane w tym przykładzie, aby dopasować regionu platformy Azure, którego używasz. Można znaleźć te informacje w [HDInsight przy użyciu sieciowych grup zabezpieczeń i trasy zdefiniowane przez użytkownika](#hdinsight-ip) sekcji.

```powershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"
# Get the Virtual Network object
$vnet = Get-AzVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName
# Get the region the Virtual network is in.
$location = $vnet.Location
# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule5" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule6" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `
# Set the changes to the security group
Set-AzNetworkSecurityGroup -NetworkSecurityGroup $nsg
# Apply the NSG to the subnet
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

> [!IMPORTANT]  
> W tym przykładzie przedstawiono sposób dodawania reguły zezwalające na ruch przychodzący na wymaganych adresów IP. Nie zawiera zasadę, aby ograniczyć dostęp dla ruchu przychodzącego z innych źródeł.
>
> Poniższy przykład ilustruje sposób umożliwić dostęp SSH z Internetu:
>
> ```powershell
> Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
> ```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Wykonaj następujące kroki, aby utworzyć sieć wirtualną, która ogranicza ruch przychodzący, ale zezwala na ruch z adresów IP związanej z HDInsight.

1. Użyj następującego polecenia, aby utworzyć nową sieciową grupę zabezpieczeń o nazwie `hdisecure`. Zastąp `RESOURCEGROUP` z grupą zasobów, która zawiera Azure Virtual Network. Zastąp `LOCATION` z grupa została utworzona w lokalizacji (regionu).

    ```azurecli
    az network nsg create -g RESOURCEGROUP -n hdisecure -l LOCATION
    ```

    Po utworzeniu grupy możesz uzyskiwać informacje dotyczące nowej grupy.

2. Aby dodać reguły do nowej grupy zabezpieczeń sieci, dzięki czemu komunikacji dla ruchu przychodzącego na porcie 443 z usługi kondycji i zarządzania usługi Azure HDInsight, należy użyć następującego. Zastąp `RESOURCEGROUP` nazwą grupy zasobów, która zawiera Azure Virtual Network.

    > [!IMPORTANT]  
    > Zmień adresy IP używane w tym przykładzie, aby dopasować regionu platformy Azure, którego używasz. Można znaleźć te informacje w [HDInsight przy użyciu sieciowych grup zabezpieczeń i trasy zdefiniowane przez użytkownika](#hdinsight-ip) sekcji.

    ```azurecli
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule6 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. Aby pobrać Unikatowy identyfikator dla tej grupy zabezpieczeń sieci, użyj następującego polecenia:

    ```azurecli
    az network nsg show -g RESOURCEGROUP -n hdisecure --query 'id'
    ```

    To polecenie zwraca wartość podobny do następującego tekstu:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    Użyj podwójnych cudzysłowów wokół `id` w poleceniu, jeśli nie otrzymujesz oczekiwanych wyników.

4. Użyj następującego polecenia, aby zastosować sieciową grupę zabezpieczeń do podsieci. Zastąp `GUID` i `RESOURCEGROUP` zwracane wartości, które z nich, które w poprzednim kroku. Zastąp `VNETNAME` i `SUBNETNAME` o nazwie sieci wirtualnej i nazwę podsieci, która ma zostać utworzona.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUP --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Po wykonaniu tego polecenia można zainstalować HDInsight w sieci wirtualnej.

> [!IMPORTANT]  
> Te kroki otwierać tylko dostęp do usługi HDInsight kondycji i zarządzania w chmurze Azure. Wszystkie inne dostęp do klastra HDInsight z spoza sieci wirtualnej jest zablokowany. Aby włączyć dostęp spoza sieci wirtualnej, należy dodać dodatkowe reguły sieciowej grupy zabezpieczeń.
>
> Poniższy przykład ilustruje sposób umożliwić dostęp SSH z Internetu:
>
> ```azurecli
> az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
> ```

## <a id="example-dns"></a> Przykład: Konfiguracja usługi DNS

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Rozpoznawanie nazw między sieci wirtualnej i sieci połączonych w środowisku lokalnym

W tym przykładzie sprawia, że następujące założenia:

* Masz z siecią wirtualną platformy Azure, która jest połączona z siecią lokalną za pomocą bramy sieci VPN.

* Niestandardowego serwera DNS w sieci wirtualnej działa jako system operacyjny Linux lub Unix.

* [Powiąż](https://www.isc.org/downloads/bind/) jest zainstalowany na niestandardowego serwera DNS.

Na niestandardowego serwera DNS w sieci wirtualnej:

1. Użyj programu Azure PowerShell lub wiersza polecenia platformy Azure, aby znaleźć sufiks DNS w sieci wirtualnej:

    Zastąp `RESOURCEGROUP` nazwą grupy zasobów zawierającej sieć wirtualną, a następnie wpisz polecenie:

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Na niestandardowego serwera DNS dla sieci wirtualnej, skorzystaj z poniższego tekstu jako zawartość `/etc/bind/named.conf.local` pliku:

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Zastąp `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` wartość sufiksu DNS w sieci wirtualnej.

    Ta konfiguracja trasy wszystkich żądań DNS dla sufiksu DNS w sieci wirtualnej do mechanizmu rozpoznawania cyklicznego platformy Azure.

2. Na niestandardowego serwera DNS dla sieci wirtualnej, skorzystaj z poniższego tekstu jako zawartość `/etc/bind/named.conf.options` pliku:

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * Zastąp `10.0.0.0/16` wartość z zakresu adresów IP w sieci wirtualnej. Ten wpis umożliwia adresów żądań rozpoznawania nazw, w tym zakresie.

    * Dodaj zakres adresów IP w sieci lokalnej w celu `acl goodclients { ... }` sekcji.  Wpis umożliwia żądań rozpoznawania nazw z zasobów w sieci lokalnej.
    
    * Zastąp wartość symbolu `192.168.0.1` przy użyciu adresu IP serwera DNS w środowisku lokalnym. Ten wpis kieruje wszystkie żądania DNS do serwera DNS w środowisku lokalnym.

3. Do korzystania z konfiguracji, uruchom ponownie powiązania. Na przykład `sudo service bind9 restart`.

4. Dodaj warunkowego przesyłania dalej do lokalnego serwera DNS. Konfigurowanie warunkowego przesyłania dalej do wysyłania żądań dla sufiksu DNS z kroku 1 do niestandardowego serwera DNS.

    > [!NOTE]  
    > W dokumentacji oprogramowania DNS, aby uzyskać szczegółowe informacje na temat dodawania warunkowego przesyłania dalej.

Po wykonaniu tych kroków, można połączyć się z zasobami w dowolnej sieci przy użyciu w pełni kwalifikowanych nazw domen (FQDN). Można teraz zainstalować HDInsight w sieci wirtualnej.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Rozpoznawanie nazw między dwiema sieciami wirtualnymi połączone

W tym przykładzie sprawia, że następujące założenia:

* Masz dwie sieci wirtualne platformy Azure, które są połączone za pomocą bramy sieci VPN lub komunikacji równorzędnej.

* Niestandardowego serwera DNS w obu sieciach działa jako system operacyjny Linux lub Unix.

* [Powiąż](https://www.isc.org/downloads/bind/) jest zainstalowany na niestandardowych serwerów DNS.

1. Użyj programu Azure PowerShell lub wiersza polecenia platformy Azure, aby znaleźć sufiks DNS obie sieci wirtualne:

    Zastąp `RESOURCEGROUP` nazwą grupy zasobów zawierającej sieć wirtualną, a następnie wpisz polecenie:

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Skorzystaj z poniższego tekstu jako zawartość `/etc/bind/named.config.local` pliku na niestandardowego serwera DNS. Na serwerze DNS niestandardowych w obu sieciach wirtualnych, należy wprowadzić tę zmianę.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Zastąp `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` wartością sufiksu DNS __innych__ sieci wirtualnej. Ten wpis kieruje żądania dla sufiksu DNS w sieci zdalnej, do niestandardowego serwera DNS w tej sieci.

3. Na niestandardowe serwery DNS w obu sieciach wirtualnych, skorzystaj z poniższego tekstu jako zawartość `/etc/bind/named.conf.options` pliku:

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver         
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
   Zastąp `10.0.0.0/16` i `10.1.0.0/16` wartości z adresem IP odnoszą się do zakresów sieci wirtualnych. Ten wpis umożliwia zasoby w każdej sieci na wysyłanie żądań do serwerów DNS.

    Wszystkie żądania, które się nie sufiksy DNS, sieci wirtualnych (na przykład microsoft.com) jest obsługiwane przez mechanizm rozpoznawania cyklicznego platformy Azure.

4. Do korzystania z konfiguracji, uruchom ponownie powiązania. Na przykład `sudo service bind9 restart` na obu serwerach DNS.

Po wykonaniu tych kroków, można połączyć się z zasobami w sieci wirtualnej przy użyciu w pełni kwalifikowanych nazw domen (FQDN). Można teraz zainstalować HDInsight w sieci wirtualnej.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać przykład end-to-end konfigurowania HDInsight, aby nawiązać połączenie między siecią lokalną, zobacz [Connect HDInsight z siecią lokalną](./connect-on-premises-network.md).
* Podczas konfigurowania klastrów Apache Hbase w sieci wirtualnej platformy Azure, zobacz [tworzenie bazy danych Apache HBase klastrów HDInsight w usłudze Azure Virtual Network](hbase/apache-hbase-provision-vnet.md).
* Do konfigurowania replikacji geograficznej bazy danych Apache HBase, zobacz [Konfigurowanie replikacji klaster bazy danych Apache HBase w sieci wirtualnej platformy Azure](hbase/apache-hbase-replication.md).
* Aby uzyskać więcej informacji na temat sieci wirtualnych platformy Azure, zobacz [Omówienie usługi Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

* Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md).

* Aby uzyskać więcej informacji na temat trasy zdefiniowane przez użytkownika, zobacz [trasy zdefiniowane przez użytkownika i przesyłaniu dalej IP](../virtual-network/virtual-networks-udr-overview.md).
