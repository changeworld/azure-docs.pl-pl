---
title: 'Współdziałanie w funkcjach łączność z zaplecza platformy Azure: Szczegóły konfiguracji | Dokumentacja firmy Microsoft'
description: W tym artykule opisano szczegóły konfiguracji dla konfiguracji testu, które służy do analizowania współdziałanie usługi ExpressRoute, sieci VPN typu lokacja lokacja i wirtualnych sieci równorzędnych na platformie Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 2ceb4aeac55bd555a41c29bd41b00c771490e5f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60425758"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-configuration-details"></a>Współdziałanie w funkcjach łączność z zaplecza platformy Azure: Szczegóły konfiguracji testu

W tym artykule opisano szczegóły konfiguracji [Testuj ustawienia][Setup]. Ustawienia testu pomaga analizować jak usługi platformy Azure networking współdziałać na poziomie poziom płaszczyzna kontroli i płaszczyzny danych.

## <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Typu gwiazda połączenie między sieciami przy użyciu komunikacji równorzędnej sieci wirtualnych

Na poniższej ilustracji przedstawiono szczegóły dotyczące komunikacji równorzędnej sieci wirtualnej platformy Azure szprychy sieci wirtualnej (VNet). Aby dowiedzieć się, jak skonfigurować komunikację równorzędną między dwiema sieciami wirtualnymi, zobacz [zarządzania wirtualną siecią równorzędną][VNet-Config]. Jeśli chcesz, aby szprychy sieci wirtualnej, aby użyć bram, które są połączone z piastą, wybierz **Użyj bram zdalnych**.

[![1]][1]

Na poniższej ilustracji przedstawiono szczegóły dotyczące komunikacji równorzędnej sieci wirtualnej z sieci wirtualnej serwera centralnego. Szprychy wirtualnymi za pomocą bram sieci wirtualnej koncentratora, zaznacz **Użyj bram zdalnych**.

[![2]][2]

## <a name="branch-vnet-connectivity-by-using-a-site-to-site-vpn"></a>Połączenie między sieciami gałęzi przy użyciu sieci VPN lokacja lokacja

Konfigurowanie połączenia sieci VPN lokacja lokacja między Centrum i gałąź sieci wirtualnych za pomocą bram sieci VPN w usłudze Azure VPN Gateway. Domyślnie, użyj wartości numer (ASN) prywatnej systemu autonomicznego bram sieci VPN i bramy usługi Azure ExpressRoute **65515**. Możesz zmienić wartość numer ASN w bramie sieci VPN. W konfiguracji testu została zmieniona wartość numeru ASN gałęzi bramy sieci VPN sieci wirtualnej **65516** do obsługi eBGP routingu między Centrum i sieciami wirtualnymi w oddziale firmy.


[![3]][3]


## <a name="on-premises-location-1-connectivity-by-using-expressroute-and-a-site-to-site-vpn"></a>Lokalizacja 1 połączeniami lokalnymi przy użyciu usługi ExpressRoute i sieci VPN lokacja lokacja

### <a name="expressroute-1-configuration-details"></a>Szczegóły konfiguracji usługi ExpressRoute 1

Na poniższej ilustracji przedstawiono konfiguracji obwodu usługi ExpressRoute 1 regionu Azure kierunku routery graniczne (CE) klienta 1 lokalizacji lokalnej:

[![4]][4]

Na poniższej ilustracji przedstawiono konfigurację połączenia między obwodu usługi ExpressRoute 1 a piastą:

[![5]][5]

Na poniższej liście przedstawiono podstawowa konfiguracja routera CE dla połączenia prywatnej komunikacji równorzędnej usługi ExpressRoute. (Cisco ASR1000 routery są używane jako routery CE w ustawieniach testu). Jeśli skonfigurowano obwodów ExpressRoute i sieci VPN typu lokacja lokacja w sposób równoległy, połączyć sieć lokalną na platformę Azure, Azure priorytetem obwód usługi ExpressRoute domyślnie. Aby uniknąć routingu asymetrycznego, sieć lokalną również nadać priorytet połączenia usługi ExpressRoute za pośrednictwem połączenia sieci VPN lokacja lokacja. Następująca konfiguracja ustanawia priorytetyzacji przy użyciu protokołu BGP **preferencji lokalnej** atrybutu:

    interface TenGigabitEthernet0/0/0.300
     description Customer 30 private peering to Azure
     encapsulation dot1Q 30 second-dot1q 300
     ip vrf forwarding 30
     ip address 192.168.30.17 255.255.255.252
    !
    interface TenGigabitEthernet1/0/0.30
     description Customer 30 to south bound LAN switch
     encapsulation dot1Q 30
     ip vrf forwarding 30
     ip address 192.168.30.0 255.255.255.254
     ip ospf network point-to-point
    !
    router ospf 30 vrf 30
     router-id 10.2.30.253
     redistribute bgp 65021 subnets route-map BGP2OSPF
     network 192.168.30.0 0.0.0.1 area 0.0.0.0
    default-information originate always
     default-metric 10
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 192.168.30.18 remote-as 12076
      neighbor 192.168.30.18 activate
      neighbor 192.168.30.18 next-hop-self
      neighbor 192.168.30.18 soft-reconfiguration inbound
      neighbor 192.168.30.18 route-map prefer-ER-over-VPN in
      neighbor 192.168.30.18 prefix-list Cust30_to_Private out
     exit-address-family
    !
    route-map prefer-ER-over-VPN permit 10
     set local-preference 200
    !
    ip prefix-list Cust30_to_Private seq 10 permit 10.2.30.0/25
    !

