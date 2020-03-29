---
title: 'Azure ExpressRoute: Konfigurowanie BFD'
description: Ten artykuł zawiera instrukcje dotyczące konfigurowania BFD (Dwukierunkowe wykrywanie przekazywania) za dodatkową komunikacją równorzędną dla usługi ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 11/1/2018
ms.author: rambala
ms.openlocfilehash: 378b639e89ffd46f6b32d7004f934104dd4b5407
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064836"
---
# <a name="configure-bfd-over-expressroute"></a>Konfigurowanie BFD zawersają wtrysku

Usługa ExpressRoute obsługuje dwukierunkowe wykrywanie przekazywania przekazywania (BFD) zarówno w trybie prywatnym, jak i w komunikacji równorzędnej firmy Microsoft. Włączenie bfd za pomocą usługi ExpressRoute umożliwia przyspieszenie wykrywania awarii łącza między urządzeniami microsoft enterprise edge (MSEE) a routerami, na których kończy się obwód usługi ExpressRoute (CE/PE). Usługę ExpressRoute można zakończyć za pomocą urządzeń routingu usługi Customer Edge lub urządzeń routingu usługi Partner Edge (jeśli została wyposażona w zarządzaną usługę połączenia warstwy 3). Ten dokument przeprowadzi Cię przez potrzebę BFD i jak włączyć BFD za pośrednictwem usługi ExpressRoute.

## <a name="need-for-bfd"></a>Potrzeba BFD

Na poniższym diagramie przedstawiono korzyści wynikające z włączenia BFD w obwodzie usługi ExpressRoute: [![1]][1]

Obwodzie usługi ExpressRoute można włączyć za pomocą połączeń warstwy 2 lub zarządzanych połączeń warstwy 3. W obu przypadkach, jeśli w ścieżce połączenia usługi ExpressRoute znajduje się jedno lub więcej urządzeń warstwy 2, odpowiedzialność za wykrycie błędów łącza w ścieżce spoczywa na nadmiernie rozkręceniu protokołu BGP.

Na urządzeniach MSEE, BGP keepalive i hold-time są zazwyczaj skonfigurowane jako 60 i 180 sekund odpowiednio. W związku z tym po awarii łącza zajęłoby do trzech minut, aby wykryć awarię łącza i przełączyć ruch na połączenie alternatywne.

Czasomierze BGP można sterować, konfigurując niższe czasy przechowywania I wstrzymania BGP na urządzeniu komunikacji równorzędnej krawędzi klienta. Jeśli czasomierze BGP są niezgodne między dwoma urządzeniami komunikacji równorzędnej, sesja BGP między elementami równorzędnymi będzie używać niższej wartości czasomierza. Zachować BGP można ustawić tak niskie, jak trzy sekundy, a czas wstrzymania w porządku kilkudziesięciu sekund. Jednak ustawienie czasomierzy BGP agresywnie jest mniej korzystne, ponieważ protokół jest proces intensywnie.

W tym scenariuszu BFD może pomóc. BFD zapewnia wykrywanie awarii łącza o niskim poziomie narzutów w podsekundowym przedziale czasu. 


## <a name="enabling-bfd"></a>Włączanie bfd

BFD jest domyślnie skonfigurowany w ramach wszystkich nowo utworzonych prywatnych interfejsów komunikacji równorzędnej usługi ExpressRoute na MSEE. W związku z tym, aby włączyć BFD, musisz po prostu skonfigurować BFD na PE / PE (zarówno na urządzeniach podstawowych, jak i pomocniczych). Konfigurowanie BFD jest procesem dwuetapowym: należy skonfigurować BFD w interfejsie, a następnie połączyć go z sesją BGP.

Poniżej przedstawiono przykładową konfigurację CE/PE (przy użyciu cisco IOS XE). 

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
>Aby włączyć BFD w ramach już istniejącej prywatnej komunikacji równorzędnej; należy zresetować komunikację równorzędnej. Zobacz [Resetowanie komunikacji równorzędnej usługi ExpressRoute][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>Negocjacja czasomierza BFD

Między 10 000 000 000 000 000 000 000 000 000 000 000 000 000 Interwały transmisji/odbioru BFD są ustawione na 300 milisekund. W niektórych scenariuszach interwał może być ustawiony na wyższą wartość 750 milisekund. Konfigurując wyższe wartości, można wymusić, aby te interwały były dłuższe; ale nie krócej.

>[!NOTE]
>Jeśli skonfigurowano obwody usługi ExpressRoute geograficznie nadmiarowe lub używasz łączności IPSec ipsec lokacji jako kopii zapasowej; włączenie BFD pomogłoby szybciej w trybie failover po awarii łączności usługi ExpressRoute. 
>

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji lub pomoc, zapoznaj się z następującymi łączami:

- [Tworzenie i modyfikowanie obwodu usługi ExpressRoute][CreateCircuit]
- [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD przyspiesza czas potrącenia awarii łącza"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering






