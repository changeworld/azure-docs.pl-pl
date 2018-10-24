---
title: 'Współdziałanie usługi ExpressRoute, sieci VPN typu lokacja lokacja i komunikacja równorzędna sieci wirtualnych — płaszczyzny danych analizy: łączności z zapleczem platformy Azure, funkcje współpracy | Dokumentacja firmy Microsoft'
description: Ta strona zawiera analizy płaszczyzny danych konfiguracji testu, utworzone w celu analizowania interoperacyjności funkcje usługi ExpressRoute, sieci VPN typu lokacja lokacja i komunikacja równorzędna sieci wirtualnych.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: c9f3824b1e0f44338696ba3c2e434d60eee3af8b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947266"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---data-plane-analysis"></a>Współdziałanie usługi ExpressRoute, Site-to-site VPN i sieci wirtualnej komunikacji równorzędnej — analiza płaszczyzny danych

W tym artykule Omówmy analizy płaszczyzny danych ustawień testu. Aby przejrzeć ustawienia testu, zobacz [konfiguracji testu][Setup]. Aby przejrzeć szczegóły konfiguracji konfiguracji testu, zobacz [konfiguracji ustawień testu][Configuration]. Aby zapoznać się z analizy płaszczyzna kontroli konfiguracji testu, zobacz [analizy płaszczyzna kontroli][Control-Analysis].

Analiza płaszczyzny danych sprawdza, czy ścieżka pakietów przechodzenie z jednej sieci lokalnej (LAN/sieć wirtualna) do innego w ramach topologii. Ścieżki danych między dwiema sieciami lokalny może nie być koniecznie symetryczne. W związku z tym w tym artykule umożliwia analizowanie ścieżki przekazywania z sieci lokalnej do innego, niezależnie od Odwróć ścieżkę.

##<a name="data-path-from-hub-vnet"></a>Ścieżka danych z sieci wirtualnej koncentratora

###<a name="path-to-spoke-vnet"></a>Ścieżka do typu gwiazda sieci wirtualnej

Komunikacja równorzędna sieci wirtualnych emuluje funkcjonalność Mostek sieciowy między dwiema sieciami wirtualnymi, równorzędne. Dane wyjściowe polecenie traceroute z piastą do maszyny Wirtualnej w sieć wirtualną będącą szprychą znajduje się poniżej:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

Poniższy klip ekranu jest widok graficzny połączenia piastą i szprychy przedstawiony przez usługę Azure Network Watcher w sieci wirtualnej:


[![1]][1]

###<a name="path-to-branch-vnet"></a>Ścieżka do gałęzi w sieci wirtualnej

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

W powyższym traceroute pierwszym przeskokiem jest bramy sieci VPN sieci wirtualnej koncentratora. Drugim przeskokiem jest bramy sieci VPN sieci wirtualnej gałęzi, w których adres IP nie jest on anonsowany w sieci wirtualnej koncentratora. Trzeci przeskoku jest maszyna wirtualna w sieci wirtualnej gałęzi.

Poniższy klip ekranu jest widok graficzny połączenia sieci wirtualnej koncentratora i siecią wirtualną gałęzi przedstawiony przez usługę Azure Network Watcher:

[![2]][2]

Dla tego samego połączenia następujący klip ekranu jest przedstawiony przez usługę Azure Network Watcher widoku siatki:

[![3]][3]

###<a name="path-to-on-premises-location-1"></a>Ścieżka do lokalnej lokalizacji-1

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

W powyższym traceroute pierwszym przeskokiem jest punktem końcowym tunelu bramy usługi ExpressRoute do rozwiązania MSEE. Drugi i trzeci przeskok odpowiednio są CE router IP sieci LAN 1 lokalizacji lokalnej, te adresy IP nie są anonsowane w sieci wirtualnej koncentratora. Czwarty przeskokiem jest maszyny Wirtualnej na 1 lokalizacji lokalnej.


###<a name="path-to-on-premises-location-2"></a>Ścieżka do lokalnej lokalizacji-2

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

W powyższym traceroute pierwszym przeskokiem jest punktem końcowym tunelu bramy usługi ExpressRoute do rozwiązania MSEE. Drugi i trzeci przeskok odpowiednio są CE router IP sieci LAN 2 lokalizacji lokalnej, te adresy IP nie są anonsowane w sieci wirtualnej koncentratora. Czwarty przeskokiem jest maszyn wirtualnych w lokalizacji lokalnej-2.

###<a name="path-to-remote-vnet"></a>Ścieżka zdalna sieć wirtualna

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

W powyższym traceroute pierwszym przeskokiem jest punktem końcowym tunelu bramy usługi ExpressRoute do rozwiązania MSEE. Drugim przeskokiem jest adresu IP bramy zdalnej sieci wirtualnej. Drugi przeskok zakres adresów IP nie jest on anonsowany w sieci wirtualnej koncentratora. Trzeci przeskokiem jest maszyna wirtualna na zdalna sieć wirtualna.

##<a name="data-path-from-spoke-vnet"></a>Ścieżka danych z sieci typu gwiazda

Odwołania, sieci typu gwiazda udostępnić widok sieci w sieci wirtualnej koncentratora. Za pomocą komunikacji równorzędnej sieci wirtualnej, sieć wirtualną będącą szprychą używa łączności bramy zdalnej sieci wirtualnej serwera centralnego tak, jakby były bezpośrednio podłączone do sieć wirtualną będącą szprychą.

###<a name="path-to-hub-vnet"></a>Ścieżka do piastą

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

###<a name="path-to-branch-vnet"></a>Ścieżka do gałęzi w sieci wirtualnej

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

W powyższym traceroute pierwszym przeskokiem jest bramy sieci VPN sieci wirtualnej koncentratora. Drugim przeskokiem jest bramy sieci VPN sieci wirtualnej gałęzi, w których adres IP nie jest on anonsowany w sieci wirtualnej/Gwiazda. Trzeci przeskoku jest maszyna wirtualna w sieci wirtualnej gałęzi.

###<a name="path-to-on-premises-location-1"></a>Ścieżka do lokalnej lokalizacji-1

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

W powyższym traceroute pierwszym przeskokiem jest punkt końcowy tunelu bramy usługi ExpressRoute sieci wirtualnej koncentratora do rozwiązania MSEE. Drugi i trzeci przeskok odpowiednio są CE router IP sieci LAN 1 lokalizacji lokalnej, te adresy IP nie są anonsowane w sieci wirtualnej/Gwiazda. Czwarty przeskokiem jest maszyny Wirtualnej na 1 lokalizacji lokalnej.

###<a name="path-to-on-premises-location-2"></a>Ścieżka do lokalnej lokalizacji-2

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

W powyższym traceroute pierwszym przeskokiem jest punkt końcowy tunelu bramy usługi ExpressRoute sieci wirtualnej koncentratora do rozwiązania MSEE. Drugi i trzeci przeskok odpowiednio są CE router IP sieci LAN 2 lokalizacji lokalnej, te adresy IP nie są anonsowane w ramach sieci wirtualnych/Gwiazda. Czwarty przeskokiem jest maszyn wirtualnych w lokalizacji lokalnej-2.

###<a name="path-to-remote-vnet"></a>Ścieżka zdalna sieć wirtualna

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

W powyższym traceroute pierwszym przeskokiem jest punkt końcowy tunelu bramy usługi ExpressRoute sieci wirtualnej koncentratora do rozwiązania MSEE. Drugim przeskokiem jest adresu IP bramy zdalnej sieci wirtualnej. Drugi przeskok zakres adresów IP nie jest on anonsowany w sieci wirtualnej/Gwiazda. Trzeci przeskokiem jest maszyna wirtualna na zdalna sieć wirtualna.

##<a name="data-path-from-branch-vnet"></a>Ścieżka danych z sieci wirtualnej gałęzi

###<a name="path-to-hub-vnet"></a>Ścieżka do piastą

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

W powyższym traceroute pierwszym przeskokiem jest bramy sieci VPN sieci wirtualnej gałęzi. Drugim przeskokiem jest bramy sieci VPN sieci wirtualnej koncentratora, której adres IP nie jest on anonsowany w zdalnej sieci wirtualnej. Trzeci przeskoku jest maszyna wirtualna w sieci wirtualnej koncentratora.

###<a name="path-to-spoke-vnet"></a>Ścieżka do typu gwiazda sieci wirtualnej

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

W powyższym traceroute pierwszym przeskokiem jest bramy sieci VPN sieci wirtualnej gałęzi. Drugim przeskokiem jest bramy sieci VPN sieci wirtualnej koncentratora, której adres IP nie jest on anonsowany w ramach zdalna sieć wirtualna, a trzeci przeskoku jest maszyna wirtualna w sieci wirtualnej typu gwiazda.

###<a name="path-to-on-premises-location-1"></a>Ścieżka do lokalnej lokalizacji-1

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

W powyższym traceroute pierwszym przeskokiem jest bramy sieci VPN sieci wirtualnej gałęzi. Drugim przeskokiem jest bramy sieci VPN sieci wirtualnej koncentratora, której adres IP nie jest on anonsowany w zdalnej sieci wirtualnej. Trzeci przeskokiem jest punkt zakończenia tunelu sieci VPN na routerze CE podstawowego. Czwarty przeskokiem jest wewnętrzny adres IP adres IP sieci LAN lokalizacji-1 w środowisku lokalnym, który nie jest on anonsowany poza routera CE. Piąty przeskokiem jest docelowej maszyny Wirtualnej w lokalizacji lokalnej-1.

###<a name="path-to-on-premises-location-2-and-remote-vnet"></a>Ścieżka do lokalnej sieci wirtualnej 2 lokalizacji i zdalnych

Jak wspomniano wcześniej w analizie płaszczyzna kontroli, gałęzi sieci wirtualnej ma nie wgląd do lokalnej lokalizacji-2 lub zdalna sieć wirtualna dla konfiguracji sieci. Upewnij się, z następującymi wynikami ping faktu. 

    C:\Users\rb>ping 10.1.31.10

    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

    C:\Users\rb>ping 10.17.30.4

    Pinging 10.17.30.4 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.17.30.4:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

##<a name="data-path-from-on-premises-location-1"></a>Ścieżka danych z lokalnej lokalizacji-1.

###<a name="path-to-hub-vnet"></a>Ścieżka do piastą

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

W powyższym traceroute pierwsze dwa przeskoki są częścią sieci lokalnej. Trzeci przeskoku jest podstawowym interfejsem MSEE połączonego z routera CE. Czwarty przeskokiem jest ExpressRoute G/W Centrum sieci wirtualnej, której zakres adresów IP nie jest on anonsowany w sieci lokalnej. Piąty przeskokiem jest docelowej maszyny Wirtualnej.

Azure Network Watcher zawiera tylko widokowi skoncentrowanemu na platformie Azure. W związku z tym do lokalnego widoku użyliśmy platformy Azure Network Performance Monitor (NPM). NPM zawiera agentów, które można zainstalowanych serwerów w sieci poza systemem Azure i wykonywać analizę ścieżki danych.

Poniższy klip ekranu jest widok topologii połączeń lokalnych 1 lokalizacji maszyny Wirtualnej do maszyny Wirtualnej na piastą za pośrednictwem usługi ExpressRoute.

[![4]][4]

Pamiętamy, konfiguracji testu używa sieci VPN typu lokacja-lokacja jako kopii zapasowej łączności dla usługi ExpressRoute między lokalnej lokalizacji-1 i piastą. Aby przetestować zaplecze ścieżki danych, możemy occurs wystąpił błąd linku ExpressRoute między routerem CE głównej lokalizacji-1 w lokalnych i odpowiedniego rozwiązania MSEE przez zamknięcie interfejsu CE połączonego z rozwiązania MSEE.

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

Poniższy klip ekranu jest widok topologii połączeń lokalnych 1 lokalizacji maszyny Wirtualnej do maszyny Wirtualnej na piastą za pośrednictwem połączenia sieci VPN typu lokacja-lokacja, podczas połączenia usługi ExpressRoute nie działa.

[![5]][5]

###<a name="path-to-spoke-vnet"></a>Ścieżka do typu gwiazda sieci wirtualnej

Daj nam przywrócić połączenia podstawowej usługi ExpressRoute, aby wykonywać analizę ścieżki danych do sieci typu gwiazda.

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

Daj nam przywołać podstawowej łączności usługi ExpressRoute-1 w pozostałej części ścieżki danych analizy.

###<a name="path-to-branch-vnet"></a>Ścieżka do gałęzi w sieci wirtualnej

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

###<a name="path-to-on-premises-location-2"></a>Ścieżka do lokalnej lokalizacji-2

Tak jak Omówiliśmy to starszy analizy płaszczyzna kontroli, lokalnej lokalizacji-1 ma nie wgląd w lokalnej lokalizacji-2 na konfigurację sieci. Upewnij się, z następującymi wynikami ping faktu. 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

###<a name="path-to-remote-vnet"></a>Ścieżka zdalna sieć wirtualna

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

##<a name="data-path-from-on-premises-location-2"></a>Ścieżka danych z lokalnej lokalizacji-2

###<a name="path-to-hub-vnet"></a>Ścieżka do piastą

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

###<a name="path-to-spoke-vnet"></a>Ścieżka do typu gwiazda sieci wirtualnej

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

###<a name="path-to-branch-vnet-on-premises-location-1-and-remote-vnet"></a>Ścieżka do gałęzi sieć wirtualna-sieć wirtualna 1 lokalizacji i zdalnych w środowisku lokalnym

Jak wspomniano wcześniej w analizie płaszczyzna kontroli, lokalnej lokalizacji-1 ma nie wgląd w gałęzi sieci wirtualnej, lokalnej lokalizacji-1 i zdalna sieć wirtualna dla konfiguracji sieci. 

##<a name="data-path-from-remote-vnet"></a>Ścieżka danych z zdalna sieć wirtualna

###<a name="path-to-hub-vnet"></a>Ścieżka do piastą

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

###<a name="path-to-spoke-vnet"></a>Ścieżka do typu gwiazda sieci wirtualnej

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-branch-vnet-and-on-premises-location-2"></a>Ścieżka do gałęzi w sieci wirtualnej i lokalnej lokalizacji-2

Jak wspomniano wcześniej w analizie płaszczyzna kontroli, zdalna sieć wirtualna ma nie widoczność gałęzi sieci wirtualnej i lokalnej lokalizacji-2 na konfigurację sieci. 


### <a name="path-to-on-premises-location-1"></a>Ścieżka do lokalnej lokalizacji-1

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


## <a name="further-reading"></a>Dalsze informacje

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Za pomocą połączenia sieci VPN usługi ExpressRoute i lokacja-lokacja w tandem

####<a name="site-to-site-vpn-over-expressroute"></a>Site-to-Site VPN za pośrednictwem usługi ExpressRoute

Sieci VPN typu lokacja-lokacja można skonfigurować za pośrednictwem komunikacji równorzędnej Microsoft usługi ExpressRoute do prywatnie wymiany danych między siecią lokalną i sieciami wirtualnymi platformy Azure z poufności, powtarzaniu, autentyczności i integralności. Aby uzyskać więcej informacji na temat sposobu konfigurowania lokacja-lokacja protokołu IPSec sieci VPN w trybie tunelowania za pośrednictwem komunikacji równorzędnej firmy Microsoft usługi ExpressRoute, zobacz [Site-to-site VPN za pośrednictwem usługi ExpressRoute — komunikacja równorzędna firmy Microsoft][S2S-Over-ExR]. 

Główne ograniczenie konfigurowania sieci VPN S2S za pośrednictwem komunikacji równorzędnej firmy Microsoft jest przepływność. Przepływność za pośrednictwem tunelu IPSec jest ograniczona przez pojemność bramy sieci VPN. Przepływność bramy sieci VPN jest mniejsza w porównaniu do przepustowości usługi ExpressRoute. W takich scenariuszach duże zwiększenie ruchu w bezpiecznym i prywatnej komunikacji równorzędnej dla wszystkich innych ruchu przy użyciu tunelu IPSec może pomóc zoptymalizować wykorzystanie przepustowości usługi ExpressRoute.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Site-to-Site VPN jako bezpieczna ścieżka trybu failover dla usługi ExpressRoute
Usługa ExpressRoute jest oferowana jako para nadmiarowe obwodu, aby zapewnić wysoką dostępność. Magazynu geograficznie nadmiarowego połączenia usługi ExpressRoute można skonfigurować w różnych regionach platformy Azure. Również jako gotowe w naszej konfiguracji testu w danym regionie platformy Azure, aby ścieżka trybu failover dla Twojego połączenia usługi ExpressRoute można zrobić za pomocą Site-to-Site VPN. Gdy te same prefiksy są anonsowane za pośrednictwem usługi ExpressRoute oraz sieci VPN S2S, Azure preferuje usługę ExpressRoute sieci VPN S2S. Aby uniknąć asymetryczne routingu usługi ExpressRoute i połączeń VPN S2S lokalne konfiguracji sieci należy również gospodarzami preferowanie usługi ExpressRoute za pośrednictwem połączenia sieci VPN S2S.

Aby uzyskać więcej informacji na temat sposobu konfigurowania współistniejących połączeń usługi ExpressRoute i sieci VPN typu lokacja-lokacja, zobacz [usługi ExpressRoute i współistnienia lokacja-lokacja][ExR-S2S-CoEx].

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>Rozszerzanie łączności z zapleczem do typu gwiazda sieci wirtualnych i gałęzi lokalizacje

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>Łączność z siecią wirtualną za pomocą komunikacji równorzędnej sieci wirtualnych szprych

Gwiazda sieć wirtualna architektura jest powszechnie używana. Centrum jest używana sieć wirtualna (VNet) na platformie Azure, która działa jako centralny punkt łączności między Twoje sieci wirtualne będące szprychami i siecią lokalną. Szprychy są sieciami wirtualnymi równorzędnymi z piastą i mogą być używane do izolowania obciążeń. Ruch przepływa między lokalnym centrum danych a piastą za pośrednictwem połączenia usługi ExpressRoute lub sieci VPN. Aby uzyskać szczegółowe informacje o architekturze, zobacz [Gwiazda — architektura][Hub-n-Spoke]

Wirtualne sieci równorzędne w obrębie regionu umożliwia szprychy sieciach wirtualnych do komunikowania się z sieciami zdalnymi za pomocą bram sieci wirtualnej koncentratora (bramy sieci VPN i ExpressRoute).

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Gałąź połączenie między sieciami przy użyciu sieci VPN typu lokacja-lokacja

Jeśli chcesz, aby gałąź sieciami wirtualnymi (w różnych regionach) i sieciami lokalnymi komunikują się ze sobą za pośrednictwem sieci wirtualnej koncentratora, natywne rozwiązanie na platformie Azure jest połączenie z siecią VPN lokacja lokacja przy użyciu sieci VPN. Jest dostępna Alternatywna opcja na potrzeby routingu w piaście urządzenia WUS.

Do konfigurowania bramy sieci VPN, zobacz [konfigurowania bramy sieci VPN][VPN]. Do wdrażania urządzeń WUS wysokiej dostępności, zobacz [wdrożenia o wysokiej dostępności urządzeń WUS][Deploy-NVA].

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak wiele obwodów usługi ExpressRoute, możesz nawiązać połączenie bramy usługi ExpressRoute lub ile bram usługi ExpressRoute można nawiązać połączenie obwodu usługi ExpressRoute lub Dowiedz się, inne limity skalowania usługi expressroute, zobacz [ExpressRoute — często zadawane pytania][ExR-FAQ]


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "widok usługi network Watcher łączności z piastą do sieci typu gwiazda"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "widok łączności z piastą do gałęzi w sieci wirtualnej usługi network Watcher"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "widoku siatki łączności z piastą do gałęzi w sieci wirtualnej usługi network Watcher"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "widok rozwiązania network Performance Monitor połączeń maszyny Wirtualnej 1 lokalizacji z piastą za pośrednictwem usługi ExpressRoute 1"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "widok rozwiązania network Performance Monitor połączeń maszyny Wirtualnej 1 lokalizacji z piastą za pośrednictwem sieci VPN S2S"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-config
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




