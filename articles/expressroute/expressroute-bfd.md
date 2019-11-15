---
title: 'Azure ExpressRoute: Konfigurowanie BFD'
description: Ten artykuł zawiera instrukcje dotyczące sposobu konfigurowania BFD (dwukierunkowe przesyłanie dalej wykrywanie) za pośrednictwem prywatnej komunikacji równorzędnej w obwodu usługi ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 11/1/2018
ms.author: rambala
ms.openlocfilehash: 608b5e0011d4ed656ff61fec84a23f2fb22373b3
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74080792"
---
# <a name="configure-bfd-over-expressroute"></a>Konfigurowanie BFD za pośrednictwem usługi ExpressRoute

ExpressRoute obsługuje wykrywanie dwukierunkowego przekazywania (BFD) zarówno w komunikacji równorzędnej, jak i za pośrednictwem firmy Microsoft. Włączenie funkcji BFD over ExpressRoute umożliwia przyspieszenie wykrywania awarii łącza między urządzeniami Microsoft Enterprise Edge (MSEE) i routerami, na których kończy się obwód ExpressRoute (CE/PE). Możesz zakończyć działanie usługi ExpressRoute za pośrednictwem routingu urządzenia brzegowe klienta lub routingu urządzenia brzegowe partnera (jeśli zakończył się za pomocą zarządzanej usługi połączenia warstwy 3). Ten dokument przeprowadzi Cię przez potrzebę BFD oraz jak włączyć BFD za pośrednictwem usługi ExpressRoute.

## <a name="need-for-bfd"></a>Potrzebę BFD

Na poniższym diagramie przedstawiono korzyści włączania BFD za pośrednictwem obwodu usługi ExpressRoute: [ ![1]][1]

Można włączyć obwód usługi ExpressRoute za połączenia w warstwie 2 lub zarządzane połączenia w warstwie 3. W obu przypadkach Jeśli co najmniej jedno urządzenie warstwy 2 znajduje się w ścieżce połączenia usługi ExpressRoute, odpowiedzialność wykrywania błędów łącze w ścieżce spoczywa inne protokołu BGP.

Na urządzeniach MSEE keepalive protokołu BGP i czas wstrzymania zwykle są skonfigurowane jako 60 i 180 sekund odpowiednio. W związku z tym po awarii łącza, jaki zajmuje maksymalnie trzy minuty do wykrywania dowolny link błędu i przełączać ruch do alternatywne połączenie.

Czasomierze protokołu BGP można kontrolować, konfigurując niższe keepalive protokołu BGP i czasu blokady na urządzeniu komunikacji równorzędnej krawędź klienta. Jeśli niezgodność czasomierzy protokołu BGP między dwoma urządzeniami komunikacji równorzędnej, sesji protokołu BGP między komputerami Użyj niższą wartość czasomierza. Keepalive protokołu BGP można ustawić najmniejsza trzy sekundy, a czas wstrzymania zgodnie z kolejnością dziesiątki sekund. Jednak ustawienie czasomierzy protokołu BGP agresywnie jest mniej zalecane, ponieważ protokół jest intensywnie przetwarzany.

W tym scenariuszu BFD może pomóc. BFD zapewnia wykrywanie awarii łącza małym obciążeniem w odstępie czasu subsecond. 


## <a name="enabling-bfd"></a>Włączanie BFD

BFD jest domyślnie skonfigurowane, w ramach wszystkich nowo utworzonej usługi ExpressRoute prywatnej komunikacji równorzędnej interfejsów w rozwiązaniami Msee. W związku z tym, aby włączyć BFD, należy skonfigurować BFD na serwerze rejestracji certyfikatów (zarówno na urządzeniach podstawowych, jak i dodatkowych). Konfigurowanie BFD jest procesem dwuetapowym: należy skonfigurować BFD w interfejsie, a następnie połączyć go z sesją BGP.

Poniżej przedstawiono przykład CE/PE (z zastosowaniem konfiguracji Cisco IOS XE). 

    interface TenGigabitEthernet2/0/0.150
      description private peering to Azure
      encapsulation dot1Q 15 second-dot1q 150
      ip vrf forwarding 15
      ip address 192.168.15.17 255.255.255.252
      bfd interval 300 min_rx 300 multiplier 3


    router bgp 65020
      address-family ipv4 vrf 15
        network 10.1.15.0 mask 255.255.255.128
        neighbor 192.168.15.18 remote-as 12076
        neighbor 192.168.15.18 fall-over bfd
        neighbor 192.168.15.18 activate
        neighbor 192.168.15.18 soft-reconfiguration inbound
      exit-address-family

>[!NOTE]
>Aby włączyć BFD w ramach istniejącego prywatnej komunikacji równorzędnej; należy zresetować komunikacji równorzędnej. Zobacz [Resetowanie komunikacji równorzędnej ExpressRoute][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>Negocjowanie czasomierza BFD

Między komputerami równorzędnymi BFD wolniejsze z dwoma komputerami określają szybkość transmisji. 300 MS można ustawić Msee BFD transmisji/odbierania w odstępach czasu. W niektórych scenariuszach może ustawić interwał wyższa wartość 750 milisekund. Konfigurując wyższych wartości, możesz wymusić interwałami dłuższe; jednak nie krótszy.

>[!NOTE]
>Jeśli skonfigurowano nadmiarowe obwody usługi ExpressRoute lub używasz połączenia sieci VPN typu lokacja-lokacja jako kopii zapasowej; włączenie BFD może pomóc w szybszym przejściu w tryb failover po błędzie łączności ExpressRoute. 
>

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje lub pomoc zapoznaj się z następujących linków:

- [Tworzenie i modyfikowanie obwodu usługi ExpressRoute][CreateCircuit]
- [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD skracają czas dedukcji awarii łącza"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering






