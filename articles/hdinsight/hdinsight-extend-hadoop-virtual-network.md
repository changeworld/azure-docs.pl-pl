---
title: Rozszerzenie usługi HDInsight za pomocą sieci wirtualnej - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać sieci wirtualnej platformy Azure do łączenia usługi HDInsight do innych zasobów w chmurze lub zasobów w centrum danych.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: 37b9b600-d7f8-4cb1-a04a-0b3a827c6dcc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/21/2018
ms.author: larryfr
ms.openlocfilehash: 842746561b74860e674fbaa298c78bb0ac58bd68
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112137"
---
# <a name="extend-azure-hdinsight-using-an-azure-virtual-network"></a>Rozszerzenie Azure HDInsight przy użyciu sieci wirtualnej platformy Azure

Dowiedz się, jak używać usługi HDInsight za pomocą [sieci wirtualnej Azure](../virtual-network/virtual-networks-overview.md). Przy użyciu sieci wirtualnej platformy Azure umożliwia następujące scenariusze:

* Łączenie z usługą HDInsight bezpośrednio z sieci lokalnej.

* Łączenie z danymi usługi HDInsight są przechowywane w sieci wirtualnej platformy Azure.

* Bezpośredni dostęp do usług Hadoop, które nie są dostępne publicznie w Internecie. Na przykład interfejsy API Kafka lub interfejsu API języka Java HBase.

> [!WARNING]
> Informacje przedstawione w tym dokumencie wymaga zrozumienia sieci TCP/IP. Jeśli nie masz doświadczenia z TCP/IP w sieci, należy partnera z osobą, która jest przed dokonaniem zmiany w sieciach produkcyjnych.

> [!IMPORTANT]
> Jeśli szukasz wskazówki krok po kroku dotyczące łączenia HDInsight do lokalnej sieci za pomocą sieci wirtualnej platformy Azure, zobacz [HDInsight połączyć się z siecią lokalną](connect-on-premises-network.md) dokumentu.

## <a name="planning"></a>Planowanie

Poniżej przedstawiono pytania, na które musi odpowiedzieć w przypadku planowania instalacji usługi HDInsight w sieci wirtualnej:

