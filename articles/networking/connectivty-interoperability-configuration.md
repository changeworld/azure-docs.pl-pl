---
title: 'Współdziałanie usługi ExpressRoute, sieci VPN typu lokacja lokacja i komunikacja równorzędna sieci wirtualnych — szczegóły konfiguracji: łączności z zapleczem platformy Azure, funkcje współpracy | Dokumentacja firmy Microsoft'
description: Ta strona zawiera szczegóły konfiguracji w konfiguracji testu, który służy do analizowania współdziałanie funkcje usługi ExpressRoute, sieci VPN typu lokacja lokacja i komunikacja równorzędna sieci wirtualnych.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: d94900b764331c6fff0e0384e6edbebc88ac938b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947263"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---test-configuration-details"></a>Współdziałanie usługi ExpressRoute, Site-to-site VPN i sieci wirtualnej komunikacji równorzędnej — szczegóły konfiguracji testu

W tym artykule Omówmy szczegóły konfiguracji ustawień testu. Aby przejrzeć ustawienia testu, zobacz [konfiguracji testu][Setup]. 

##<a name="spoke-vnet-connectivity-using-vnet-peering"></a>Łączność z siecią wirtualną za pomocą komunikacji równorzędnej sieci wirtualnych szprych

Poniższy zrzut ekranu portalu platformy Azure przedstawia szczegóły dotyczące komunikacji równorzędnej sieci wirtualnej z sieć wirtualną będącą szprychą. Aby uzyskać instrukcje skonfigurować wirtualną sieć równorzędną między dwiema sieciami wirtualnymi, zobacz [zarządzania wirtualną siecią równorzędną][VNet-Config]. Jeśli chcesz, aby sieć typu gwiazda, za pomocą bram podłączonej do sieci wirtualnej koncentratora, należy sprawdzić *Użyj bram zdalnych*.

[![1]][1]

Poniższy zrzut ekranu portalu platformy Azure przedstawia szczegóły dotyczące komunikacji równorzędnej sieci wirtualnej z sieci wirtualnej serwera centralnego. Jeśli chcesz, aby piastą, aby umożliwić sieci typu gwiazda, za pomocą jego bram, należy sprawdzić *Użyj bram zdalnych*.

[![2]][2]

##<a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Gałąź połączenie między sieciami przy użyciu sieci VPN typu lokacja-lokacja

Połączenia sieci VPN lokacja-lokacja między Centrum i sieciami wirtualnymi gałęzi jest skonfigurowany przy użyciu bram sieci VPN. Domyślnie bram sieci VPN i ExpressRoute są skonfigurowane z wartością prywatny numer ASN 65515. Brama VPN umożliwia zmianę wartości numer ASN. Ustawienia testu jak pokazano na poniższym zrzucie Azure portal, wartość numeru ASN gałęzi bramy sieci VPN sieci wirtualnej jest zmieniany na 65516 umożliwiające eBGP routingu między Centrum i gałęzi w sieciach wirtualnych.


[![3]][3]


##<a name="location-1-on-premises-connectivity-using-expressroute-and-site-to-site-vpn"></a>Łączność lokalizacji-1 w środowisku lokalnym przy użyciu usługi ExpressRoute i sieci VPN typu lokacja-lokacja

###<a name="expressroute1-configuration-details"></a>Szczegóły konfiguracji ExpressRoute1

Poniższy zrzut ekranu portalu pokazano konfigurację obwodu ExpressRoute 1 regionu systemu Azure do routerów CE lokalnych lokalizacji-1.

[![4]][4]

Poniższy zrzut ekranu portalu pokazano konfigurację połączenia między ExpressRoute1 obwód i sieci wirtualnej koncentratora.

[![5]][5]

