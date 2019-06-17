---
title: Planowanie sieci wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak planować dla sieci wirtualnych na podstawie izolacji, łącznością i wymogów dotyczących lokalizacji.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 3a4a9aea-7608-4d2e-bb3c-40de2e537200
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2018
ms.author: kumud
ms.openlocfilehash: 0a80630ffa363d2b633667d8104cc0326c4afa2e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66478592"
---
# <a name="plan-virtual-networks"></a>Planowanie sieci wirtualnej

Tworzenie sieci wirtualnej, aby eksperymentować z jest dość proste, ale jest szansa, wraz z upływem czasu do obsługi potrzeb produkcyjnych w organizacji będzie wdrożenie wielu sieci wirtualnych. Przy planowaniu, można wdrożyć sieci wirtualnych i połączyć zasoby, których potrzebujesz bardziej efektywnie. Informacje przedstawione w tym artykule jest najbardziej przydatne, jeśli już znasz sieci wirtualnych i ma pewne doświadczenie w pracy z nimi. Jeśli nie znasz sieci wirtualnych, zalecane jest przeczytanie [Omówienie usługi Virtual network](virtual-networks-overview.md).

## <a name="naming"></a>Nadawanie nazw

Wszystkie zasoby platformy Azure ma nazwy. Nazwa musi być unikatowa w obrębie zakresu, który może być inna dla każdego typu zasobu. Na przykład nazwa sieci wirtualnej musi być unikatowa w obrębie [grupy zasobów](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), ale mogą być zduplikowane w ramach [subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) lub na platformie Azure [region](https://azure.microsoft.com/regions/#services). Definiowanie konwencji nazewnictwa, która umożliwia spójne podczas nadawania nazw zasobów jest przydatne podczas zarządzania zasobami sieci w kilku wraz z upływem czasu. Aby uzyskać sugestie, zobacz [konwencje nazewnictwa](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#networking).

## <a name="regions"></a>Regiony

Wszystkie zasoby platformy Azure są tworzone w regionie platformy Azure i subskrypcji. Zasób można tworzyć tylko w sieci wirtualnej, która znajduje się w tym samym regionie i subskrypcji co zasób. Można jednak połączyć sieci wirtualne znajdujące się w różnych subskrypcjach i regionach. Aby uzyskać więcej informacji, zobacz [łączności](#connectivity). Przy podejmowaniu decyzji, które regiony wdrażać zasoby w, należy wziąć pod uwagę gdzie fizycznie znajdują się użytkownicy, zasoby:

- Osoby korzystające z zasobów zazwyczaj ma najniższe opóźnienie sieci do swoich zasobów. Aby określić względną opóźnień między określonej lokalizacji i regionami platformy Azure, zobacz [wyświetlanie względnej opóźnienia](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Czy masz wymagania dotyczące rezydencji, niezależność, zgodności i odporności danych? Jeśli tak, wybierając region pasującą do wymagań jest krytyczna. Aby uzyskać więcej informacji, zobacz [lokalizacjach geograficznych platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/).
- Jest wymagane odporności w strefach dostępności platformy Azure w ramach tego samego regionu platformy Azure dla zasobów, które możesz wdrożyć? Zasoby, takie jak maszyny wirtualne (VM) można wdrożyć w różnych strefach dostępności w ramach tej samej sieci wirtualnej. Nie wszystkie regiony platformy Azure, które jednak obsługuje stref dostępności. Aby dowiedzieć się więcej na temat stref dostępności i regionów, które je obsługują, zobacz [strefy dostępności](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="subscriptions"></a>Subscriptions

Jak wiele sieci wirtualnych, zgodnie z potrzebami w ramach każdej subskrypcji można wdrożyć do [limit](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Niektóre organizacje mają różne subskrypcje dla różnych działów, na przykład. Aby uzyskać więcej informacji i zagadnienia dotyczące subskrypcji, zobacz [nadzór subskrypcji](/azure/architecture/cloud-adoption-guide/subscription-governance#define-your-hierarchy).

## <a name="segmentation"></a>Segmentacja

Można utworzyć wiele sieci wirtualnych w jednej subskrypcji i regionu. Możesz utworzyć wiele podsieci w ramach każdej sieci wirtualnej. Zagadnienia, które należy wykonać pomóc w ustaleniu, jak wiele sieci wirtualnych i podsieci potrzebujesz:

### <a name="virtual-networks"></a>Sieci wirtualne

Sieć wirtualna jest wirtualna, izolowane część sieci publicznej platformy Azure. Każda sieć wirtualna jest dedykowany do Twojej subskrypcji. Warto wziąć pod uwagę podczas podejmowania decyzji o utworzenia jednej sieci wirtualnej lub wielu sieci wirtualnych w ramach subskrypcji:

- Czy wszelkich wymagań dotyczących zabezpieczeń w organizacji istnieją izolowania ruchu sieciowego w oddzielnych sieciach wirtualnych? Można połączyć sieci wirtualne, czy nie. Jeśli łączysz sieci wirtualne, możesz wdrożyć sieciowe urządzenie wirtualne, takie jak zapory, aby sterować przepływem ruchu między sieciami wirtualnymi. Aby uzyskać więcej informacji, zobacz [zabezpieczeń](#security) i [łączności](#connectivity).
- Czy istnieje żadnych wymagań organizacyjnych izolowania sieci wirtualne w oddzielnych [subskrypcje](#subscriptions) lub [regionów](#regions)?
- A [interfejs sieciowy](virtual-network-network-interface.md) pozwala maszynie Wirtualnej komunikację z innymi zasobami. Każdy interfejs sieciowy ma co najmniej jeden prywatnych adresów IP przypisanych do niego. Jak wiele interfejsów sieciowych i [prywatnych adresów IP](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) są wymagane w sieci wirtualnej? Istnieją [limity](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) do liczby interfejsów sieciowych i prywatnych adresów IP używanych w ramach sieci wirtualnej.
- Czy chcesz połączyć sieć wirtualną z inną siecią wirtualną lub siecią lokalną? Można połączyć kilka sieci wirtualne do siebie lub sieciami lokalnymi, ale innych nie. Aby uzyskać więcej informacji, zobacz [łączności](#connectivity). Każdej sieci wirtualnej, która Połącz z inną siecią wirtualną lub siecią lokalną, musi mieć unikatową przestrzeń adresowa. Każda sieć wirtualna ma jeden lub więcej zakresów adresów publicznych lub prywatnych przypisane do jego przestrzeni adresowej. Zakres adresów jest określony format classless internet domeny routingu (CIDR), takie jak 10.0.0.0/16. Dowiedz się więcej o [zakresy adresów](manage-virtual-network.md#add-or-remove-an-address-range) dla sieci wirtualnych.
- Czy masz wszystkie wymagania w zakresie zasobów organizacyjnych administracji w różnych sieciach wirtualnych? Jeśli tak, może być oddzielisz zasoby do oddzielnych sieci wirtualnej, aby uprościć [przypisanie uprawnień](#permissions) do osób w Twojej organizacji lub przypisać różnych zasad do różnych sieci wirtualnych.
- Podczas wdrażania niektórych zasobów usługi platformy Azure w sieci wirtualnej, tworzą własne sieci wirtualnej. Aby ustalić, czy usługi platformy Azure tworzy własną sieć wirtualną, zapoznaj się z informacjami o każdym [usługi platformy Azure, które można wdrożyć w sieci wirtualnej](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network).

### <a name="subnets"></a>Podsieci

Sieć wirtualną można podzielić na co najmniej jednej podsieci do [limity](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Rzeczy, które należy wziąć pod uwagę podczas podejmowania decyzji, czy należy utworzyć jedną podsieć lub wielu sieci wirtualnych w ramach subskrypcji:

- Każda podsieć musi mieć unikatowy zakres adresów, określone w formacie CIDR w przestrzeni adresowej sieci wirtualnej. Zakres adresów nie może nakładać się przy użyciu innych podsieci w sieci wirtualnej.
- Jeśli planujesz wdrażanie niektórych zasobów usługi platformy Azure w sieci wirtualnej, ich może wymagają, lub utworzyć swojej własnej podsieci, więc musi być wystarczającej ilości nieprzydzielonego miejsca na ich nie skonfigurują. Aby ustalić, czy usługi platformy Azure tworzy własnej podsieci, zapoznaj się z informacjami o każdym [usługi platformy Azure, które można wdrożyć w sieci wirtualnej](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Na przykład jeśli łączysz sieć wirtualną z siecią lokalną za pomocą bramy sieci VPN platformy Azure, sieć wirtualna musi mieć dedykowanej podsieci bramy. Dowiedz się więcej o [podsieci bramy](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub).
- Platforma Azure kieruje ruchem sieciowym między wszystkich podsieci w sieci wirtualnej, domyślnie. Można zastąpić domyślne platformy Azure, routingu, aby zapobiec Azure routing między podsieciami lub do kierowania ruchem między podsieciami za pomocą wirtualnego urządzenia sieciowego, na przykład. Jeśli potrzebujesz, aby ten ruch między zasobami w taki sam przepływ w sieci wirtualnej za pośrednictwem wirtualnego urządzenia sieciowego (WUS), należy wdrożyć zasoby do różnych podsieci. Dowiedz się więcej w [zabezpieczeń](#security).
- Można ograniczyć dostęp do zasobów platformy Azure, takie jak konto usługi Azure storage lub Azure SQL database do określonych podsieci z punktem końcowym usługi sieci wirtualnej. Ponadto możesz odmówić dostępu do zasobów z Internetu. Możesz utworzyć wiele podsieci i włączanie punktu końcowego usługi dla niektórych podsieci, ale innych nie. Dowiedz się więcej o [punkty końcowe usługi](virtual-network-service-endpoints-overview.md), i zasobów platformy Azure można włączyć je w celu.
- Możesz skojarzyć zero lub jedna grupa zabezpieczeń sieci w każdej podsieci w sieci wirtualnej. Możesz skojarzyć taki sam lub inny, sieciowe grupy zabezpieczeń w każdej podsieci. Każda grupa zabezpieczeń sieci zawiera zasady, które blokują lub zezwalają na ruch do i z źródeł i miejsc docelowych. Dowiedz się więcej o [sieciowe grupy zabezpieczeń](#traffic-filtering).

## <a name="security"></a>Bezpieczeństwo

Można filtrować ruch sieciowy do i z zasobami w sieci wirtualnej przy użyciu sieciowych grup zabezpieczeń i wirtualnych urządzeń sieciowych. Można kontrolować, jak platforma Azure kieruje ruch z podsieci. Można również ograniczyć, kto w organizacji można pracować z zasobami w sieciach wirtualnych.

### <a name="traffic-filtering"></a>Filtrowanie ruchu

- Można filtrować ruch sieciowy między zasobami w sieci wirtualnej przy użyciu sieciowej grupy zabezpieczeń, urządzenia WUS, które filtruje ruchu sieciowego i / lub. Aby wdrożyć urządzenie WUS, takimi jak zapora, do filtrowania ruchu sieciowego, zobacz [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1). Za pomocą wirtualnego urządzenia Sieciowego, można również utworzyć trasy niestandardowe do kierowania ruchu z podsieci do urządzenia WUS. Dowiedz się więcej o [routing ruchu](#traffic-routing).
- Sieciowa grupa zabezpieczeń zawiera kilka domyślnych reguł zabezpieczeń, które blokują lub zezwalają na ruch do lub z zasobów. Sieciowa grupa zabezpieczeń może być skojarzona z interfejsem sieciowym i/lub podsieci, w której znajduje się interfejs sieciowy. Aby uprościć zarządzanie zasadami zabezpieczeń, zalecane jest, aby skojarzyć sieciową grupę zabezpieczeń do poszczególnych podsieci, a nie interfejsy sieciowe poszczególnych w obrębie podsieci, jeśli to możliwe.
- Jeśli zasady zabezpieczeń zastosowanych do nich różnych maszyn wirtualnych w podsieci, można skojarzyć interfejsu sieciowego w maszynie Wirtualnej na co najmniej jedną grupę zabezpieczeń aplikacji. W jego źródło i miejsce docelowe, reguły zabezpieczeń można określić grupy zabezpieczeń aplikacji. Tej reguły następnie dotyczy tylko do interfejsów sieciowych, które są członkami grupy zabezpieczeń aplikacji. Dowiedz się więcej o [sieciowe grupy zabezpieczeń](security-overview.md) i [grupy zabezpieczeń aplikacji](security-overview.md#application-security-groups).
- Platforma Azure utworzy domyślną kilka reguł zabezpieczeń w każdej grupie zabezpieczeń sieci. Jedna domyślna reguła zezwala na cały ruch przepływ między wszystkimi zasobami w sieci wirtualnej. Aby zmienić to zachowanie, należy użyć zabezpieczeń sieci groups, niestandardowego routingu do kierowania ruchu do urządzenia WUS i / lub. Zalecane jest, zapoznaj się ze wszystkimi Azure [domyślnych regułach zabezpieczeń](security-overview.md#default-security-rules) i zrozumieć, jak reguły sieciowej grupy zabezpieczeń są stosowane do zasobu.

Możesz wyświetlić przykładowe projekty do implementowania sieci obwodowej (DMZ) między platformą Azure i z Internetu za pomocą [urządzenie WUS](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

### <a name="traffic-routing"></a>Routing ruchu

Platforma Azure tworzy kilka tras domyślnych dla ruchu wychodzącego z podsieci. Można zastąpić domyślne platformy Azure routing, tworząc tabelę tras i kojarząc ją z podsiecią. Typowe przyczyny są routingu Zastępowanie domyślnego platformy Azure:
- Ponieważ chcesz, aby ruch pomiędzy podsieciami, które będą przepływać za pośrednictwem urządzenia NVA. Aby dowiedzieć się więcej na temat [skonfigurować tabele tras w celu wymuszania ruchu za pośrednictwem urządzenia NVA](tutorial-create-route-table-portal.md).
- Ponieważ chcesz wymusić całego ruchu skierowanego do Internetu za pośrednictwem wirtualnego urządzenia Sieciowego lub lokalnie, za pośrednictwem bramy sieci VPN platformy Azure. Wymuszenie internet ruchu środowiska lokalnego do inspekcji i rejestrowania jest często określany mianem wymuszonego tunelowania. Dowiedz się więcej o sposobie konfigurowania [wymuszonego tunelowania](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

Jeśli musisz zaimplementować niestandardowy routing, zalecane jest, należy zapoznać się z [routing na platformie Azure](virtual-networks-udr-overview.md).

## <a name="connectivity"></a>Łączność

Możesz połączyć sieci wirtualnej z innymi sieciami wirtualnymi za pomocą komunikacji równorzędnej sieci wirtualnej lub z siecią lokalną za pomocą bramy sieci VPN platformy Azure.

### <a name="peering"></a>Komunikacja równorzędna

Korzystając z [wirtualne sieci równorzędne](virtual-network-peering-overview.md), sieci wirtualne mogą znajdować się w tej samej lub różnych, obsługiwanych regionach platformy Azure. Sieci wirtualne mogą być w tej samej lub innej subskrypcji platformy Azure (nawet subskrypcje należące do różnych dzierżaw usługi Azure Active Directory). Zanim utworzysz komunikację równorzędną, zalecane jest, zapoznaj się ze wszystkimi komunikacji równorzędnej [wymagania i ograniczenia](virtual-network-manage-peering.md#requirements-and-constraints). Przepustowość między zasobami w sieciach wirtualnych równorzędne w tym samym regionie jest taki sam, jakby zasoby były w tej samej sieci wirtualnej.

### <a name="vpn-gateway"></a>Brama sieci VPN

Można użyć platformy Azure [bramy sieci VPN](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) połączyć sieć wirtualną z siecią lokalną z wykorzystaniem [sieci VPN typu lokacja lokacja](../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json), lub za pomocą dedykowanego połączenia z platformą Azure [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Można połączyć komunikacji równorzędnej i bramy sieci VPN, aby utworzyć [sieci typu gwiazda](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json), gdzie sieci wirtualne będące szprychami nawiązać połączenie z sieci wirtualnej koncentratora i Centrum łączy się z siecią lokalną, na przykład.

### <a name="name-resolution"></a>Rozpoznawanie nazw

Zasoby w jednej sieci wirtualnej nie można rozpoznać nazwy zasobów w wirtualnej sieci równorzędnej przy użyciu platformy Azure [wbudowanych DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md). Rozpoznawanie nazw w równorzędnej sieci wirtualnej [wdrażanie własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server), lub za pomocą usługi Azure DNS [domen prywatnych](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Rozpoznawanie nazw między zasobami w sieci wirtualnej i sieciami lokalnymi wymaga również wdrażanie własnego serwera DNS.

## <a name="permissions"></a>Uprawnienia

Korzysta z usługi Azure [kontrola dostępu oparta na rolach](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (RBAC) do zasobów. Uprawnienia są przypisywane [zakres](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope) w następującej hierarchii: Subskrypcji, grupy zarządzania, grupa zasobów i poszczególnych zasobów. Aby dowiedzieć się więcej na temat hierarchii, zobacz [organizowanie zasobów](../azure-resource-manager/management-groups-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Aby pracować z sieciami wirtualnymi platformy Azure i wszystkie ich powiązane funkcje, takie jak komunikacja równorzędna, sieciowe grupy zabezpieczeń, punkty końcowe usługi i tabel tras, można przypisać członków organizacji do wbudowanej [właściciela](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner), [Współautor](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor), lub [Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) role i następnie przypisz rolę do wybranego zakresu. Jeśli chcesz przypisać określone uprawnienia do podzbioru funkcji sieci wirtualnej, Utwórz [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i przypisz określonych uprawnień wymaganych do [sieci wirtualnych](manage-virtual-network.md#permissions), [ podsieci i punktów końcowych usługi](virtual-network-manage-subnet.md#permissions), [interfejsy sieciowe](virtual-network-network-interface.md#permissions), [komunikacji równorzędnej](virtual-network-manage-peering.md#permissions), [grup zabezpieczeń sieci i aplikacji](manage-network-security-group.md#permissions), lub [tabele tras](manage-route-table.md#permissions) do roli.

## <a name="policy"></a>Zasady

Usługa Azure Policy umożliwia tworzenie i przypisywanie oraz Zarządzanie definicjami zasad. Definicje zasad wymuszają różne reguły dotyczące zasobów, dzięki czemu zasoby pozostają zgodne ze standardami organizacji i umowy dotyczące poziomu usług. Usługa Azure Policy przeprowadza ocenę zasobów, skanowanie w poszukiwaniu zasobów, które nie są zgodne z definicjami zasad, do których masz. Na przykład można zdefiniować i zastosuj zasadę, która umożliwia tworzenie sieci wirtualnych w określonej grupie zasobów lub regionie. Inne zasady mogą wymagać, że każdej podsieci ma sieciową grupę zabezpieczeń skojarzoną do niego. Zasady są następnie oceniane podczas tworzenia i aktualizowania zasobów.

Zasady zostaną zastosowane do następującej hierarchii: Subskrypcją, grupą zarządzania a grupą zasobów. Dowiedz się więcej o [usługa Azure policy](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub wdrożyć niektóre sieć wirtualną [szablonu zasad](policy-samples.md) przykładów.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o wszystkich zadań, ustawienia i opcje dla [sieci wirtualnej](manage-virtual-network.md), [podsieci i usługa punkt końcowy](virtual-network-manage-subnet.md), [interfejs sieciowy](virtual-network-network-interface.md), [komunikacji równorzędnej](virtual-network-manage-peering.md), [grupy zabezpieczeń sieci i aplikacji](manage-network-security-group.md), lub [tabeli tras](manage-route-table.md).