* Czy trzeba zainstalować usługi HDInsight w istniejącej sieci wirtualnej? Lub w przypadku tworzenia nowej sieci?

    Jeśli korzystasz z istniejącej sieci wirtualnej, może być konieczne modyfikowanie konfiguracji sieciowej, przed zainstalowaniem usługi HDInsight. Aby uzyskać więcej informacji, zobacz [dodać HDInsight do istniejącej sieci wirtualnej](#existingvnet) sekcji.

* Czy chcesz połączyć sieć wirtualną zawierający HDInsight do innej sieci wirtualnej lub w sieci lokalnej?

    Aby łatwo pracy z zasobami w sieciach, może być konieczne utworzyć niestandardowe DNS i skonfigurować przesyłania dalej DNS. Aby uzyskać więcej informacji, zobacz [łączenie wielu sieci](#multinet) sekcji.

* Czy chcesz ograniczyć/przekierowanie ruchu przychodzącego i wychodzącego do usługi HDInsight

    HDInsight musi mieć nieograniczony komunikację z określonych adresów IP w centrum danych Azure. Istnieje kilka portów, które może do komunikacji z klientem za pośrednictwem zapór. Aby uzyskać więcej informacji, zobacz [kontrolowanie ruchu w sieci](#networktraffic) sekcji.

## <a id="existingvnet"></a>Dodaj HDInsight do istniejącej sieci wirtualnej

Wykonaj kroki w tej sekcji, aby dowiedzieć się, jak dodać nowy HDInsight do istniejącej sieci wirtualnej platformy Azure.

> [!NOTE]
> Nie można dodać istniejącego klastra usługi HDInsight w sieci wirtualnej.

1. Dla sieci wirtualnej jest używany klasyczny lub modelu wdrażania usługi Resource Manager?

    HDInsight 3.4 i większa wymaga sieci wirtualnej Menedżera zasobów. Wcześniejszych wersji usługi hdinsight wymagane klasycznej sieci wirtualnej.

    Istniejącej sieci w przypadku klasycznych sieci wirtualnej, należy utworzyć sieć wirtualną Resource Manager i połącz dwa. [Klasyczne sieci wirtualne nawiązywania połączenia z nowej sieci wirtualnych](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    Po dołączone do usługi HDInsight w sieci usługi Resource Manager może współdziałać z zasobów w klasycznej sieci.

2. Należy używać tunelowania wymuszonego? Wymuszanie tunelowania jest ustawienia podsieci, która wymusza wychodzący ruch internetowy do urządzenie na potrzeby inspekcji i rejestrowania. HDInsight nie obsługuje wymuszanie tunelowania. Usuń wymuszanie tunelowania przed zainstalowaniem usługi HDInsight do podsieci albo utwórz nową podsieć dla usługi HDInsight.

3. Używasz grup zabezpieczeń sieci, trasy zdefiniowane przez użytkownika lub urządzenia sieci wirtualne do ograniczania ruchu do lub z sieci wirtualnej?

    Jako zarządzanej usługi HDInsight wymaga nieograniczony dostęp do wielu adresów IP w centrum danych Azure. Aby umożliwić komunikację z tych adresów IP, zaktualizuj istniejących grup zabezpieczeń sieci ani trasy zdefiniowane przez użytkownika.

    HDInsight obsługuje wielu usług, które korzystają z różnych portów. Nie blokuje ruchu skierowanego do tych portów. Aby uzyskać listę portów, aby umożliwić przez urządzenie wirtualne zapory, zobacz [zabezpieczeń](#security) sekcji.

    Aby znaleźć istniejącej konfiguracji zabezpieczeń, użyj następujących poleceń programu Azure PowerShell lub interfejsu wiersza polecenia Azure:

    * Grupy zabezpieczeń sieci

        ```powershell
        $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
        get-azurermnetworksecuritygroup -resourcegroupname $resourceGroupName
        ```

        ```azurecli-interactive
        read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
        az network nsg list --resource-group $RESOURCEGROUP
        ```

        Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z grup zabezpieczeń sieci](../virtual-network/diagnose-network-traffic-filter-problem.md) dokumentu.

        > [!IMPORTANT]
        > Reguły grupy zabezpieczeń sieci są stosowane w kolejności, w oparciu o priorytet reguły. Zastosowano pierwszej reguły, która odpowiada wzorcowi ruchu, a nie inne są stosowane dla tego ruchu. Kolejność reguł z najbardziej ograniczająca najbardziej ograniczająca. Aby uzyskać więcej informacji, zobacz [filtrowania ruchu sieciowego z grup zabezpieczeń sieci](../virtual-network/security-overview.md) dokumentu.

    * Trasy definiowane przez użytkownika

        ```powershell
        $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
        get-azurermroutetable -resourcegroupname $resourceGroupName
        ```

        ```azurecli-interactive
        read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
        az network route-table list --resource-group $RESOURCEGROUP
        ```

        Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z tras](../virtual-network/diagnose-network-routing-problem.md) dokumentu.

4. Tworzenie klastra usługi HDInsight i wybierz sieć wirtualną Azure podczas konfigurowania. Zrozumienie procesu tworzenia klastra, wykonaj kroki w następujących dokumentach:

    * [Create HDInsight using the Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md) (Tworzenie klastrów usługi HDInsight za pomocą usługi Azure Portal)
    * [Create HDInsight using Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) (Tworzenie klastrów usługi HDInsight przy użyciu programu Azure PowerShell)
    * [Tworzenie usługi HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 1.0](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Tworzenie usługi HDInsight przy użyciu szablonu usługi Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

  > [!IMPORTANT]
  > Dodawanie HDInsight do sieci wirtualnej jest to krok konfiguracji opcjonalnej. Należy wybrać sieć wirtualną podczas konfigurowania klastra.

## <a id="multinet"></a>Łączenie wielu sieci

Największych wyzwanie z konfiguracji usługi sieciowej jest rozpoznawania nazw między sieciami.

Platforma Azure udostępnia rozpoznawanie nazw dla usług Azure, które są zainstalowane w sieci wirtualnej. To rozwiązanie nazwa wbudowanego umożliwia HDInsight połączyć się z następującymi zasobami za pomocą w pełni kwalifikowaną nazwą domeny (FQDN):

* Zasób, który jest dostępny w Internecie. Na przykład microsoft.com, google.com.

* Dowolnego zasobu, który znajduje się w tej samej sieci wirtualnej Azure, używając __wewnętrzna nazwa DNS__ zasobu. Na przykład podczas rozpoznawania nazwy domyślnej, poniżej przedstawiono przykład wewnętrznej nazwy DNS przypisane do węzłów procesu roboczego usługi HDInsight:

    * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
    * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Oba te węzły mogą komunikować się bezpośrednio ze sobą oraz inne węzły w usłudze HDInsight, za pomocą wewnętrznej nazwy DNS.

Rozpoznawanie nazw domyślny jest __nie__ Zezwalaj HDInsight do rozpoznawania nazw zasobów w sieci, które są przyłączone do sieci wirtualnej. Na przykład jest wspólne, aby dołączyć do sieci wirtualnej sieci lokalnej. Tylko domyślny rozpoznawania nazw HDInsight nie uzyskiwanie dostępu do zasobów w sieci lokalnej według nazwy. Odwrotny również ma wartość true, zasobów w sieci lokalnej nie może uzyskać dostęp do zasobów w sieci wirtualnej według nazwy.

> [!WARNING]
> Należy utworzyć niestandardowy serwer DNS i skonfiguruj sieć wirtualną w celu używania go przed utworzeniem klastra usługi HDInsight.

Aby włączyć rozpoznawanie nazw między zasobami w przyłączone do sieci i sieci wirtualnej, należy wykonać następujące czynności:

1. Utwórz niestandardowy serwer DNS w sieci wirtualnej platformy Azure, której zamierzasz zainstalować usługi HDInsight.

2. Skonfiguruj sieć wirtualną do użycia niestandardowego serwera DNS.

3. Znajdź Azure przypisane sufiksu DNS dla sieci wirtualnej. Ta wartość jest podobny do `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`. Aby uzyskać informacje o sufiks DNS, zobacz [przykład: niestandardowe DNS](#example-dns) sekcji.

4. Konfiguruj przekazywanie między serwerami DNS. Konfiguracja zależy od wybranego typu sieci zdalnej.

    * W przypadku sieci zdalnej przez sieć lokalną DNS należy skonfigurować następująco:
        
        * __Niestandardowe DNS__ (w sieci wirtualnej):

            * Przesyłania żądań dla sufiksu DNS w sieci wirtualnej do platformy Azure cyklicznego programu rozpoznawania nazw (168.63.129.16). Azure obsługuje żądania zasobów w sieci wirtualnej

            * Przekazuj wszystkie żądania do lokalnego serwera DNS. DNS lokalnymi obsługuje wszystkich innych żądań rozpoznawania nazw, nawet żądania zasobów internetowych, takich jak Microsoft.com.

        * __DNS lokalnymi__: przesyłać żądania dla sufiksu DNS sieci wirtualnej do niestandardowego serwera DNS. Niestandardowy serwer DNS przekazuje następnie do platformy Azure cyklicznego programu rozpoznawania nazw.

        Tej konfiguracji żądania trasy dla w pełni kwalifikowane nazwy domen, zawierające sufiks DNS w sieci wirtualnej do niestandardowego serwera DNS. Wszystkie żądania (nawet w przypadku publicznych adresów internetowych) są obsługiwane przez lokalny serwer DNS.

    * W przypadku sieci zdalnej inna sieć wirtualna Azure DNS należy skonfigurować następująco:

        * __Niestandardowe DNS__ (w każdej sieci wirtualnej):

            * Żądania dla sufiksu DNS, sieci wirtualnych są przekazywane do niestandardowych serwerów DNS. Serwer DNS w każdej sieci wirtualnej jest odpowiedzialny za rozpoznawania zasobów w sieci.

            * Przesyła wszystkich innych żądaniach Azure cyklicznego programu rozpoznawania nazw. Cyklicznego programu rozpoznawania nazw jest odpowiedzialny za rozwiązania lokalnego i zasobów w Internecie.

        Serwer DNS dla każdej sieci przekazuje żądania do drugiej strony, na podstawie sufiksu DNS. Inne żądania zostaną rozwiązane, za pomocą usługi Azure cyklicznego programu rozpoznawania nazw.

    Na przykład każdej konfiguracji zobacz [przykład: niestandardowe DNS](#example-dns) sekcji.

Aby uzyskać więcej informacji, zobacz [rozpoznawanie nazwy dla maszyn wirtualnych i wystąpień roli](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) dokumentu.

## <a name="directly-connect-to-hadoop-services"></a>Bezpośrednio z usługi Hadoop

Większość dokumentacji w usłudze HDInsight założono, że dostęp do klastra za pośrednictwem Internetu. Na przykład możesz połączyć się z klastrem pod adresem https://CLUSTERNAME.azurehdinsight.net. Ten adres używa publicznego brama nie jest dostępna w przypadku użycia grup NSG lub Udr ograniczyć dostęp z Internetu.

Aby połączyć się Ambari i stron sieci web za pośrednictwem sieci wirtualnej, użyj następujące czynności:

1. Aby odnaleźć wewnętrzny pełni kwalifikowanych nazw domen (FQDN) węzły klastra usługi HDInsight, użyj jednej z następujących metod:

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

    Na liście węzłów zwrócił znaleźć nazwę FQDN dla węzłów głównych i nawiązywania Ambari i innych usług sieci web przy użyciu nazw FQDN. Na przykład użyć `http://<headnode-fqdn>:8080` do narzędzia Ambari.

    > [!IMPORTANT]
    > Niektóre usługi hostowanej o węzłach głównych są tylko aktywne na jednym węźle naraz. Jeśli spróbujesz uzyskiwanie dostępu do usługi na jednym węźle głównym i zwraca błąd 404, przełącz się do innego węzła głównego.

2. Aby określić węzeł i port, który usługa jest dostępna na, zobacz [porty używane przez usługi Hadoop w usłudze HDInsight](./hdinsight-hadoop-port-settings-for-services.md) dokumentu.

## <a id="networktraffic"></a> Kontrolowanie ruchu w sieci

Ruch sieciowy w sieciach wirtualnych platformy Azure można sterować przy użyciu następujących metod:

* **Sieciowe grupy zabezpieczeń** (NSG) umożliwiają filtrowanie ruchu przychodzącego i wychodzącego do sieci. Aby uzyskać więcej informacji, zobacz [filtrowania ruchu sieciowego z grup zabezpieczeń sieci](../virtual-network/security-overview.md) dokumentu.

    > [!WARNING]
    > HDInsight nie obsługuje ograniczenia ruchu wychodzącego.

* **Trasy zdefiniowane przez użytkownika** (przez) zdefiniuj, jak przepływa ruch między zasobami w sieci. Aby uzyskać więcej informacji, zobacz [trasy zdefiniowane przez użytkownika i przesyłanie dalej IP](../virtual-network/virtual-networks-udr-overview.md) dokumentu.

* **Sieci wirtualnych urządzeń** replikować funkcjonalność urządzeń, takich jak routery i zapory. Aby uzyskać więcej informacji, zobacz [urządzenia sieciowe](https://azure.microsoft.com/solutions/network-appliances) dokumentu.

Jako zarządzanej usługi HDInsight wymaga nieograniczony dostęp do usług platformy Azure kondycji i zarządzania w chmurze Azure. Przy użyciu grup NSG i Udr, należy się upewnić, że HDInsight tych usług można nadal komunikować się z usługą HDInsight.

HDInsight udostępnia usługi na kilku portów. Używając urządzenie wirtualne zapory, musisz zezwolić na ruch na portach używanych na potrzeby tych usług. Aby uzyskać więcej informacji zobacz sekcję [wymagane porty].

### <a id="hdinsight-ip"></a> HDInsight z grup zabezpieczeń sieci i trasy zdefiniowane przez użytkownika

Jeśli planujesz używanie **sieciowej grupy zabezpieczeń** lub **trasy zdefiniowane przez użytkownika** do kontroli ruchu sieciowego, wykonaj następujące czynności przed zainstalowaniem usługi HDInsight:

1. Określ region platformy Azure, które będą używane dla usługi HDInsight.

2. Określ adresy IP wymagane przez usługi HDInsight. Aby uzyskać więcej informacji, zobacz [adresy IP wymagane przez HDInsight](#hdinsight-ip) sekcji.

3. Utwórz lub zmodyfikuj grupy zabezpieczeń sieci lub zdefiniowanej przez użytkownika tras dla podsieci, które zamierzasz zainstalować HDInsight do.

    * __Sieciowe grupy zabezpieczeń__: Zezwalaj na __przychodzących__ ruch na porcie __443__ z adresu IP, adresy.
    * __Trasy zdefiniowane przez użytkownika__: Utwórz trasę do każdego adresu IP i ustaw __następnego przeskoku typu__ do __Internet__.

Aby uzyskać więcej informacji na sieciowych grup zabezpieczeń lub trasy zdefiniowane przez użytkownika Zobacz następującą dokumentację:

* [Sieciowa grupa zabezpieczeń](../virtual-network/security-overview.md)

* [Trasy zdefiniowane przez użytkownika](../virtual-network/virtual-networks-udr-overview.md)

#### <a name="forced-tunneling"></a>Wymuszone tunelowanie

Wymuszanie tunelowania jest zdefiniowane przez użytkownika Konfiguracja routingu którym cały ruch z podsieci będzie zmuszony do określonej sieci lub lokalizacji, takiej jak sieć lokalną. HDInsight jest __nie__ obsługi wymuszonego tunelowania.

## <a id="hdinsight-ip"></a> Wymaganych adresów IP

> [!IMPORTANT]
> Usług kondycji oraz zarządzania platformy Azure musi mieć możliwość komunikacji z usługą HDInsight. Jeśli używasz grup zabezpieczeń sieci lub trasy zdefiniowane przez użytkownika, zezwalać na ruch z adresów IP dla tych usług do usługi HDInsight.
>
> Jeśli nie używasz grup zabezpieczeń sieci lub trasy zdefiniowane przez użytkownika do kontroli ruchu, można zignorować w tej sekcji.

Jeśli używasz grup zabezpieczeń sieci lub trasy zdefiniowane przez użytkownika, musisz zezwolić na ruch z usług kondycji i zarządzania Azure do usługi HDInsight. Wykonaj następujące kroki, aby znaleźć adresy IP, które może:

1. Zawsze musi zezwalać na ruch z następujących adresów IP:

    | Adres IP | Port dozwolone | Kierunek |
    | ---- | ----- | ----- |
    | 168.61.49.99 | 443 | Przychodzący |
    | 23.99.5.239 | 443 | Przychodzący |
    | 168.61.48.131 | 443 | Przychodzący |
    | 138.91.141.162 | 443 | Przychodzący |

2. W przypadku klastra HDInsight w jednym z następujących regionach, następnie należy zezwolić na ruch z wymienionych dla tego obszaru adresów IP:

    > [!IMPORTANT]
    > Jeśli nie ma region platformy Azure, którego używasz, następnie używać tylko cztery adresów IP z kroku 1.

    | Kraj | Region | Dozwolonych adresów IP | Port dozwolone | Kierunek |
    | ---- | ---- | ---- | ---- | ----- |
    | Azja | Azja Wschodnia | 23.102.235.122</br>52.175.38.134 | 443 | Przychodzący |
    | &nbsp; | Azja Południowo-Wschodnia | 13.76.245.160</br>13.76.136.249 | 443 | Przychodzący |
    | Australia | Australia Wschodnia | 104.210.84.115</br>13.75.152.195 | 443 | Przychodzący |
    | &nbsp; | Australia Południowo-Wschodnia | 13.77.2.56</br>13.77.2.94 | 443 | Przychodzący |
    | Brazylia | Brazylia Południowa | 191.235.84.104</br>191.235.87.113 | 443 | Przychodzący |
    | Kanada | Kanada Wschodnia | 52.229.127.96</br>52.229.123.172 | 443 | Przychodzący |
    | &nbsp; | Kanada Środkowa | 52.228.37.66</br>52.228.45.222 | 443 | Przychodzący |
    | Chiny | Chiny Północne | 42.159.96.170</br>139.217.2.219 | 443 | Przychodzący |
    | &nbsp; | Chiny Wschodnie | 42.159.198.178</br>42.159.234.157 | 443 | Przychodzący |
    | Europa | Europa Północna | 52.164.210.96</br>13.74.153.132 | 443 | Przychodzący |
    | &nbsp; | Europa Zachodnia| 52.166.243.90</br>52.174.36.244 | 443 | Przychodzący |
    | Niemcy | Niemcy Środkowe | 51.4.146.68</br>51.4.146.80 | 443 | Przychodzący |
    | &nbsp; | Niemcy Północno-Wschodnie | 51.5.150.132</br>51.5.144.101 | 443 | Przychodzący |
    | Indie | Indie Środkowe | 52.172.153.209</br>52.172.152.49 | 443 | Przychodzący |
    | Japonia | Japonia Wschodnia | 13.78.125.90</br>13.78.89.60 | 443 | Przychodzący |
    | &nbsp; | Japonia Zachodnia | 40.74.125.69</br>138.91.29.150 | 443 | Przychodzący |
    | Korea | Korea Środkowa | 52.231.39.142</br>52.231.36.209 | 433 | Przychodzący |
    | &nbsp; | Korea Południowa | 52.231.203.16</br>52.231.205.214 | 443 | Przychodzący
    | Wielka Brytania | Zachodnie Zjednoczone Królestwo | 51.141.13.110</br>51.141.7.20 | 443 | Przychodzący |
    | &nbsp; | Południowe Zjednoczone Królestwo | 51.140.47.39</br>51.140.52.16 | 443 | Przychodzący |
    | Stany Zjednoczone | Środkowe stany USA | 13.67.223.215</br>40.86.83.253 | 443 | Przychodzący |
    | &nbsp; | Wschodnie stany USA | 13.82.225.233</br>40.71.175.99 | 443 | Przychodzący |
    | &nbsp; | Środkowo-północne stany USA | 157.56.8.38</br>157.55.213.99 | 443 | Przychodzący |
    | &nbsp; | Środkowo-zachodnie stany USA | 52.161.23.15</br>52.161.10.167 | 443 | Przychodzący |
    | &nbsp; | Zachodnie stany USA | 13.64.254.98</br>23.101.196.19 | 443 | Przychodzący |
    | &nbsp; | Zachodnie stany USA 2 | 52.175.211.210</br>52.175.222.222 | 443 | Przychodzący |

    Aby uzyskać informacje dotyczące adresów IP do użycia na potrzeby Azure dla instytucji rządowych, zobacz [Azure dla instytucji rządowych analizy i analiza](https://docs.microsoft.com/azure/azure-government/documentation-government-services-intelligenceandanalytics) dokumentu.

3. Jeśli używasz niestandardowego serwera DNS z sieci wirtualnej, należy także zezwolić na dostęp z __168.63.129.16__. Ten adres jest platformy Azure, cyklicznego programu rozpoznawania nazw. Aby uzyskać więcej informacji, zobacz [rozpoznawanie nazw dla maszyn wirtualnych i roli wystąpień](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) dokumentu.

Aby uzyskać więcej informacji, zobacz [kontrolowanie ruchu w sieci](#networktraffic) sekcji.

## <a id="hdinsight-ports"></a> Wymagane porty

Jeśli planowane jest użycie sieci **urządzenie wirtualne zapory** do zabezpieczania sieci wirtualnej, musisz zezwolić na ruch wychodzący na następujące porty:

* 53
* 443
* 1433
* 11000-11999
* 14000-14999

Aby uzyskać listę portów do określonych usług, zobacz [porty używane przez usługi Hadoop w usłudze HDInsight](hdinsight-hadoop-port-settings-for-services.md) dokumentu.

Aby uzyskać więcej informacji na reguły zapory dla urządzenia wirtualnego, zobacz [scenariuszu urządzenie wirtualne](../virtual-network/virtual-network-scenario-udr-gw-nva.md) dokumentu.

## <a id="hdinsight-nsg"></a>Przykład: sieciowych grup zabezpieczeń z usługą HDInsight

Przykłady w tej sekcji przedstawiają sposób tworzenia zabezpieczeń sieci zasady grupy, zezwalających na HDInsight do komunikowania się z usługami zarządzania platformy Azure. Przed użyciem w przykładach, Dostosuj adresy IP, aby odpowiadały na region platformy Azure, którego używasz. Niniejsze informacje można znaleźć [HDInsight z grup zabezpieczeń sieci i trasy zdefiniowane przez użytkownika](#hdinsight-ip) sekcji.

### <a name="azure-resource-management-template"></a>Szablonu zarządzania zasobami platformy Azure

Następujący szablon zarządzanie zasobami tworzy sieć wirtualną, która ogranicza ruchu przychodzącego, ale zezwala na ruch sieciowy z adresów IP wymaganych przez usługi HDInsight. Ten szablon umożliwia również tworzenie klastra usługi HDInsight w sieci wirtualnej.

* [Wdrażanie zabezpieczonej sieci wirtualnej platformy Azure i klaster usługi HDInsight Hadoop](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

> [!IMPORTANT]
> Zmień adresy IP używane w tym przykładzie, aby dopasować region platformy Azure, którego używasz. Niniejsze informacje można znaleźć [HDInsight z grup zabezpieczeń sieci i trasy zdefiniowane przez użytkownika](#hdinsight-ip) sekcji.

### <a name="azure-powershell"></a>Azure PowerShell

Użyj następującego skryptu programu PowerShell można utworzyć sieci wirtualnej, która ogranicza ruchu przychodzącego i zezwala na ruch sieciowy z adresów IP dla regionu Europa Północna.

> [!IMPORTANT]
> Zmień adresy IP używane w tym przykładzie, aby dopasować region platformy Azure, którego używasz. Niniejsze informacje można znaleźć [HDInsight z grup zabezpieczeń sieci i trasy zdefiniowane przez użytkownika](#hdinsight-ip) sekcji.

```powershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"
# Get the Virtual Network object
$vnet = Get-AzureRmVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName
# Get the region the Virtual network is in.
$location = $vnet.Location
# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzureRmNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzureRmNetworkSecurityRuleConfig `
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
    | Add-AzureRmNetworkSecurityRuleConfig `
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
    | Add-AzureRmNetworkSecurityRuleConfig `
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
    | Add-AzureRmNetworkSecurityRuleConfig `
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
    | Add-AzureRmNetworkSecurityRuleConfig `
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
    | Add-AzureRmNetworkSecurityRuleConfig `
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
Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
# Apply the NSG to the subnet
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzureRmVirtualNetwork
```

> [!IMPORTANT]
> W tym przykładzie pokazano, jak dodać reguły zezwalające na ruch przychodzący na wymaganych adresów IP. Nie zawiera zasadę, aby ograniczyć dostęp dla ruchu przychodzącego z innych źródeł.
>
> W poniższym przykładzie pokazano, jak włączyć SSH dostępu z Internetu:
>
> ```powershell
> Add-AzureRmNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
> ```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Poniższe kroki umożliwiają utworzenie sieci wirtualnej, która ogranicza ruchu przychodzącego, ale zezwala na ruch sieciowy z adresów IP wymaganych przez usługi HDInsight.

1. Użyj następującego polecenia, aby utworzyć nową grupę zabezpieczeń sieci o nazwie `hdisecure`. Zastąp **RESOURCEGROUPNAME** z grupą zasobów, który zawiera sieci wirtualnej platformy Azure. Zastąp **lokalizacji** z grupa została utworzona w lokalizacji (regionu).

    ```azurecli
    az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION
    ```

    Po utworzeniu grupy, pojawi się informacji na temat nowej grupy.

2. Użyj następującego polecenia można dodać reguły do nowej grupy zabezpieczeń sieci Zezwalaj na komunikację ruchu przychodzącego na porcie 443 w usłudze Azure HDInsight kondycji i zarządzania. Zastąp **RESOURCEGROUPNAME** z nazwą grupy zasobów, który zawiera sieci wirtualnej platformy Azure.

    > [!IMPORTANT]
    > Zmień adresy IP używane w tym przykładzie, aby dopasować region platformy Azure, którego używasz. Niniejsze informacje można znaleźć [HDInsight z grup zabezpieczeń sieci i trasy zdefiniowane przez użytkownika](#hdinsight-ip) sekcji.

    ```azurecli
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. Aby pobrać Unikatowy identyfikator dla tej grupy zabezpieczeń sieci, użyj następującego polecenia:

    ```azurecli
    az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'
    ```

    To polecenie zwraca wartość podobny do następującego tekstu:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    Otrzymasz oczekiwanych rezultatów, użyj podwójnych cudzysłowów wokół identyfikatora w poleceniu.

4. Użyj następującego polecenia, aby zastosować sieciową grupę zabezpieczeń do podsieci. Zastąp __GUID__ i __RESOURCEGROUPNAME__ wartości z tymi zwracane z poprzedniego kroku. Zastąp __VNETNAME__ i __SUBNETNAME__ z wirtualną nazwą sieciową i nazwę podsieci, która ma zostać utworzona.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Po zakończeniu wykonywania tego polecenia, można zainstalować usługi HDInsight w sieci wirtualnej.

> [!IMPORTANT]
> Te kroki otwierać tylko dostęp do usługi HDInsight kondycji i zarządzania w chmurze Azure. Inne dostęp do klastra usługi HDInsight z spoza sieci wirtualnej jest zablokowany. Aby włączyć dostęp spoza sieci wirtualnej, należy dodać dodatkowych reguł sieciowej grupy zabezpieczeń.
>
> W poniższym przykładzie pokazano, jak włączyć SSH dostępu z Internetu:
>
> ```azurecli
> az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
> ```

## <a id="example-dns"></a> Przykład: Konfiguracji DNS

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Rozpoznawanie nazw między sieci wirtualnej i sieci połączonych lokalnie

W tym przykładzie powoduje, że następujące założenia:

* Masz sieci wirtualnej Azure, która jest połączona z siecią lokalną przy użyciu bramy sieci VPN.

* Niestandardowy serwer DNS w sieci wirtualnej działa jako systemu operacyjnego Linux lub Unix.

* [Powiąż](https://www.isc.org/downloads/bind/) jest zainstalowany na serwerze DNS niestandardowych.

Na niestandardowy serwer DNS w sieci wirtualnej:

1. Użyj programu Azure PowerShell lub interfejsu wiersza polecenia Azure, aby znaleźć sufiks DNS w sieci wirtualnej:

    ```powershell
    $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
    $NICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli-interactive
    read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
    az network nic list --resource-group $RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Na niestandardowy serwer DNS dla sieci wirtualnej, użyj następującego fragmentu jako zawartość `/etc/bind/named.conf.local` pliku:

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Zastąp `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` wartość sufiksu DNS, sieci wirtualnych.

    Ta konfiguracja kieruje wszystkie żądania DNS dla sufiksu DNS w sieci wirtualnej do platformy Azure cyklicznego programu rozpoznawania nazw.

2. Na niestandardowy serwer DNS dla sieci wirtualnej, użyj następującego fragmentu jako zawartość `/etc/bind/named.conf.options` pliku:

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
    
    * Zastąp `10.0.0.0/16` wartość z zakresu adresów IP sieci wirtualnej. Ten wpis umożliwia adresy żądań rozpoznawania nazw w tym zakresie.

    * Dodaj zakres adresów IP w sieci lokalnej do `acl goodclients { ... }` sekcji.  Wpis umożliwia żądań rozpoznawania nazw z zasobów w sieci lokalnej.
    
    * Zastąp wartość `192.168.0.1` przy użyciu adresu IP serwera DNS lokalnie. Ten wpis kieruje wszystkich innych żądaniach DNS do lokalnego serwera DNS.

3. Do korzystania z konfiguracji, uruchom ponownie powiązania. Na przykład `sudo service bind9 restart`.

4. Dodaj warunkowego przesyłania dalej do lokalnego serwera DNS. Konfigurowanie warunkowego przesyłania dalej do wysyłania żądań dla sufiksu DNS z kroku 1 do niestandardowego serwera DNS.

    > [!NOTE]
    > W dokumentacji oprogramowania DNS dla szczegółowych informacji na temat dodawania warunkowego przesyłania dalej.

Po wykonaniu tych czynności można połączyć się z zasobami w sieci, albo przy użyciu w pełni kwalifikowanych nazw domen (FQDN). Teraz można zainstalować usługi HDInsight w sieci wirtualnej.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Rozpoznawanie nazw między dwiema połączone sieci wirtualne

W tym przykładzie powoduje, że następujące założenia:

* Masz dwie sieci wirtualnych Azure, które są połączone przy użyciu bramy sieci VPN lub komunikacji równorzędnej.

* Niestandardowy serwer DNS w obu sieci działa jako systemu operacyjnego Linux lub Unix.

* [Powiąż](https://www.isc.org/downloads/bind/) jest zainstalowany na niestandardowych serwerów DNS.

1. Użyj programu Azure PowerShell lub interfejsu wiersza polecenia Azure, aby znaleźć sufiks DNS obie sieci wirtualne:

    ```powershell
    $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
    $NICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli-interactive
    read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
    az network nic list --resource-group $RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Skorzystaj z poniższego tekstu jako zawartość `/etc/bind/named.config.local` pliku na niestandardowy serwer DNS. To zrobić na serwerze DNS niestandardowych w obu sieciach wirtualnych.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Zastąp `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` wartości z sufiksem DNS __innych__ sieci wirtualnej. Ten wpis kieruje żądania dla sufiksu DNS w sieci zdalnej niestandardowe DNS w sieci.

3. W niestandardowych serwerów DNS w sieci zarówno wirtualnych, użyj następującego fragmentu jako zawartość `/etc/bind/named.conf.options` pliku:

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
    
    * Zastąp `10.0.0.0/16` i `10.1.0.0/16` wartości IP odnoszą się do zakresów sieci wirtualne. Ten wpis umożliwia zasobów w każdej sieci na wysyłanie żądań do serwerów DNS.

    Wszystkie żądania, które nie są dostępne dla sufiksów DNS, sieci wirtualnych (na przykład microsoft.com) jest obsługiwane przez Azure cyklicznego programu rozpoznawania nazw.

4. Do korzystania z konfiguracji, uruchom ponownie powiązania. Na przykład `sudo service bind9 restart` na obu serwerach DNS.

Po wykonaniu tych czynności można połączyć się z zasobami w sieci wirtualnej przy użyciu w pełni kwalifikowanych nazw domen (FQDN). Teraz można zainstalować usługi HDInsight w sieci wirtualnej.

## <a name="next-steps"></a>Kolejne kroki

* Na przykład na trasie konfigurowania usługi HDInsight można nawiązać połączenia z siecią lokalną, zobacz [HDInsight połączenia z siecią lokalną](./connect-on-premises-network.md).
* Podczas konfigurowania klastrów Hbase w sieci wirtualnych platformy Azure, zobacz [HBase Tworzenie klastrów HDInsight w sieci wirtualnej Azure](hbase/apache-hbase-provision-vnet.md).
* Do konfigurowania replikacja geograficzna HBase, zobacz [replikacji klastra HBase w sieciach wirtualnych platformy Azure](hbase/apache-hbase-replication.md).
* Aby uzyskać więcej informacji o sieci wirtualnych platformy Azure, zobacz [Omówienie usługi Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

* Aby uzyskać więcej informacji dotyczących grup zabezpieczeń sieci, zobacz [sieciowej grupy zabezpieczeń](../virtual-network/security-overview.md).

* Aby uzyskać więcej informacji dotyczących tras zdefiniowanych przez użytkownika, zobacz [trasy zdefiniowane przez użytkownika i przesyłanie dalej IP](../virtual-network/virtual-networks-udr-overview.md).