### <a name="site-to-site-vpn-configuration-details"></a>Szczegóły konfiguracji sieci VPN typu lokacja lokacja

Na poniższej liście przedstawiono podstawowej konfiguracji routera CE połączenie sieci VPN typu lokacja lokacja:

    crypto ikev2 proposal Cust30-azure-proposal
     encryption aes-cbc-256 aes-cbc-128 3des
     integrity sha1
     group 2
    !
    crypto ikev2 policy Cust30-azure-policy
     match address local 66.198.12.106
     proposal Cust30-azure-proposal
    !
    crypto ikev2 keyring Cust30-azure-keyring
     peer azure
      address 52.168.162.84
      pre-shared-key local IamSecure123
      pre-shared-key remote IamSecure123
    !
    crypto ikev2 profile Cust30-azure-profile
     match identity remote address 52.168.162.84 255.255.255.255
     identity local address 66.198.12.106
     authentication local pre-share
     authentication remote pre-share
     keyring local Cust30-azure-keyring
    !
    crypto ipsec transform-set Cust30-azure-ipsec-proposal-set esp-aes 256 esp-sha-hmac
     mode tunnel
    !
    crypto ipsec profile Cust30-azure-ipsec-profile
     set transform-set Cust30-azure-ipsec-proposal-set
     set ikev2-profile Cust30-azure-profile
    !
    interface Loopback30
     ip address 66.198.12.106 255.255.255.255
    !
    interface Tunnel30
     ip vrf forwarding 30
     ip address 10.2.30.125 255.255.255.255
     tunnel source Loopback30
     tunnel mode ipsec ipv4
     tunnel destination 52.168.162.84
     tunnel protection ipsec profile Cust30-azure-ipsec-profile
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 10.10.30.254 remote-as 65515
      neighbor 10.10.30.254 ebgp-multihop 5
      neighbor 10.10.30.254 update-source Tunnel30
      neighbor 10.10.30.254 activate
      neighbor 10.10.30.254 soft-reconfiguration inbound
     exit-address-family
    !
    ip route vrf 30 10.10.30.254 255.255.255.255 Tunnel30

## <a name="on-premises-location-2-connectivity-by-using-expressroute"></a>Lokalizacji 2 połączeniami lokalnymi przy użyciu usługi ExpressRoute

Drugi obwód usługi ExpressRoute, się bliżej do lokalnej lokalizacji 2, łączy się sieci wirtualnej serwera centralnego z lokalnej lokalizacji 2. Na poniższej ilustracji przedstawiono drugi konfiguracji usługi ExpressRoute:

[![6]][6]

Na poniższej ilustracji przedstawiono konfigurację połączenia między drugim obwodu usługi ExpressRoute i sieci wirtualnej serwera centralnego:

[![7]][7]

ExpressRoute 1 łączy zarówno w sieci wirtualnej serwera centralnego, jak i lokalnej lokalizacji 1 zdalna sieć wirtualna w innym regionie platformy Azure:

[![8]][8]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Usługi ExpressRoute i lokacja lokacja połączenia sieci VPN w tandem

###  <a name="site-to-site-vpn-over-expressroute"></a>Site-to-site VPN za pośrednictwem usługi ExpressRoute

Sieć VPN lokacja lokacja można skonfigurować przy użyciu usługi ExpressRoute komunikacji równorzędnej firmy Microsoft do prywatnie wymiany danych między siecią lokalną i sieciami wirtualnymi platformy Azure. W przypadku tej konfiguracji można wymiany danych z poufność, autentyczności i integralności. Wymiana danych jest także powtarzaniu. Aby uzyskać więcej informacji na temat konfigurowania sieci VPN lokacja lokacja protokołu IPsec w trybie tunelowania za pomocą komunikacji równorzędnej firmy Microsoft usługi ExpressRoute, zobacz [Site-to-site VPN za pośrednictwem komunikacji równorzędnej firmy Microsoft usługi ExpressRoute][S2S-Over-ExR]. 

Głównym ograniczeniem konfigurowania sieci VPN lokacja lokacja, który korzysta z komunikacji równorzędnej firmy Microsoft jest przepływność. Przepływność za pośrednictwem tunelu IPsec jest ograniczona przez pojemność bramy sieci VPN. Przepływność bramy sieci VPN jest mniejszy niż przepływności usługi ExpressRoute. W tym scenariuszu przy użyciu tunelu IPsec, wysoce bezpieczna ruchu i za pomocą prywatnej komunikacji równorzędnej dla innego ruchu pomaga zoptymalizować wykorzystanie przepustowości usługi ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Site-to-site VPN jako bezpieczna ścieżka trybu failover dla usługi ExpressRoute

