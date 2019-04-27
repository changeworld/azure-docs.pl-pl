---
title: Dotyczące monitorowania sieci w usłudze Azure Monitor dzienników | Dokumentacja firmy Microsoft
description: Omówienie monitorowania rozwiązań, w tym Menedżer pakietów NPM oraz do zarządzania sieciami w środowiskach w chmurze, lokalne i hybrydowe sieci.
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
ms.openlocfilehash: 42fb5d69a1f32d669ad5191e342b3f2f880b8c98
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564819"
---
# <a name="network-monitoring-solutions"></a>Rozwiązania do monitorowania sieci 

Platforma Azure oferuje wiele rozwiązań do monitorowania zasobów sieciowych. Platforma Azure oferuje rozwiązania i narzędzia, aby monitorować łączność sieciową, kondycji obwodów usługi ExpressRoute i analizowania ruchu sieciowego w chmurze.

## <a name="network-performance-monitor-npm"></a>Rozwiązanie Network Performance Monitor (NPM)

Network Performance Monitor (NPM) to zestaw funkcji, z których każdy jest skierowana ku monitorowanie kondycji sieci, łączności sieciowej do aplikacji i zapewnia wgląd w wydajność sieci. NPM jest oparta na chmurze i zapewnia rozwiązanie monitorujące, które monitoruje łączność między sieci hybrydowych:
 
* Lokalizacje wdrożeń lokalnych i w chmurze
* Wiele centrów danych i biur oddziałów
* Misja krytyczne wielowarstwowej aplikacji/mikrousługi
* Lokalizacji użytkownika i aplikacji sieci web (HTTP/HTTPs) 

Monitor wydajności, Monitor usługi ExpressRoute i Monitor łączności usługi monitorowanymi możliwości w ramach NPM i są opisane poniżej.

## <a name="performance-monitor"></a>Monitorowanie wydajności

Monitor wydajności jest częścią NPM i jest monitorowania sieci w chmurze, hybrydowych i dla środowisk lokalnych. Można monitorować łączność sieciową w oddziałach zdalnych i biurach terenowych, lokalizacji magazynu, centrach danych i chmury. Można wykrywać problemy z siecią, zanim użytkownicy zaczną zgłaszać skargi. Dostępne są następujące kluczowe korzyści:

* Monitorowanie strat i opóźnień w różnych podsieciach oraz ustawianie alertów
* Monitorowanie wszystkich ścieżek (w tym nadmiarowe ścieżki) w sieci
* Rozwiązywanie problemów przejściowych i w momencie problemów z siecią, które są trudne do replikacji
* Ustalanie konkretnych segmentów w sieci, który jest odpowiedzialny za pogorszenie wydajności
* Monitorowanie kondycji sieci bez potrzeby korzystania z protokołu SNMP

![Mapy topologii NPM](./media/network-monitoring-overview/npm-topology-map.png) 

Aby uzyskać więcej informacji Wyświetl następujące artykuły:

* [Konfigurowanie rozwiązania monitora wydajności sieci w dziennikach w usłudze Azure Monitor](../azure-monitor/insights/network-performance-monitor.md) 
* [Przypadki użycia](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)
*  Aktualizacje produktów: [Luty 2017](https://blogs.technet.microsoft.com/msoms/2017/02/27/oms-network-performance-monitor-is-now-generally-available/), [sierpnia 2017 r.](https://blogs.technet.microsoft.com/msoms/2017/08/14/improvements-to-oms-network-performance-monitor/)

## <a name="expressroute-monitor"></a>Monitor usługi ExpressRoute

NPM dla usługi ExpressRoute oferuje kompleksowe usługi ExpressRoute monitorowania platformy Azure — prywatna komunikacja równorzędna i połączenia komunikacji równorzędnej firmy Microsoft. Możesz monitorować łączność E2E i wydajności między oddziałami i Azure za pośrednictwem usługi ExpressRoute. Do najważniejszych możliwości należą:

* Automatyczne wykrywanie obwodów ER skojarzonych z subskrypcją
* Wykrywanie topologii sieci ze środowiska lokalnego do aplikacji w chmurze
* Planowanie pojemności, analiza wykorzystania przepustowości
* Monitorowanie i alerty w ścieżkach podstawowych i pomocniczych
* Monitorowanie łączności na platformie Azure usług takich jak Office 365, Dynamics 365 za pośrednictwem usługi ExpressRoute
* Wykrywanie obniżenia łączności z sieciami wirtualnymi

![Ruch przedstawiający mapę geograficzną między regionami](./media/network-monitoring-overview/expressroute-topology-map.png) 

Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Konfigurowanie monitora wydajności sieci dla usługi ExpressRoute](../expressroute/how-to-npm.md)
* [Wpis w blogu](https://aka.ms/NPMExRmonitorGA)

## <a name="service-connectivity-monitor"></a>Monitorowanie łączności usług

Za pomocą łączność z usługą monitorowania, można przetestować dostępność aplikacji i wykrywać wąskie gardła wydajności w środowisku lokalnym, sieci i centrów danych w chmurze/prywatny.

* Monitorować łączność sieciową end-to-end do aplikacji
* Skorelowane dostarczanie aplikacji przy użyciu wydajność sieci, wykrywanie dokładną lokalizację na obniżenie wydajności wzdłuż ścieżki między użytkownikiem i aplikacji
* Testowanie osiągalności aplikacji z wielu lokalizacji użytkownika na całym świecie
* Określić czas oczekiwania i pakietów utrata połączenia sieciowego dla Twojego biznesowych i aplikacji SaaS
* Określenia punktów aktywnych w sieci, który może powodować niską wydajność aplikacji
* Monitoruj dostępność aplikacji usługi Office 365, przy użyciu wbudowanych testów dla Microsoft Office 365, Dynamics 365, Skype dla firm i innych usług firmy Microsoft

Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Konfigurowanie rozwiązania Network Performance Monitor do monitorowania punktów końcowych usługi](../azure-monitor/insights/network-performance-monitor-service-connectivity.md#configuration)
* [Wpis w blogu](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>Analiza ruchu
Analiza ruchu jest oparta na chmurze rozwiązania, który zapewnia wgląd w aktywność użytkownika i aplikacji w sieciach w chmurze. Dzienniki przepływów sieciowych grup zabezpieczeń, są analizowane udostępniają szczegółowe dane:

* Ruch odbywa się za pośrednictwem sieci między platformą Azure i Internet, regionach chmury publicznej, sieci wirtualne i podsieci
* Aplikacje i protokołów w sieci bez potrzeby ataki penetratorów lub urządzenia modułu zbierającego dedykowanych przepływu
* Najważniejsze nadajniki aplikacje intensywnych konwersacji maszyny Wirtualnej w chmurze, ruchu hotspotami
* Źródeł i miejsc docelowych ruchu między sieciami wirtualnymi, sieciami relacje między usług biznesowych i aplikacji
* Otwórz zabezpieczenia — złośliwy ruch, porty do Internetu, aplikacji lub maszyny wirtualne, próby uzyskania dostępu do Internetu...
* Wykorzystanie pojemności — pomaga wyeliminować problemy z konieczności nadmiernej aprowizacji lub zapobiec niedostatecznemu wykorzystaniu przez monitorowanie trendów wykorzystania bram sieci VPN i innych usług

Analiza ruchu wyposaża z możliwością wykonywania akcji informacji o inspekcji aktywności w sieci organizacji, bezpiecznych aplikacji i danych, pomaga zoptymalizować wydajność obciążenia i pozostają zgodne.

![Ruch przedstawiający mapę geograficzną między regionami](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

Linki pokrewne:
* [Wpis w blogu](https://aka.ms/trafficanalytics), [dokumentacji](https://aka.ms/trafficanalyticsdocs), [— często zadawane pytania](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-faq)

## <a name="dns-analytics"></a>Analiza DNS
Wbudowane administratorów DNS, to rozwiązanie zbiera, analizuje i koreluje dzienniki DNS zapewnienie bezpieczeństwa, operacji i informacje związane z wydajnością.  Niektóre funkcje są:

* Identyfikacja klientów próbujących rozpoznać złośliwe domeny
* Identyfikowanie starych rekordów
* Wgląd w domenie często poszukiwanych nazw i próg klienci DNS
* Wgląd w obciążenie żądaniami na serwerach DNS
* Monitorowanie niepowodzeń rejestracji dynamicznych DNS

![Pulpit nawigacyjny analiza DNS](./media/network-monitoring-overview/dns-analytics-overview.png) 

Linki pokrewne:
* [wpis w blogu](https://blogs.technet.microsoft.com/msoms/2017/04/19/introducing-oms-dns-analytics/), [dokumentacja](https://docs.microsoft.com/azure/log-analytics/log-analytics-dns)

## <a name="miscellaneous"></a>Różne postanowienia

* [Nowy cennik](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor-pricing-faq)
