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
ms.openlocfilehash: 3b908406c8717d2fa8834bc4dff1bcd27ec4761f
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241423"
---
# <a name="what-is-azure-virtual-network"></a>Co to jest usługa Azure Virtual Network?

Sieć wirtualna platformy Azure (VNet) jest podstawowym elementem konstrukcyjnym sieci prywatnej na platformie Azure. Sieć wirtualna umożliwia wiele typów zasobów platformy Azure, takich jak maszyny wirtualne platformy Azure (VM), aby bezpiecznie komunikować się ze sobą, Internet i sieci lokalne. Sieć wirtualna jest podobna do tradycyjnej sieci, która będzie działać we własnym centrum danych, ale zapewnia dodatkowe korzyści infrastruktury platformy Azure, takie jak skalowanie, dostępność i izolacja.

## <a name="vnet-concepts"></a>Pojęcia sieci wirtualnej

- **Przestrzeń adresowa:** Podczas tworzenia sieci wirtualnej należy określić niestandardową prywatną przestrzeń adresową IP przy użyciu adresów publicznych i prywatnych (RFC 1918). Platforma Azure przypisze zasobom w sieci wirtualnej prywatny adres IP z przydzielonej przestrzeni adresowej. Na przykład jeśli wdrożysz maszynę wirtualną w sieci wirtualnej z przestrzenią adresową 10.0.0.0/16, maszyna wirtualna zostanie przypisana prywatny adres IP, taki jak 10.0.0.4.
- **Podsieci:** Podsieci umożliwiają segmentowanie sieci wirtualnej w co najmniej jedną podsieci i przydzielanie części przestrzeni adresowej sieci wirtualnej do każdej podsieci. Następnie można wdrożyć zasoby platformy Azure w określonej podsieci. Podobnie jak w tradycyjnej sieci, podsieci umożliwiają segmentowanie przestrzeni adresowej sieci wirtualnej na segmenty odpowiednie dla sieci wewnętrznej organizacji. Zwiększa to również efektywność alokacji adresów. Zasoby w podsieciach można zabezpieczyć za pomocą sieciowych grup zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Grupy zabezpieczeń](security-overview.md).
- **Regiony:** Sieci wirtualnej jest objęty zakresem do jednego regionu/lokalizacji; jednak wiele sieci wirtualnych z różnych regionów mogą być połączone ze sobą za pomocą komunikacji równorzędnej sieci wirtualnej.
- **Subskrypcja:** Sieci wirtualnej jest zakres subskrypcji. W każdej [subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) platformy Azure oraz w każdym [regionie](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) świadczenia usługi Azure możesz zaimplementować wiele sieci wirtualnych.

## <a name="best-practices"></a>Najlepsze rozwiązania

Podczas tworzenia sieci na platformie Azure należy pamiętać o następujących uniwersalnych zasadach projektowania:

- Upewnij się, że nie nakładające się przestrzenie adresowe. Upewnij się, że przestrzeń adresowa sieci wirtualnej (blok CIDR) nie pokrywa się z innymi zakresami sieci w organizacji.
- Podsieci nie powinny obejmować całej przestrzeni adresowej sieci wirtualnej. Planuj z wyprzedzeniem i rezerwuj trochę miejsca na przyszłość.
- Zaleca się, że masz mniej dużych sieci wirtualnych niż wiele małych sieci wirtualnych. Zapobiegnie to obciążeniu zarządem.
- Zabezpiecz sieć wirtualną, przypisując sieciowe grupy zabezpieczeń (NSG) do podsieci pod nimi.

## <a name="communicate-with-the-internet"></a>Komunikacja z Internetem

Wszystkie zasoby w sieci wirtualnej można komunikować się wychodzących do Internetu, domyślnie. Z zasobem w ruchu przychodzącym możesz komunikować się przez przypisanie publicznego adresu IP lub publicznego modułu równoważenia obciążenia. Publicznego adresu IP lub publicznego modułu równoważenia obciążenia możesz używać również do zarządzania połączeniami w ruchu wychodzącym.  Aby dowiedzieć się więcej na temat połączeń wychodzących na platformie Azure, zobacz [Połączenia wychodzące](../load-balancer/load-balancer-outbound-connections.md), [Publiczne adresy IP](virtual-network-public-ip-address.md) i [Moduł równoważenia obciążenia](../load-balancer/load-balancer-overview.md).

