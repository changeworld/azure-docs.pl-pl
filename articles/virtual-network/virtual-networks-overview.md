---
title: Azure Virtual Network | Microsoft Docs
description: Poznaj pojęcia związane z usługą Azure Virtual Network i jej funkcje.
services: virtual-network
documentationcenter: na
author: anavinahar
tags: azure-resource-manager
Customer intent: As someone with a basic network background that is new to Azure, I want to understand the capabilities of Azure Virtual Network, so that my Azure resources such as VMs, can securely communicate with each other, the internet, and my on-premises resources.
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2019
ms.author: anavin
ms.openlocfilehash: 22c1e3050915fc697a62862620ef492ef22f80b8
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542833"
---
# <a name="what-is-azure-virtual-network"></a>Co to jest usługa Azure Virtual Network?

Usługa Azure Virtual Network (VNet) jest podstawowym blokiem sieci prywatnej, na platformie Azure. Sieć wirtualna umożliwia wielu typom zasobów platformy Azure, takich jak Azure Virtual Machines (VM), bezpieczne komunikowanie się ze sobą internetowego i sieciami lokalnymi. Sieć wirtualna jest podobny do tradycyjnego sieci, która będzie działać w centrum danych, ale wnosi dodatkowych zalet infrastruktury platformy Azure, takie jak skalowalność, dostępność i izolację.

## <a name="vnet-concepts"></a>Pojęcia dotyczące sieci wirtualnej

- **Przestrzeń adresowa:** Podczas tworzenia sieci wirtualnej, należy określić niestandardowe przestrzeń prywatnych adresów IP przy użyciu publicznych i prywatnych adresów (RFC 1918). Platforma Azure przypisze zasobom w sieci wirtualnej prywatny adres IP z przydzielonej przestrzeni adresowej. Na przykład w przypadku wdrożenia maszyny Wirtualnej w sieci wirtualnej z przestrzenią adresów 10.0.0.0/16, maszyna wirtualna zostanie przypisany prywatny adres IP, takich jak 10.0.0.4.
- **Podsieci:** Podsieci umożliwiają segmentu sieci wirtualnej do co najmniej jedną podrzędną sieć i przydzielić części przestrzeni adresowej sieci wirtualnej w każdej podsieci. Następnie można wdrożyć zasoby platformy Azure w określonej podsieci. Podobnie jak w przypadku tradycyjnej sieci podsieci pozwalają na segment przestrzeń adresowa sieci wirtualnej na segmenty, które są odpowiednie dla sieci wewnętrznej organizacji. Zwiększa to wydajność przydziału adresu. Można zabezpieczyć zasobów w podsieci za pomocą sieciowych grup zabezpieczeń. Aby uzyskać więcej informacji, zobacz [grup zabezpieczeń](security-overview.md).
- **Regiony**: Sieć wirtualna jest ograniczone do jednego regionu/lokalizacji; Jednak wiele sieci wirtualnych z różnych regionów mogą być połączone ze sobą przy użyciu komunikacji równorzędnej sieci wirtualnej.
- **Subskrypcja:** Subskrypcja obejmuje sieć wirtualną. W każdej [subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) platformy Azure oraz w każdym [regionie](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) świadczenia usługi Azure możesz zaimplementować wiele sieci wirtualnych.

## <a name="best-practices"></a>Najlepsze praktyki

Podczas tworzenia sieci na platformie Azure należy pamiętać o następujących zasadach uniwersalnego projektu:

- Upewnij się, bez nakładających się przestrzeni adresowych. Upewnij się, ponieważ przestrzeń adresowa sieci wirtualnej (blok CIDR) nie nakładają się z Twoją organizacją w pozostałych zakresów sieci.
- Podsieci nie powinno obejmować całą przestrzenią adresową sieci wirtualnej. Planuj z wyprzedzeniem i zarezerwować kilka przestrzeni adresowej w przyszłości.
- Zalecane jest, że masz mniej dużych sieciach wirtualnych niż wiele małych sieci wirtualnych. Uniemożliwi to narzut na zarządzanie.
- Zabezpieczanie sieci wirtualnej przy użyciu sieciowych grup zabezpieczeń (NSG).

## <a name="communicate-with-the-internet"></a>Komunikacja z Internetem

