---
title: 'Współdziałanie w funkcjach łączność z zaplecza platformy Azure: Analiza na płaszczyźnie danych | Dokumentacja firmy Microsoft'
description: Ten artykuł zawiera analizy płaszczyzny danych konfiguracji testu, które służy do analizowania współdziałanie usługi ExpressRoute, sieci VPN typu lokacja lokacja i wirtualnych sieci równorzędnych na platformie Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: f4d94536a8c1b509e0ce435a764e69984b5d415e
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499305"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-data-plane-analysis"></a>Współdziałanie w funkcjach łączność z zaplecza platformy Azure: Analiza płaszczyzny danych

W tym artykule opisano analizę płaszczyzny danych [Testuj ustawienia][Setup]. Możesz również przejrzeć [w konfiguracji testu] [ Configuration] i [analizy płaszczyznę sterowania] [ Control-Analysis] ustawień testu.

Analiza płaszczyzny danych sprawdza, czy ścieżka pakietów przechodzących z jednej sieci lokalnej (LAN lub sieć wirtualna) do innego w ramach topologii. Ścieżka danych między dwiema sieciami lokalnego nie jest koniecznie symetryczne. W związku z tym w tym artykule analizowane ścieżki przekazywania z sieci lokalnej z inną siecią, który jest oddzielony od Odwróć ścieżkę.

## <a name="data-path-from-the-hub-vnet"></a>Ścieżka danych z sieci wirtualnej serwera centralnego

### <a name="path-to-the-spoke-vnet"></a>Ścieżka do sieć wirtualną będącą szprychą.

Komunikacja równorzędna sieci wirtualnych (VNet) emuluje funkcjonalności mostek sieci między dwiema sieciami wirtualnymi, równorzędne. Polecenie traceroute dane wyjściowe z sieci wirtualnej serwera centralnego z maszyną wirtualną w szprysze, których sieci wirtualnej jest następująca:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

Poniższa ilustracja przedstawia widok graficzny połączenia sieci wirtualnej serwera centralnego i sieć wirtualną będącą szprychą pod względem usługi Azure Network Watcher:


[![1]][1]

### <a name="path-to-the-branch-vnet"></a>Ścieżka do gałęzi sieci wirtualnej

Polecenie traceroute dane wyjściowe z sieci wirtualnej serwera centralnego z maszyną wirtualną w gałęzi, którą sieci wirtualnej jest następująca:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

W tym traceroute pierwszym przeskokiem jest bramy sieci VPN w usłudze Azure VPN Gateway z sieci wirtualnej serwera centralnego. Drugim przeskokiem jest usługa VPN gateway gałęzi sieci wirtualnej. Adres IP bramy sieci VPN gałęzi sieci wirtualnej nie są anonsowane w sieci wirtualnej serwera centralnego. Trzeci przeskoku jest maszyną Wirtualną, w gałęzi sieci wirtualnej.

Poniższa ilustracja przedstawia widok graficzny połączenia sieci wirtualnej serwera centralnego i gałęzi sieci wirtualnej z perspektywy obserwatora sieciowego:

[![2]][2]

Dla tego samego połączenia na poniższej ilustracji przedstawiono widoku siatki w usługi Network Watcher:

[![3]][3]

### <a name="path-to-on-premises-location-1"></a>Ścieżka do lokalizacji lokalnej 1

Dane wyjściowe polecenie traceroute z piastą do maszyny Wirtualnej w lokalnej lokalizacji 1 jest następujący:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

W tym traceroute pierwszym przeskokiem jest punktem końcowym tunelu bramy usługi Azure ExpressRoute do firmy Microsoft Enterprise krawędzi routera (MSEE). Przeskoków druga i trzecia to router brzegowy (CE) klienta i adresów IP sieci LAN lokalnej lokalizacji 1. Te adresy IP nie są anonsowane w sieci wirtualnej serwera centralnego. Czwarty przeskokiem jest maszynę Wirtualną w lokalizacji 1 w środowisku lokalnym.