Lista głównej routera CE (Cisco ASR1000 routery służą jako routery CE w ustawieniach testu) jest następująca konfiguracja konfiguracji związane z prywatnego połączenia komunikacji równorzędnej usługi ExpressRoute. Gdy Site-to-Site VPN i obwodem usługi ExpressRoute są skonfigurowane w sposób równoległy, połączyć sieć lokalną na platformę Azure; Azure preferowany obwód usługi ExpressRoute domyślnie. Aby uniknąć routingu asymetrycznego, w sieci lokalnej należy również preferuje usługę ExpressRoute za pośrednictwem połączenia VPN lokacja-lokacja, dla tras odebranych zarówno za pośrednictwem usługi ExpressRoute i sieci VPN typu lokacja-lokacja. Jest to osiągane w następującej konfiguracji za pomocą atrybutu preferencji lokalnej BGP. 

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

###<a name="site-to-site-vpn-configuration-details"></a>Szczegóły konfiguracji sieci VPN typu lokacja-lokacja

Oto lista podstawowa konfiguracja routera CE związane połączenie sieci VPN typu lokacja-lokacja:

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

##<a name="location-2-on-premises-connectivity-using-expressroute"></a>Łączność lokalna lokalizacji 2 przy użyciu usługi ExpressRoute

Drugi obwód usługi ExpressRoute, w pobliżu bliżej lokalizacji 2 w środowisku lokalnym, łączy się sieci wirtualnej serwera centralnego z 2 lokalizacji lokalnej. Poniższy zrzut ekranu portalu przedstawia drugi konfigurację usługi ExpressRoute.

[![6]][6]

Poniższy zrzut ekranu portalu pokazano konfigurację połączenia między drugim obwodu usługi ExpressRoute i sieci wirtualnej koncentratora.

[![7]][7]

ExpressRoute1 łączy się zdalna sieć wirtualna w innym regionie platformy Azure z piastą i lokalizacja 1 w środowisku lokalnym.

[![8]][8]

## <a name="further-reading"></a>Dalsze informacje

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Za pomocą połączenia sieci VPN usługi ExpressRoute i lokacja-lokacja w tandem

#### <a name="site-to-site-vpn-over-expressroute"></a>Site-to-Site VPN za pośrednictwem usługi ExpressRoute

Sieci VPN typu lokacja-lokacja można skonfigurować za pośrednictwem komunikacji równorzędnej Microsoft usługi ExpressRoute do prywatnie wymiany danych między siecią lokalną i sieciami wirtualnymi platformy Azure z poufności, powtarzaniu, autentyczności i integralności. Aby uzyskać więcej informacji na temat sposobu konfigurowania lokacja-lokacja protokołu IPSec sieci VPN w trybie tunelowania za pośrednictwem komunikacji równorzędnej firmy Microsoft usługi ExpressRoute, zobacz [Site-to-site VPN za pośrednictwem usługi ExpressRoute — komunikacja równorzędna firmy Microsoft][S2S-Over-ExR]. 

Główne ograniczenie konfigurowania sieci VPN S2S za pośrednictwem komunikacji równorzędnej firmy Microsoft jest przepływność. Przepływność za pośrednictwem tunelu IPSec jest ograniczona przez pojemność bramy sieci VPN. Przepływność bramy sieci VPN jest mniejsza w porównaniu do przepustowości usługi ExpressRoute. W takich scenariuszach duże zwiększenie ruchu w bezpiecznym i prywatnej komunikacji równorzędnej dla wszystkich innych ruchu przy użyciu tunelu IPSec może pomóc zoptymalizować wykorzystanie przepustowości usługi ExpressRoute.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Site-to-Site VPN jako bezpieczna ścieżka trybu failover dla usługi ExpressRoute
Usługa ExpressRoute jest oferowana jako para nadmiarowe obwodu, aby zapewnić wysoką dostępność. Magazynu geograficznie nadmiarowego połączenia usługi ExpressRoute można skonfigurować w różnych regionach platformy Azure. Również jako gotowe w naszej konfiguracji testu w danym regionie platformy Azure, aby ścieżka trybu failover dla Twojego połączenia usługi ExpressRoute można zrobić za pomocą Site-to-Site VPN. Gdy te same prefiksy są anonsowane za pośrednictwem usługi ExpressRoute oraz sieci VPN S2S, Azure preferuje usługę ExpressRoute sieci VPN S2S. Aby uniknąć asymetryczne routingu usługi ExpressRoute i połączeń VPN S2S lokalne konfiguracji sieci należy również gospodarzami preferowanie usługi ExpressRoute za pośrednictwem połączenia sieci VPN S2S.