Wszystkie zasoby w sieci wirtualnej mogą komunikować się ruch wychodzący do Internetu, domyślnie. Z zasobem w ruchu przychodzącym możesz komunikować się przez przypisanie publicznego adresu IP lub publicznego modułu równoważenia obciążenia. Publicznego adresu IP lub publicznego modułu równoważenia obciążenia możesz używać również do zarządzania połączeniami w ruchu wychodzącym.  Aby dowiedzieć się więcej na temat połączeń wychodzących na platformie Azure, zobacz [Połączenia wychodzące](../load-balancer/load-balancer-outbound-connections.md), [Publiczne adresy IP](virtual-network-public-ip-address.md) i [Moduł równoważenia obciążenia](../load-balancer/load-balancer-overview.md).

>[!NOTE]
>W przypadku korzystania tylko z wewnętrznej [usługi Load Balancer w warstwie Standardowa](../load-balancer/load-balancer-standard-overview.md) łączność wychodząca nie jest dostępna, dopóki nie zdefiniujesz współdziałania [połączeń wychodzących](../load-balancer/load-balancer-outbound-connections.md) z publicznym adresem IP na poziomie wystąpienia lub publicznym modułem równoważenia obciążenia.

## <a name="communicate-between-azure-resources"></a>Komunikacja pomiędzy zasobami platformy Azure

Zasoby platformy Azure komunikują się bezpiecznie ze sobą nawzajem, korzystając z jednego z następujących sposobów:

- **Za pośrednictwem sieci wirtualnej**: możesz wdrożyć maszyny wirtualne i kilka innych typów zasobów platformy Azure w sieci wirtualnej, np. środowiskach Azure App Service Environment, usłudze Azure Kubernetes Service (AKS) oraz zestawach usługi Azure Virtual Machine Scale Sets. Aby wyświetlić kompletną listę zasobów platformy Azure, które można wdrożyć w sieci wirtualnej, zobacz [Integracja sieci wirtualnej z usługą](virtual-network-for-azure-services.md).
- **Za pośrednictwem punktu końcowego usługi dla sieci wirtualnej**: możesz rozszerzyć prywatną przestrzeń adresową oraz tożsamość sieci wirtualnej na zasoby usług Azure, takie jak konta usługi Azure Storage i bazy danych Azure SQL Database, korzystając z połączenia bezpośredniego. Punkty końcowe usługi umożliwiają zabezpieczanie krytycznych zasobów usługi platformy Azure tylko do sieci wirtualnej. Aby dowiedzieć się więcej, zobacz [Omówienie punktów końcowych usługi dla sieci wirtualnej](virtual-network-service-endpoints-overview.md).
- **Za pomocą komunikacji równorzędnej sieci wirtualnych**: Sieci wirtualne możesz łączyć ze sobą, co umożliwi komunikację pomiędzy zasobami w każdej z sieci wirtualnych przy użyciu komunikacji równorzędnej sieci wirtualnych. Łączone sieci wirtualne mogą znajdować się w tym samym regionie lub w różnych regionach świadczenia usługi Azure. Aby dowiedzieć się więcej, zobacz [Komunikacja równorzędna sieci wirtualnych](virtual-network-peering-overview.md).

## <a name="communicate-with-on-premises-resources"></a>Komunikacja z zasobami lokalnymi

Komputery i sieci lokalne możesz połączyć z siecią wirtualną przy użyciu dowolnej kombinacji następujących opcji:

- **Wirtualna sieć prywatna typu punkt-lokacja:** sieć określana pomiędzy siecią wirtualną i jednym komputerem w Twojej sieci. Każdy komputer, który chce nawiązać łączność z siecią wirtualną, musi skonfigurować swoje połączenie. Ten typ połączenia jest świetny, jeśli dopiero rozpoczynasz pracę z platformą Azure. Jest też odpowiedni dla deweloperów, ponieważ wymaga niewielkich zmian w istniejącej sieci lub nie wymaga ich wcale. Komunikacja pomiędzy komputerem i siecią wirtualną jest wysyłana przez szyfrowany tunel za pośrednictwem Internetu. Aby dowiedzieć się więcej, zobacz [Sieć VPN typu punkt-lokacja](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#P2S).
- **Sieć VPN typu lokacja-lokacja:** określana pomiędzy lokalnym urządzeniem VPN i bramą Azure VPN Gateway, która jest wdrażana w sieci wirtualnej. Ten typ połączenia umożliwia dowolnym zasobom lokalnym, które uzyskają autoryzację, uzyskiwanie dostępu do sieci wirtualnej. Komunikacja między lokalnym urządzeniem VPN i bramą Azure VPN Gateway jest wysyłana przez szyfrowany tunel za pośrednictwem Internetu. Aby dowiedzieć się więcej, zobacz [Sieć VPN typu lokacja-lokacja](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti).
- **Azure ExpressRoute:** połączenie nawiązywane pomiędzy siecią i platformą Azure za pośrednictwem partnera usługi ExpressRoute. To połączenie jest prywatne. Ruch nie przechodzi przez Internet. Więcej informacji znajduje się w artykule [ExpressRoute](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ExpressRoute).

## <a name="filter-network-traffic"></a>Filtrowanie ruchu sieciowego

Ruch sieciowy pomiędzy podsieciami możesz filtrować przy użyciu jednej lub obu poniższych opcji:

- **Grupy zabezpieczeń:** sieciowe grupy zabezpieczeń i grupy zabezpieczeń aplikacji mogą zawierać wiele reguł zabezpieczeń dla ruchu przychodzącego i wychodzącego, które umożliwiają filtrowanie ruchu związanego z zasobami według źródłowych i docelowych adresów IP, portów i protokołów. Aby dowiedzieć się więcej, zobacz [Sieciowe grupy zabezpieczeń](security-overview.md#network-security-groups) i [Grupy zabezpieczeń aplikacji](security-overview.md#application-security-groups).
- **Wirtualne urządzenia sieciowe:** wirtualne urządzenie sieciowe to maszyna wirtualna wykonująca funkcję sieciową, np. funkcję zapory, optymalizacji WAN lub inną. Aby wyświetlić listę dostępnych wirtualnych urządzeń sieciowych, które możesz wdrożyć w sieci wirtualnej, zobacz witrynę [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

## <a name="route-network-traffic"></a>Routing ruchu sieciowego

Platforma Azure domyślnie kieruje ruchem pomiędzy podsieciami, połączonymi sieciami wirtualnymi, sieciami lokalnymi oraz Internetem. Możesz zaimplementować jedną lub obie poniższe opcje, aby zastąpić domyślne trasy tworzone przez platformę Azure:

- **Tabele tras:** możesz utworzyć niestandardowe tabele tras z trasami kontrolującymi przekierowywanie ruchu do każdej podsieci. Dowiedz się więcej o [tabelach tras](virtual-networks-udr-overview.md#user-defined).
- **Trasy protokołu BGP (Border Gateway Protocol):** jeśli łączysz sieć wirtualną z siecią lokalną przy użyciu bramy Azure VPN Gateway lub połączenia ExpressRoute, możesz propagować trasy lokalne BGP do sieci wirtualnych. Dowiedz się więcej o użyciu protokołu BGP z bramą [Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i usługą [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange).

## <a name="azure-vnet-limits"></a>Limity sieci wirtualnej platformy Azure

Istnieją pewne ograniczenia na liczbę zasobów platformy Azure, które można wdrożyć. Ograniczenia sieci najbardziej platformy Azure znajdują się w wartości maksymalnej. Można jednak [zwiększyć określone limity dotyczące sieci](../azure-supportability/networking-quota-requests.md) jak określono na [sieci ogranicza strony](../azure-subscription-service-limits.md#networking-limits). 

## <a name="pricing"></a>Cennik

Nie ma opłaty za korzystanie z sieci wirtualnej platformy Azure, jest bezpłatna. Standardowe opłaty są odpowiednie dla zasobów, takich jak maszyny wirtualne (VM) i innych produktów. Aby dowiedzieć się więcej, zobacz [cennik sieci wirtualnej](https://azure.microsoft.com/pricing/details/virtual-network/) i Azure [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/).

## <a name="next-steps"></a>Kolejne kroki

 Aby rozpocząć korzystanie z sieci wirtualnej, utwórz ją, wdróż w niej kilka maszyn wirtualnych i rozpocznij komunikację pomiędzy maszynami wirtualnymi. Aby dowiedzieć się więcej, zobacz przewodnik Szybki start [Tworzenie sieci wirtualnej](quick-create-portal.md).
