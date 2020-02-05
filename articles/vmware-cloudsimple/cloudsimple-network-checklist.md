---
title: Azure VMware Solutions (Automatyczna synchronizacja) — Lista kontrolna sieci
description: Lista kontrolna alokacji sieci CIDR w rozwiązaniu Azure VMware przy użyciu narzędzia do automatycznej synchronizacji
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5cbb0fc0514c17fe34f8e57806e6620cfa8b3f68
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025014"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-avs"></a>Wymagania wstępne dotyczące sieci dla rozwiązań VMware platformy Azure przez automatyczna synchronizacja

Usługa Azure VMware Solutions (Automatyczna synchronizacja) oferuje środowisko chmury prywatnej VMware, które jest dostępne dla użytkowników i aplikacji ze środowisk lokalnych, urządzeń zarządzanych przez przedsiębiorstwo i zasobów platformy Azure. Łączność jest dostarczana za pośrednictwem usług sieciowych, takich jak sieci VPN i połączenia usługi Azure ExpressRoute. Niektóre z tych usług sieciowych wymagają określenia zakresów adresów sieciowych umożliwiających włączenie usług. 

Tabele w tym artykule opisują zestaw zakresów adresów i odpowiadające im usługi korzystające z określonych adresów. Niektóre z tych adresów są obowiązkowe, a niektóre z nich zależą od usług, które chcesz wdrożyć. Te przestrzenie adresowe nie powinny nakładać się na żadną z podsieci lokalnych, podsieci platformy Azure Virtual Network lub zaplanowanych podsieci obciążeń.

## <a name="network-address-ranges-required-for-creating-an-avs-private-cloud"></a>Zakresy adresów sieciowych wymagane do utworzenia chmury prywatnej automatycznej synchronizacji

Podczas tworzenia usługi automatycznej synchronizacji i chmury prywatnej do automatycznej synchronizacji należy przestrzegać określonych zakresów klasy sieciowej routingu między domenami (CIDR) w następujący sposób.

| Nazwa/użyta dla     | Opis                                                                                                                            | Zakres adresów            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| CIDR bramy      | Wymagane dla usług brzegowych (bramy sieci VPN). Ten CIDR jest wymagany podczas tworzenia usługi automatycznej synchronizacji i musi pochodzić z przestrzeni RFC 1918. | /28                      |
| vSphere/sieci vSAN CIDR | Wymagane w przypadku sieci zarządzania VMware. Ten CIDR należy określić podczas tworzenia chmury prywatnej automatycznej synchronizacji.                                    | /24 lub/23 lub/22 lub/21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>Zakres adresów sieciowych wymagany dla połączenia sieciowego platformy Azure z siecią lokalną

Nawiązywanie połączenia z [siecią lokalną z siecią prywatną w chmurze](on-premises-connection.md) w usłudze ExpressRoute ustanawia połączenie Global REACH. Połączenie używa protokołu Border Gateway Protocol (BGP) do wymiany tras między siecią lokalną, siecią chmurową w chmurze prywatnej i sieci platformy Azure.

| Nazwa/użyta dla             | Opis                                                                                                                                                                             | Zakres adresów |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| ExpressRoute komunikacji równorzędnej | Wymagany w przypadku korzystania z ExpressRoute Global Reach dla połączeń lokalnych. Ten CIDR należy podać, gdy Global Reach żądanie połączenia za pomocą biletu pomocy technicznej. | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>Zakres adresów sieciowych wymagany do korzystania z połączenia sieci VPN typu lokacja-lokacja z siecią lokalną

Połączenie z [sieci lokalnej do sieci prywatnej chmury automatycznej synchronizacji przy użyciu sieci VPN typu lokacja-lokacja](vpn-gateway.md) wymaga następujących adresów IP, sieci lokalnej i identyfikatorów. 

| Zakres adresów/adresów | Opis                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Adres IP elementu równorzędnego               | Publiczny adres IP lokalnej bramy sieci VPN. Wymagane do ustanowienia połączenia sieci VPN typu lokacja-lokacja między lokalnym centrum danych a regionem usługi automatycznej synchronizacji. Ten adres IP jest wymagany podczas tworzenia bramy sieci VPN typu lokacja-lokacja.                                         |
| Identyfikator elementu równorzędnego       | Identyfikator równorzędny lokalnej bramy sieci VPN. Jest to zwykle takie samo jak **adres IP elementu równorzędnego**.  Jeśli unikatowy identyfikator jest określony w lokalnej bramie sieci VPN, należy określić identyfikator.  Identyfikator elementu równorzędnego jest wymagany podczas tworzenia bramy sieci VPN typu lokacja-lokacja.   |
| Sieci lokalne   | Lokalne prefiksy, które potrzebują dostępu do automatycznej synchronizacji sieci w regionie.  Uwzględnij wszystkie prefiksy z sieci lokalnej, które będą miały dostęp do sieci automatycznej synchronizacji, w tym z sieci klienta, z której użytkownicy będą uzyskiwać dostęp do sieci.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>Zakres adresów sieciowych wymagany do korzystania z połączeń sieci VPN typu punkt-lokacja

Połączenie sieci VPN typu punkt-lokacja umożliwia dostęp do sieci automatycznej synchronizacji z komputera klienckiego. [Aby skonfigurować sieć VPN typu punkt-lokacja](vpn-gateway.md), należy określić następujący zakres adresów sieciowych.

| Zakres adresów/adresów | Opis                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Podsieć klienta         | Adresy DHCP są udostępniane przez podsieć klienta podczas nawiązywania połączenia przy użyciu sieci VPN typu punkt-lokacja. Ta podsieć jest wymagana podczas tworzenia bramy sieci VPN typu punkt-lokacja w portalu automatycznej synchronizacji. Sieć jest podzielona na dwie podsieci, jedną dla połączenia UDP i drugą dla połączeń TCP. |

## <a name="next-steps"></a>Następne kroki

* [Lokalna konfiguracja zapory na potrzeby uzyskiwania dostępu do chmury prywatnej do automatycznej synchronizacji](on-premises-firewall-configuration.md)
* [Szybki Start — Tworzenie usługi automatycznej synchronizacji](quickstart-create-cloudsimple-service.md)
* [Szybki Start — Konfigurowanie chmury prywatnej automatycznej synchronizacji](quickstart-create-private-cloud.md)
* Dowiedz się więcej o [usłudze Azure Network Connections](cloudsimple-azure-network-connection.md)
* Dowiedz się więcej o [bramach sieci VPN](cloudsimple-vpn-gateways.md)
