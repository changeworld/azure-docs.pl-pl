---
title: Rozwiązanie Azure VMware by CloudSimple — lista kontrolna sieci
description: Lista kontrolna alokacji cidr sieci na platformie Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bfb170036293dc9f519259dc92737f30380aa84a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025014"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Wymagania wstępne dotyczące sieci dla rozwiązania Azure VMware firmy CloudSimple

Rozwiązanie Azure VMware firmy CloudSimple oferuje środowisko chmury prywatnej VMware dostępne dla użytkowników i aplikacji ze środowisk lokalnych, urządzeń zarządzanych przez przedsiębiorstwo i zasobów platformy Azure. Łączność jest dostarczana za pośrednictwem usług sieciowych, takich jak sieci VPN i połączenia Azure ExpressRoute. Niektóre z tych usług sieciowych wymagają określenia zakresów adresów sieciowych w celu włączenia usług. 

Tabele w tym artykule opisują zestaw zakresów adresów i odpowiednich usług, które używają określonych adresów. Niektóre adresy są obowiązkowe, a niektóre zależą od usług, które chcesz wdrożyć. Te przestrzenie adresowe nie powinny pokrywać się z żadnymi podsieciami lokalnymi, podsieciami sieci wirtualnej platformy Azure ani planowanymi podsieciami obciążenia CloudSimple.

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>Zakresy adresów sieciowych wymagane do utworzenia chmury prywatnej

Podczas tworzenia usługi CloudSimple i chmury prywatnej należy przestrzegać określonych zakresów routingu bezklasowego sieci (CIDR) w następujący sposób.

| Nazwa/użyte     | Opis                                                                                                                            | Zakres adresów            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| Brama CIDR      | Wymagane dla usług brzegowych (bramy sieci VPN).  Ten cidr jest wymagany podczas tworzenia usługi CloudSimple i musi pochodzić z przestrzeni RFC 1918. | /28                      |
| vSfera/vSAN CIDR | Wymagane dla sieci zarządzania VMware. Ten CIDR musi być określony podczas tworzenia chmury prywatnej.                                    | /24 lub /23 lub /22 lub /21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>Zakres adresów sieciowych wymagany dla połączenia sieciowego platformy Azure z siecią lokalną

Łączenie się z [sieci lokalnej z siecią chmury prywatnej za pośrednictwem usługi ExpressRoute](on-premises-connection.md) ustanawia połączenie globalnego zasięgu.  Połączenie używa protokołu BGP (Border Gateway Protocol) do wymiany tras między siecią lokalną, siecią chmury prywatnej i sieciami platformy Azure.

| Nazwa/użyte             | Opis                                                                                                                                                                             | Zakres adresów |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| Cidr komunikacji równorzędnej usługi ExpressRoute | Wymagane podczas korzystania z usługi ExpressRoute Global Reach dla łączności lokalnej. Ten cidr musi być dostarczony, gdy żądanie połączenia globalnego zasięgu jest dokonywane za pośrednictwem biletu pomocy technicznej. | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>Zakres adresów sieciowych wymagany do korzystania z połączenia sieci VPN lokacja-lokacja z siecią lokalną

Łączenie się z [sieci lokalnej z siecią chmury prywatnej przy użyciu sieci VPN typu lokacja lokacja](vpn-gateway.md) wymaga następujących adresów IP, sieci lokalnej i identyfikatorów. 

| Zakres adresów/adresów | Opis                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Adres IP elementu równorzędnego               | Lokalny publiczny adres IP bramy sieci VPN. Wymagane do ustanowienia połączenia sieci VPN lokacja lokacja między lokalnym centrum danych a regionem usługi CloudSimple. Ten adres IP jest wymagany podczas tworzenia bramy sieci VPN typu lokacja lokacja.                                         |
| Identyfikator elementu równorzędnego       | Identyfikator elementu równorzędnego lokalnej bramy sieci VPN. Zwykle jest to takie samo jak **peer IP**.  Jeśli unikatowy identyfikator jest określony w lokalnej bramie sieci VPN, należy określić identyfikator.  Identyfikator równorzędny jest wymagany podczas tworzenia bramy sieci VPN typu lokacja lokacja.   |
| Sieci lokalne   | Prefiksy lokalne, które wymagają dostępu do sieci CloudSimple w regionie.  Dołącz wszystkie prefiksy z sieci lokalnej, która będzie uzyskiwać dostęp do sieci CloudSimple, w tym sieci klienta, z której użytkownicy będą uzyskiwać dostęp do sieci.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>Zakres adresów sieciowych wymagany do korzystania z połączeń sieci VPN typu punkt-lokacja

Połączenie sieci VPN typu punkt-lokacja umożliwia dostęp do sieci CloudSimple z komputera klienckiego.  [Aby skonfigurować sieć VPN typu punkt-lokacja,](vpn-gateway.md)należy określić następujący zakres adresów sieciowych.

| Zakres adresów/adresów | Opis                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Podsieć klienta         | Adresy DHCP są dostarczane przez podsieć klienta podczas łączenia się przy użyciu sieci VPN typu punkt-lokacja. Ta podsieć jest wymagana podczas tworzenia bramy sieci VPN typu punkt-lokacja w portalu CloudSimple.  Sieć jest podzielona na dwie podsieci; jednego dla połączenia UDP, a drugiego dla połączeń TCP. |

## <a name="next-steps"></a>Następne kroki

* [Konfiguracja zapory lokalnej w celu uzyskania dostępu do chmury prywatnej](on-premises-firewall-configuration.md)
* [Szybki start — tworzenie usługi CloudSimple](quickstart-create-cloudsimple-service.md)
* [Szybki start — konfigurowanie chmury prywatnej](quickstart-create-private-cloud.md)
* Dowiedz się więcej o [połączeniach sieciowych platformy Azure](cloudsimple-azure-network-connection.md)
* Dowiedz się więcej o [bramkach sieci VPN](cloudsimple-vpn-gateways.md)
