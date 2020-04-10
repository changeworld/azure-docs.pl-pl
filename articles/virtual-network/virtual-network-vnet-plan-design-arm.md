---
title: Planowanie sieci wirtualnych platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak zaplanować sieci wirtualne na podstawie wymagań dotyczących izolacji, łączności i lokalizacji.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2020
ms.author: kumud
ms.openlocfilehash: 95dd7be118e869aed02bb55918ab0cefa0d05d03
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998873"
---
# <a name="plan-virtual-networks"></a>Planowanie sieci wirtualnych

Tworzenie sieci wirtualnej do eksperymentowania jest dość łatwe, ale są szanse, że z czasem wdrożysz wiele sieci wirtualnych, aby zaspokoić potrzeby produkcyjne organizacji. Dzięki niektórym planom będzie można wdrażać sieci wirtualne i efektywniej łączyć potrzebne zasoby. Informacje zawarte w tym artykule są najbardziej przydatne, jeśli znasz już sieci wirtualne i masz pewne doświadczenie w pracy z nimi. Jeśli nie znasz sieci wirtualnych, zaleca się zapoznanie się z [omówieniem sieci wirtualnej](virtual-networks-overview.md).

## <a name="naming"></a>Nazewnictwo

Wszystkie zasoby platformy Azure mają nazwę. Nazwa musi być unikatowa w zakresie, które mogą się różnić dla każdego typu zasobu. Na przykład nazwa sieci wirtualnej musi być unikatowa w [grupie zasobów,](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)ale może być zduplikowana w ramach [subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) lub [regionu](https://azure.microsoft.com/regions/#services)platformy Azure . Definiowanie konwencji nazewnictwa, której można używać konsekwentnie podczas nazewnictwa zasobów, jest przydatne podczas zarządzania kilkoma zasobami sieciowymi w czasie. Aby uzyskać sugestie, zobacz [Konwencje nazewnictwa](../azure-resource-manager/management/resource-name-rules.md#microsoftnetwork).

## <a name="regions"></a>Regiony

Wszystkie zasoby platformy Azure są tworzone w regionie platformy Azure i subskrypcji. Zasób można utworzyć tylko w sieci wirtualnej, która istnieje w tym samym regionie i subskrypcji jako zasób. Można jednak połączyć sieci wirtualne, które istnieją w różnych subskrypcji i regionów. Aby uzyskać więcej informacji, zobacz [łączność](#connectivity). Przy podejmowaniu decyzji, w którym regionie(-ach) należy wdrożyć zasoby, należy wziąć pod uwagę, gdzie fizycznie znajdują się konsumenci zasobów:

- Konsumenci zasobów zazwyczaj chcą najniższe opóźnienie sieci do swoich zasobów. Aby określić względne opóźnienia między określoną lokalizacją a regionami platformy Azure, zobacz [Wyświetlanie względnych opóźnień](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Czy masz wymagania dotyczące rezydencji danych, suwerenności, zgodności lub odporności? Jeśli tak, wybór regionu, który jest wyrównany do wymagań jest krytyczna. Aby uzyskać więcej informacji, zobacz [obszary geograficzne platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/).
- Czy potrzebujesz odporności w strefach dostępności platformy Azure w tym samym regionie platformy Azure dla wdrażanych zasobów? Zasoby, takie jak maszyny wirtualne (VM) można wdrożyć w różnych strefach dostępności w tej samej sieci wirtualnej. Nie wszystkie regiony platformy Azure obsługują jednak strefy dostępności. Aby dowiedzieć się więcej o strefach dostępności i regionach, które je obsługują, zobacz [Strefy dostępności](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="subscriptions"></a>Subskrypcje

Można wdrożyć dowolną liczbę sieci wirtualnych, ile jest to wymagane w ramach każdej subskrypcji, do [limitu.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) Niektóre organizacje mają różne subskrypcje dla różnych działów, na przykład. Aby uzyskać więcej informacji i zagadnień dotyczących subskrypcji, zobacz [Zarządzanie subskrypcjami](/azure/cloud-adoption-framework/reference/migration-with-enterprise-scaffold#define-your-hierarchy).

## <a name="segmentation"></a>Segmentacja

Można utworzyć wiele sieci wirtualnych na subskrypcję i na region. Można utworzyć wiele podsieci w każdej sieci wirtualnej. Następujące zagadnienia pomagają określić liczbę sieci wirtualnych i podsieci, których potrzebujesz:

### <a name="virtual-networks"></a>Sieci wirtualne

Sieć wirtualna jest wirtualną, odizolowaną częścią sieci publicznej platformy Azure. Każda sieć wirtualna jest dedykowana twojej subskrypcji. Rzeczy, które należy wziąć pod uwagę przy podejmowaniu decyzji, czy utworzyć jedną sieć wirtualną lub wiele sieci wirtualnych w ramach subskrypcji:

- Czy istnieją jakieś wymagania dotyczące zabezpieczeń organizacyjnych dotyczące izolowania ruchu do oddzielnych sieci wirtualnych? Można połączyć sieci wirtualne, czy nie. Po podłączeniu sieci wirtualnych można zaimplementować wirtualne urządzenie sieciowe, takie jak zapora, w celu kontrolowania przepływu ruchu między sieciami wirtualnymi. Aby uzyskać więcej informacji, zobacz [bezpieczeństwo](#security) i [łączność](#connectivity).
- Czy istnieją jakieś wymagania organizacyjne dotyczące izolowania sieci wirtualnych do [oddzielnych subskrypcji](#subscriptions) lub [regionów?](#regions)
- [Interfejs sieciowy](virtual-network-network-interface.md) umożliwia maszynie wirtualnej komunikowanie się z innymi zasobami. Każdy interfejs sieciowy ma przypisany jeden lub więcej prywatnych adresów IP. Ile interfejsów sieciowych i [prywatnych adresów IP](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) wymaga w sieci wirtualnej? Istnieją [ograniczenia liczby](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) interfejsów sieciowych i prywatnych adresów IP, które można mieć w sieci wirtualnej.
- Czy chcesz połączyć sieć wirtualną z inną siecią wirtualną lub siecią lokalną? Można połączyć niektóre sieci wirtualne ze sobą lub sieciami lokalnymi, ale nie z innymi. Aby uzyskać więcej informacji, zobacz [łączność](#connectivity). Każda sieć wirtualna, która łączy się z inną siecią wirtualną lub siecią lokalną, musi mieć unikatową przestrzeń adresową. Każda sieć wirtualna ma jeden lub więcej publicznych lub prywatnych zakresów adresów przypisanych do jej przestrzeni adresowej. Zakres adresów jest określony w formacie CIDR (bezklasowy routing domeny internetowej), na przykład 10.0.0.0/16. Dowiedz się więcej o [zakresach adresów](manage-virtual-network.md#add-or-remove-an-address-range) sieci wirtualnych.
- Czy masz jakieś wymagania dotyczące administracji organizacyjnej dotyczące zasobów w różnych sieciach wirtualnych? Jeśli tak, można podzielić zasoby na oddzielną sieć wirtualną, aby uprościć [przypisywanie uprawnień](#permissions) osobom w organizacji lub przypisać różne zasady do różnych sieci wirtualnych.
- Po wdrożeniu niektórych zasobów usługi platformy Azure w sieci wirtualnej tworzą one własną sieć wirtualną. Aby ustalić, czy usługa platformy Azure tworzy własną sieć wirtualną, zobacz informacje dotyczące każdej [usługi platformy Azure, którą można wdrożyć w sieci wirtualnej.](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)

### <a name="subnets"></a>Podsieci

Sieć wirtualną można podzielić na jedną lub więcej podsieci do [granic możliwości.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) Rzeczy, które należy wziąć pod uwagę przy podejmowaniu decyzji o utworzeniu jednej podsieci lub wielu sieci wirtualnych w ramach subskrypcji:

- Każda podsieć musi mieć unikatowy zakres adresów, określony w formacie CIDR, w przestrzeni adresowej sieci wirtualnej. Zakres adresów nie może pokrywać się z innymi podsieciami w sieci wirtualnej.
- Jeśli planujesz wdrożyć niektóre zasoby usługi platformy Azure w sieci wirtualnej, mogą one wymagać lub utworzyć własną podsieć, więc musi być wystarczająco dużo nieprzydzielonego miejsca dla nich to zrobić. Aby ustalić, czy usługa platformy Azure tworzy własną podsieć, zobacz informacje dotyczące każdej [usługi platformy Azure, którą można wdrożyć w sieci wirtualnej.](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) Na przykład jeśli łączysz sieć wirtualną z siecią lokalną przy użyciu bramy sieci VPN platformy Azure, sieć wirtualna musi mieć dedykowaną podsieć bramy. Dowiedz się więcej o [podsieciach bramy](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub).
- Platforma Azure domyślnie kieruje ruch sieciowy między wszystkimi podsieciami w sieci wirtualnej. Możesz zastąpić domyślny routing platformy Azure, aby zapobiec routingowi platformy Azure między podsieciami lub na przykład kierować ruch między podsieciami za pośrednictwem wirtualnego urządzenia sieciowego. Jeśli wymagany jest ruch między zasobami w tej samej sieci wirtualnej przepływu za pośrednictwem sieciowego urządzenia wirtualnego (NVA), należy wdrożyć zasoby do różnych podsieci. Dowiedz się więcej o [bezpieczeństwie](#security).
- Można ograniczyć dostęp do zasobów platformy Azure, takich jak konto magazynu platformy Azure lub bazy danych SQL platformy Azure, do określonych podsieci z punktem końcowym usługi sieci wirtualnej. Ponadto można odmówić dostępu do zasobów z Internetu. Można utworzyć wiele podsieci i włączyć punkt końcowy usługi dla niektórych podsieci, ale nie innych. Dowiedz się więcej o [punktach końcowych usługi](virtual-network-service-endpoints-overview.md)i zasobach platformy Azure, dla których można je włączyć.
- Zer lub jedną sieciową grupą zabezpieczeń można skojarzyć z każdą podsiecią w sieci wirtualnej. Tę samą lub inną grupę zabezpieczeń sieci można skojarzyć z każdą podsiecią. Każda grupa zabezpieczeń sieci zawiera reguły, które zezwalają lub odmawiają ruchu do i ze źródeł i miejsc docelowych. Dowiedz się więcej o [sieciowych grupach zabezpieczeń](#traffic-filtering).

## <a name="security"></a>Zabezpieczenia

Ruch sieciowy można filtrować do i z zasobów w sieci wirtualnej przy użyciu sieciowych grup zabezpieczeń i wirtualnych urządzeń sieciowych. Można kontrolować sposób, w jaki platforma Azure kieruje ruch z podsieci. Można również ograniczyć, kto w organizacji może pracować z zasobami w sieciach wirtualnych.

### <a name="traffic-filtering"></a>Filtrowanie ruchu

- Ruch sieciowy między zasobami w sieci wirtualnej można filtrować przy użyciu sieciowej grupy zabezpieczeń, urządzenia wuszyniu urządzenia wuszy, który filtruje ruch sieciowy, lub obu tych czynników. Aby wdrożyć urządzenie WUS, takie jak zapora, w celu filtrowania ruchu sieciowego, zobacz [portal Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1). Korzystając z urządzenia WUS, można również utworzyć trasy niestandardowe do kierowania ruchu z podsieci do urządzenia WUS. Dowiedz się więcej o [routingu ruchu](#traffic-routing).
- Sieciowa grupa zabezpieczeń zawiera kilka domyślnych reguł zabezpieczeń, które zezwalają lub odmawiają ruchu do lub z zasobów. Sieciową grupę zabezpieczeń można skojarzyć z interfejsem sieciowym, podsiecią, w jakiej znajduje się interfejs sieciowy, lub z obiema. Aby uprościć zarządzanie regułami zabezpieczeń, zaleca się skojarzenie sieciowej grupy zabezpieczeń z poszczególnymi podsieciami, a nie z poszczególnymi interfejsami sieciowymi w podsieci, gdy tylko jest to możliwe.
- Jeśli różne maszyny wirtualne w podsieci wymagają różnych reguł zabezpieczeń zastosowanych do nich, można skojarzyć interfejs sieciowy na maszynie wirtualnej z co najmniej jedną grupą zabezpieczeń aplikacji. Reguła zabezpieczeń może określić grupę zabezpieczeń aplikacji w źródle, miejscu docelowym lub obu. Ta reguła ma zastosowanie tylko do interfejsów sieciowych, które są członkami grupy zabezpieczeń aplikacji. Dowiedz się więcej o [sieciowych grupach zabezpieczeń](security-overview.md) i [grupach zabezpieczeń aplikacji](security-overview.md#application-security-groups).
- Platforma Azure tworzy kilka domyślnych reguł zabezpieczeń w ramach każdej sieciowej grupy zabezpieczeń. Jedna reguła domyślna umożliwia przepływ całego ruchu między wszystkimi zasobami w sieci wirtualnej. Aby zastąpić to zachowanie, użyj sieciowych grup zabezpieczeń, routingu niestandardowego, aby kierować ruch do urządzenia wust lub obu tych. Zaleca się zapoznanie się ze wszystkimi [domyślnymi regułami zabezpieczeń](security-overview.md#default-security-rules) platformy Azure i zrozumienie, jak reguły sieciowej grupy zabezpieczeń są stosowane do zasobu.

Można wyświetlić przykładowe projekty implementacji sieci obwodowej (znanej również jako strefa DMZ) między platformą Azure a Internetem przy użyciu urządzenia [WUS.](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json)

### <a name="traffic-routing"></a>Routing ruchu

Platforma Azure tworzy kilka tras domyślnych dla ruchu wychodzącego z podsieci. Routing domyślny platformy Azure można zastąpić, tworząc tabelę marszrut i kojarząc ją z podsiecią. Typowe przyczyny zastąpienia routingu domyślnego platformy Azure to:
- Ponieważ ruch między podsieciami przepływa przez urządzenia WUS. Aby dowiedzieć się więcej o [konfigurowaniu tabel tras w celu wymuszania ruchu przez urządzenie WUS](tutorial-create-route-table-portal.md).
- Ponieważ chcesz wymusić cały ruch związany z Internetem za pośrednictwem urządzenia WUS lub lokalnie, za pośrednictwem bramy sieci VPN platformy Azure. Wymuszanie lokalnego ruchu internetowego w celu kontroli i rejestrowania jest często określane jako wymuszone tunelowanie. Dowiedz się więcej o konfigurowaniu [tunelowania wymuszonego.](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json)

Jeśli chcesz zaimplementować routing niestandardowy, zaleca się zapoznanie się z [routingiem na platformie Azure.](virtual-networks-udr-overview.md)

## <a name="connectivity"></a>Łączność

Sieć wirtualną można połączyć z innymi sieciami wirtualnymi przy użyciu komunikacji równorzędnej w sieci wirtualnej lub do sieci lokalnej przy użyciu bramy sieci VPN platformy Azure.

### <a name="peering"></a>Komunikacja równorzędna

Podczas korzystania z [komunikacji równorzędnej sieci wirtualnej](virtual-network-peering-overview.md)sieci wirtualne mogą znajdować się w tych samych lub różnych obsługiwanych regionach platformy Azure. Sieci wirtualne mogą znajdować się w tych samych lub różnych subskrypcjach platformy Azure (nawet subskrypcje należące do różnych dzierżaw usługi Azure Active Directory). Przed utworzeniem komunikacji równorzędnej zaleca się zapoznanie się ze wszystkimi [wymaganiami i ograniczeniami](virtual-network-manage-peering.md#requirements-and-constraints)dotyczącymi komunikacji równorzędnej. Przepustowość między zasobami w sieciach wirtualnych równorzędnych w tym samym regionie jest taka sama, jak gdyby zasoby znajdowały się w tej samej sieci wirtualnej.

### <a name="vpn-gateway"></a>Brama sieci VPN

Za pomocą bramy [sieci VPN](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) platformy Azure można połączyć sieć wirtualną z siecią lokalną za pomocą sieci VPN typu [lokacja lokacja](../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json)lub przy użyciu dedykowanego połączenia z usługą Azure [ExpressRoute.](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

Można połączyć komunikację równorzędną i bramę sieci VPN, aby utworzyć [sieci koncentratora i szprychy,](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)w których sieci wirtualne szprychowe łączą się z siecią wirtualną koncentratora, a koncentrator łączy się na przykład z siecią lokalną.

### <a name="name-resolution"></a>Rozpoznawanie nazw

Zasoby w jednej sieci wirtualnej nie mogą rozpoznać nazw zasobów w sieci wirtualnej równorzędnej przy użyciu [wbudowanego systemu DNS platformy](virtual-networks-name-resolution-for-vms-and-role-instances.md)Azure. Aby rozpoznać nazwy w sieci wirtualnej równorzędnej, [należy wdrożyć własny serwer DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)lub użyć domen [prywatnych](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)usługi Azure DNS . Rozpoznawanie nazw między zasobami w sieci wirtualnej i sieci lokalnej wymaga również wdrożenia własnego serwera DNS.

## <a name="permissions"></a>Uprawnienia

Platforma Azure korzysta z [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (RBAC) do zasobów. Uprawnienia są przypisywane do [zakresu](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope) w następującej hierarchii: grupa zarządzania, subskrypcja, grupa zasobów i pojedynczy zasób. Aby dowiedzieć się więcej o hierarchii, zobacz [Organizowanie zasobów](../azure-resource-manager/management-groups-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Aby pracować z sieciami wirtualnymi platformy Azure i wszystkimi powiązanymi z nimi funkcjami, takimi jak komunikacja równorzędna, sieciowe grupy zabezpieczeń, punkty końcowe usług i tabele tras, można przypisać członków instytucji do wbudowanych ról [Właściciel,](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner) [Współautor](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor)lub [Współautor sieci,](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) a następnie przypisać rolę do odpowiedniego zakresu. Jeśli chcesz przypisać określone uprawnienia dla podzbioru możliwości sieci wirtualnej, utwórz [rolę niestandardową](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i przypisz określone uprawnienia wymagane dla [sieci wirtualnych,](manage-virtual-network.md#permissions) [podsieci i punktów końcowych usługi,](virtual-network-manage-subnet.md#permissions) [interfejsów sieciowych,](virtual-network-network-interface.md#permissions) [komunikacji równorzędnej,](virtual-network-manage-peering.md#permissions) [grup zabezpieczeń sieci i aplikacji](manage-network-security-group.md#permissions)lub [tabele tras](manage-route-table.md#permissions) do roli.

## <a name="policy"></a>Zasady

Usługa Azure Policy umożliwia tworzenie, przypisywanie i zarządzanie definicjami zasad. Definicje zasad wymuszają różne reguły dotyczące zasobów, dzięki czemu zasoby pozostają zgodne ze standardami organizacyjnymi i umowami dotyczącymi poziomu usług. Usługa Azure Policy uruchamia ocenę zasobów, skanowanie w poszukiwaniu zasobów, które nie są zgodne z definicjami zasad, które masz. Na przykład można zdefiniować i zastosować zasadę, która umożliwia tworzenie sieci wirtualnych tylko w określonej grupie zasobów lub regionie. Inne zasady mogą wymagać, aby w każdej podsieci była skojarzona grupa zabezpieczeń sieciowych. Zasady są następnie oceniane podczas tworzenia i aktualizowania zasobów.

Zasady są stosowane do następującej hierarchii: grupa zarządzania, subskrypcja i grupa zasobów. Dowiedz się więcej o [zasadach platformy Azure](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub wdrażaj przykłady [szablonów zasad](policy-samples.md) sieci wirtualnej.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o wszystkich zadaniach, ustawieniach i opcjach [sieci wirtualnej,](manage-virtual-network.md) [podsieci i punktu końcowego usługi,](virtual-network-manage-subnet.md)interfejsu [sieciowego, komunikacji równorzędnej,](virtual-network-manage-peering.md) [grupy zabezpieczeń sieci i aplikacji](manage-network-security-group.md)lub [tabeli tras.](manage-route-table.md) [network interface](virtual-network-network-interface.md)