Usługa ExpressRoute służy jako parę nadmiarowych obwodu, aby zapewnić wysoką dostępność. Magazynu geograficznie nadmiarowego połączenia usługi ExpressRoute można skonfigurować w różnych regionach platformy Azure. Również jak pokazano w naszej konfiguracji testu, w obrębie regionu Azure, można użyć sieci VPN lokacja lokacja można utworzyć ścieżki pracy awaryjnej dla usługi połączenia usługi ExpressRoute. Gdy te same prefiksy są anonsowane za pośrednictwem usługi ExpressRoute oraz sieci VPN lokacja lokacja, Azure priorytetem usługi ExpressRoute. Aby uniknąć asymetryczne routingu usługi ExpressRoute i sieci VPN typu lokacja lokacja, lokalne konfiguracji sieci, należy również gospodarzami przy użyciu połączenia usługi ExpressRoute używa połączenie sieci VPN typu lokacja lokacja.

Aby uzyskać więcej informacji o sposobie konfigurowania współistniejących połączeń usługi ExpressRoute i sieci VPN lokacja lokacja, zobacz [usługi ExpressRoute i współistnienia lokacja lokacja][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Rozszerzanie zaplecza łączność sieci wirtualne będące szprychami i biur

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Typu gwiazda połączenie między sieciami przy użyciu komunikacji równorzędnej sieci wirtualnych

Gwiazda architektury sieci wirtualnej jest powszechnie używana. Piasta to sieć wirtualną na platformie Azure, która działa jako centralny punkt łączności między Twoje sieci wirtualne będące szprychami i siecią lokalną. Szprychy są sieciami wirtualnymi równorzędnymi z piastą, oraz tych, które można użyć do izolowania obciążeń. Ruch przepływa między lokalnym centrum danych a piastą za pośrednictwem połączenia usługi ExpressRoute lub sieci VPN. Aby uzyskać więcej informacji na temat architektury, zobacz [zaimplementować topologii sieci piasty i szprych na platformie Azure][Hub-n-Spoke].

W sieci wirtualnej komunikacji równorzędnej w regionie sieci wirtualne będące szprychami można użyć bramy sieci wirtualnej koncentratora (bramy sieci VPN i ExpressRoute) do komunikowania się z sieciami zdalnymi.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Połączenie między sieciami gałęzi przy użyciu sieci VPN typu lokacja lokacja

Możesz chcieć gałęzi sieci wirtualne, które znajdują się w różnych regionach i sieciami lokalnymi do komunikowania się ze sobą za pośrednictwem sieci wirtualnej serwera centralnego. Natywne rozwiązanie platformy Azure dla tej konfiguracji jest połączenie sieci VPN typu lokacja lokacja przy użyciu sieci VPN. Alternatywą jest na potrzeby routingu w piaście wirtualnego urządzenia sieciowego (WUS).

Aby uzyskać więcej informacji, zobacz [co to jest usługa VPN Gateway?] [ VPN] i [wdrożenia o wysokiej dostępności urządzeń WUS][Deploy-NVA].

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [analizy płaszczyznę sterowania] [ Control-Analysis] konfiguracji testu i widoki różnych sieci wirtualnych lub sieci VLAN w topologii.

Dowiedz się więcej o [analizy na płaszczyźnie danych] [ Data-Analysis] konfiguracji testu i widokami funkcji monitorowania sieci platformy Azure.

Zobacz [ExpressRoute — często zadawane pytania] [ ExR-FAQ] do:
-   Dowiedz się, jak wiele obwodów usługi ExpressRoute, możesz nawiązać połączenie bramy usługi ExpressRoute.
-   Dowiedz się, ile bram usługi ExpressRoute, można połączyć z obwodem usługi ExpressRoute.
-   Poznaj inne limity skalowania usługi expressroute.


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "ramię sieci wirtualne sieci równorzędne"
[2]: ./media/backend-interoperability/HubVNet-peering.png "Centrum sieci wirtualne sieci równorzędne"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "konfiguracji bramy sieci VPN sieci wirtualnej gałęzi"
[4]: ./media/backend-interoperability/ExR1.png "konfiguracji usługi ExpressRoute 1"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "konfiguracji połączenia ExpressRoute 1 w z koncentratorem bramy sieci wirtualnej ExR"
[6]: ./media/backend-interoperability/ExR2.png "konfiguracji usługi ExpressRoute 2"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "konfiguracji połączenia usługi ExpressRoute 2 do bramy sieci wirtualnej ExR koncentratora"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "konfiguracji połączenia usługi ExpressRoute 2 do bramy sieci wirtualnej ExR zdalnego"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering


