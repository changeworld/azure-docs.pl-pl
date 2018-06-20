---
title: Omówienie zabezpieczeń sieci na platformie Azure | Microsoft Docs
description: Dowiedz się więcej na temat opcji zabezpieczeń na potrzeby kontrolowania przepływu ruchu sieciowego między zasobami platformy Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: jdial
ms.openlocfilehash: 11178c574bcfa2224d15f81653f7d202ba88fb55
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657591"
---
# <a name="network-security"></a>Bezpieczeństwo sieci

Można ograniczyć ruch sieciowy do zasobów w sieci wirtualnej przy użyciu grupy zabezpieczeń sieci. Grupa zabezpieczeń sieci zawiera listę reguł zabezpieczeń, które zezwalają na lub blokują przychodzący lub wychodzący ruch sieciowy na podstawie źródłowego lub docelowego adresu IP, portu i protokołu. 

## <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

Każdy interfejs sieciowy ma zero lub jedną skojarzoną grupę zabezpieczeń sieci. Każdy interfejs sieciowy istnieje w podsieci [sieci wirtualnej](virtual-networks-overview.md). Podsieć również może mieć zero lub jedną skojarzoną grupę zabezpieczeń sieci. 

Po zastosowaniu do podsieci reguły zabezpieczeń są stosowane do wszystkich zasobów w podsieci. Obok interfejsów sieciowych możesz mieć wystąpienia innych usług platformy Azure, takich jak usługa HDInsight, zestawy skalowania maszyn wirtualnych i środowiska usług aplikacji, wdrożone w tej podsieci.

Sposób stosowania grup zabezpieczeń sieci w przypadku skojarzenia grup zabezpieczeń sieci zarówno z interfejsem sieciowym, jak i podsiecią interfejsu sieciowego, jest następujący:

- **Ruch przychodzący**: grupa zabezpieczeń sieci skojarzona z podsiecią, w której znajduje się interfejs sieciowy, jest stosowana jako pierwsza. Cały ruch dozwolony przez grupę zabezpieczeń sieci skojarzoną z podsiecią jest następnie oceniany przez grupę zabezpieczeń sieci skojarzoną z interfejsem sieciowym. Na przykład możesz wymagać dostępu ruchu przychodzącego do maszyny wirtualnej za pośrednictwem portu 80 z Internetu. Jeśli grupa zabezpieczeń sieci została skojarzona z interfejsem sieciowym i podsiecią, w której znajduje się interfejs sieciowy, grupa zabezpieczeń sieci skojarzona z podsiecią i interfejsem sieciowym musi zezwalać na port 80. Jeśli port 80 jest dozwolony tylko przez grupę zabezpieczeń sieci skojarzoną z podsiecią lub interfejsem sieciowym, w której znajduje się podsieć, komunikacja zakończy się niepowodzeniem z powodu domyślnych reguł zabezpieczeń. Zobacz [domyślne reguły zabezpieczeń](#default-security-rules), aby uzyskać szczegółowe informacje. Jeśli grupa zabezpieczeń sieci została zastosowana tylko do podsieci lub tylko do interfejsu sieciowego i zawiera regułę, która zezwala na ruch przychodzący na porcie port 80, komunikacja zakończy się pomyślnie. 
- **Ruch wychodzący**: grupa zabezpieczeń sieci skojarzona z interfejsem sieciowym jest stosowana jako pierwsza. Cały ruch dozwolony przez grupę zabezpieczeń sieci skojarzoną z interfejsem sieciowym jest następnie oceniany przez grupę zabezpieczeń sieci skojarzoną z podsiecią.

Możesz czasami nie wiedzieć, że grupy zabezpieczeń sieci są stosowane do interfejsu sieciowego i podsieci. Reguły agregowane stosowane do interfejsu sieciowego można łatwo wyświetlić, wyświetlając [obowiązujące reguły zabezpieczeń](virtual-network-network-interface.md#view-effective-security-rules) dla interfejsu sieciowego. Możesz również skorzystać z możliwości [weryfikowania przepływu protokołu IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) w celu ustalenia, czy komunikacja z lub do interfejsu sieciowego jest dozwolona. Narzędzie informuje, czy komunikacja jest dozwolona, oraz która reguła zabezpieczeń sieci zezwala lub nie zezwala na ruch.
 
> [!NOTE]
> Grupy zabezpieczeń sieci są skojarzone z podsieciami lub maszynami wirtualnymi i usługami w chmurze wdrożonymi w klasycznym modelu wdrażania, a nie z interfejsami sieciowymi w modelu wdrażania przy użyciu usługi Resource Manager. Aby dowiedzieć się więcej na temat modeli wdrażania platformy Azure, zapoznaj się z artykułem [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Informacje na temat modeli wdrażania platformy Azure).

Tę samą grupę zabezpieczeń sieci można zastosować do dowolnej liczby indywidualnych interfejsów sieciowych i podsieci.

## <a name="security-rules"></a>Reguły zabezpieczeń

Grupa zabezpieczeń sieci nie zawiera żadnych reguł lub dowolną liczbę reguł zgodnie z potrzebami, w ramach [limitów](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) subskrypcji platformy Azure. Każda reguła określa następujące właściwości:

|Właściwość  |Wyjaśnienie  |
|---------|---------|
|Name (Nazwa)|Unikatowa nazwa w obrębie grupy zabezpieczeń sieci.|
|Priorytet | Liczba z zakresu od 100 do 4096. Reguły są przetwarzane w kolejności priorytetów. Im niższy numer, tym wyższy priorytet, więc te o niższych numerach są przetwarzane przed tymi o wyższych numerach. Kiedy ruch jest zgodny z regułą, przetwarzanie zostaje zatrzymane. W związku z tym żadne istniejące reguły o niższych priorytetach (wyższych numerach), które mają takie same atrybuty jak reguły o wyższych priorytetach, nie będą przetwarzane.|
|Obiekt źródłowy lub docelowy| Dowolny lub indywidualny adres IP, blok CIDR (na przykład 10.0.0.0/24), tag usługi lub grupa zabezpieczeń aplikacji. Dowiedz się więcej o [tagach usługi](#service-tags) i [grupach zabezpieczeń aplikacji](#application-security-groups). Określenie zakresu, tagu usługi lub grupy zabezpieczeń aplikacji umożliwia utworzenie mniejszej liczby reguł zabezpieczeń. Możliwość określenia wielu poszczególnych adresów IP i zakresów (nie można określić wielu tagów usługi ani grup aplikacji) w regule nosi nazwę rozszerzonych reguł zabezpieczeń. Dowiedz się więcej o [rozszerzonych regułach zabezpieczeń](#augmented-security-rules). Rozszerzone reguły zabezpieczeń można tworzyć tylko w grupach zabezpieczeń sieci utworzonych za pośrednictwem modelu wdrażania przy użyciu usługi Resource Manager. Nie można określić wielu adresów IP i zakresów adresów IP w grupach zabezpieczeń sieci utworzonych za pomocą klasycznego modelu wdrażania.|
|Protokół     | TCP, UDP lub Dowolny (obejmuje protokoły TCP, UDP i ICMP). Nie można określić samego protokołu ICMP, a więc jeśli potrzebujesz protokołu ICMP, użyj opcji Dowolny. |
|Kierunek| Określa, czy ta reguła ma zastosowanie do ruchu przychodzącego, czy wychodzącego.|
|Zakres portów     |Można określić pojedynczy port lub zakres portów. Na przykład można określić port 80 lub 10000–10005. Określenie zakresów umożliwia utworzenie mniejszej liczby reguł zabezpieczeń. Rozszerzone reguły zabezpieczeń można tworzyć tylko w grupach zabezpieczeń sieci utworzonych za pośrednictwem modelu wdrażania przy użyciu usługi Resource Manager. Nie można określić wielu portów lub zakresów portów w grupach zabezpieczeń sieci utworzonych za pomocą klasycznego modelu wdrażania.   |
|Akcja     | Zezwolenie lub zablokowanie        |

Reguły zabezpieczeń sieciowej grupy zabezpieczeń są oceniane według priorytetu na podstawie krotki składającej się z pięciu informacji (urządzenie źródłowe, port źródłowy, urządzenie docelowe, port docelowy i protokół) w celu zezwolenia na ruch lub zablokowania go. Dla istniejących połączeń tworzony jest rekord przepływu, a komunikacja jest dozwolona lub blokowana na podstawie stanu połączenia rekordów przepływu. Dzięki temu sieciowa grupa zabezpieczeń jest stanowa. Jeśli zostanie określona reguła zabezpieczeń dla ruchu wychodzącego do dowolnego adresu za pośrednictwem (na przykład) portu 80, nie trzeba określać żadnej reguły zabezpieczeń ruchu przychodzącego dla odpowiedzi na ruch wychodzący. Należy tylko określić regułę zabezpieczeń dla ruchu przychodzącego w przypadku, jeśli komunikacja jest inicjowana zewnętrznie. Jest to również prawdziwe w odwrotnym przypadku. Jeśli ruch przychodzący jest dozwolony przez port, nie trzeba określać reguły zabezpieczeń dla ruchu wychodzącego, aby odpowiadać na ruch przychodzący przez port.
Istniejące połączenia mogą nie zostać przerwanie po usunięciu reguły zabezpieczeń, która zezwoliła na przepływ. Przepływy ruchu są przerywane po zakończeniu połączenia, gdy przez co najmniej kilka minut nie ma ruchu z żadnej strony.

Aby dowiedzieć się więcej o limitach podczas tworzenia reguł zabezpieczeń, zobacz [limity platformy Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="augmented-security-rules"></a>Rozszerzone reguły zabezpieczeń

Rozszerzone reguły upraszczają definicję zabezpieczeń dla sieci wirtualnych, umożliwiając definiowanie reguł zabezpieczeń większych i złożonych sieci przy użyciu mniejszej liczby reguł. Można połączyć wiele portów, wiele jawnych adresów IP, usługi tagów i grupy zabezpieczeń aplikacji w jedną, łatwo zrozumiałą regułę zabezpieczeń. Rozszerzone reguły stosuje się w polach źródła, obiektu docelowego i portów reguły. Podczas tworzenia reguły można określić wiele jawnych adresów IP, zakresów CIDR i portów. Aby uprościć zarządzanie definicją reguły zabezpieczeń, połącz rozszerzone reguły zabezpieczeń z tagami usług lub grupami zabezpieczeń aplikacji. 

Aby dowiedzieć się więcej o limitach podczas tworzenia rozszerzonych reguł zabezpieczeń, zobacz [limity platformy Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="default-security-rules"></a>Domyślne reguły zabezpieczeń

Jeśli z podsiecią lub interfejsem sieciowym nie skojarzono grupy zabezpieczeń sieci, dozwolony będzie cały ruch przychodzący do lub wychodzący z podsieci lub interfejsu sieciowego. Natychmiast po utworzeniu grupy zabezpieczeń sieci platforma Azure tworzy następujące reguły domyślne w ramach grupy zabezpieczeń sieci:

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

W kolumnach **Źródło** i **Obiekt docelowy** elementy *VirtualNetwork*, *AzureLoadBalancer* i *Internet* są [tagami usługi](#tags), a nie adresami IP. W kolumnie protokołu pozycja **Wszystkie** obejmuje protokoły TCP, UDP i ICMP. Podczas tworzenia reguły można określić protokół TCP, UDP lub Wszystkie, ale nie można określić wyłącznie protokołu ICMP. W związku z tym jeśli reguła wymaga protokołu ICMP, musisz wybrać wartość *Wszystkie* w kolumnie protokołu. Wartość *0.0.0.0/0* w kolumnach **Źródło** i **Obiekt docelowy** reprezentuje wszystkie adresy.
 
Nie można usunąć reguł domyślnych, ale można je przesłonić, tworząc reguły o wyższych priorytetach.

## <a name="service-tags"></a>Tagi usługi

 Tag usługi reprezentuje grupę prefiksów adresów IP, aby zminimalizować złożoność tworzenia reguły zabezpieczeń. Nie można utworzyć własnego tagu usługi ani określić adresów IP uwzględnionych w tagu. Firma Microsoft zarządza prefiksami adresów obejmowanymi przez tag usługi i automatycznie aktualizuje tag usługi, gdy adresy ulegną zmianie. Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Poniżej wymienione tagi usługi są dostępne do użycia w definicji reguły zabezpieczeń. Ich nazwy różnią się nieco między sobą w [modelach wdrażania platformy Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

* **VirtualNetwork** (model wdrażania przy użyciu usługi Resource Manager) (**VIRTUAL_NETWORK** — model klasyczny): ten tag obejmuje przestrzeń adresową sieci wirtualnej (wszystkie zakresy CIDR zdefiniowane dla sieci wirtualnej), wszystkie połączone lokalne przestrzenie adresowe oraz [skomunikowane równorzędnie](virtual-network-peering-overview.md) sieci wirtualne lub sieć wirtualną połączoną z [bramą sieci wirtualnej](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* **AzureLoadBalancer** (model usługi Resource Manager) (**AZURE_LOADBALANCER** — model klasyczny): ten znacznik określa moduł równoważenia obciążenia infrastruktury platformy Azure. Ten znacznik przekłada się na [adres IP centrum danych Azure](https://www.microsoft.com/download/details.aspx?id=41653), z którego pochodzą sondy kondycji platformy Azure. Jeśli nie jest używana usługa Azure Load Balancer, tę zasadę można przesłonić.
* **Internet** (model usługi Resource Manager) (**INTERNET** — model klasyczny): ten tag określa przestrzeń adresów IP, która znajduje się poza siecią wirtualną i do której można uzyskać dostęp w publicznym Internecie. Ten zakres adresów obejmuje [publiczną przestrzeń adresów IP należącą do platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653).
* **AzureTrafficManager** (tylko model usługi Resource Manager): ten tag określa przestrzeń adresów IP sondy usługi Azure Traffic Manager. Więcej informacji na temat adresów IP sondy usługi Traffic Manager można znaleźć w temacie [Azure Traffic Manager FAQ (Azure Traffic Manager — często zadawane pytania)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs).
* **Storage** (tylko model wdrażania przy użyciu usługi Resource Manager): ten tag określa przestrzeń adresów IP dla usługi Azure Storage. W przypadku określenia wartości *Storage* dozwolony lub blokowany jest ruch do usługi Storage. Jeśli chcesz zezwolić na dostęp do usługi Storage w konkretnym [regionie](https://azure.microsoft.com/regions), możesz określić region. Jeśli na przykład chcesz zezwolić na dostęp do usługi Azure Storage tylko w regionie Wschodnie stany USA, możesz określić *Storage.EastUS* jako tag usługi. Tag reprezentuje usługę, ale nie konkretne wystąpienia usługi. Na przykład tag reprezentuje usługę Azure Storage, ale nie konkretne konto usługi Azure Storage. Wszystkie prefiksy adresów reprezentowane przez ten tag są również reprezentowane przez tag **Internet**.
* **Sql** (tylko model wdrażania przy użyciu usługi Resource Manager): ten tag określa prefiksy adresów usług Azure SQL Database i Azure SQL Data Warehouse. W przypadku określenia wartości *Sql* dozwolony lub blokowany jest ruch do usługi Sql. Jeśli chcesz zezwolić na dostęp do usługi Sql w konkretnym [regionie](https://azure.microsoft.com/regions), możesz określić region. Jeśli na przykład chcesz zezwolić na dostęp do usługi Azure SQL Database tylko w regionie Wschodnie stany USA, możesz określić *Sql.EastUS* jako tag usługi. Tag reprezentuje usługę, ale nie konkretne wystąpienia usługi. Na przykład tag reprezentuje usługę Azure SQL Database, ale nie konkretną bazę danych lub serwer SQL. Wszystkie prefiksy adresów reprezentowane przez ten tag są również reprezentowane przez tag **Internet**.
* **AzureCosmosDB** (tylko Menedżer zasobów): ten tag określa prefiksy adresów usługi Azure Cosmos Database. W przypadku określenia wartości *AzureCosmosDB* dozwolony lub blokowany jest ruch do usługi AzureCosmosDB. Jeśli chcesz zezwolić na dostęp do usługi AzureCosmosDB w wybranym [regionie](https://azure.microsoft.com/regions), możesz określić ten region za pomocą następującego formatu: AzureCosmosDB.[nazwa regionu].
* **AzureKeyVault** (tylko Menedżer zasobów): ten tag określa prefiksy adresów usługi Azure KeyVault. W przypadku określenia wartości *AzureKeyVault* dozwolony lub blokowany jest ruch do usługi AzureKeyVault. Jeśli chcesz zezwolić na dostęp do usługi AzureKeyVault w wybranym [regionie](https://azure.microsoft.com/regions), możesz określić ten region za pomocą następującego formatu: AzureKeyVault.[nazwa regionu].

> [!NOTE]
> Tagi usług Azure określają prefiksy adresów określonych chmur, które są używane. Tagi regionalnych usług nie są obsługiwane przez chmury krajowe. Obsługiwane są wyłącznie formaty globalne, takie jak Storage czy Sql.

> [!NOTE]
> W przypadku zaimplementowania [punktu końcowego usługi sieci wirtualnej](virtual-network-service-endpoints-overview.md) dla usługi takiej jak usługa Azure Storage lub Azure SQL Database, platforma Azure dodaje trasę do podsieci sieci wirtualnej dla usługi. Prefiksy adresów dla trasy to te same prefiksy adresów lub zakresy CIDR, co w odpowiednim tagu usługi.

## <a name="application-security-groups"></a>Grupy zabezpieczeń aplikacji

Grupy zabezpieczeń aplikacji umożliwiają skonfigurowanie zabezpieczeń sieci jako naturalnego rozszerzenia struktury aplikacji, co pozwala na grupowanie maszyn wirtualnych i definiowanie zasad zabezpieczeń sieci na podstawie tych grup. Ta funkcja umożliwia ponowne używanie zasady zabezpieczeń na dużą skalę bez ręcznej obsługi jawnych adresów IP. Platforma obsługuje złożoność jawnych adresów IP i wiele zestawów reguł, co pozwala skupić się na logice biznesowej.

Jako źródło i obiekt docelowy reguły zabezpieczeń można określić pojedynczą grupę zabezpieczeń aplikacji. Jako źródło i obiekt docelowy nie można podać kilku grup zabezpieczeń aplikacji. Po zdefiniowaniu zasad zabezpieczeń można utworzyć maszyny wirtualne i przypisać interfejsy sieciowe w maszynie wirtualnej do grupy zabezpieczeń aplikacji. Zasady są stosowane na podstawie członkostwa w grupie zabezpieczeń aplikacji każdego interfejsu sieciowego w maszynie wirtualnej. Poniższy przykład przedstawia sposób użycia grupy zabezpieczeń aplikacji dla wszystkich serwerów sieci web w danej subskrypcji:

1. Utwórz grupę zabezpieczeń aplikacji o nazwie *WebServers*.
2. Utwórz grupę zabezpieczeń sieci o nazwie *MyNSG*.
3. Utwórz regułę zabezpieczeń dla ruchu przychodzącego w grupie zabezpieczeń sieci, określając tag usługi *Internet* dla adresu źródłowego i grupę zabezpieczeń aplikacji *WebServers* jako adres docelowy, i zezwalając na porty 80 i 443.
4. Wdróż maszynę wirtualną, na której działa aplikacja serwera sieci web. Określ interfejs sieciowy w maszynie wirtualnej jako członka grupy zabezpieczeń aplikacji *WebServers*. Porty 80 i 443 będą wówczas dozwolone na maszynie wirtualnej. Porty będą również dozwolone dla dowolnych kolejnych serwerów internetowych utworzonych jako członkowie grupy zabezpieczeń aplikacji *WebServers*. 

W przypadku utworzenia innych reguł aplikacji, określających inne grupy zabezpieczeń aplikacji jako obiekty docelowe, reguły te nie będą stosowane do serwerów sieci web z poprzedniego przykładu. Reguły określające grupy zabezpieczeń aplikacji są stosowane tylko do interfejsów sieciowych, które są członkami grupy zabezpieczeń aplikacji. Grupy zabezpieczeń aplikacji, w połączeniu z rozszerzonymi regułami zabezpieczeń i tagami usługi, umożliwiają utworzenie minimalnej liczby grup zabezpieczeń sieci do zarządzania zabezpieczeniami sieci w ramach subskrypcji.
 
Aby dowiedzieć się więcej o limitach podczas tworzenia grup zabezpieczeń aplikacji i określania ich w regułach zabezpieczeń, zobacz [limity platformy Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

Grupy zabezpieczeń aplikacji mają następujące ograniczenia:

-   Wszystkie interfejsy sieciowe przypisane do grupy zabezpieczeń aplikacji muszą istnieć w tej samej sieci wirtualnej, co pierwszy interfejs sieciowy przypisany do danej grupy zabezpieczeń aplikacji. Na przykład, jeśli pierwszy interfejs sieciowy przypisany do grupy zabezpieczeń aplikacji o nazwie *ASG1* istnieje w sieci wirtualnej o nazwie *VNet1*, wszystkie kolejne interfejsy sieciowe przypisane do *ASG1* muszą istnieć w sieci *VNet1*. Interfejsy sieciowe z różnych sieci wirtualnych nie mogą być dodawane do tej samej grupy zabezpieczeń aplikacji.
- Jeśli określisz grupy zabezpieczeń aplikacji jako źródło i obiekt docelowy w regule zabezpieczeń, interfejsy sieciowe w obu grupach zabezpieczeń aplikacji muszą istnieć w tej samej sieci wirtualnej. Na przykład jeśli grupa ASG1 zawiera interfejsy sieciowe z sieci VNet1, a grupa ASG2 zawiera interfejsy sieciowe z sieci VNet2, nie można przypisać grupy ASG1 jako źródła i grupy ASG2 jako obiektu docelowego w regule. Wszystkie interfejsy sieciowe muszą istnieć w sieci VNet1.

## <a name="azure-platform-considerations"></a>Zagadnienia dotyczące platformy Azure

- **Wirtualny adres IP węzła hosta**: podstawowe usługi infrastruktury, takie jak DHCP, DNS i monitorowanie kondycji, są realizowane za pośrednictwem zwirtualizowanych adresów IP hosta 168.63.129.16 i 169.254.169.254. Te publiczne adresy IP należą do firmy Microsoft i są jedynymi zwirtualizowanymi adresami IP używanymi do tego celu we wszystkich regionach. Adresy mapują na fizyczny adres IP komputera serwera (węzła hosta) obsługującego maszynę wirtualną. Węzeł hosta pełni rolę przekaźnika DHCP, cyklicznego programu rozpoznawania nazw DNS i źródła sondy kondycji modułu równoważenia obciążenia oraz sondy kondycji komputera. Komunikacja z tymi adresami IP nie jest atakiem. Jeśli zablokujesz ruch do lub z tych adresów IP, maszyna wirtualna może nie działać poprawnie.
- **Licencjonowanie (usługa zarządzania kluczami):** obrazy systemu Windows uruchomione na maszynach wirtualnych muszą być licencjonowane. W celu zapewnienia licencjonowania do serwerów hosta usługi zarządzania kluczami zostaje wysłane żądanie licencjonowania, które takie żądania obsługują. Żądanie jest wysyłane za pomocą portu 1688. W przypadku wdrożeń korzystających z konfiguracji [default route 0.0.0.0/0](virtual-networks-udr-overview.md#default-route) ta reguła platformy zostanie wyłączona.
- **Maszyny wirtualne w pulach ze zrównoważonym obciążeniem**: port źródłowy i zakres adresów stosowane są z komputera źródłowego, nie modułu równoważenia obciążenia. Port docelowy i zakres adresów dotyczą komputera docelowego, a nie modułu równoważenia obciążenia.
- **Wystąpienia usług platformy Azure**: wystąpienia kilku usług platformy Azure, takich jak usługa HDInsight, środowiska usług aplikacji i zestawy skalowania maszyn wirtualnych, są wdrażane w podsieciach sieci wirtualnej. Aby uzyskać pełną listę usług, które można wdrażać w sieciach wirtualnych, zobacz [Virtual network for Azure services (Sieć wirtualna dla usług platformy Azure)](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Upewnij się, że należy zapoznać się z wymaganiami dotyczącymi portów dla każdej usługi przed zastosowaniem grupy zabezpieczeń sieci do podsieci, w której zasób został wdrożony. Jeśli porty wymagane przez usługę zostaną zablokowane, usługa nie będzie działać prawidłowo.
- **Wysyłanie wychodzących wiadomości e-mail**: firma Microsoft zaleca używanie usług uwierzytelnionego przekazywania SMTP (zwykle połączonych za pośrednictwem portu 587 protokołu TCP, ale często również innych portów) do wysyłania wiadomości e-mail z usługi Azure Virtual Machines. Usługi przekazywania SMTP specjalizują się w obsłudze reputacji nadawcy, aby ograniczyć możliwość odrzucenia wiadomości e-mail przez zewnętrznych dostawców poczty e-mail. Takie usługi przekazywania SMTP obejmują między innymi usługi Exchange Online Protection i SendGrid. Korzystanie z usług przekazywania SMTP nie jest w żaden sposób ograniczone na platformie Azure, niezależnie od typu subskrypcji. 

  Jeśli subskrypcja platformy Azure została utworzona przed 15 listopada 2017 r., oprócz używania usług przekazywania SMTP można wysłać wiadomości e-mail bezpośrednio za pośrednictwem portu 25 protokołu TCP. Jeśli subskrypcja została utworzona po 15 listopada 2017 r., wysyłanie wiadomości e-mail bezpośrednio przez port 25 może okazać się niemożliwe. Zachowanie komunikacji wychodzącej za pośrednictwem portu 25 zależy od typu Twojej subskrypcji w następujący sposób:

     - **Umowa Enterprise Agreement**: komunikacja wychodząca przez port 25 jest dozwolona. Wychodzące wiadomości e-mail można wysyłać bezpośrednio z maszyn wirtualnych do zewnętrznych dostawców poczty e-mail bez żadnych ograniczeń powiązanych z platformą Azure. 
     - **Płatność zgodnie z rzeczywistym użyciem:** komunikacja wychodząca przez port 25 jest zablokowana dla wszystkich zasobów. Jeśli musisz wysyłać wiadomości e-mail z maszyny wirtualnej bezpośrednio do zewnętrznych dostawców poczty e-mail (bez użycia uwierzytelnionego przekazywania SMTP), możesz zgłosić wniosek o usunięcie ograniczenia. Wnioski są przeglądane i zatwierdzane według uznania firmy Microsoft, a odpowiednie prawa są przyznawane dopiero po pomyślnym zakończeniu kontroli mającej na celu zapobieganie oszustwom. Aby przesłać wniosek, otwórz zgłoszenie do pomocy technicznej z typem problemu *Techniczny*, *Łączność sieciowa*, *Nie można wysłać wiadomości e-mail (SMTP/port 25)*. W tym zgłoszeniu do pomocy technicznej szczegółowo opisz, dlaczego w ramach subskrypcji musisz wysyłać wiadomości e-mail bezpośrednio do dostawców poczty, zamiast korzystać z uwierzytelnionego przekazywania protokołu SMTP. Jeśli subskrypcja zostanie uznana za wyjątek, tylko maszyny wirtualne utworzone po dacie uznania będą mogły obsługiwać komunikację wychodzącą przez port 25.
     - **Dostawca usług w chmurze, MSDN, Azure — dostęp próbny, Azure w ramach programu licencjonowania Open, Education, BizSpark i bezpłatna wersja próbna**: komunikacja wychodząca przez port 25 jest zablokowana dla wszystkich zasobów. Nie można wysyłać żadnych wniosków o usunięcie ograniczenia, ponieważ takie prawa nie są przyznawane. Aby wysyłać wiadomości e-mail z maszyny wirtualnej, musisz skorzystać z usługi przekazywania SMTP.

  Jeśli platforma Azure zezwoli Ci na wysyłanie wiadomości e-mail za pośrednictwem portu 25, firma Microsoft nie gwarantuje, że dostawcy poczty e-mail będą akceptować przychodzące wiadomości e-mail z maszyny wirtualnej. Jeśli określony dostawca odrzuci pocztę z maszyny wirtualnej, musisz w bezpośredniej współpracy z dostawcą rozwiązać wszelkie problemy z dostarczaniem wiadomości lub filtrowaniem spamu albo użyć usługi uwierzytelnionego przekazywania SMTP. 


## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć sieciową grupę zabezpieczeń](tutorial-filter-network-traffic.md).
