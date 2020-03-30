---
title: 'Współdziałanie w funkcjach łączności zaplecza platformy Azure: szczegóły konfiguracji | Dokumenty firmy Microsoft'
description: W tym artykule opisano szczegóły konfiguracji konfiguracji testu, których można użyć do analizowania współdziałania między usługą ExpressRoute, sieci VPN typu lokacja-lokacja i komunikacji równorzędnej w sieci wirtualnej na platformie Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 9c4a57111566248d3537cab0d9d85c0c3be874a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68335930"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-configuration-details"></a>Współdziałanie w funkcjach łączności zaplecza platformy Azure: szczegóły konfiguracji testu

W tym artykule opisano szczegóły konfiguracji [konfiguracji testu][Setup]. Konfiguracja testu pomaga analizować, jak usługi sieciowe platformy Azure współdziałają na poziomie płaszczyzny sterowania i płaszczyzny danych.

## <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Szprychowa łączność sieci wirtualnej przy użyciu komunikacji równorzędnej sieci wirtualnej

Na poniższej ilustracji przedstawiono szczegóły komunikacji równorzędnej sieci wirtualnej platformy Azure sieci wirtualnej (sieci wirtualnej). Aby dowiedzieć się, jak skonfigurować komunikację równorzędnej między dwiema sieciami wirtualnymi, zobacz [Zarządzanie komunikacją równorzędna sieci wirtualnej][VNet-Config]. Jeśli chcesz, aby szprychowa sieci wirtualnej używać bram, które są podłączone do sieci wirtualnej koncentratora, wybierz użyj **bramy zdalne**.

[![1]][1]

Na poniższej ilustracji przedstawiono szczegóły komunikacji równorzędnej sieci wirtualnej sieci wirtualnej koncentratora. Jeśli chcesz, aby hubowa sieci wirtualnej zezwoliła sieci wirtualnej szprychowej na korzystanie z bram koncentratora, wybierz pozycję **Zezwalaj na przesyłanie bramy**.

[![2]][2]

## <a name="branch-vnet-connectivity-by-using-a-site-to-site-vpn"></a>Łączność sieci wirtualnej oddziału przy użyciu sieci VPN lokacji lokacji

Konfigurowanie łączności sieci VPN między lokacjami a sieciami wirtualnymi w oddziale przy użyciu bram sieci VPN w usłudze Azure VPN Gateway. Domyślnie bramy sieci VPN i bramy usługi Azure ExpressRoute używają wartości numeru prywatnego systemu autonomicznego (ASN) **65515.** Wartość ASN można zmienić w bramie sieci VPN. W konfiguracji testowej wartość ASN bramy sieci wirtualnej sieci wirtualnej oddziału została zmieniona na **65516** w celu obsługi routingu eBGP między koncentratorem a sieciami wirtualnymi oddziału.


[![3]][3]


## <a name="on-premises-location-1-connectivity-by-using-expressroute-and-a-site-to-site-vpn"></a>Łączność lokalna lokalizacja 1 przy użyciu usługi ExpressRoute i sieci VPN typu lokacja lokacja lokacja

### <a name="expressroute-1-configuration-details"></a>Szczegóły konfiguracji usługi ExpressRoute 1

Na poniższej ilustracji przedstawiono konfigurację obwodu usługi Azure Region 1 Usługi ExpressRoute w kierunku lokalnych routerów brzegowych klienta lokalizacji 1 (CE):The following figure shows the Azure Region 1 ExpressRoute circuit configuration towards on-premises Location 1 customer edge (CE) routery:

[![4]][4]

Na poniższej ilustracji przedstawiono konfigurację połączenia między obwodem usługi ExpressRoute 1 a siecią wirtualną koncentratora:

[![5]][5]

