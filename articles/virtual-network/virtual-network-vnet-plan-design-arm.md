---
title: Planowanie sieci wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak planować sieci wirtualne oparte na izolacji, łączności i wymagania dotyczące lokalizacji.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: 3a4a9aea-7608-4d2e-bb3c-40de2e537200
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2018
ms.author: jdial
ms.openlocfilehash: 83558b9d8d47ac5e6bd15dd54db38125376d11bd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="plan-virtual-networks"></a>Planowanie sieci wirtualnych

Tworzenie sieci wirtualnej do eksperymentów z jest dość proste, ale prawdopodobnie, będzie wdrożenie wielu sieci wirtualnych w czasie, aby obsługiwały produkcyjnym wymagania organizacji. Przy planowaniu, można wdrożyć sieci wirtualne i zasoby, które skuteczniej należy połączyć. Informacje przedstawione w tym artykule jest najbardziej przydatna, jeśli znasz już sieci wirtualnych i mają pewne doświadczenie w pracy z nimi. Jeśli nie masz doświadczenia z sieciami wirtualnymi, zaleca się przeczytanie [omówienie sieci wirtualnej](virtual-networks-overview.md).

## <a name="naming"></a>Nazewnictwo

Wszystkie zasoby platformy Azure ma nazwy. Nazwa musi być unikatowa w zakresie, które mogą być różne dla każdego typu zasobu. Na przykład nazwa sieci wirtualnej muszą być unikatowe w obrębie [grupy zasobów](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), ale można duplikować, w ramach [subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) lub Azure [region](https://azure.microsoft.com/regions/#services). Definiowanie konwencji nazewnictwa, która służy stale w nazwach zasobów jest przydatne, gdy zarządzanie kilku zasobów sieciowych w czasie. Wskazówki dotyczące zasobów, zobacz [konwencje nazewnictwa](/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="regions"></a>Regiony

Wszystkie zasoby platformy Azure są tworzone w regionie platformy Azure i subskrypcji. Zasób można tworzyć tylko w sieci wirtualnej, który istnieje w tym samym regionie i subskrypcji co zasób. Można jednak połączyć sieci wirtualnych, które istnieją w ramach różnych subskrypcji i regionów. Aby uzyskać więcej informacji, zobacz [łączności](#connectivity). Podczas wybierania który regionów wdrażania zasobów w należy wziąć pod uwagę gdzie fizycznie znajdują się konsumentów zasobów:

- Konsumenci zasoby mają zwykle najniższe opóźnienia sieci, do ich zasobów. Aby określić względną opóźnienia między określonej lokalizacji i regiony platformy Azure, zobacz [wyświetlić względną opóźnienia](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Czy istnieją wymagania dotyczące siedziby suwerenności, zgodności i odporności danych? Jeśli tak, wybierając obszar, aby była zgodna z wymaganiami jest krytyczne. Aby uzyskać więcej informacji, zobacz [Azure lokalizacji geograficznych](https://azure.microsoft.com/global-infrastructure/geographies/).
- Czy potrzebne odporności różnych strefach dostępności Azure w ramach tego samego regionu Azure dla zasobów wdrażanych? Zasoby, takie jak maszyn wirtualnych (VM) można wdrożyć w różnych dostępności stref w tej samej sieci wirtualnej. Nie wszystkie regiony platformy Azure obsługuje natomiast dostępność strefy. Aby dowiedzieć się więcej na temat dostępności stref i regionów, które je obsługują, zobacz [stref dostępności](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="subscriptions"></a>Subskrypcje

Jak wiele sieci wirtualnych zgodnie z wymaganiami w ramach każdej subskrypcji można wdrożyć do [limit](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Niektóre organizacje mają różnych subskrypcji dla różnych działów, np. Aby uzyskać więcej informacji i dodatkowe uwagi wokół subskrypcji, zobacz [ładu subskrypcji](../azure-resource-manager/resource-manager-subscription-governance.md?toc=%2fazure%2fvirtual-network%2ftoc.json#define-your-hierarchy).

## <a name="segmentation"></a>Segmentacja

Można utworzyć wiele sieci wirtualnych dla poszczególnych subskrypcji i regionu. Możesz utworzyć wiele podsieci w każdej sieci wirtualnej. Kwestie, które należy wykonać pomóc w ustaleniu, jak wiele sieci wirtualnych i podsieci wymagają:

### <a name="virtual-networks"></a>Sieci wirtualne

Sieć wirtualna jest wirtualny, izolowanego część sieci publicznej platformy Azure. Każdej sieci wirtualnej jest dedykowany do subskrypcji. Zagadnienia do rozważenia podczas decydowania o Utwórz jedną sieć wirtualną lub wiele sieci wirtualnych w ramach subskrypcji:

- Czy wszelkich wymagań dotyczących zabezpieczeń w organizacji istnieją dla izolowanie ruchu w różnych sieciach wirtualnych? Można połączyć sieci wirtualnych, czy nie. Jeśli możesz połączyć sieci wirtualnych, można wdrożyć urządzenie wirtualne sieci, takie jak zapory, aby sterować przepływem ruchu między sieciami wirtualnymi. Aby uzyskać więcej informacji, zobacz [zabezpieczeń](#security) i [łączności](#connectivity).
- Czy wszystkie wymagania organizacyjne istnieje izolowania sieci wirtualnych w oddzielne [subskrypcje](#subscriptions) lub [regionów](#regions)?
- A [interfejsu sieciowego](virtual-network-network-interface.md) umożliwia maszyny Wirtualnej do komunikowania się z innymi zasobami. Każdy interfejs sieciowy ma co najmniej jeden prywatne adresy IP przypisane do niej. Jak wiele interfejsów sieciowych i [prywatnych adresów IP](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) są wymagane w sieci wirtualnej? Brak [limity](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) liczba interfejsów sieciowych i prywatnych adresów IP, które mogą mieć w ramach sieci wirtualnej.
- Czy chcesz połączyć sieć wirtualną z innej sieci wirtualnej lub sieci lokalnej? Możesz połączyć niektórych sieci wirtualnych do siebie lub sieciach lokalnych, a innych nie. Aby uzyskać więcej informacji, zobacz [łączności](#connectivity). Każdej sieci wirtualnej, które jest podłączone do innej sieci wirtualnej lub sieci lokalnej musi mieć unikatowy adres miejsca. Każda sieć wirtualna ma przypisane do jego przestrzeni adresowej jeden lub więcej zakresów adresów publicznych lub prywatnych. Zakres adresów jest określony format classless internet domeny routingu (CIDR), takich jak 10.0.0.0/16. Dowiedz się więcej o [zakresy adresów](manage-virtual-network.md#add-or-remove-an-address-range) dla sieci wirtualnych.
- Czy masz wszelkie wymagania organizacyjne administracji dla zasobów w różnych sieciach wirtualnych? Jeśli tak, może podzielić zasoby na oddzielnych sieci wirtualnej, aby uprościć [przypisanie uprawnień](#permissions) do osób w organizacji lub przypisać różne [zasady](#policies) na różne wirtualnego sieci.
- Jeśli niektóre zasoby usługi Azure podczas wdrażania w sieci wirtualnej, tworzenia własnych sieci wirtualnej. Aby określić, czy usługa Azure utworzy własnej sieci wirtualnej, zobacz informacje dla każdego [usługi Azure, które można wdrożyć w sieci wirtualnej](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network).

### <a name="subnets"></a>Podsieci

Sieć wirtualną można segmentowanych do co najmniej jednej podsieci do [limity](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Zagadnienia do rozważenia podczas decydowania o utworzyć jedną podsieć lub wiele sieci wirtualnych w ramach subskrypcji:

- Każda podsieć musi mieć unikatowy zakres adresów, określona w formacie CIDR w przestrzeni adresowej sieci wirtualnej. Zakres adresów nie może nakładać się przy użyciu innych podsieci w sieci wirtualnej.
- Jeśli planujesz wdrożenie niektórych zasobów usługi Azure w sieci wirtualnej, ich może wymagają, lub utworzyć własne podsieci, więc musi być wystarczającej ilości nieprzydzielonego miejsca na nich w tym celu. Aby określić, czy usługa Azure utworzy jego własnej podsieci, zobacz informacje dla każdego [usługi Azure, które można wdrożyć w sieci wirtualnej](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Na przykład jeśli można nawiązać połączenie sieci wirtualnej sieci lokalnej przy użyciu bramy sieci VPN platformy Azure, sieć wirtualna musi mieć dedykowany podsieci bramy. Dowiedz się więcej o [podsieci bramy](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub).
- Azure kieruje ruchem sieciowym między wszystkich podsieci w sieci wirtualnej, domyślnie. Można zastąpić routingu, aby zapobiec Azure routing między podsieciami, lub kierować ruchem między podsieciami przez urządzenie wirtualne sieci, na przykład domyślny platformy Azure. Jeśli potrzebujesz tego ruchu między zasobami w tym samym przepływie sieci wirtualnej przez urządzenie wirtualne sieci (NVA) wdrażanie zasobów do różnych podsieci. Dowiedz się więcej w [zabezpieczeń](#security).
- Można ograniczyć dostęp do zasobów platformy Azure, takich jak konta magazynu Azure lub bazy danych Azure SQL, do określonych podsieci z punktem końcowym usługi sieci wirtualnej. Ponadto można odmówić dostępu do zasobów z Internetu. Możesz utworzyć wiele podsieci i włączyć punkt końcowy usługi dla niektórych podsieci, a innych nie. Dowiedz się więcej o [punkty końcowe usługi](virtual-network-service-endpoints-overview.md), i zasobów platformy Azure można włączyć je na.
- Możesz skojarzyć zero lub jedną grupę zabezpieczeń sieci do każdej podsieci w sieci wirtualnej. Możesz skojarzyć takie same, lub innej, sieciowej grupy zabezpieczeń do każdej podsieci. Każda grupa zabezpieczeń sieci zawiera reguły, które akceptować lub odrzucać ruch do i z źródeł i miejsc docelowych. Dowiedz się więcej o [sieciowej grupy zabezpieczeń](#traffic-filtering).

## <a name="security"></a>Bezpieczeństwo

Można filtrować ruch sieciowy do i z zasobów w sieci wirtualnej przy użyciu grup zabezpieczeń sieci i wirtualnych urządzeń sieciowych. Można kontrolować, jak Azure kieruje ruch z podsieci. Można również ograniczyć, kto w organizacji może współpracować z zasobami w sieciach wirtualnych.

### <a name="traffic-filtering"></a>Filtrowanie ruchu

- Można filtrować ruch sieciowy między zasobami w sieci wirtualnej za pomocą grupy zabezpieczeń sieci, NVA filtrujące ruchu sieciowego, lub obie. Aby wdrożyć NVA, takie jak zapory, filtrowania ruchu w sieci, zobacz [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1). Przy użyciu NVA, możesz również utworzyć trasy niestandardowe przekierowujący ruch z podsieci do analizę NVA. Dowiedz się więcej o [routingu ruchu](#traffic-routing).
- Grupa zabezpieczeń sieci zawiera kilka zabezpieczeń reguły domyślne akceptować lub odrzucać ruchu do lub z zasobów. Grupa zabezpieczeń sieci może być skojarzona z karty sieciowej i podsieci, w której znajduje się interfejs sieciowy. Aby uprościć zarządzanie zasadami zabezpieczeń, zalecane jest, aby skojarzyć sieciową grupę zabezpieczeń do poszczególnych podsieci, a nie interfejsy sieciowe poszczególnych w obrębie podsieci, jeśli to możliwe.
- Jeśli różnych maszyn wirtualnych w obrębie podsieci muszą zasad zabezpieczeń zastosowanych do nich, można skojarzyć interfejsu sieciowego w maszynie Wirtualnej na co najmniej jedną grupę zabezpieczeń aplikacji. Reguła zabezpieczeń można określić grupy zabezpieczeń aplikacji w jego źródło i miejsce docelowe. Tej reguły następnie ma zastosowanie tylko do interfejsów sieciowych, które są członkami grupy zabezpieczeń aplikacji. Dowiedz się więcej o [sieciowej grupy zabezpieczeń](security-overview.md) i [grup zabezpieczeń aplikacji](security-overview.md#application-security-groups).
- Platforma Azure tworzy kilka domyślne zasady zabezpieczeń w ramach każdej grupy zabezpieczeń sieci. Jedna reguła domyślna umożliwia cały ruch między wszystkich zasobów w sieci wirtualnej. Aby zmienić to zachowanie, użyj zabezpieczenia sieci grup niestandardowych, routing kierowanie ruchem do NVA i/lub. Zalecane jest, zapoznaj się ze wszystkimi Azure [domyślne reguły zabezpieczeń](security-overview.md#default-security-rules) i zrozumieć, jak zasady grupy zabezpieczeń sieci są stosowane do zasobu.

Możesz wyświetlić przykładowe projekty wykonywania DMZ platformy Azure i Internetu za pomocą [NVA](/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) lub [sieciowej grupy zabezpieczeń](virtual-networks-dmz-nsg.md).

### <a name="traffic-routing"></a>routing ruchu

Platforma Azure tworzy kilka trasy domyślnej dla ruchu wychodzącego z podsieci. Można zastąpić domyślne Azure routingu, tworząc tabelę tras i kojarzenie go do podsieci. Typowe przyczyny są routingu dla Zastępowanie domyślnego platformy Azure:
- Ponieważ chcesz, aby ruch między podsieciami przepływ NVA. Aby dowiedzieć się więcej o sposobie [skonfigurować tabele tras, aby wymusić ruchu za pośrednictwem NVA](tutorial-create-route-table-portal.md)
- Ponieważ chcesz wymusić wszystkich ruch do Internetu za pośrednictwem NVA lub lokalnymi, za pośrednictwem bramy sieci VPN platformy Azure. Wymuszenie internet ruchu lokalnego do inspekcji i rejestrowanie jest często określane jako wymuszonego tunelowania. Dowiedz się więcej o sposobie konfigurowania [wymuszone tunelowanie](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

Jeśli musisz wdrożyć niestandardowy routing, zalecane jest zapoznanie się z [routing na platformie Azure](virtual-networks-udr-overview.md).

## <a name="connectivity"></a>Łączność

Można połączyć sieć wirtualną do innych sieci wirtualnych za pomocą sieci wirtualnej komunikacji równorzędnej, lub do sieci lokalnej przy użyciu bramy sieci VPN platformy Azure.

### <a name="peering"></a>Komunikacja równorzędna

Korzystając z [sieci wirtualnej komunikacji równorzędnej](virtual-network-peering-overview.md), sieci wirtualne mogą być w tej samej lub różnych, obsługiwane regiony platformy Azure. Sieci wirtualne można w tym samym lub różnych subskrypcji platformy Azure, tak długo, jak obie subskrypcje są przypisane do tej samej dzierżawy usługi Azure Active Directory. Przed utworzeniem komunikacji równorzędnej, zalecane jest, zapoznaj się ze wszystkimi komunikacji równorzędnej [wymagań i ograniczeń](virtual-network-manage-peering.md#requirements-and-constraints). Przepustowość między zasobami w sieciach wirtualnych połączyć za pomocą jest taka sama, jak gdyby zasoby w tej samej sieci wirtualnej.

### <a name="vpn-gateway"></a>Brama sieci VPN

Można użyć Azure [bramy sieci VPN](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nawiązać sieci wirtualnej sieci lokalnej przy użyciu [sieci VPN typu lokacja lokacja](../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json), lub za pomocą dedykowanego połączenia z platformą Azure [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Możesz połączyć ze sobą równorzędna i bramy sieci VPN, aby utworzyć [gwiazdy sieci](/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json), gdzie sieci wirtualnych gwiazdy połączyć sieć wirtualną koncentratora i koncentrator łączy się z sieci lokalnej, na przykład.

### <a name="name-resolution"></a>Rozpoznawanie nazw

Zasoby w jednej sieci wirtualnej nie można rozpoznać nazwy zasobów w peered sieci wirtualnej przy użyciu platformy Azure [wbudowanych DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md). Rozpoznawanie nazw w sieci wirtualnej peered [wdrożenia serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server), lub użyj usługi Azure DNS [domenach prywatnych](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Rozpoznawanie nazw między zasobami w sieci wirtualnej i sieci lokalnej również wymaga wdrożenia serwera DNS.

## <a name="permissions"></a>Uprawnienia

Korzysta z usługi Azure [kontrola dostępu oparta na rolach](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (RBAC) do zasobów. Uprawnienia są przypisane do [zakres](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-hierarchy-and-access-inheritance) w następujących hierarchii: subskrypcji, grupy zarządzania, grupy zasobów i pojedynczego zasobu. Aby dowiedzieć się więcej na temat hierarchii, zobacz [organizowania zasobów](../azure-resource-manager/management-groups-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Aby pracować z sieci wirtualnych platformy Azure i wszystkie ich powiązanych możliwości, takich jak komunikacji równorzędnej, grup zabezpieczeń sieci punktów końcowych usługi i tabele tras, członków organizacji można przypisać do wbudowanej [właściciela](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner), [Współautora](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor), lub [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ról i następnie przypisać rolę do odpowiedniego zakresu. Jeśli chcesz przypisać uprawnienia określone dla podzbioru funkcji sieci wirtualnej, Utwórz [niestandardowej roli zabezpieczeń](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i przypisz określonych uprawnień wymaganych do [sieci wirtualnych](manage-virtual-network.md#permissions), [ podsieci i punktów końcowych usługi](virtual-network-manage-subnet.md#permissions), [interfejsy sieciowe](virtual-network-network-interface.md), [równorzędna](virtual-network-manage-peering.md#permissions), [grup zabezpieczeń sieci i aplikacji](manage-network-security-group.md#permissions), lub [tabel tras](manage-route-table.md#permissions) do roli.

## <a name="policy"></a>Zasady

Zasady usługi Azure umożliwia tworzenia, przypisywania i definicje zasad zarządzania. Definicje zasad wymusić różne zasady i wpływ nad zasobami, więc zasoby pozostają zgodne z standardów organizacji i umowy dotyczącej poziomu usług. Zasady usługi Azure uruchomienie ocenę zasobów, wyszukiwanie zasobów, które nie są zgodne z definicje zasad, do których masz. Na przykład może mieć zasady, które umożliwia tworzenie sieci wirtualnych w grupie określonego zasobu. Inne zasady może być wymagane każdej podsieci jest grupa zabezpieczeń sieci skojarzonych z nim. Zasady są oceniane oddzielnie podczas tworzenia i aktualizowania zasobów.

Zasady są stosowane do następujących hierarchii: subskrypcji, grupy zarządzania i grupy zasobów. Dowiedz się więcej o [Azure zasad](../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub wdrożyć niektóre sieci wirtualnej [szablonu zasad](policy-samples.md) próbek.
