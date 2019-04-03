---
title: Przegląd grup zabezpieczeń platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się więcej na temat grup zabezpieczeń sieci i aplikacji. Grupy zabezpieczeń pomagają filtrować ruch sieciowy między zasobami platformy Azure.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2018
ms.author: malop;kumud
ms.openlocfilehash: 9d45f3a7d20545ac7b66d27505078f21f06adddc
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58881855"
---
# <a name="security-groups"></a>Grupy zabezpieczeń
<a name="network-security-groups"></a>

Ruch sieciowy przychodzący do zasobów platformy Azure i wychodzący z nich w [sieci wirtualnej](virtual-networks-overview.md) platformy Azure można filtrować za pomocą grupy zabezpieczeń sieci. Grupa zabezpieczeń sieci zawiera [reguły zabezpieczeń](#security-rules), które zezwalają na lub blokują przychodzący ruch sieciowy lub wychodzący ruch sieciowy dla kilku typów zasobów platformy Azure. Aby dowiedzieć się więcej o zasobach platformy Azure, które można wdrożyć w sieci wirtualnej i skojarzyć z nimi grupy zabezpieczeń sieci, zobacz [Integracja z siecią wirtualną dla usług platformy Azure](virtual-network-for-azure-services.md). Dla każdej reguły można określić źródło i obiekt docelowy, port i protokół.

W tym artykule pojęcia dotyczące grup zabezpieczeń sieci, aby ułatwić efektywne korzystanie z nich. Jeśli nie masz doświadczenia w tworzeniu grup zabezpieczeń sieci, możesz ukończyć szybki [samouczek](tutorial-filter-network-traffic.md), aby zyskać pewne doświadczenie w tym zakresie. Jeśli znasz grupy zabezpieczeń sieci i chcesz nimi zarządzać, zobacz [Manage a network security group](manage-network-security-group.md) (Zarządzanie sieciową grupą zabezpieczeń). Jeśli występują problemy z komunikacją i musisz rozwiązać problemy z grupami zabezpieczeń sieci, zobacz [Diagnozowanie problemu z filtrowaniem ruchu sieciowego maszyny wirtualnej](diagnose-network-traffic-filter-problem.md). Możesz włączyć [dzienniki przepływu grupy zabezpieczeń sieci](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) w celu [analizowania ruchu sieciowego](../network-watcher/traffic-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do i z zasobów, z którymi skojarzono grupę zabezpieczeń sieci.

## <a name="security-rules"></a>Reguły zabezpieczeń

Grupa zabezpieczeń sieci nie zawiera żadnych reguł lub dowolną liczbę reguł zgodnie z potrzebami, w ramach [limitów](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) subskrypcji platformy Azure. Każda reguła określa następujące właściwości:

|Właściwość  |Wyjaśnienie  |
|---------|---------|
|Name (Nazwa)|Unikatowa nazwa w obrębie grupy zabezpieczeń sieci.|
|Priorytet | Liczba z zakresu od 100 do 4096. Reguły są przetwarzane w kolejności priorytetów. Im niższy numer, tym wyższy priorytet, więc te o niższych numerach są przetwarzane przed tymi o wyższych numerach. Kiedy ruch jest zgodny z regułą, przetwarzanie zostaje zatrzymane. W związku z tym żadne istniejące reguły o niższych priorytetach (wyższych numerach), które mają takie same atrybuty jak reguły o wyższych priorytetach, nie będą przetwarzane.|
|Obiekt źródłowy lub docelowy| Dowolny lub indywidualny adres IP, blok CIDR (na przykład 10.0.0.0/24), [tag usługi](#service-tags) lub [grupa zabezpieczeń aplikacji](#application-security-groups). W przypadku określenia adresu dla zasobu platformy Azure należy określić prywatny adres IP przypisany do zasobu. W przypadku ruchu przychodzącego grupy zabezpieczeń sieci są przetwarzane po tym, jak platforma Azure przetłumaczy publiczny adres IP na prywatny adres IP, a w przypadku ruchu wychodzącego — zanim platforma Azure przetłumaczy prywatny adres IP na publiczny adres IP. Dowiedz się więcej o [adresach IP](virtual-network-ip-addresses-overview-arm.md) platformy Azure. Określenie zakresu, tagu usługi lub grupy zabezpieczeń aplikacji umożliwia utworzenie mniejszej liczby reguł zabezpieczeń. Możliwość określenia wielu poszczególnych adresów IP i zakresów (nie można określić wielu tagów usługi ani grup aplikacji) w regule nosi nazwę [rozszerzonych reguł zabezpieczeń](#augmented-security-rules). Rozszerzone reguły zabezpieczeń można tworzyć tylko w grupach zabezpieczeń sieci utworzonych za pośrednictwem modelu wdrażania przy użyciu usługi Resource Manager. Nie można określić wielu adresów IP i zakresów adresów IP w grupach zabezpieczeń sieci utworzonych za pomocą klasycznego modelu wdrażania. Dowiedz się więcej o [modelach wdrażania platformy Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
|Protokół     | TCP, UDP lub dowolny, w tym (między innymi) TCP, UDP i ICMP. Nie można określić samego protokołu ICMP, a więc jeśli potrzebujesz protokołu ICMP, użyj opcji Dowolny. |
|Kierunek| Określa, czy ta reguła ma zastosowanie do ruchu przychodzącego, czy wychodzącego.|
|Zakres portów     |Można określić pojedynczy port lub zakres portów. Na przykład można określić port 80 lub 10000–10005. Określenie zakresów umożliwia utworzenie mniejszej liczby reguł zabezpieczeń. Rozszerzone reguły zabezpieczeń można tworzyć tylko w grupach zabezpieczeń sieci utworzonych za pośrednictwem modelu wdrażania przy użyciu usługi Resource Manager. Nie można określić wielu portów lub zakresów portów w grupach zabezpieczeń sieci utworzonych za pomocą klasycznego modelu wdrażania.   |
|Akcja     | Zezwolenie lub zablokowanie        |

Reguły zabezpieczeń grupy zabezpieczeń sieci są oceniane według priorytetu na podstawie krotki składającej się z pięciu informacji (źródło, port źródłowy, obiekt docelowy, port docelowy i protokół) w celu zezwolenia na ruch lub zablokowania go. Rekord przepływu tworzony jest dla istniejących połączeń. Komunikacja jest dozwolona lub zablokowana na podstawie stanu połączenia z rekordu przepływu. Dzięki rekordowi przepływu grupa zabezpieczeń sieci jest stanowa. Jeśli zostanie określona reguła zabezpieczeń dla ruchu wychodzącego do dowolnego adresu za pośrednictwem (na przykład) portu 80, nie trzeba określać żadnej reguły zabezpieczeń ruchu przychodzącego dla odpowiedzi na ruch wychodzący. Należy tylko określić regułę zabezpieczeń dla ruchu przychodzącego w przypadku, jeśli komunikacja jest inicjowana zewnętrznie. Jest to również prawdziwe w odwrotnym przypadku. Jeśli ruch przychodzący jest dozwolony przez port, nie trzeba określać reguły zabezpieczeń dla ruchu wychodzącego, aby odpowiadać na ruch przychodzący przez port.
Istniejące połączenia mogą nie zostać przerwane po usunięciu reguły zabezpieczeń, która zezwoliła na przepływ. Przepływy ruchu są przerywane po zakończeniu połączenia, gdy przez co najmniej kilka minut nie ma ruchu z żadnej strony.

Istnieją ograniczenia dotyczące liczby reguł zabezpieczeń, które można utworzyć w grupie zabezpieczeń sieci. Aby uzyskać więcej informacji, zobacz [Azure limits (Ograniczenia platformy Azure)](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="augmented-security-rules"></a>Rozszerzone reguły zabezpieczeń

Rozszerzone reguły zabezpieczeń upraszczają definicję zabezpieczeń dla sieci wirtualnych, umożliwiając definiowanie zasad zabezpieczeń większych i złożonych sieci przy użyciu mniejszej liczby reguł. Można połączyć wiele portów, wiele jawnych adresów IP i zakresów w jedną, łatwo zrozumiałą regułę zabezpieczeń. Rozszerzone reguły stosuje się w polach źródła, obiektu docelowego i portów reguły. Aby uprościć zarządzanie definicją reguły zabezpieczeń, połącz rozszerzone reguły zabezpieczeń z [tagami usług](#service-tags) lub [grupami zabezpieczeń aplikacji](#application-security-groups). Istnieją limity liczby adresów, zakresy adresów i portów, które można określić w regule. Aby uzyskać więcej informacji, zobacz [Azure limits (Ograniczenia platformy Azure)](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="service-tags"></a>Tagi usługi

 Tag usługi reprezentuje grupę prefiksów adresów IP, aby zminimalizować złożoność tworzenia reguły zabezpieczeń. Nie można utworzyć własnego tagu usługi ani określić adresów IP uwzględnionych w tagu. Firma Microsoft zarządza prefiksami adresów obejmowanymi przez tag usługi i automatycznie aktualizuje tag usługi, gdy adresy ulegną zmianie. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. 
 
 Można pobrać i zintegrować z lokalną zaporą listę tagów usługi ze szczegółami prefiksów znajdującą się w publikacjach tygodniowych dotyczących następujących chmur platformy Azure: [Publiczna](https://www.microsoft.com/download/details.aspx?id=56519), [Rząd USA](https://www.microsoft.com/download/details.aspx?id=57063), [Chiny](https://www.microsoft.com/download/details.aspx?id=57062) i [Niemcy](https://www.microsoft.com/download/details.aspx?id=57064).

 Poniżej wymienione tagi usługi są dostępne do użycia w definicji reguły zabezpieczeń. Ich nazwy różnią się nieco między sobą w [modelach wdrażania platformy Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

* **VirtualNetwork** (Resource Manager) (**VIRTUAL_NETWORK** — model klasyczny): ten tag obejmuje przestrzeń adresową sieci wirtualnej (wszystkie zakresy CIDR zdefiniowane dla sieci wirtualnej), wszystkie połączone lokalne przestrzenie adresowe oraz [skomunikowane równorzędnie](virtual-network-peering-overview.md) sieci wirtualne lub sieć wirtualną połączoną z [bramą sieci wirtualnej](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* **AzureLoadBalancer** (Resource Manager) (**AZURE_LOADBALANCER** — model klasyczny): ten tag moduł równoważenia obciążenia infrastruktury platformy Azure. Ten znacznik przekłada się na [wirtualny adres IP hosta](security-overview.md#azure-platform-considerations) (168.63.129.16), z którego pochodzą sondy kondycji platformy Azure. Jeśli nie jest używana usługa Azure Load Balancer, tę zasadę można przesłonić.
* **Internet** (Resource Manager) (**INTERNET** — model klasyczny): określa przestrzeń adresów IP, która znajduje się poza siecią wirtualną i do której można uzyskać dostęp w publicznym Internecie. Ten zakres adresów obejmuje [publiczną przestrzeń adresów IP należącą do platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653).
* **AzureCloud** (tylko usługa Resource Manager): ten tag określa przestrzeń adresów IP dla platformy Azure, w tym wszystkie [publiczne adresy IP centrum danych](https://www.microsoft.com/download/details.aspx?id=41653). W przypadku określenia wartości *AzureCloud* dozwolony lub blokowany jest ruch do publicznych adresów IP platformy Azure. Jeśli chcesz zezwolić na dostęp do usługi AzureCloud w konkretnym [regionie](https://azure.microsoft.com/regions), możesz określić region. Jeśli na przykład chcesz zezwolić na dostęp do usługi AzureCloud platformy Azure tylko w regionie Wschodnie stany USA, możesz określić *AzureCloud.EastUS* jako tag usługi. 
* **AzureTrafficManager** (tylko usługa Resource Manager): ten tag określa przestrzeń adresów IP dla adresów IP sondy usługi Azure Traffic Manager. Więcej informacji na temat adresów IP sondy usługi Traffic Manager można znaleźć w temacie [Usługa Azure Traffic Manager — często zadawane pytania](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). 
* **Storage** (tylko usługa Resource Manager): ten tag określa przestrzeń adresów IP dla usługi Azure Storage. W przypadku określenia wartości *Storage* dozwolony lub blokowany jest ruch do usługi Storage. Jeśli chcesz zezwolić na dostęp do usługi Storage w konkretnym [regionie](https://azure.microsoft.com/regions), możesz określić region. Jeśli na przykład chcesz zezwolić na dostęp do usługi Azure Storage tylko w regionie Wschodnie stany USA, możesz określić *Storage.EastUS* jako tag usługi. Tag reprezentuje usługę, ale nie konkretne wystąpienia usługi. Na przykład tag reprezentuje usługę Azure Storage, ale nie konkretne konto usługi Azure Storage. Wszystkie prefiksy adresów reprezentowane przez ten tag są również reprezentowane przez tag **Internet**. 
* **SQL** (tylko usługa Resource Manager): Ten tag określa prefiksy adresów usługi Azure SQL Database, Azure Database for MySQL, — Azure Database for PostgreSQL i usługi Azure SQL Data Warehouse. W przypadku określenia wartości *Sql* dozwolony lub blokowany jest ruch do usługi Sql. Jeśli chcesz zezwolić na dostęp do usługi Sql w konkretnym [regionie](https://azure.microsoft.com/regions), możesz określić region. Jeśli na przykład chcesz zezwolić na dostęp do usługi Azure SQL Database tylko w regionie Wschodnie stany USA, możesz określić *Sql.EastUS* jako tag usługi. Tag reprezentuje usługę, ale nie konkretne wystąpienia usługi. Na przykład tag reprezentuje usługę Azure SQL Database, ale nie konkretną bazę danych lub serwer SQL. Wszystkie prefiksy adresów reprezentowane przez ten tag są również reprezentowane przez tag **Internet**. 
* **AzureCosmosDB** (tylko usługa Resource Manager): ten tag określa prefiksy adresów usługi Azure Cosmos Database. W przypadku określenia wartości *AzureCosmosDB* dozwolony lub blokowany jest ruch do usługi AzureCosmosDB. Jeśli chcesz zezwolić na dostęp do usługi AzureCosmosDB w wybranym [regionie](https://azure.microsoft.com/regions), możesz określić ten region za pomocą następującego formatu: AzureCosmosDB.[nazwa regionu]. 
* **AzureKeyVault** (tylko usługa Resource Manager): ten tag określa prefiksy adresów usługi platformy Azure KeyVault. W przypadku określenia wartości *AzureKeyVault* dozwolony lub blokowany jest ruch do usługi AzureKeyVault. Jeśli chcesz zezwolić na dostęp do usługi AzureKeyVault w wybranym [regionie](https://azure.microsoft.com/regions), możesz określić ten region za pomocą następującego formatu: AzureKeyVault.[nazwa regionu]. 
* **EventHub** (tylko usługa Resource Manager): ten tag określa prefiksy adresów usługi Azure EventHub. W przypadku określenia wartości *EventHub* dozwolony lub blokowany jest ruch do usługi EventHub. Jeśli chcesz zezwolić na dostęp do usługi EventHub w wybranym [regionie](https://azure.microsoft.com/regions), możesz określić ten region za pomocą następującego formatu: EventHub.[nazwa regionu]. 
* **ServiceBus** (tylko usługa Resource Manager): Ten tag określa prefiksy adresów usługi platformy Azure, magistrali usług przy użyciu warstwy Premium. W przypadku określenia wartości *ServiceBus* dozwolony lub blokowany jest ruch do usługi ServiceBus. Jeśli chcesz zezwolić na dostęp do usługi ServiceBus w wybranym [regionie](https://azure.microsoft.com/regions), możesz określić ten region za pomocą następującego formatu: ServiceBus.[nazwa regionu]. 
* **MicrosoftContainerRegistry** (tylko usługa Resource Manager): ten tag określa prefiksy adresów usługi Microsoft Container Registry. W przypadku określenia wartości *MicrosoftContainerRegistry* dozwolony lub blokowany jest ruch do usługi MicrosoftContainerRegistry. Jeśli chcesz zezwolić na dostęp do usługi MicrosoftContainerRegistry w wybranym [regionie](https://azure.microsoft.com/regions), możesz określić ten region za pomocą następującego formatu: MicrosoftContainerRegistry.[nazwa regionu]. 
* **AzureContainerRegistry** (tylko usługa Resource Manager): ten tag określa prefiksy adresów usługi Azure Container Registry. W przypadku określenia wartości *AzureContainerRegistry* dozwolony lub blokowany jest ruch do usługi AzureContainerRegistry. Jeśli chcesz zezwolić na dostęp do usługi AzureContainerRegistry w wybranym [regionie](https://azure.microsoft.com/regions), możesz określić ten region za pomocą następującego formatu: AzureContainerRegistry.[nazwa regionu]. 
* **AppService** (tylko usługa Resource Manager): ten tag określa prefiksy adresów usługi Azure AppService. W przypadku określenia wartości *AppService* dozwolony lub blokowany jest ruch do usługi AppService. Jeśli chcesz zezwolić na dostęp do usługi AppService w wybranym [regionie](https://azure.microsoft.com/regions), możesz określić ten region za pomocą następującego formatu: AppService.[nazwa regionu]. 
* **AppServiceManagement** (tylko usługa Resource Manager): ten tag określa prefiksy adresów usługi Azure AppService Management. W przypadku określenia wartości *AppServiceManagement* dozwolony lub blokowany jest ruch do usługi AppServiceManagement. 
* **ApiManagement** (tylko usługa Resource Manager): ten tag określa prefiksy adresów usługi Azure Api Management. W przypadku określenia wartości *ApiManagement* dozwolony lub blokowany jest ruch do usługi ApiManagement.  
* **AzureConnectors** (tylko usługa Resource Manager): ten tag określa prefiksy adresów usługi Azure Connectors. W przypadku określenia wartości *AzureConnectors* dozwolony lub blokowany jest ruch do usługi AzureConnectors. Jeśli chcesz zezwolić na dostęp do usługi AzureConnectors w wybranym [regionie](https://azure.microsoft.com/regions), możesz określić ten region za pomocą następującego formatu: AzureConnectors.[nazwa regionu]. 
* **GatewayManager** (tylko usługa Resource Manager): ten tag określa prefiksy adresów usługi Azure Gateway Manager. W przypadku określenia wartości *GatewayManager* dozwolony lub blokowany jest ruch do usługi GatewayManager.  
* **AzureDataLake** (tylko usługa Resource Manager): ten tag określa prefiksy adresów usługi Azure Data Lake. W przypadku określenia wartości *AzureDataLake* dozwolony lub blokowany jest ruch do usługi AzureDataLake. 
* **AzureActiveDirectory** (tylko usługa Resource Manager): ten tag określa prefiksy adresów usługi AzureActiveDirectory. W przypadku określenia wartości *AzureActiveDirectory* dozwolony lub blokowany jest ruch do usługi AzureActiveDirectory.  
* **AzureMonitor** (tylko model Resource Manager): Ten tag określa prefiksy adresów usługi AzureMonitor. Jeśli określisz *AzureMonitor* wartości, ruch jest dozwolony lub zablokowany do AzureMonitor. 
* **ServiceFabric** (tylko model Resource Manager): Ten tag określa prefiksy adresów usługi ServiceFabric. Jeśli określisz *ServiceFabric* wartości, ruch jest dozwolony lub zablokowany do ServiceFabric. 
* **AzureMachineLearning** (tylko model Resource Manager): Ten tag określa prefiksy adresów usługi AzureMachineLearning. Jeśli określisz *AzureMachineLearning* wartości, ruch jest dozwolony lub zablokowany do AzureMachineLearning. 

> [!NOTE]
> Tagi usług platformy Azure określają prefiksy adresów określonych chmur, które są używane. 

> [!NOTE]
> W przypadku zaimplementowania [punktu końcowego usługi sieci wirtualnej](virtual-network-service-endpoints-overview.md) dla usługi takiej jak usługa Azure Storage lub Azure SQL Database, platforma Azure dodaje [trasę](virtual-networks-udr-overview.md#optional-default-routes) do podsieci sieci wirtualnej dla usługi. Prefiksy adresów dla trasy to te same prefiksy adresów lub zakresy CIDR, co w odpowiednim tagu usługi.

## <a name="default-security-rules"></a>Domyślne reguły zabezpieczeń

Platforma Azure tworzy następujące reguły domyślne w każdej tworzonej grupie zabezpieczeń sieci:

### <a name="inbound"></a>Przychodzący

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|Priorytet|Element źródłowy|Porty źródłowe|Element docelowy|Porty docelowe|Protokół|Dostęp|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Wszyscy|Zezwalaj|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Priorytet|Element źródłowy|Porty źródłowe|Element docelowy|Porty docelowe|Protokół|Dostęp|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Wszyscy|Zezwalaj|

#### <a name="denyallinbound"></a>DenyAllInbound

|Priorytet|Element źródłowy|Porty źródłowe|Element docelowy|Porty docelowe|Protokół|Dostęp|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Wszyscy|Zablokuj|

### <a name="outbound"></a>Wychodzący

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Priorytet|Element źródłowy|Porty źródłowe| Element docelowy | Porty docelowe | Protokół | Dostęp |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Wszyscy | Zezwalaj |

#### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Priorytet|Element źródłowy|Porty źródłowe| Element docelowy | Porty docelowe | Protokół | Dostęp |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Wszyscy | Zezwalaj |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|Priorytet|Element źródłowy|Porty źródłowe| Element docelowy | Porty docelowe | Protokół | Dostęp |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Wszyscy | Zablokuj |

W kolumnach **Źródło** i **Obiekt docelowy** elementy *VirtualNetwork*, *AzureLoadBalancer* i *Internet* są [tagami usługi](#service-tags), a nie adresami IP. W kolumnie protokołu pozycja **Wszystkie** obejmuje protokoły TCP, UDP i ICMP. Podczas tworzenia reguły można określić protokół TCP, UDP lub Wszystkie, ale nie można określić wyłącznie protokołu ICMP. W związku z tym jeśli reguła wymaga protokołu ICMP, wybierz wartość *Wszystkie* w kolumnie protokołu. Wartość *0.0.0.0/0* w kolumnach **Źródło** i **Obiekt docelowy** reprezentuje wszystkie adresy. Klientów, takich jak witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub za pomocą programu Powershell * lub dowolny dla tego wyrażenia.
 
Nie można usunąć reguł domyślnych, ale można je przesłonić, tworząc reguły o wyższych priorytetach.

## <a name="application-security-groups"></a>Grupy zabezpieczeń aplikacji

Grupy zabezpieczeń aplikacji umożliwiają skonfigurowanie zabezpieczeń sieci jako naturalnego rozszerzenia struktury aplikacji, co pozwala na grupowanie maszyn wirtualnych i definiowanie zasad zabezpieczeń sieci na podstawie tych grup. Możesz ponownie używać zasad zabezpieczeń na dużą skalę bez ręcznej obsługi jawnych adresów IP. Platforma obsługuje złożoność jawnych adresów IP i wiele zestawów reguł, co pozwala skupić się na logice biznesowej. Aby lepiej zrozumieć grupy zabezpieczeń aplikacji, rozważmy następujący przykład:

![Grupy zabezpieczeń aplikacji](./media/security-groups/application-security-groups.png)

Na poprzedniej ilustracji interfejsy sieciowe *NIC1* i *NIC2* są elementami członkowskimi grupy zabezpieczeń aplikacji *AsgWeb*. Interfejs sieciowy *NIC3* jest elementem członkowskim grupy zabezpieczeń aplikacji *AsgLogic*. Interfejs sieciowy *NIC4* jest elementem członkowskim grupy zabezpieczeń aplikacji *AsgDb*. Chociaż każdy interfejs sieciowy w tym przykładzie jest elementem członkowskim tylko jednej grupy zabezpieczeń aplikacji, interfejs sieciowy może być elementem członkowskim wielu grup zabezpieczeń aplikacji, w granicach [limitów platformy Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Żaden z interfejsów sieciowych nie ma skojarzonej grupy zabezpieczeń sieci. Grupa *NSG1* jest skojarzona z obiema podsieciami i zawiera następujące reguły:

### <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

Ta reguła jest potrzebna w celu zezwolenia na ruch z Internetu do serwerów internetowych. Ponieważ ruch przychodzący z Internetu jest blokowany przez domyślną regułę zabezpieczeń [DenyAllInbound](#denyallinbound), dodatkowa reguła nie jest potrzebna w przypadku grup zabezpieczeń aplikacji *AsgLogic* i *AsgDb*.

|Priorytet|Element źródłowy|Porty źródłowe| Element docelowy | Porty docelowe | Protokół | Dostęp |
|---|---|---|---|---|---|---|
| 100 | Internet | * | AsgWeb | 80 | TCP | Zezwalaj |

### <a name="deny-database-all"></a>Deny-Database-All

Ponieważ domyślna reguła zabezpieczeń [AllowVNetInBound](#allowvnetinbound) zezwala na całą komunikację między zasobami w tej samej sieci wirtualnej, ta zasada jest potrzebna w celu blokowania ruchu ze wszystkich zasobów.

|Priorytet|Element źródłowy|Porty źródłowe| Element docelowy | Porty docelowe | Protokół | Dostęp |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | Wszyscy | Zablokuj |

### <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

Ta reguła zezwala na ruch z grupy zabezpieczeń aplikacji *AsgLogic* do grupy zabezpieczeń aplikacji *AsgDb*. Priorytet tej reguły jest wyższy niż priorytet reguły *Deny-Database-All*. W rezultacie ta reguła jest przetwarzana przed regułą *Deny-Database-All*, a więc ruch z grupy zabezpieczeń aplikacji *AsgLogic* jest dozwolony, natomiast cały pozostały ruch jest blokowany.

|Priorytet|Element źródłowy|Porty źródłowe| Element docelowy | Porty docelowe | Protokół | Dostęp |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | Zezwalaj |

Reguły określające grupę zabezpieczeń aplikacji jako źródło lub obiekt docelowy są stosowane tylko do interfejsów sieciowych, które są elementami członkowskimi grupy zabezpieczeń aplikacji. Jeśli interfejs sieciowy nie jest elementem członkowskim grupy zabezpieczeń aplikacji, reguła nie jest stosowana do tego interfejsu sieciowego, mimo że grupa zabezpieczeń sieci jest skojarzona z podsiecią.

Grupy zabezpieczeń aplikacji mają następujące ograniczenia:

-   Istnieją limity liczby grup zabezpieczeń aplikacji, które możesz mieć w ramach subskrypcji, a także inne ograniczenia dotyczące grup zabezpieczeń aplikacji. Aby uzyskać więcej informacji, zobacz [Azure limits (Ograniczenia platformy Azure)](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Jako źródło i obiekt docelowy reguły zabezpieczeń można określić pojedynczą grupę zabezpieczeń aplikacji. Jako źródła lub obiektu docelowego nie można określić kilku grup zabezpieczeń aplikacji.
- Wszystkie interfejsy sieciowe przypisane do grupy zabezpieczeń aplikacji muszą istnieć w tej samej sieci wirtualnej, co pierwszy interfejs sieciowy przypisany do danej grupy zabezpieczeń aplikacji. Na przykład jeśli pierwszy interfejs sieciowy przypisany do grupy zabezpieczeń aplikacji o nazwie *AsgWeb* istnieje w sieci wirtualnej o nazwie *VNet1*, wszystkie kolejne interfejsy sieciowe przypisane do grupy *AsgWeb* muszą istnieć w sieci *VNet1*. Interfejsy sieciowe z różnych sieci wirtualnych nie mogą być dodawane do tej samej grupy zabezpieczeń aplikacji.
- Jeśli określisz grupy zabezpieczeń aplikacji jako źródło i obiekt docelowy w regule zabezpieczeń, interfejsy sieciowe w obu grupach zabezpieczeń aplikacji muszą istnieć w tej samej sieci wirtualnej. Na przykład jeśli grupa *AsgLogic* zawiera interfejsy sieciowe z sieci *VNet1*, a grupa *AsgDb* zawiera interfejsy sieciowe z sieci *VNet2*, nie można przypisać grupy *AsgLogic* jako źródła i grupy *AsgDb* jako obiektu docelowego w regule. Wszystkie interfejsy sieciowe dla źródłowych i docelowych grup zabezpieczeń aplikacji muszą istnieć w tej samej sieci wirtualnej.

> [!TIP]
> Jeśli to możliwe, aby zminimalizować liczbę niezbędnych reguł zabezpieczeń i konieczność zmiany reguł, zaplanuj potrzebne grupy zabezpieczeń aplikacji i utwórz reguły przy użyciu tagów usług lub grup zabezpieczeń aplikacji, a nie pojedynczych adresów IP lub zakresów adresów IP.

## <a name="how-traffic-is-evaluated"></a>Sposób oceniania ruchu

W sieci wirtualnej platformy Azure można wdrożyć zasoby z kilku usług platformy Azure. Aby uzyskać pełną listę, zobacz [Services that can be deployed into a virtual network](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (Usługi, które można wdrożyć w sieci wirtualnej). Z każdą siecią [podsiecią](virtual-network-manage-subnet.md#change-subnet-settings) i [interfejsem sieciowym](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) sieci wirtualnej na maszynie wirtualnej możesz skojarzyć zero lub jedną grupę zabezpieczeń sieci. Tę samą grupę zabezpieczeń sieci można zastosować do dowolnej liczby interfejsów sieciowych i podsieci.

Poniższa ilustracja przedstawia różne scenariusze dotyczące sposobu wdrażania grup zabezpieczeń sieci w celu zezwolenia na ruch sieciowy do i z Internetu przez port TCP 80:

![Sieciowe grupy zabezpieczeń — przetwarzanie](./media/security-groups/nsg-interaction.png)

Odwołaj się do poprzedniej ilustracji i następującego tekstu, aby zrozumieć, w jaki sposób platforma Azure przetwarza reguły ruchu przychodzącego i wychodzącego dla grup zabezpieczeń sieci:

### <a name="inbound-traffic"></a>Ruch przychodzący

W przypadku ruchu przychodzącego platforma Azure przetwarza najpierw reguły w grupie zabezpieczeń sieci skojarzonej z podsiecią, jeśli taka istnieje, a następnie reguły w grupie zabezpieczeń sieci skojarzonej z interfejsem sieciowym, jeśli taka istnieje.

- **VM1**: przetwarzane są reguły zabezpieczeń w grupie *NSG1*, ponieważ jest została ona skojarzona z podsiecią *Subnet1*, a maszyna wirtualna *VM1* znajduje się w podsieci *Subnet1*. O ile nie utworzono reguły zezwalającej na port 80 dla ruchu przychodzącego, ruch jest blokowany przez domyślną regułę zabezpieczeń [DenyAllInbound](#denyallinbound) i nigdy nie jest oceniany przez grupę *NSG2*, ponieważ grupa *NSG2* jest skojarzona z interfejsem sieciowym. Jeśli grupa *NSG1* zawiera regułę zabezpieczeń, która zezwala na port 80, ruch jest następnie przetwarzany przez grupę *NSG2*. Aby zezwolić na ruch przychodzący na porcie 80 do maszyny wirtualnej, obie grupy *NSG1* i *NSG2* muszą zawierać regułę zezwalającą ruch przychodzący na porcie 80 z Internetu.
- **VM2**: przetwarzane są reguły w grupie *NSG1*, ponieważ maszyna wirtualna *VM2* znajduje się również w podsieci *Subnet1*. Ponieważ maszyna wirtualna *VM2* nie ma grupy zabezpieczeń sieci skojarzonej z jej interfejsem sieciowym, odbiera cały ruch dozwolony przez grupę *NSG1* lub nie odbiera całego ruchu blokowanego przez grupę *NSG1*. Ruch jest dozwolony albo blokowany dla wszystkich zasobów w tej samej podsieci, gdy grupa zabezpieczeń sieci jest skojarzona z podsiecią.
- **VM3**: ponieważ ma żadnej sieciowej grupy zabezpieczeń skojarzonej z podsiecią *Subnet2*, ruch jest dozwolony do tej podsieci i przetwarzany przez grupę *NSG2*, ponieważ grupa *NSG2* została skojarzona z interfejsem sieciowym dołączonym do maszyny wirtualnej *VM3*.
- **VM4**: ruch jest dozwolony do maszyny wirtualnej *VM4*, ponieważ sieciowa grupa zabezpieczeń nie została skojarzona z podsiecią *Subnet3* ani interfejsem sieciowym w tej maszynie wirtualnej. Cały ruch sieciowy przez podsieć i interfejs sieciowy jest dozwolony, jeśli nie mają one skojarzonej żadnej sieciowej grupy zabezpieczeń.

### <a name="outbound-traffic"></a>Ruch wychodzący

W przypadku ruchu wychodzącego platforma Azure przetwarza najpierw reguły w grupie zabezpieczeń sieci skojarzonej z interfejsem sieciowym, jeśli taka istnieje, a następnie reguły w grupie zabezpieczeń sieci skojarzonej z podsiecią, jeśli taka istnieje.

- **VM1**: przetwarzane są reguły zabezpieczeń w grupie *NSG2*. O ile nie utworzono reguły zabezpieczeń, która blokuje ruch wychodzący do Internetu na porcie 80, ruch jest dozwolony przez domyślną regułą zabezpieczeń [AllowInternetOutbound](#allowinternetoutbound) w obu grupach *NSG1* i *NSG2*. Jeśli grupa *NSG2* zawiera regułę zabezpieczeń, która nie zezwala na port 80, ruch jest blokowany i nigdy nie jest oceniany przez grupę *NSG1*. Aby zablokować port 80 na maszynie wirtualnej, jedna lub obie grupy zabezpieczeń sieci muszą zawierać regułę, która blokuje ruch do Internetu na porcie 80.
- **VM2**: cały ruch jest wysyłany przez interfejs sieciowy do podsieci, ponieważ interfejs sieciowy dołączony do maszyny wirtualnej *VM2* nie ma skojarzonej sieciowej grupy zabezpieczeń. Reguły w grupie *NSG1* są przetwarzane.
- **VM3**: jeśli grupa *NSG2* zawiera regułę zabezpieczeń, która blokuje port 80, ruch jest blokowany. Jeśli grupa *NSG2* zawiera regułę zabezpieczeń, która zezwala na użycie portu 80, ruch wychodzący do Internetu przez port 80 jest dozwolony, ponieważ grupa zabezpieczeń sieci nie jest skojarzona z podsiecią *Subnet2*.
- **VM4**: cały ruch sieciowy z maszyny wirtualnej *VM4* jest dozwolony, ponieważ żadna sieciowa grupa zabezpieczeń nie została skojarzona z interfejsem sieciowym dołączonym do maszyny wirtualnej ani z podsiecią *Subnet3*.

Reguły agregowane stosowane do interfejsu sieciowego można łatwo wyświetlić, wyświetlając [obowiązujące reguły zabezpieczeń](virtual-network-network-interface.md#view-effective-security-rules) dla interfejsu sieciowego. Możesz również skorzystać z możliwości [weryfikowania przepływu protokołu IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) w celu ustalenia, czy komunikacja z lub do interfejsu sieciowego jest dozwolona. Weryfikowanie przepływu adresów IP informuje, czy komunikacja jest dozwolona lub zablokowana, oraz która reguła zabezpieczeń sieci zezwala lub nie zezwala na ruch.

> [!NOTE]
> Sieciowe grupy zabezpieczeń są skojarzone z podsieciami lub maszyn wirtualnych i usług w chmurze wdrożone w klasycznym modelu wdrażania oraz z podsieciami lub interfejsami sieciowymi w modelu wdrażania usługi Resource Manager. Aby dowiedzieć się więcej na temat modeli wdrażania platformy Azure, zapoznaj się z artykułem [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Informacje na temat modeli wdrażania platformy Azure).

> [!TIP]
> Zaleca się skojarzenie grupy zabezpieczeń sieci z podsiecią lub interfejsem sieciowym, ale nie obydwoma tymi elementami naraz, o ile nie ma po temu konkretnego powodu. Ponieważ reguły w grupie zabezpieczeń sieci skojarzonej z podsiecią mogą powodować konflikt z regułami w grupie zabezpieczeń sieci skojarzonej z interfejsem sieciowym, możesz napotkać nieoczekiwane problemy z komunikacją, co będzie wymagać rozwiązywania problemów.

## <a name="azure-platform-considerations"></a>Zagadnienia dotyczące platformy Azure

- **Wirtualny adres IP węzła hosta**: podstawowe usługi infrastruktury, takie jak DHCP, DNS, IMDS i monitorowanie kondycji, są realizowane za pośrednictwem zwirtualizowanych adresów IP hosta 168.63.129.16 i 169.254.169.254. Te adresy IP należą do firmy Microsoft i są jedynymi zwirtualizowanymi adresami IP używanymi do tego celu we wszystkich regionach.
- **Licencjonowanie (usługa zarządzania kluczami)**: obrazy systemu Windows działające na maszynach wirtualnych muszą być licencjonowane. W celu zapewnienia licencjonowania do serwerów hosta usługi zarządzania kluczami zostaje wysłane żądanie licencjonowania, które takie żądania obsługują. Żądanie jest wysyłane za pomocą portu 1688. W przypadku wdrożeń korzystających z konfiguracji [default route 0.0.0.0/0](virtual-networks-udr-overview.md#default-route) ta reguła platformy zostanie wyłączona.
- **Maszyny wirtualne w pulach ze zrównoważonym obciążeniem**: port źródłowy i zakres adresów są stosowane z komputera źródłowego, nie modułu równoważenia obciążenia. Port docelowy i zakres adresów dotyczą komputera docelowego, a nie modułu równoważenia obciążenia.
- **Wystąpienia usług platformy Azure**: wystąpienia kilku usług platformy Azure, takich jak usługa HDInsight, środowiska usług aplikacji i Virtual Machine Scale Sets, są wdrażane w podsieciach sieci wirtualnej. Aby uzyskać pełną listę usług, które można wdrażać w sieciach wirtualnych, zobacz [Virtual network for Azure services (Sieć wirtualna dla usług platformy Azure)](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Upewnij się, że należy zapoznać się z wymaganiami dotyczącymi portów dla każdej usługi przed zastosowaniem grupy zabezpieczeń sieci do podsieci, w której zasób został wdrożony. Jeśli porty wymagane przez usługę zostaną zablokowane, usługa nie będzie działać prawidłowo.
- **Wysyłanie wychodzących wiadomości e-mail**: firma Microsoft zaleca używanie usług uwierzytelnionego przekazywania SMTP (zwykle połączonych za pośrednictwem portu 587 protokołu TCP, ale często również innych portów) do wysyłania wiadomości e-mail z usługi Azure Virtual Machines. Usługi przekazywania SMTP specjalizują się w obsłudze reputacji nadawcy, aby ograniczyć możliwość odrzucenia wiadomości e-mail przez zewnętrznych dostawców poczty e-mail. Takie usługi przekazywania SMTP obejmują między innymi usługi Exchange Online Protection i SendGrid. Korzystanie z usług przekazywania SMTP nie jest w żaden sposób ograniczone na platformie Azure, niezależnie od typu subskrypcji. 

  Jeśli subskrypcja platformy Azure została utworzona przed 15 listopada 2017 r., oprócz używania usług przekazywania SMTP można wysłać wiadomości e-mail bezpośrednio za pośrednictwem portu 25 protokołu TCP. Jeśli subskrypcja została utworzona po 15 listopada 2017 r., wysyłanie wiadomości e-mail bezpośrednio przez port 25 może okazać się niemożliwe. Zachowanie komunikacji wychodzącej za pośrednictwem portu 25 zależy od typu Twojej subskrypcji w następujący sposób:

     - **Umowa Enterprise Agreement**: komunikacja wychodząca przez port 25 jest dozwolona. Wychodzące wiadomości e-mail można wysyłać bezpośrednio z maszyn wirtualnych do zewnętrznych dostawców poczty e-mail bez żadnych ograniczeń powiązanych z platformą Azure. 
     - **Płatność zgodnie z rzeczywistym użyciem**: komunikacja wychodząca przez port 25 jest blokowana dla wszystkich zasobów. Jeśli musisz wysyłać wiadomości e-mail z maszyny wirtualnej bezpośrednio do zewnętrznych dostawców poczty e-mail (bez użycia uwierzytelnionego przekazywania SMTP), możesz zgłosić wniosek o usunięcie ograniczenia. Wnioski są przeglądane i zatwierdzane według uznania firmy Microsoft, a odpowiednie prawa są przyznawane dopiero po pomyślnym zakończeniu kontroli mającej na celu zapobieganie oszustwom. Aby przesłać wniosek, otwórz zgłoszenie do pomocy technicznej z typem problemu *Techniczny*, *Łączność sieciowa*, *Nie można wysłać wiadomości e-mail (SMTP/port 25)*. W tym zgłoszeniu do pomocy technicznej szczegółowo opisz, dlaczego w ramach subskrypcji musisz wysyłać wiadomości e-mail bezpośrednio do dostawców poczty, zamiast korzystać z uwierzytelnionego przekazywania protokołu SMTP. Jeśli subskrypcja zostanie uznana za wyjątek, tylko maszyny wirtualne utworzone po dacie uznania będą mogły obsługiwać komunikację wychodzącą przez port 25.
     - **MSDN, Azure — dostęp próbny, Azure w ramach programu licencjonowania Open, Education, BizSpark i bezpłatna wersja próbna**: komunikacja wychodząca przez port 25 jest blokowana dla wszystkich zasobów. Nie można wysyłać żadnych wniosków o usunięcie ograniczenia, ponieważ takie prawa nie są przyznawane. Aby wysyłać wiadomości e-mail z maszyny wirtualnej, musisz skorzystać z usługi przekazywania SMTP.
     - **Dostawca usług w chmurze**: klienci korzystający z zasobów platformy Azure za pośrednictwem dostawcy usług w chmurze mogą utworzyć zgłoszenie do pomocy technicznej za pomocą swojego dostawcy usług w chmurze i zażądać, aby dostawca utworzył przypadek odblokowania w ich imieniu, jeśli nie można użyć bezpiecznego przekazywania protokołu SMTP.

  Jeśli platforma Azure zezwoli Ci na wysyłanie wiadomości e-mail za pośrednictwem portu 25, firma Microsoft nie gwarantuje, że dostawcy poczty e-mail będą akceptować przychodzące wiadomości e-mail z maszyny wirtualnej. Jeśli określony dostawca odrzuca pocztę z maszyny wirtualnej, musisz w bezpośredniej współpracy z dostawcą rozwiązać wszelkie problemy z dostarczaniem wiadomości lub filtrowaniem spamu albo użyć usługi uwierzytelnionego przekazywania SMTP.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [utworzyć sieciową grupę zabezpieczeń](tutorial-filter-network-traffic.md).