Na poniższej liście przedstawiono podstawową konfigurację routera CE dla prywatnej łączności równorzędnej usługi ExpressRoute. (Routery Cisco ASR1000 są używane jako routery CE w konfiguracji testowej). Gdy obwody sieci VPN między lokacjami i usługi ExpressRoute są skonfigurowane równolegle do łączenia sieci lokalnej z platformą Azure, platforma Azure domyślnie nadaje priorytet obwodowi usługi ExpressRoute. Aby uniknąć routingu asymetrycznego, sieć lokalna powinna również priorytetowo traktować łączność usługi ExpressRoute nad łącznością sieci VPN między lokacjami. Następująca konfiguracja ustanawia priorytety przy użyciu atrybutu **preferencji lokalnych** BGP:

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

### <a name="site-to-site-vpn-configuration-details"></a>Szczegóły konfiguracji sieci VPN między lokacjami

Na poniższej liście przedstawiono podstawową konfigurację routera CE dla łączności sieci VPN między lokacjami:

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

## <a name="on-premises-location-2-connectivity-by-using-expressroute"></a>Łączność lokalna lokalizacja 2 przy użyciu usługi ExpressRoute

Drugi obwód usługi ExpressRoute, znajdujący się w pobliżu lokalizacji lokalnej 2, łączy lokalną lokalizację 2 z siecią wirtualną koncentratora. Na poniższej ilustracji przedstawiono drugą konfigurację usługi ExpressRoute:

[![6]][6]

Na poniższej ilustracji przedstawiono konfigurację połączenia między drugim obwodem usługi ExpressRoute a siecią wirtualną koncentratora:

[![7]][7]

Usługa ExpressRoute 1 łączy zarówno hubnet wirtualnej i lokalnej lokalizacji 1 do zdalnej sieci wirtualnej w innym regionie platformy Azure:

[![8]][8]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Łączność sieci VPN typu usługi ExpressRoute i lokacja lokacja w tandemie

###  <a name="site-to-site-vpn-over-expressroute"></a>Sieć VPN typu lokacja za pośrednictwem usługi ExpressRoute

Sieć VPN typu lokacja-lokacja umożliwia korzystanie z usługi ExpressRoute Microsoft w celu prywatnej wymiany danych między siecią lokalną a sieciami wirtualnymi platformy Azure. Dzięki tej konfiguracji można wymieniać dane z poufnością, autentycznością i integralnością. Wymiana danych jest również anty-powtórka. Aby uzyskać więcej informacji na temat konfigurowania sieci VPN IPsec między lokacjami w trybie tunelowania przy użyciu komunikacji równorzędnej firmy ExpressRoute Microsoft, zobacz [Sieć VPN typu lokacja-lokacja za pośrednictwem usługi ExpressRoute Microsoft peering][S2S-Over-ExR]. 

Podstawowym ograniczeniem konfigurowania sieci VPN typu lokacja lokacja, która korzysta z komunikacji równorzędnej firmy Microsoft, jest przepływność. Przepływność w tunelu IPsec jest ograniczona przez pojemność bramy sieci VPN. Przepływność bramy sieci VPN jest niższa niż przepływność usługi ExpressRoute. W tym scenariuszu przy użyciu tunelu IPsec dla wysoce bezpiecznego ruchu i przy użyciu prywatnej komunikacji równorzędnej dla wszystkich innych ruchu pomaga zoptymalizować wykorzystanie przepustowości usługi ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Sieć VPN typu lokacja-lokacja jako bezpieczna ścieżka trybu failover dla usługi ExpressRoute

Usługa ExpressRoute służy jako nadmiarowa para obwodów, aby zapewnić wysoką dostępność. Łączność geograficznie nadmiarowa usługi ExpressRoute można skonfigurować w różnych regionach platformy Azure. Ponadto, jak pokazano w naszej konfiguracji testów, w regionie platformy Azure można użyć sieci VPN lokacji do lokacji, aby utworzyć ścieżkę trybu failover dla łączności usługi ExpressRoute. Gdy te same prefiksy są anonsowane zarówno za pośrednictwem usługi ExpressRoute, jak i sieci VPN typu lokacja lokacja, platforma Azure nadaje priorytety usługi ExpressRoute. Aby uniknąć asymetrycznego routingu między usługą ExpressRoute a siecią VPN typu lokacja lokacja, konfiguracja sieci lokalnej powinna również odwzajemnić się przy użyciu łączności usługi ExpressRoute, zanim będzie używana łączność sieci VPN między lokacjami.

