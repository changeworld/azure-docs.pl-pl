---
title: Azure Virtual Network | Microsoft Docs
description: Poznaj pojęcia związane z usługą Azure Virtual Network i jej funkcje.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
Customer intent: As someone with a basic network background that is new to Azure, I want to understand the capabilities of Azure Virtual Network, so that my Azure resources such as VMs, can securely communicate with each other, the internet, and my on-premises resources.
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/12/2018
ms.author: kumud
ms.openlocfilehash: 23093cd8bcb5793b9e5b9abc835f64233e666ce1
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241741"
---
# <a name="what-is-azure-virtual-network"></a>Co to jest usługa Azure Virtual Network?

Usługa Azure Virtual Network umożliwia wielu typom zasobów platformy Azure, np. maszynom wirtualnym Azure, bezpieczne komunikowanie się ze sobą, z Internetem oraz sieciami lokalnymi. Sieci wirtualnej jest ograniczony do jednego regionu platformy Azure. Azure [region](https://azure.microsoft.com/global-infrastructure/regions/) to zestaw centrów danych wdrożonego w ramach obwodu o zdefiniowanym opóźnieniu i połączony za pośrednictwem dedykowanej, regionalnej sieci o małych opóźnieniach. 

Sieci wirtualne są tworzone podsieci. Podsieć jest zakresem adresów IP w ramach sieci wirtualnej. Podsieci, takie jak sieci wirtualne są ograniczone do jednego regionu platformy Azure. 

Wiele sieci wirtualnych z różnych regionów, mogą być połączone ze sobą przy użyciu komunikacji równorzędnej sieci wirtualnej.

Usługa Azure Virtual Network oferuje następujące kluczowe funkcje:

## <a name="isolation-and-segmentation"></a>Izolacja i segmentacja

W każdej [subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) platformy Azure oraz w każdym [regionie](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) świadczenia usługi Azure możesz zaimplementować wiele sieci wirtualnych. Każda sieć wirtualna jest odizolowana od innych sieci wirtualnych. Dla każdej sieci wirtualnej można wykonać następujące czynności:
- Określenie niestandardowej prywatnej przestrzeni adresowej IP przy użyciu adresów publicznych i prywatnych (RFC 1918). Platforma Azure przypisze zasobom w sieci wirtualnej prywatny adres IP z przydzielonej przestrzeni adresowej.
- Segmentowanie sieci wirtualnej na jedną lub więcej podsieci, a także przypisywanie części przestrzeni adresowej sieci wirtualnej do każdej podsieci.
- Używanie rozpoznawania nazw platformy Azure lub określenie własnego serwera DNS do użycia przez zasoby w sieci wirtualnej.

## <a name="communicate-with-the-internet"></a>Komunikacja z Internetem

Wszystkie zasoby w sieci wirtualnej mogą domyślnie komunikować się z Internetem w ruchu wychodzącym. Z zasobem w ruchu przychodzącym możesz komunikować się przez przypisanie publicznego adresu IP lub publicznego modułu równoważenia obciążenia. Publicznego adresu IP lub publicznego modułu równoważenia obciążenia możesz używać również do zarządzania połączeniami w ruchu wychodzącym.  Aby dowiedzieć się więcej na temat połączeń wychodzących na platformie Azure, zobacz [Połączenia wychodzące](../load-balancer/load-balancer-outbound-connections.md), [Publiczne adresy IP](virtual-network-public-ip-address.md) i [Moduł równoważenia obciążenia](../load-balancer/load-balancer-overview.md).

>[!NOTE]
>W przypadku korzystania tylko z wewnętrznej [usługi Load Balancer w warstwie Standardowa](../load-balancer/load-balancer-standard-overview.md) łączność wychodząca nie jest dostępna, dopóki nie zdefiniujesz współdziałania [połączeń wychodzących](../load-balancer/load-balancer-outbound-connections.md) z publicznym adresem IP na poziomie wystąpienia lub publicznym modułem równoważenia obciążenia.

## <a name="communicate-between-azure-resources"></a>Komunikacja pomiędzy zasobami platformy Azure

Zasoby platformy Azure komunikują się bezpiecznie ze sobą nawzajem, korzystając z jednego z następujących sposobów:

- **Za pośrednictwem sieci wirtualnej**: możesz wdrożyć maszyny wirtualne i kilka innych typów zasobów platformy Azure w sieci wirtualnej, np. środowiskach Azure App Service Environment, usłudze Azure Kubernetes Service (AKS) oraz zestawach usługi Azure Virtual Machine Scale Sets. Aby wyświetlić kompletną listę zasobów platformy Azure, które można wdrożyć w sieci wirtualnej, zobacz [Integracja sieci wirtualnej z usługą](virtual-network-for-azure-services.md). 
- **Za pośrednictwem punktu końcowego usługi dla sieci wirtualnej**: możesz rozszerzyć prywatną przestrzeń adresową oraz tożsamość sieci wirtualnej na zasoby usług Azure, takie jak konta usługi Azure Storage i bazy danych Azure SQL Database, korzystając z połączenia bezpośredniego. Punkty końcowe usługi umożliwiają zabezpieczanie krytycznych zasobów usługi platformy Azure tylko do sieci wirtualnej. Aby dowiedzieć się więcej, zobacz [Omówienie punktów końcowych usługi dla sieci wirtualnej](virtual-network-service-endpoints-overview.md).
 
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

## <a name="connect-virtual-networks"></a>Łączenie sieci wirtualnych

Sieci wirtualne możesz łączyć ze sobą, co umożliwi komunikację pomiędzy zasobami w każdej z sieci wirtualnych przy użyciu komunikacji równorzędnej sieci wirtualnych. Łączone sieci wirtualne mogą znajdować się w tym samym regionie lub w różnych regionach świadczenia usługi Azure. Aby dowiedzieć się więcej, zobacz [Komunikacja równorzędna sieci wirtualnych](virtual-network-peering-overview.md).

## <a name="next-steps"></a>Kolejne kroki

Teraz masz już podstawowe informacje o usłudze Azure Virtual Network. Aby rozpocząć korzystanie z sieci wirtualnej, utwórz ją, wdróż w niej kilka maszyn wirtualnych i rozpocznij komunikację pomiędzy maszynami wirtualnymi. Aby dowiedzieć się więcej, zobacz przewodnik Szybki start [Tworzenie sieci wirtualnej](quick-create-portal.md).
