---
title: Monitorowanie sieci w dziennikach usługi Azure Monitor — informacje | Dokumenty firmy Microsoft
description: Omówienie rozwiązań do monitorowania sieci, w tym NPM, do zarządzania sieciami w chmurze, lokalnie i środowiskach hybrydowych.
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: agummadi
ms.openlocfilehash: 2912488286745bf8d2e567d09e445b0a44dc7c39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67672183"
---
# <a name="network-monitoring-solutions"></a>Rozwiązania do monitorowania sieci 

Platforma Azure oferuje wiele rozwiązań do monitorowania zasobów sieciowych. Platforma Azure ma rozwiązania i narzędzia do monitorowania łączności sieciowej, kondycji obwodów usługi ExpressRoute i analizowania ruchu sieciowego w chmurze.

## <a name="network-performance-monitor-npm"></a>Monitor wydajności sieci (NPM)

Monitor wydajności sieci (NPM) to zestaw funkcji, z których każdy jest ukierunkowany na monitorowanie kondycji sieci, łączności sieciowej z aplikacjami i zapewnia wgląd w wydajność sieci. Serwer NPM jest oparty na chmurze i zapewnia rozwiązanie do monitorowania sieci hybrydowej, które monitoruje łączność między:
 
* Wdrożenia w chmurze i lokalizacje lokalne
* Wiele centrów danych i oddziałów
* Krytyczne aplikacje wielowarstwowe/mikroserce
* Lokalizacje użytkowników i aplikacje internetowe (HTTP/HTTPs) 

Monitor wydajności, monitor usługi ExpressRoute i monitor łączności usługi są funkcje monitorowania w ramach npm i są opisane poniżej.

## <a name="performance-monitor"></a>Monitorowanie wydajności

Monitor wydajności jest częścią serwera NPM i jest monitorowaniem sieci dla środowisk chmurowych, hybrydowych i lokalnych. Możesz monitorować łączność sieciową w zdalnych oddziałach i biurach terenowych, lokalizacjach sklepów, centrach danych i chmurach. Możesz wykryć problemy z siecią, zanim użytkownicy zdasz skargę. Główne zalety to:

* Monitorowanie utraty i opóźnień w różnych podsieciach i ustawianie alertów
* Monitorowanie wszystkich ścieżek (w tym ścieżek nadmiarowych) w sieci
* Rozwiązywanie problemów z siecią przejściową i punktu w czasie, które są trudne do powtórzenia
* Określ konkretny segment w sieci, który jest odpowiedzialny za spadek wydajności
* Monitorowanie kondycji sieci bez konieczności stosowania

![Mapa topologii NPM](./media/network-monitoring-overview/npm-topology-map.png) 

Aby uzyskać więcej informacji, zobacz następujące artykuły:

* [Konfigurowanie rozwiązania Monitora wydajności sieci w dziennikach usługi Azure Monitor](../azure-monitor/insights/network-performance-monitor.md) 
* [Przypadki zastosowań](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)
* Aktualizacje produktów:
  * [Luty 2017](https://blogs.technet.microsoft.com/msoms/2017/02/27/oms-network-performance-monitor-is-now-generally-available/)
  * [Sierpień 2017](https://blogs.technet.microsoft.com/msoms/2017/08/14/improvements-to-oms-network-performance-monitor/)

## <a name="expressroute-monitor"></a>ExpressRoute Monitor

Serwer NPM dla usługi ExpressRoute oferuje kompleksowe monitorowanie usługi ExpressRoute dla prywatnej komunikacji równorzędnej platformy Azure i połączeń równorzędnych firmy Microsoft. Możesz monitorować łączność i wydajność usługi E2E między oddziałami firmy a platformą Azure za pomocą usługi ExpressRoute. Kluczowe możliwości to:

* Automatyczne wykrywanie obwodów ER skojarzonych z subskrypcją
* Wykrywanie topologii sieci z lokalnych aplikacji do aplikacji w chmurze
* Planowanie pojemności, analiza wykorzystania przepustowości
* Monitorowanie i ostrzeganie zarówno na ścieżkach podstawowych, jak i pomocniczych
* Monitorowanie łączności z usługami platformy Azure, takimi jak Office 365, Dynamics 365, ... nad programem ExpressRoute
* Wykrywanie degradacji łączności z sieciami wirtualnymi

![Mapa geograficzna pokazująca ruch w różnych regionach](./media/network-monitoring-overview/expressroute-topology-map.png) 

Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Konfigurowanie monitora wydajności sieci dla usługi ExpressRoute](../expressroute/how-to-npm.md)
* [Blogu](https://aka.ms/NPMExRmonitorGA)

## <a name="service-connectivity-monitor"></a>Monitorowanie łączności usług

Dzięki monitorowaniu łączności usług można teraz testować osiągalność aplikacji i wykrywać wąskie gardła wydajności w lokalnych, sieciach operatorskich oraz chmurowych/prywatnych centrach danych.

* Monitorowanie kompleksowej łączności sieciowej z aplikacjami
* Skorelowanie dostarczania aplikacji z wydajnością sieci, wykrywanie dokładnej lokalizacji degradacji wzdłuż ścieżki między użytkownikiem a aplikacją
* Testowanie osiągów aplikacji z wielu lokalizacji użytkowników na całym świecie
* Określanie opóźnień sieci i utraty pakietów dla swojej linii biznesowej i aplikacji SaaS
* Określanie punktów zapalnych w sieci, które mogą powodować niską wydajność aplikacji
* Monitorowanie osiągów aplikacji usługi Office 365 przy użyciu wbudowanych testów dla usług Microsoft Office 365, Dynamics 365, Skype dla firm i innych usług firmy Microsoft

Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Konfigurowanie Monitora wydajności sieci do monitorowania punktów końcowych usługi](../azure-monitor/insights/network-performance-monitor-service-connectivity.md#configuration)
* [Blogu](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>Analiza ruchu
Usługa Traffic Analytics to rozwiązanie oparte na chmurze, które zapewnia wgląd w aktywność użytkowników i aplikacji w sieciach w chmurze. Dzienniki przepływu nsg są analizowane w celu zapewnienia wglądu w:

* Ruch przepływa przez sieci między platformą Azure a Internetem, regionami chmury publicznej, sieciami wirtualnymi i podsieciami
* Aplikacje i protokoły w sieci, bez konieczności stosowania snifferów lub dedykowanych urządzeń modułu zbierającego przepływ
* Top talkers, chatty aplikacji, rozmowy VM w chmurze, hotspoty ruchu
* Źródła i miejsca docelowe ruchu w sieciach wirtualnych, wzajemnych relacjach między krytycznymi usługami biznesowymi a aplikacjami
* Bezpieczeństwo — złośliwy ruch, porty otwarte dla Internetu, aplikacje lub maszyny wirtualne próbujące uzyskać dostęp do Internetu...
* Wykorzystanie pojemności — pomaga wyeliminować problemy z nadmierną aprowizacją lub niepełną licencją, monitorując trendy wykorzystania bram SIECI VPN i innych usług

Usługa Traffic Analytics zapewnia użyteczne informacje, które pomagają kontrolować aktywność sieciową organizacji, zabezpieczać aplikacje i dane, optymalizować wydajność obciążenia i zachować zgodność.

![Mapa geograficzna pokazująca ruch w różnych regionach](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

Powiązane linki:
* [Wpis w blogu](https://aka.ms/trafficanalytics), [Dokumentacja](https://aka.ms/trafficanalyticsdocs), [Często zadawane pytania](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-faq)

## <a name="dns-analytics"></a>Analiza DNS
Stworzone z myślą o administratorach systemu DNS, to rozwiązanie zbiera, analizuje i koreluje dzienniki DNS w celu zapewnienia zabezpieczeń, operacji i szczegółowych informacji związanych z wydajnością.  Niektóre z możliwości to:

* Identyfikacja klientów, którzy próbują rozpoznać złośliwe domeny
* Identyfikacja starych rekordów zasobów
* Wgląd w często poszukiwane nazwy domen i rozmownych klientów DNS
* Wgląd w obciążenie żądania na serwerach DNS
* Monitorowanie dynamicznych niepowodzeń rejestracji DNS

![Pulpit nawigacyjny analizy DNS](./media/network-monitoring-overview/dns-analytics-overview.png) 

Powiązane linki:
* [wpis w blogu](https://blogs.technet.microsoft.com/msoms/2017/04/19/introducing-oms-dns-analytics/), [dokumentacja](https://docs.microsoft.com/azure/log-analytics/log-analytics-dns)

## <a name="miscellaneous"></a>Różne

* [Nowe ceny](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor-pricing-faq)
