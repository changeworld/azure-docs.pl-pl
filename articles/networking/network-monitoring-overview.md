---
title: Dotyczące monitorowania sieci w Log Analytics | Dokumentacja firmy Microsoft
description: Omówienie rozwiązania NPM, zarządzać sieciami środowiskach chmury, lokalne i hybrydowe monitorowania sieci.
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ajaycode
ms.openlocfilehash: aea43eed0cb66b0e0d3bb7a393f569714feb475d
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850141"
---
# <a name="network-monitoring-solutions"></a>Monitorowanie rozwiązań sieci 

System Azure oferuje hosta rozwiązań do monitorowania zasobów sieciowych. Platforma Azure ma rozwiązania i narzędzia do monitorowania łączność z siecią, kondycję obwody usługi ExpressRoute i analizowania ruchu sieciowego w chmurze.

## <a name="network-performance-monitor-npm"></a>Monitor wydajności sieci (NPM)

Monitor wydajności sieci (NPM) to zestaw funkcji, z których każdy jest przeznaczone dla monitorowanie kondycji sieci, połączenie sieciowe z aplikacjami i zapewnia wgląd w wydajności sieci. NPM jest oparta na chmurze i udostępnia sieci hybrydowe rozwiązanie monitorujące, które monitoruje łączność między:
 
* Lokalizacje wdrożeń i lokalnej w chmurze
* Wiele centrów danych oraz biura oddziałów
* Misji wielowarstwowe aplikacje/micro-obsługi usług krytycznych
* Lokalizacji użytkownika i aplikacji sieci web (HTTP/HTTPs) 

Monitor wydajności, ExpressRoute Monitor i Monitor punktu końcowego usługi monitorowania możliwości w ramach programu NPM i są opisane poniżej.

## <a name="performance-monitor"></a>Monitorowanie wydajności

Monitor wydajności jest częścią programu NPM i jest monitorowanie sieci dla chmury, hybrydowej i lokalnych środowisk. Można monitorować łączność sieciową między gałęzi zdalnej i pola oddziałów, lokalizacji magazynu centrów danych i chmur. Użytkownicy skarżą się, można wykrywać problemy z siecią. Dostępne są następujące kluczowe korzyści:

* Monitorowanie strat i opóźnień w różnych podsieciach oraz ustawianie alertów
* Monitorowanie wszystkich ścieżek (w tym nadmiarowe ścieżki) w sieci
* Przejściowych i w momencie Rozwiązywanie problemów z sieci, które są trudne do replikacji
* Określić określonego segmentu w sieci, która jest odpowiedzialna za pogorszenie wydajności
* Monitorowanie kondycji sieci bez potrzeby korzystania z protokołu SNMP

![Mapy topologii NPM](./media/network-monitoring-overview/npm-topology-map.png) 

Aby uzyskać więcej informacji Wyświetl następujące artykuły:

* [Konfigurowanie rozwiązania monitora wydajności sieci w analizy dzienników](../log-analytics/log-analytics-network-performance-monitor.md) 
* [Przypadki użycia](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)
*  Aktualizacje produktu: [lutego 2017](https://blogs.technet.microsoft.com/msoms/2017/02/27/oms-network-performance-monitor-is-now-generally-available/), [2017 sierpnia](https://blogs.technet.microsoft.com/msoms/2017/08/14/improvements-to-oms-network-performance-monitor/)

## <a name="expressroute-monitor"></a>Monitor usługi ExpressRoute

NPM dla ExpressRoute oferuje kompleksowe ExpressRoute monitorowania Azure prywatnej komunikacji równorzędnej i połączenia komunikacji równorzędnej firmy Microsoft. Można monitorować łączność E2E i wydajności między oddziałach i Azure za pośrednictwem usługi ExpressRoute. Do najważniejszych możliwości należą:

* Automatyczne wykrywanie obwody ER skojarzone z subskrypcją
* Wykrywanie topologii sieci z lokalnej do aplikacji w chmurze
* Planowanie pojemności, analiza wykorzystania przepustowości
* Monitorowanie i alerty na głównych i dodatkowych ścieżek
* Monitorowanie łączności Azure usług takich jak Office 365, Dynamics 365... za pośrednictwem usługi ExpressRoute
* Wykryj spadek łączność sieci wirtualnych

![Obszar mapy przedstawiający ruchu w regionach](./media/network-monitoring-overview/expressroute-topology-map.png) 

Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Konfigurowanie monitora wydajności sieci dla usługi ExpressRoute](../expressroute/how-to-npm.md)
* [wpis w blogu](https://aka.ms/NPMExRmonitorGA)

## <a name="service-endpoint-monitor"></a>Monitor punktu końcowego usługi

Z punktu końcowego usługi monitorowania, można przetestować uzyskiwanie aplikacji i wykrywania wąskich gardeł wydajności między lokalnymi, sieci i centrów danych w chmurze i prywatnego.

* Monitoruje łączność sieciową na trasie do aplikacji
* Korelowanie dostarczanie aplikacji z wydajność sieci, wykrywanie dokładnej lokalizacji degradacji wzdłuż ścieżki między użytkownikiem i aplikacji
* Testowanie aplikacji uzyskiwanie z wielu lokalizacji użytkownika na całym świecie
* Określić strat opóźnienia i pakietów sieciowych dla linii firm i aplikacji SaaS
* Określić punkty aktywne w sieci, który może powodować niską wydajnością
* Monitorowanie uzyskiwanie aplikacji usługi Office 365, przy użyciu wbudowanych testów dla usługi Microsoft Office 365, Dynamics 365 Skype dla firm i innych usług firmy Microsoft

Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Konfigurowanie monitora wydajności sieci do monitorowania punktów końcowych usług](https://aka.ms/applicationconnectivitymonitorguide)
* [wpis w blogu](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>Analiza ruchu
Analiza ruchu jest oparta na chmurze rozwiązaniem, które zapewnia wgląd w działania użytkowników i aplikacji w sieciach w chmurze. Przepływ NSG dzienniki są analizowane zapewniające wgląd w informacje:

* Ruch w sieci platformy Azure i Internet, regionów chmury publicznej, sieci wirtualnych i podsieci
* Aplikacje i protokołów w sieci, bez konieczności ataki penetratorów lub dedykowanych przepływu modułu zbierającego urządzenia
* Górny talkers chatty aplikacji konwersacje maszyny Wirtualnej w chmurze, ruch punkty aktywne
* Źródła i miejsc docelowych ruchu między sieciami wirtualnymi, między relacje między usługi o krytycznym znaczeniu dla firmy i aplikacji
* Zabezpieczenia — szkodliwy ruch, porty Otwórz internetowych, aplikacji lub maszyn wirtualnych próby dostępu do Internetu...
* Wykorzystanie pojemności — pomaga wyeliminować problemy przerostu lub niedostateczne wykorzystanie zasobów przez monitorowanie trendów użycia bram sieci VPN i innych usług

Analiza ruchu wyposaża można wykonać informacje, czy pomaga monitorować aktywność sieci organizacji, bezpiecznych aplikacji i danych, optymalizowanie obciążenia i pozostać zgodne.

![Obszar mapy przedstawiający ruchu w regionach](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

Linki pokrewne:
* [Wpis w blogu](https://aka.ms/trafficanalytics), [dokumentacji](https://aka.ms/trafficanalyticsdocs), [— często zadawane pytania](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-faq)

## <a name="dns-analytics"></a>Analiza DNS
Utworzony dla administratorów DNS, to rozwiązanie gromadzi, analizuje i skorelowany dzienniki DNS zapewnienie bezpieczeństwa, operacje i związanych z wydajnością szczegółowych informacji.  Niektóre funkcje są:

* Identyfikacja klientów, którzy rozwiązywania złośliwego domen
* Identyfikacja starych rekordów
* Wgląd w często, którego dotyczy kwerenda domen i talkative klientów DNS
* Wgląd w ładunku żądania na serwerach DNS
* Monitorowanie niepowodzeń dynamicznej rejestracji DNS

![Pulpitu nawigacyjnego Analytics DNS](./media/network-monitoring-overview/dns-analytics-overview.png) 

Linki pokrewne:
* [Wpis w blogu](https://blogs.technet.microsoft.com/msoms/2017/04/19/introducing-oms-dns-analytics/), [dokumentacji](https://docs.microsoft.com/azure/log-analytics/log-analytics-dns)

## <a name="miscellaneous"></a>Różne postanowienia

* [Nowe ceny](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor-pricing-faq)