### <a name="path-to-on-premises-location-2"></a>Ścieżka do lokalizacji lokalnej 2

Dane wyjściowe polecenie traceroute z piastą do maszyny Wirtualnej w lokalnej lokalizacji 2 jest następujący:

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

W tym traceroute pierwszym przeskokiem jest punkt końcowy tunelu bramy usługi ExpressRoute do rozwiązania MSEE. Przeskoków druga i trzecia to CE router i adresów IP sieci LAN lokalnej lokalizacji 2. Te adresy IP nie są anonsowane w sieci wirtualnej serwera centralnego. Czwarty przeskoku jest maszyną Wirtualną w lokalizacji 2 w środowisku lokalnym.

### <a name="path-to-the-remote-vnet"></a>Ścieżka zdalna sieć wirtualna

Dane wyjściowe polecenie traceroute z piastą do maszyny Wirtualnej na platformie zdalna sieć wirtualna jest następujący:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

W tym traceroute pierwszym przeskokiem jest punkt końcowy tunelu bramy usługi ExpressRoute do rozwiązania MSEE. Drugim przeskokiem jest zdalna sieć wirtualna IP bramy. Drugi przeskok zakres adresów IP nie są anonsowane w sieci wirtualnej serwera centralnego. Trzeci przeskokiem jest maszyna wirtualna na zdalna sieć wirtualna.

## <a name="data-path-from-the-spoke-vnet"></a>Ścieżka danych z sieć wirtualną będącą szprychą.

Sieć wirtualną będącą szprychą udostępnia widok sieci w sieci wirtualnej serwera centralnego. Za pomocą komunikacji równorzędnej sieci wirtualnej, sieć wirtualną będącą szprychą używa łączności bramy zdalnej sieci wirtualnej serwera centralnego tak, jakby jest bezpośrednio połączona sieć wirtualną będącą szprychą.

### <a name="path-to-the-hub-vnet"></a>Ścieżka do sieci wirtualnej serwera centralnego

Polecenie traceroute dane wyjściowe z sieć wirtualną będącą szprychą do maszyny Wirtualnej w piaście, których sieci wirtualnej jest następująca:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet"></a>Ścieżka do gałęzi sieci wirtualnej

Polecenie traceroute dane wyjściowe z sieć wirtualną będącą szprychą do maszyny Wirtualnej w gałęzi, którą sieci wirtualnej jest następująca:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

W tym traceroute pierwszym przeskokiem jest brama sieci VPN sieci wirtualnej serwera centralnego. Drugim przeskokiem jest usługa VPN gateway gałęzi sieci wirtualnej. Adres IP bramy sieci VPN gałęzi sieci wirtualnej nie są anonsowane w ramach Centrum/sieć wirtualną będącą szprychą. Trzeci przeskoku jest maszyną Wirtualną, w gałęzi sieci wirtualnej.

### <a name="path-to-on-premises-location-1"></a>Ścieżka do lokalizacji lokalnej 1

Polecenie traceroute dane wyjściowe sieć wirtualną będącą szprychą do maszyny Wirtualnej w lokalnej lokalizacji 1 jest następujący:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

W tym traceroute pierwszym przeskokiem jest punkt końcowy tunelu bramy usługi ExpressRoute sieci wirtualnej koncentratora do rozwiązania MSEE. Przeskoków druga i trzecia to CE router i adresów IP sieci LAN lokalnej lokalizacji 1. Te adresy IP nie są anonsowane w Centrum/sieć wirtualną będącą szprychą. Czwarty przeskokiem jest maszynę Wirtualną w lokalizacji 1 w środowisku lokalnym.

### <a name="path-to-on-premises-location-2"></a>Ścieżka do lokalizacji lokalnej 2

Dane wyjściowe polecenie traceroute z sieć wirtualną będącą szprychą do maszyny Wirtualnej w lokalnej lokalizacji 2 jest następujący:


    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

