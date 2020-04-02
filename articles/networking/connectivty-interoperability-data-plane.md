---
title: 'Interoperacyjność na platformie Azure: Analiza płaszczyzny danych'
description: Ten artykuł zawiera analizę płaszczyzny danych konfiguracji testu, której można użyć do analizowania współdziałania między usługą ExpressRoute, sieci VPN typu lokacja-lokacja i komunikacji równorzędnej w sieci wirtualnej na platformie Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: fe7b74b0d4d065d4f222fefbbdc4a1d434d1163b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80518260"
---
# <a name="interoperability-in-azure--data-plane-analysis"></a>Interoperacyjność na platformie Azure: Analiza płaszczyzny danych

W tym artykule opisano analizę płaszczyzny danych [konfiguracji testu][Setup]. Można również przejrzeć [konfigurację konfiguracji testu][Configuration] i [analizę płaszczyzny sterowania][Control-Analysis] konfiguracji testu.

Analiza płaszczyzny danych sprawdza ścieżkę przebytą przez pakiety przechodzące z jednej sieci lokalnej (LAN lub sieci wirtualnej) do innej w ramach topologii. Ścieżka danych między dwiema sieciami lokalnymi nie musi być symetryczna. W związku z tym w tym artykule analizujemy ścieżkę przekazywania z sieci lokalnej do innej sieci, która jest oddzielona od ścieżki odwrotnej.

## <a name="data-path-from-the-hub-vnet"></a>Ścieżka danych z sieci wirtualnej koncentratora

### <a name="path-to-the-spoke-vnet"></a>Ścieżka do szprychy VNet

Komunikacja równorzędna sieci wirtualnej (VNet) emuluje funkcjonalność mostka sieciowego między dwiema sieciami wirtualnymi, które są równorzędne. Traceroute dane wyjściowe z hubowej sieci wirtualnej do maszyny Wirtualnej w sieci wirtualnej szprychy jest pokazany tutaj:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

Na poniższej ilustracji przedstawiono graficzny widok połączenia sieci wirtualnej koncentratora i sieci wirtualnej szprychy z punktu widzenia usługi Azure Network Watcher:


![1][1]

### <a name="path-to-the-branch-vnet"></a>Ścieżka do sieci wirtualnej oddziału

Traceroute dane wyjściowe z hubowej sieci wirtualnej do maszyny Wirtualnej w sieci wirtualnej gałęzi jest pokazany tutaj:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

W tej traceroute pierwszy przeskok jest bramą sieci VPN w usłudze Azure VPN Gateway sieci wirtualnej centrum. Drugi przeskok to brama sieci VPN sieci wirtualnej oddziału. Adres IP bramy sieci VPN sieci wirtualnej oddziału nie jest anonsowany w sieci wirtualnej centrum. Trzeci przeskok jest maszyną wirtualną na sieci wirtualnej gałęzi.

Na poniższym rysunku przedstawiono graficzny widok połączenia sieci wirtualnej koncentratora i sieci wirtualnej oddziału z punktu widzenia obserwatora sieciowego:

![2][2]

W przypadku tego samego połączenia na poniższej ilustracji przedstawiono widok siatki w obserwatorze sieci:

![3][3]

### <a name="path-to-on-premises-location-1"></a>Ścieżka do lokalizacji lokalnej 1

Traceroute dane wyjściowe z hubowej sieci wirtualnej do maszyny Wirtualnej w lokalnej lokalizacji 1 jest pokazany tutaj:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

W tej traceroute pierwszy przeskok jest punkt końcowy tunelu bramy usługi Azure ExpressRoute do routera Microsoft Enterprise Edge Router (MSEE). Drugi i trzeci przeskok to router ce (CE) klienta i lokalne adresy IP LAN lokalizacji 1. Te adresy IP nie są anonsowane w sieci wirtualnej centrum. Czwarty przeskok jest maszyną wirtualną w lokalizacji lokalnej 1.


### <a name="path-to-on-premises-location-2"></a>Ścieżka do lokalizacji lokalnej 2

Traceroute dane wyjściowe z hubowej sieci wirtualnej do maszyny Wirtualnej w lokalnej lokalizacji 2 jest pokazany tutaj:

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

W tej traceroute pierwszy przeskok jest punktem końcowym tunelu bramy usługi ExpressRoute do MSEE. Drugi i trzeci przeskok to router CE i lokalne adresy IP LAN lokalizacji 2. Te adresy IP nie są anonsowane w sieci wirtualnej centrum. Czwarty przeskok jest maszyną wirtualną w lokalizacji lokalnej 2.

### <a name="path-to-the-remote-vnet"></a>Ścieżka do zdalnej sieci wirtualnej

Traceroute dane wyjściowe z hubowej sieci wirtualnej do maszyny Wirtualnej w zdalnej sieci wirtualnej jest pokazany tutaj:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

W tej traceroute pierwszy przeskok jest punktem końcowym tunelu bramy usługi ExpressRoute do MSEE. Drugi przeskok to adres IP bramy zdalnej sieci wirtualnej. Drugi zakres adresów IP przeskoku nie jest anonsowany w sieci wirtualnej koncentratora. Trzeci przeskok jest maszyną wirtualną na zdalnej sieci wirtualnej.

## <a name="data-path-from-the-spoke-vnet"></a>Ścieżka danych z sieci wirtualnej szprychy

Szprychowa sieć wirtualna udostępnia widok sieciowy sieci wirtualnej koncentratora. Za pośrednictwem komunikacji równorzędnej sieci wirtualnej szprychy sieci wirtualnej używa łączności bramy zdalnej sieci wirtualnej koncentratora, tak jakby jest bezpośrednio połączony z siecią wirtualną szprychy.

### <a name="path-to-the-hub-vnet"></a>Ścieżka do sieci wirtualnej koncentratora

Traceroute dane wyjściowe z szprychowej sieci wirtualnej do maszyny Wirtualnej w sieci wirtualnej koncentratora jest pokazany tutaj:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet"></a>Ścieżka do sieci wirtualnej oddziału

Traceroute dane wyjściowe z szprychowej sieci wirtualnej do maszyny Wirtualnej w sieci wirtualnej gałęzi jest pokazany tutaj:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

W tej traceroute pierwszy przeskok jest bramą sieci VPN sieci wirtualnej centrum. Drugi przeskok to brama sieci VPN sieci wirtualnej oddziału. Adres IP bramy sieci VPN sieci wirtualnej oddziału nie jest anonsowany w sieci wirtualnej koncentratora/szprychy. Trzeci przeskok jest maszyną wirtualną na sieci wirtualnej gałęzi.

### <a name="path-to-on-premises-location-1"></a>Ścieżka do lokalizacji lokalnej 1

Traceroute dane wyjściowe z sieci wirtualnej szprychy do maszyny Wirtualnej w lokalizacji lokalnej 1 jest pokazany tutaj:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

W tej traceroute pierwszy przeskok jest punktem końcowym tunelu bramy usługi ExpressRoute w sieci wirtualnej koncentratora do MSEE. Drugi i trzeci przeskok to router CE i lokalne adresy IP LAN lokalizacji 1. Te adresy IP nie są anonsowane w sieci wirtualnej koncentratora/szprychy. Czwarty przeskok jest maszyną wirtualną w lokalizacji lokalnej 1.

### <a name="path-to-on-premises-location-2"></a>Ścieżka do lokalizacji lokalnej 2

Traceroute dane wyjściowe z sieci wirtualnej szprychy do maszyny Wirtualnej w lokalizacji lokalnej 2 jest pokazany tutaj:


    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    76 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

W tej traceroute pierwszy przeskok jest punktem końcowym tunelu bramy usługi ExpressRoute w sieci wirtualnej koncentratora do MSEE. Drugi i trzeci przeskok to router CE i lokalne adresy IP LAN lokalizacji 2. Te adresy IP nie są anonsowane w sieciach wirtualnych koncentratora/szprychy. Czwarty przeskok jest maszyną wirtualną w lokalizacji lokalnej 2.

### <a name="path-to-the-remote-vnet"></a>Ścieżka do zdalnej sieci wirtualnej

Traceroute dane wyjściowe z szprychowej sieci wirtualnej do maszyny Wirtualnej w zdalnej sieci wirtualnej jest pokazany tutaj:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

W tej traceroute pierwszy przeskok jest punktem końcowym tunelu bramy usługi ExpressRoute w sieci wirtualnej koncentratora do MSEE. Drugi przeskok to adres IP bramy zdalnej sieci wirtualnej. Drugi zakres adresu IP przeskoku nie jest anonsowany w sieci wirtualnej koncentratora/szprychy. Trzeci przeskok jest maszyną wirtualną na zdalnej sieci wirtualnej.

## <a name="data-path-from-the-branch-vnet"></a>Ścieżka danych z sieci wirtualnej gałęzi

### <a name="path-to-the-hub-vnet"></a>Ścieżka do sieci wirtualnej koncentratora

Traceroute dane wyjściowe z sieci wirtualnej oddziału do maszyny Wirtualnej w sieci wirtualnej koncentratora jest pokazany tutaj:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

W tym traceroute pierwszy przeskok jest bramą sieci VPN sieci wirtualnej oddziału. Drugi przeskok to brama sieci VPN sieci wirtualnej koncentratora. Adres IP bramy sieci VPN sieci wirtualnej koncentratora nie jest anonsowany w zdalnej sieci wirtualnej. Trzeci przeskok jest maszyną wirtualną na sieci wirtualnej koncentratora.

