---
title: Rozwiązanie VMware platformy Azure według CloudSimple — Lista kontrolna sieci
description: Lista kontrolna alokacji CIDR sieci w rozwiązaniu Azure VMware przez CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c31942b198697ddc913f3732fd41409334ff0ef6
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817477"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Wymagania wstępne dotyczące sieci dla rozwiązań VMware platformy Azure według CloudSimple

Rozwiązanie Azure VMware według CloudSimple oferuje środowisko chmury prywatnej VMware, które jest dostępne dla użytkowników i aplikacji ze środowisk lokalnych, z urządzeń zarządzanych przez przedsiębiorstwo oraz z zasobów platformy Azure. Połączenie jest dostarczane przy użyciu usług sieciowych, takich jak sieci VPN i połączenia ExpressRoute.  Niektóre Network Services wymagają określenia zakresów adresów sieciowych na potrzeby włączania usług.  Tabele w tym artykule opisują zestaw zakresów adresów i odpowiadające im usługi korzystające z określonych adresów.  Niektóre z nich są obowiązkowe, a niektóre z nich zależą od usług, które mają zostać wdrożone.  Te przestrzenie adresowe nie mogą nakładać się na żadne podsieci lokalne, podsieci platformy Azure Virtual Network ani podsieci obciążeń planowanych CloudSimple.

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>Zakresy adresów sieciowych wymagane do utworzenia chmury prywatnej

Podczas tworzenia usługi CloudSimple i chmury prywatnej jest wymagany następujący zakres sieci CIDR.

| Nazwa/użyta dla     | Opis                                                                                                                            | Zakres adresów            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| CIDR bramy      | Wymagane dla usług brzegowych (bramy sieci VPN).  Ten CIDR jest wymagany podczas tworzenia usługi CloudSimple i musi pochodzić z przestrzeni RFC 1918. | /28                      |
| vSphere/sieci vSAN CIDR | Wymagane w przypadku sieci zarządzania VMware. Ten CIDR należy określić podczas tworzenia chmury prywatnej.                                    | /24 lub/23 lub/22 lub/21 |

## <a name="network-address-range-required-for-azure-network-connection-to-on-premises-network"></a>Zakres adresów sieciowych wymagany dla połączenia sieciowego platformy Azure z siecią lokalną

Łączenie z [sieci lokalnej do sieci prywatnej chmury przy użyciu usługi ExpressRoute](on-premises-connection.md) ustanawia połączenie Global REACH.  Połączenie spowoduje wymianę tras za pośrednictwem protokołu BGP między siecią lokalną, siecią chmurą prywatną i sieciami platformy Azure.

| Nazwa/użyta dla             | Opis                                                                                                                                                                             | Zakres adresów |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| ExpressRoute komunikacji równorzędnej | Wymagany w przypadku używania ExpressRoute Global Reach jest używany do łączności lokalnej. Ten CIDR należy podać, gdy Global Reach żądanie połączenia za pośrednictwem biletu pomocy technicznej. | /29           |

## <a name="network-address-range-required-for-using-site-to-site-vpn-connection-to-on-premises-network"></a>Zakres adresów sieciowych wymagany do korzystania z połączenia sieci VPN typu lokacja-lokacja z siecią lokalną

Połączenie z [sieci lokalnej do sieci prywatnej chmury przy użyciu sieci VPN typu lokacja-lokacja](vpn-gateway.md) wymaga następujących adresów IP:, sieci lokalnej i identyfikatorów. 

| Zakres adresów/adresów | Opis                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Adres IP elementu równorzędnego               | Publiczny adres IP lokalnej bramy sieci VPN. Wymagane do ustanowienia połączenia sieci VPN typu lokacja-lokacja między lokalnym centrum danych a regionem usługi CloudSimple. Ten adres IP jest wymagany podczas tworzenia bramy sieci VPN typu lokacja-lokacja.                                         |
| Identyfikator elementu równorzędnego       | Identyfikator równorzędny lokalnej bramy sieci VPN. Jest to zwykle takie samo jak **adres IP elementu równorzędnego**.  Jeśli unikatowy identyfikator jest określony w lokalnej bramie sieci VPN, należy określić identyfikator.  Identyfikator elementu równorzędnego jest wymagany w trakcie tworzenia VPN Gateway między lokacjami.   |
| Sieci lokalne   | Lokalne prefiksy, które wymagają dostępu do sieci CloudSimple w regionie.  Uwzględnij wszystkie prefiksy z sieci lokalnej, które będą miały dostęp do sieci CloudSimple, w tym z sieci klienta, z której użytkownicy będą mieli dostęp.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>Zakres adresów sieciowych wymagany do korzystania z połączeń sieci VPN typu punkt-lokacja

Połączenie sieci VPN typu punkt-lokacja umożliwia dostęp do sieci CloudSimple z komputera klienckiego.  [Konfigurowanie sieci VPN typu punkt-lokacja](vpn-gateway.md) wymaga podania następującego zakresu adresów sieciowych.

| Zakres adresów/adresów | Opis                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Podsieć klienta         | Po nawiązaniu połączenia z siecią VPN typu punkt-lokacja będą nadawane adresy DHCP z podsieci klienta. Ta podsieć jest wymagana podczas tworzenia bramy sieci VPN typu punkt-lokacja w portalu CloudSimple.  Sieć zostanie podzielona na dwie podsieci, a jedna zostanie użyta do połączenia UDP i dla połączeń TCP. |

## <a name="next-steps"></a>Następne kroki

* [Szybki Start — Tworzenie usługi CloudSimple](quickstart-create-cloudsimple-service.md)
* [Szybki Start — tworzenie — Chmura prywatna](quickstart-create-private-cloud.md)
* Dowiedz się więcej o [usłudze Azure Network Connections](cloudsimple-azure-network-connection.md)
* Dowiedz się więcej o [bramach sieci VPN](cloudsimple-vpn-gateways.md)