Aby uzyskać więcej informacji na temat konfigurowania współistniejących połączeń dla usługi ExpressRoute i sieci VPN typu lokacja-lokacja, zobacz [Współistnienie usługi ExpressRoute i współistnienie lokacji.][ExR-S2S-CoEx]

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Rozszerzanie łączności zaplecza do sieci wirtualnych i oddziałów szprychowych

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Szprychowa łączność sieci wirtualnej przy użyciu komunikacji równorzędnej sieci wirtualnej

Architektura koncentratora i sieci wirtualnej jest szeroko stosowana. Centrum jest siecią wirtualną na platformie Azure, która działa jako centralny punkt łączności między sieciami wirtualnymi szprychy i siecią lokalną. Szprychy są sieci wirtualne, które równorzędne z koncentratora i które można użyć do izolowania obciążeń. Przepływy ruchu między lokalnym centrum danych a koncentratorem za pośrednictwem połączenia usługi ExpressRoute lub SIECI VPN. Aby uzyskać więcej informacji na temat architektury, zobacz [Implementowanie topologii sieci z szprychą koncentratora na platformie Azure.][Hub-n-Spoke]

W sieci wirtualnej komunikacji równorzędnej w regionie, szprychowe sieci wirtualne mogą używać bram sieci wirtualnej centrum (zarówno sieci VPN, jak i bram usługi ExpressRoute) do komunikowania się z sieciami zdalnymi.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Łączność sieci wirtualnej oddziału przy użyciu sieci VPN lokacja lokacja

Sieci wirtualne oddziałów, które znajdują się w różnych regionach, oraz sieci lokalne, mogą komunikować się ze sobą za pośrednictwem sieci wirtualnej koncentratora. Natywne rozwiązanie platformy Azure dla tej konfiguracji to łączność sieci VPN między lokacjami przy użyciu sieci VPN. Alternatywą jest użycie sieciowego urządzenia wirtualnego (NVA) do routingu w koncentratorze.

Aby uzyskać więcej informacji, zobacz [Deploy a highly available NVA][Deploy-NVA] [Co to jest brama sieci VPN?][VPN]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [analizie płaszczyzny sterowania][Control-Analysis] konfiguracji testu i widokach różnych sieci wirtualnych lub sieci VLAN w topologii.

Dowiedz się więcej o [analizie płaszczyzny danych][Data-Analysis] podczas instalacji testów i widoków funkcji monitorowania sieci platformy Azure.

Zobacz często zadawane [pytania dotyczące usługi ExpressRoute,][ExR-FAQ] aby:
-   Dowiedz się, ile obwodów usługi ExpressRoute można połączyć z bramą usługi ExpressRoute.
-   Dowiedz się, ile bram usługi ExpressRoute można połączyć z obwódem usługi ExpressRoute.
-   Dowiedz się więcej o innych ograniczeniach skali usługi ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "Komunikacja równorzędna sieci wirtualnej szprychy"
[Komunikacja]: ./media/backend-interoperability/HubVNet-peering.png "równorzędna sieci wirtualnej 2 hubu"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "Konfiguracja bramy sieci VPN sieci wirtualnej oddziału"
[4]: ./media/backend-interoperability/ExR1.png "Konfiguracja usługi ExpressRoute 1"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "Konfiguracja połączenia usługi ExpressRoute 1 z bramą usługi VNet ExR w centrum"
[6]: ./media/backend-interoperability/ExR2.png "Konfiguracja usługi ExpressRoute 2"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "Konfiguracja połączenia usługi ExpressRoute 2 z bramą usługi VNet ExR w centrum"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "Konfiguracja połączenia usługi ExpressRoute 2 ze zdalną bramą serwera wirtualnego ExR"

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