W tym traceroute pierwszym przeskokiem jest punkt końcowy tunelu bramy usługi ExpressRoute sieci wirtualnej koncentratora do rozwiązania MSEE. Przeskoków druga i trzecia to CE router i adresów IP sieci LAN lokalnej lokalizacji 2. Te adresy IP nie są anonsowane w Centrum/sieci wirtualne będące szprychami. Czwarty przeskokiem jest maszynę Wirtualną w lokalizacji 2 w środowisku lokalnym.

### <a name="path-to-the-remote-vnet"></a>Ścieżka zdalna sieć wirtualna

Dane wyjściowe polecenie traceroute z sieć wirtualną będącą szprychą do maszyny Wirtualnej na platformie zdalna sieć wirtualna jest następujący:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

W tym traceroute pierwszym przeskokiem jest punkt końcowy tunelu bramy usługi ExpressRoute sieci wirtualnej koncentratora do rozwiązania MSEE. Drugim przeskokiem jest zdalna sieć wirtualna IP bramy. Drugiego przeskoku zakresu adresów IP nie są anonsowane w Centrum/sieć wirtualną będącą szprychą. Trzeci przeskokiem jest maszyna wirtualna na zdalna sieć wirtualna.

## <a name="data-path-from-the-branch-vnet"></a>Ścieżka danych z gałęzi sieci wirtualnej

### <a name="path-to-the-hub-vnet"></a>Ścieżka do sieci wirtualnej serwera centralnego

Polecenie traceroute dane wyjściowe z gałęzi sieci wirtualnej do maszyny Wirtualnej w piaście, których sieci wirtualnej jest następująca:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

W tym traceroute pierwszym przeskokiem jest usługa VPN gateway gałęzi sieci wirtualnej. Drugim przeskokiem jest brama sieci VPN sieci wirtualnej serwera centralnego. Adres IP bramy sieci VPN sieci wirtualnej serwera centralnego nie są anonsowane w zdalnej sieci wirtualnej. Trzeci przeskokiem jest maszyna wirtualna w sieci wirtualnej serwera centralnego.

### <a name="path-to-the-spoke-vnet"></a>Ścieżka do sieć wirtualną będącą szprychą.

Polecenie traceroute dane wyjściowe z gałęzi sieci wirtualnej do maszyny Wirtualnej w szprysze, których sieci wirtualnej jest następująca:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

W tym traceroute pierwszym przeskokiem jest usługa VPN gateway gałęzi sieci wirtualnej. Drugim przeskokiem jest brama sieci VPN sieci wirtualnej serwera centralnego. Adres IP bramy sieci VPN sieci wirtualnej serwera centralnego nie są anonsowane w zdalnej sieci wirtualnej. Trzeci przeskokiem jest maszyna wirtualna na sieć wirtualną będącą szprychą.

### <a name="path-to-on-premises-location-1"></a>Ścieżka do lokalizacji lokalnej 1

Dane wyjściowe polecenie traceroute z gałęzi sieci wirtualnej do maszyny Wirtualnej w lokalnej lokalizacji 1 jest następujący:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

W tym traceroute pierwszym przeskokiem jest usługa VPN gateway gałęzi sieci wirtualnej. Drugim przeskokiem jest brama sieci VPN sieci wirtualnej serwera centralnego. Adres IP bramy sieci VPN sieci wirtualnej serwera centralnego nie są anonsowane w zdalnej sieci wirtualnej. Trzeci przeskokiem jest punkt zakończenia tunelu sieci VPN na routerze CE podstawowego. Czwarty przeskokiem jest wewnętrzny adres IP 1 lokalizacji lokalnej. Ten adres IP sieci LAN nie są anonsowane za routerem CE. Piąty przeskoku jest lokalizacją docelową maszynę Wirtualną w lokalizacji 1 w środowisku lokalnym.

### <a name="path-to-on-premises-location-2-and-the-remote-vnet"></a>Ścieżka do lokalnej lokalizacji 2 i zdalna sieć wirtualna

Tak jak Omówiliśmy to w analizie płaszczyzna kontroli, gałąź sieć wirtualna ma nie widoczność 2 lokalizacji lokalnej lub zdalnej sieci wirtualnej dla konfiguracji sieci. Upewnij się, z następującymi wynikami ping: 

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

## <a name="data-path-from-on-premises-location-1"></a>Ścieżka danych z zakresu od 1 do lokalizacji lokalnej

### <a name="path-to-the-hub-vnet"></a>Ścieżka do sieci wirtualnej serwera centralnego

Polecenie traceroute dane wyjściowe z zakresu od 1 do lokalizacji lokalnej do maszyny Wirtualnej w piaście, których sieci wirtualnej jest następująca:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

W tym traceroute przeskoków pierwsze dwa są częścią sieci lokalnej. Trzeci przeskokiem jest podstawowy interfejs MSEE skierowana routera CE. Czwarty przeskokiem jest brama usługi ExpressRoute z sieci wirtualnej serwera centralnego. Zakres adresów IP bramy usługi ExpressRoute z sieci wirtualnej serwera centralnego nie są anonsowane do sieci lokalnej. Piąty przeskokiem jest docelowej maszyny Wirtualnej.

Usługa Network Watcher zawiera tylko widokowi skoncentrowanemu na platformie Azure. Perspektywy w środowisku lokalnym używamy usługi Azure Network Performance Monitor. Rozwiązanie Network Performance Monitor zawiera agentów, które można zainstalować na serwerach w sieci poza systemem Azure dla analizy ścieżki danych.

Na poniższej ilustracji przedstawiono widok topologii połączeń maszyny Wirtualnej 1 lokalizacji lokalnych z maszyną wirtualną na piastą za pośrednictwem usługi ExpressRoute:

[![4]][4]

Zgodnie z wcześniejszym opisem konfiguracji testu używa sieci VPN lokacja lokacja jako połączenia kopii zapasowych dla usługi ExpressRoute między 1 lokalizacji lokalnej do sieci wirtualnej serwera centralnego. Aby przetestować ścieżki kopii zapasowej danych, możemy wywołać wystąpił błąd linku ExpressRoute między router lokalny 1 lokalizacji głównej CE i odpowiedniego rozwiązania MSEE. Do wywołania błąd linku ExpressRoute, należy zamknąć interfejs CE, który twarzy MSEE:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

Na poniższej ilustracji przedstawiono widok topologii połączeń maszyny Wirtualnej 1 lokalizacji lokalnych z maszyną wirtualną w sieci wirtualnej serwera centralnego za pośrednictwem połączenia sieci VPN lokacja lokacja, gdy połączenia usługi ExpressRoute nie działa:

[![5]][5]

### <a name="path-to-the-spoke-vnet"></a>Ścieżka do sieć wirtualną będącą szprychą.

Polecenie traceroute dane wyjściowe z zakresu od 1 do lokalizacji lokalnej do maszyny Wirtualnej w szprysze, których sieci wirtualnej jest następująca:

Teraz przywrócić połączenia podstawowej usługi ExpressRoute w celu analizy danych ścieżki w kierunku sieć wirtualną będącą szprychą:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

Wyświetlenie podstawowej łączności 1 usługi ExpressRoute w pozostałej części analizy ścieżki danych.

### <a name="path-to-the-branch-vnet"></a>Ścieżka do gałęzi sieci wirtualnej

Polecenie traceroute dane wyjściowe z zakresu od 1 do lokalizacji lokalnych z maszyną wirtualną w gałęzi, którą sieci wirtualnej jest następująca:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

### <a name="path-to-on-premises-location-2"></a>Ścieżka do lokalizacji lokalnej 2

Omawiamy w [analizy płaszczyznę sterowania][Control-Analysis], 1 lokalizacji lokalnej mają wgląd w nie 2 lokalizacji w środowisku lokalnym na konfigurację sieci. Upewnij się, z następującymi wynikami ping: 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

### <a name="path-to-the-remote-vnet"></a>Ścieżka zdalna sieć wirtualna

Dane wyjściowe polecenie traceroute z zakresu od 1 do lokalizacji lokalnej maszyny Wirtualnej na platformie zdalna sieć wirtualna jest następujący:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

## <a name="data-path-from-on-premises-location-2"></a>Ścieżka danych z lokalnej lokalizacji 2

### <a name="path-to-the-hub-vnet"></a>Ścieżka do sieci wirtualnej serwera centralnego

Polecenie traceroute dane wyjściowe z lokalnej lokalizacji 2 do maszyny Wirtualnej w piaście, których sieci wirtualnej jest następująca:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>Ścieżka do sieć wirtualną będącą szprychą.

Polecenie traceroute dane wyjściowe z 2 lokalizacji lokalnych z maszyną wirtualną w szprysze, których sieci wirtualnej jest następująca:

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-on-premises-location-1-and-the-remote-vnet"></a>Ścieżka do gałęzi sieci wirtualnej, w środowisku lokalnym 1 lokalizacji i zdalna sieć wirtualna

Omawiamy w [analizy płaszczyznę sterowania][Control-Analysis], 1 lokalizacji lokalnych ma nie widoczność gałęzi sieci wirtualnej, 1 lokalizacji lokalnej lub zdalna sieć wirtualna dla konfiguracji sieci. 

## <a name="data-path-from-the-remote-vnet"></a>Ścieżka danych z zdalna sieć wirtualna

### <a name="path-to-the-hub-vnet"></a>Ścieżka do sieci wirtualnej serwera centralnego

Polecenie traceroute dane wyjściowe z zdalna sieć wirtualna do maszyny Wirtualnej w piaście, których sieci wirtualnej jest następująca:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>Ścieżka do sieć wirtualną będącą szprychą.

Polecenie traceroute dane wyjściowe z zdalna sieć wirtualna do maszyny Wirtualnej w szprysze, których sieci wirtualnej jest następująca:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-and-on-premises-location-2"></a>Ścieżka do gałęzi sieci wirtualnej i lokalnej lokalizacji 2

Omawiamy w [analizy płaszczyznę sterowania][Control-Analysis], zdalna sieć wirtualna ma nie wgląd, gałęzi sieci wirtualnej lub 2 lokalizacji w środowisku lokalnym na konfigurację sieci. 

### <a name="path-to-on-premises-location-1"></a>Ścieżka do lokalizacji lokalnej 1

Polecenie traceroute dane wyjściowe z zdalna sieć wirtualna, do maszyny Wirtualnej w lokalnej lokalizacji 1 jest następujący:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


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

Zobacz [ExpressRoute — często zadawane pytania] [ ExR-FAQ] do:
-   Dowiedz się, jak wiele obwodów usługi ExpressRoute, możesz nawiązać połączenie bramy usługi ExpressRoute.
-   Dowiedz się, ile bram usługi ExpressRoute, można połączyć z obwodem usługi ExpressRoute.
-   Poznaj inne limity skalowania usługi expressroute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "widok łączności z piastą sieć wirtualną będącą szprychą usługi network Watcher"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "widok połączeń sieci wirtualnej serwera centralnego z gałęzi sieci wirtualnej usługi network Watcher"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "widoku siatki łączności z piastą do gałęzi sieci wirtualnej usługi network Watcher"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "widok rozwiązania network Performance Monitor połączeń maszyny Wirtualnej 1 lokalizacji z piastą za pośrednictwem usługi ExpressRoute 1"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "widok rozwiązania network Performance Monitor połączeń maszyny Wirtualnej 1 lokalizacji z piastą za pośrednictwem sieci VPN lokacja lokacja"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
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