### <a name="path-to-the-spoke-vnet"></a>Ścieżka do szprychy VNet

Traceroute dane wyjściowe z sieci wirtualnej gałęzi do maszyny Wirtualnej w sieci wirtualnej szprychy jest pokazany tutaj:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

W tym traceroute pierwszy przeskok jest bramą sieci VPN sieci wirtualnej oddziału. Drugi przeskok to brama sieci VPN sieci wirtualnej koncentratora. Adres IP bramy sieci VPN sieci wirtualnej koncentratora nie jest anonsowany w zdalnej sieci wirtualnej. Trzeci przeskok jest maszyną wirtualną na sieci wirtualnej szprychy.

### <a name="path-to-on-premises-location-1"></a>Ścieżka do lokalizacji lokalnej 1

Traceroute dane wyjściowe z sieci wirtualnej oddziału do maszyny Wirtualnej w lokalnej lokalizacji 1 jest pokazany tutaj:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

W tym traceroute pierwszy przeskok jest bramą sieci VPN sieci wirtualnej oddziału. Drugi przeskok to brama sieci VPN sieci wirtualnej koncentratora. Adres IP bramy sieci VPN sieci wirtualnej koncentratora nie jest anonsowany w zdalnej sieci wirtualnej. Trzeci przeskok to punkt zakończenia tunelu SIECI VPN na podstawowym routerze CE. Czwarty przeskok to wewnętrzny adres IP lokalnej lokalizacji 1. Ten adres IP sieci LAN nie jest anonsowany poza routerem CE. Piąty przeskok jest docelową maszyną wirtualną w lokalnej lokalizacji 1.

### <a name="path-to-on-premises-location-2-and-the-remote-vnet"></a>Ścieżka do lokalnej lokalizacji 2 i zdalnej sieci wirtualnej

Jak wspomniano w analizie płaszczyzny sterowania, sieć wirtualna gałęzi nie ma widoczności ani do lokalnej lokalizacji 2 lub do zdalnej sieci wirtualnej na konfigurację sieci. Następujące wyniki pingu potwierdzają: 

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

## <a name="data-path-from-on-premises-location-1"></a>Ścieżka danych z lokalnej lokalizacji 1

### <a name="path-to-the-hub-vnet"></a>Ścieżka do sieci wirtualnej koncentratora

Traceroute dane wyjściowe z lokalnej lokalizacji 1 do maszyny Wirtualnej w sieci wirtualnej koncentratora jest pokazany tutaj:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

W tej traceroute pierwsze dwa przeskoki są częścią sieci lokalnej. Trzeci przeskok jest podstawowym interfejsem MSEE, który jest skierowany do routera CE. Czwarty przeskok jest bramą usługi ExpressRoute sieci wirtualnej koncentratora. Zakres adresów IP bramy usługi ExpressRoute sieci wirtualnej koncentratora nie jest anonsowany w sieci lokalnej. Piąty przeskok jest docelową maszyną wirtualną.

Kontrola sieci zapewnia tylko widok zorientowany na platformę Azure. Z punktu widzenia lokalnego używamy Usługi Azure Network Performance Monitor. Monitor wydajności sieci udostępnia agentów, które można zainstalować na serwerach w sieciach poza platformą Azure do analizy ścieżki danych.

Na poniższej ilustracji przedstawiono widok topologii lokalnej łączności maszyny Wirtualnej lokalizacji 1 z maszyną wirtualną na hubowej sieci wirtualnej za pośrednictwem usługi ExpressRoute:

![4][4]

Jak wspomniano wcześniej, konfiguracja testu używa sieci VPN lokacji lokacji jako łączności kopii zapasowej dla usługi ExpressRoute między lokalną lokalizacją 1 a siecią wirtualną centrum. Aby przetestować ścieżkę danych kopii zapasowej, wywołajmy błąd łącza Usługi ExpressRoute między lokalnym routerem podstawowego CE lokalizacji 1 a odpowiednim msee. Aby wywołać błąd łącza Usługi ExpressRoute, zamknij interfejs CE, który jest zgodny z msee:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

Na poniższej ilustracji przedstawiono widok topologii lokalnej łączności maszyny Wirtualnej lokalizacji 1 z maszyną wirtualną na hubowej sieci wirtualnej za pośrednictwem połączenia sieci VPN między lokacjami, gdy łączność usługi ExpressRoute jest wyłączana:

![5][5]

### <a name="path-to-the-spoke-vnet"></a>Ścieżka do szprychy VNet

Traceroute dane wyjściowe z lokalnego lokalizacja 1 do maszyny Wirtualnej w sieci wirtualnej szprychy jest pokazany tutaj:

Przywróćmy podstawową łączność usługi ExpressRoute, aby wykonać analizę ścieżki danych w kierunku sieci wirtualnej szprychy:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

Przywołanie podstawowej łączności usługi ExpressRoute 1 dla pozostałej części analizy ścieżki danych.

### <a name="path-to-the-branch-vnet"></a>Ścieżka do sieci wirtualnej oddziału

Traceroute dane wyjściowe z lokalnej lokalizacji 1 do maszyny Wirtualnej w sieci wirtualnej oddziału jest pokazany tutaj:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

### <a name="path-to-on-premises-location-2"></a>Ścieżka do lokalizacji lokalnej 2

Jak omówimy w [analizie płaszczyzny sterowania,][Control-Analysis]lokalna lokalizacja 1 nie ma widoczności lokalnej lokalizacji 2 na konfigurację sieci. Następujące wyniki pingu potwierdzają: 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

### <a name="path-to-the-remote-vnet"></a>Ścieżka do zdalnej sieci wirtualnej

Traceroute dane wyjściowe z lokalnej lokalizacji 1 do maszyny Wirtualnej w zdalnej sieci wirtualnej jest pokazany tutaj:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

## <a name="data-path-from-on-premises-location-2"></a>Ścieżka danych z lokalnej lokalizacji 2

### <a name="path-to-the-hub-vnet"></a>Ścieżka do sieci wirtualnej koncentratora

Traceroute dane wyjściowe z lokalnej lokalizacji 2 do maszyny Wirtualnej w sieci wirtualnej koncentratora jest pokazany tutaj:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>Ścieżka do szprychy VNet

Traceroute dane wyjściowe z lokalnego lokalizacja 2 do maszyny Wirtualnej w sieci wirtualnej szprychy jest pokazany tutaj:

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-on-premises-location-1-and-the-remote-vnet"></a>Ścieżka do sieci wirtualnej oddziału, lokalnej lokalizacji 1 i zdalnej sieci wirtualnej

Jak omówimy w [analizie płaszczyzny sterowania,][Control-Analysis]lokalna lokalizacja 1 nie ma widoczności sieci wirtualnej oddziału, lokalnej lokalizacji 1 lub zdalnej sieci wirtualnej na konfigurację sieci. 

## <a name="data-path-from-the-remote-vnet"></a>Ścieżka danych ze zdalnej sieci wirtualnej

### <a name="path-to-the-hub-vnet"></a>Ścieżka do sieci wirtualnej koncentratora

Traceroute dane wyjściowe ze zdalnej sieci wirtualnej do maszyny Wirtualnej w sieci wirtualnej koncentratora jest pokazany tutaj:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>Ścieżka do szprychy VNet

Traceroute dane wyjściowe ze zdalnej sieci wirtualnej do maszyny Wirtualnej w sieci wirtualnej szprychy jest pokazany tutaj:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-and-on-premises-location-2"></a>Ścieżka do sieci wirtualnej oddziału i lokalna lokalizacja 2

Jak omówimy w [analizie płaszczyzny sterowania,][Control-Analysis]zdalna sieć wirtualna nie ma widoczności sieci wirtualnej oddziału lub lokalnej lokalizacji 2 na konfigurację sieci. 

### <a name="path-to-on-premises-location-1"></a>Ścieżka do lokalizacji lokalnej 1

Traceroute dane wyjściowe ze zdalnej sieci wirtualnej do maszyny Wirtualnej w lokalnej lokalizacji 1 jest pokazany tutaj:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


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

Zobacz często zadawane [pytania dotyczące usługi ExpressRoute,][ExR-FAQ] aby:
-   Dowiedz się, ile obwodów usługi ExpressRoute można połączyć z bramą usługi ExpressRoute.
-   Dowiedz się, ile bram usługi ExpressRoute można połączyć z obwódem usługi ExpressRoute.
-   Dowiedz się więcej o innych ograniczeniach skali usługi ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "Widok obserwatora sieci łączności z sieci wirtualnej koncentratora do sieci wirtualnej szprychy"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "Widok usługi Network Watcher łączności z sieci wirtualnej koncentratora do sieci wirtualnej oddziału"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "Widok siatki obserwatora sieci sieciowego łączności z sieci wirtualnej koncentratora do sieci wirtualnej oddziału"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "Widok monitora wydajności sieci łączności z maszyny Wirtualnej lokalizacji 1 z hubową siecią wirtualną za pośrednictwem usługi ExpressRoute 1"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "Widok monitora wydajności sieci łączności z maszyny Wirtualnej lokalizacji 1 z siecią wirtualną koncentratora za pośrednictwem sieci VPN typu lokacja lokacja lokacja"

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