>[!NOTE]
>W przypadku korzystania tylko z wewnętrznej [usługi Load Balancer w warstwie Standardowa](../load-balancer/load-balancer-standard-overview.md) łączność wychodząca nie jest dostępna, dopóki nie zdefiniujesz współdziałania [połączeń wychodzących](../load-balancer/load-balancer-outbound-connections.md) z publicznym adresem IP na poziomie wystąpienia lub publicznym modułem równoważenia obciążenia.

## <a name="communicate-between-azure-resources"></a>Komunikacja pomiędzy zasobami platformy Azure

Zasoby platformy Azure komunikują się bezpiecznie ze sobą nawzajem, korzystając z jednego z następujących sposobów:

- **Za pośrednictwem sieci wirtualnej**: możesz wdrożyć maszyny wirtualne i kilka innych typów zasobów platformy Azure w sieci wirtualnej, np. środowiskach Azure App Service Environment, usłudze Azure Kubernetes Service (AKS) oraz zestawach usługi Azure Virtual Machine Scale Sets. Aby wyświetlić kompletną listę zasobów platformy Azure, które można wdrożyć w sieci wirtualnej, zobacz [Integracja sieci wirtualnej z usługą](virtual-network-for-azure-services.md).
- **Za pośrednictwem punktu końcowego usługi dla sieci wirtualnej**: możesz rozszerzyć prywatną przestrzeń adresową oraz tożsamość sieci wirtualnej na zasoby usług Azure, takie jak konta usługi Azure Storage i bazy danych Azure SQL Database, korzystając z połączenia bezpośredniego. Punkty końcowe usługi umożliwiają zabezpieczanie krytycznych zasobów usługi platformy Azure tylko do sieci wirtualnej. Aby dowiedzieć się więcej, zobacz [Omówienie punktów końcowych usługi dla sieci wirtualnej](virtual-network-service-endpoints-overview.md).
- **Za pośrednictwem komunikacji równorzędnej sieci wirtualnej:** Można połączyć sieci wirtualne ze sobą, umożliwiając zasoby w każdej sieci wirtualnej do komunikowania się ze sobą, przy użyciu komunikacji równorzędnej sieci wirtualnej. Łączone sieci wirtualne mogą znajdować się w tym samym regionie lub w różnych regionach świadczenia usługi Azure. Aby dowiedzieć się więcej, zobacz [Komunikacja równorzędna sieci wirtualnych](virtual-network-peering-overview.md).

## <a name="communicate-with-on-premises-resources"></a>Komunikacja z zasobami lokalnymi

Komputery i sieci lokalne możesz połączyć z siecią wirtualną przy użyciu dowolnej kombinacji następujących opcji:

- **Wirtualna sieć prywatna typu punkt-lokacja:** sieć określana pomiędzy siecią wirtualną i jednym komputerem w Twojej sieci. Każdy komputer, który chce nawiązać łączność z siecią wirtualną, musi skonfigurować swoje połączenie. Ten typ połączenia jest świetny, jeśli dopiero rozpoczynasz pracę z platformą Azure. Jest też odpowiedni dla deweloperów, ponieważ wymaga niewielkich zmian w istniejącej sieci lub nie wymaga ich wcale. Komunikacja pomiędzy komputerem i siecią wirtualną jest wysyłana przez szyfrowany tunel za pośrednictwem Internetu. Aby dowiedzieć się więcej, zobacz [Sieć VPN typu punkt-lokacja](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#P2S).
- **Sieć VPN typu lokacja-lokacja:** określana pomiędzy lokalnym urządzeniem VPN i bramą Azure VPN Gateway, która jest wdrażana w sieci wirtualnej. Ten typ połączenia umożliwia dowolnym zasobom lokalnym, które uzyskają autoryzację, uzyskiwanie dostępu do sieci wirtualnej. Komunikacja między lokalnym urządzeniem VPN i bramą Azure VPN Gateway jest wysyłana przez szyfrowany tunel za pośrednictwem Internetu. Aby dowiedzieć się więcej, zobacz [Sieć VPN typu lokacja-lokacja](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti).
- **Azure ExpressRoute:** połączenie nawiązywane pomiędzy siecią i platformą Azure za pośrednictwem partnera usługi ExpressRoute. To połączenie jest prywatne. Ruch nie przechodzi przez Internet. Więcej informacji znajduje się w artykule [ExpressRoute](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ExpressRoute).

## <a name="filter-network-traffic"></a>Filtrowanie ruchu sieciowego

Ruch sieciowy pomiędzy podsieciami możesz filtrować przy użyciu jednej lub obu poniższych opcji:

- **Grupy zabezpieczeń:** Sieciowe grupy zabezpieczeń i grupy zabezpieczeń aplikacji mogą zawierać wiele przychodzących i wychodzących reguł zabezpieczeń, które umożliwiają filtrowanie ruchu do i z zasobów według źródłowego i docelowego adresu IP, portu i protokołu. Aby dowiedzieć się więcej, zobacz [Sieciowe grupy zabezpieczeń](security-overview.md#network-security-groups) i [Grupy zabezpieczeń aplikacji](security-overview.md#application-security-groups).
- **Wirtualne urządzenia sieciowe:** wirtualne urządzenie sieciowe to maszyna wirtualna wykonująca funkcję sieciową, np. funkcję zapory, optymalizacji WAN lub inną. Aby wyświetlić listę dostępnych wirtualnych urządzeń sieciowych, które możesz wdrożyć w sieci wirtualnej, zobacz witrynę [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

## <a name="route-network-traffic"></a>Routing ruchu sieciowego

Platforma Azure domyślnie kieruje ruchem pomiędzy podsieciami, połączonymi sieciami wirtualnymi, sieciami lokalnymi oraz Internetem. Możesz zaimplementować jedną lub obie poniższe opcje, aby zastąpić domyślne trasy tworzone przez platformę Azure:

- **Tabele tras:** możesz utworzyć niestandardowe tabele tras z trasami kontrolującymi przekierowywanie ruchu do każdej podsieci. Dowiedz się więcej o [tabelach tras](virtual-networks-udr-overview.md#user-defined).
- **Trasy protokołu BGP (Border Gateway Protocol):** jeśli łączysz sieć wirtualną z siecią lokalną przy użyciu bramy Azure VPN Gateway lub połączenia ExpressRoute, możesz propagować trasy lokalne BGP do sieci wirtualnych. Dowiedz się więcej o użyciu protokołu BGP z bramą [Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i usługą [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange).

## <a name="azure-vnet-limits"></a>Limity sieci wirtualnej platformy Azure

Istnieją pewne limity dotyczące liczby zasobów platformy Azure, które można wdrożyć. Większość limitów sieci platformy Azure są na maksymalne wartości. Można jednak [zwiększyć pewne limity sieci,](../azure-portal/supportability/networking-quota-requests.md) jak określono na [stronie limity sieci wirtualnej](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits). 

## <a name="pricing"></a>Cennik

Korzystanie z sieci wirtualnej platformy Azure jest bezpłatne. Standardowe opłaty mają zastosowanie do zasobów, takich jak maszyny wirtualne (maszyny wirtualne) i inne produkty. Aby dowiedzieć się więcej, zobacz [Ceny sieci wirtualnych](https://azure.microsoft.com/pricing/details/virtual-network/) i [kalkulator cen](https://azure.microsoft.com/pricing/calculator/)platformy Azure .

## <a name="next-steps"></a>Następne kroki

 Aby rozpocząć korzystanie z sieci wirtualnej, utwórz ją, wdróż w niej kilka maszyn wirtualnych i rozpocznij komunikację pomiędzy maszynami wirtualnymi. Aby dowiedzieć się więcej, zobacz przewodnik Szybki start [Tworzenie sieci wirtualnej](quick-create-portal.md).