Aby uzyskać więcej informacji na temat sposobu konfigurowania współistniejących połączeń usługi ExpressRoute i sieci VPN typu lokacja-lokacja, zobacz [usługi ExpressRoute i współistnienia lokacja-lokacja][ExR-S2S-CoEx].

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>Rozszerzanie łączność sieci wirtualne będące szprychami wewnętrznej bazy danych i biur

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>Łączność z siecią wirtualną za pomocą komunikacji równorzędnej sieci wirtualnych szprych

Gwiazda sieć wirtualna architektura jest powszechnie używana. Centrum jest używana sieć wirtualna (VNet) na platformie Azure, która działa jako centralny punkt łączności między Twoje sieci wirtualne będące szprychami i siecią lokalną. Szprychy są sieciami wirtualnymi równorzędnymi z piastą i mogą być używane do izolowania obciążeń. Ruch przepływa między lokalnym centrum danych a piastą za pośrednictwem połączenia usługi ExpressRoute lub sieci VPN. Aby uzyskać więcej informacji na temat architektury, zobacz [Gwiazda — architektura][Hub-n-Spoke]

Wirtualne sieci równorzędne w obrębie regionu umożliwia szprychy sieciach wirtualnych do komunikowania się z sieciami zdalnymi za pomocą bram sieci wirtualnej koncentratora (bramy sieci VPN i ExpressRoute).

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Gałąź połączenie między sieciami przy użyciu sieci VPN typu lokacja-lokacja

Jeśli chcesz, aby gałąź sieciami wirtualnymi (w różnych regionach) i sieciami lokalnymi komunikują się ze sobą za pośrednictwem sieci wirtualnej koncentratora, natywne rozwiązanie na platformie Azure jest połączenie z siecią VPN lokacja lokacja przy użyciu sieci VPN. Jest dostępna Alternatywna opcja na potrzeby routingu w piaście urządzenia WUS.

Do konfigurowania bramy sieci VPN, zobacz [konfigurowania bramy sieci VPN][VPN]. Do wdrażania urządzeń WUS wysokiej dostępności, zobacz [wdrożenia o wysokiej dostępności urządzeń WUS][Deploy-NVA].

## <a name="next-steps"></a>Kolejne kroki

Do analizy płaszczyzna kontroli ustawień testu i zrozumieć widoków innej sieci wirtualnej/sieć VLAN topologii, zobacz [analizy płaszczyznę kontroli][Control-Analysis].

Analiza płaszczyzny danych konfiguracji testu i widokami funkcji monitorowania sieci platformy Azure, zobacz [płaszczyzny danych analizy][Data-Analysis].

Aby dowiedzieć się, jak wiele obwodów usługi ExpressRoute, możesz nawiązać połączenie bramy usługi ExpressRoute lub ile bram usługi ExpressRoute można nawiązać połączenie obwodu usługi ExpressRoute lub Dowiedz się, inne limity skalowania usługi expressroute, zobacz [ExpressRoute — często zadawane pytania][ExR-FAQ]


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "ramię sieci wirtualne sieci równorzędne"
[2]: ./media/backend-interoperability/HubVNet-peering.png "Centrum sieci wirtualne sieci równorzędne"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "konfiguracji bramy sieci VPN sieci wirtualnej gałęzi"
[4]: ./media/backend-interoperability/ExR1.png "ExpressRoute1 konfiguracji"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "konfiguracji połączenia ExpressRoute1 do GW ExR sieci wirtualnej koncentratora"
[6]: ./media/backend-interoperability/ExR2.png "ExpressRoute2 konfiguracji"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "konfiguracji połączenia ExpressRoute2 do GW ExR sieci wirtualnej koncentratora"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "konfiguracji połączenia ExpressRoute2 do zdalnego GW ExR sieci wirtualnej"

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




