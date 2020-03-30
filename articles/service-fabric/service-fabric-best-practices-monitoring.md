---
title: Najważniejsze wskazówki dotyczące monitorowania sieci szkieletowej usług azure
description: Najważniejsze wskazówki dotyczące i zagadnienia dotyczące projektowania dotyczące monitorowania klastrów i aplikacji przy użyciu usługi Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: af03223e8b007cbd2a00d54c3076056cd110ecc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551820"
---
# <a name="monitoring-and-diagnostics"></a>Monitorowanie i diagnostyka

[Monitorowanie i diagnostyka](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) mają kluczowe znaczenie dla tworzenia, testowania i wdrażania obciążeń w dowolnym środowisku chmury. Na przykład można śledzić, jak aplikacje są używane, akcje podejmowane przez platformę sieci szkieletowej usług, wykorzystanie zasobów z licznikami wydajności i ogólną kondycję klastra. Te informacje można używać do diagnozowania i rozwiązywania problemów oraz zapobiegania ich występowaniu w przyszłości.

## <a name="application-monitoring"></a>Monitorowanie aplikacji

Monitorowanie aplikacji śledzi, jak funkcje i składniki aplikacji są używane. Monitoruj aplikacje, aby upewnić się, że problemy, które mają wpływ na użytkowników są przechwytywać. Monitorowanie aplikacji jest odpowiedzialny za tych, którzy rozwijają aplikację i jej usługi, ponieważ jest unikatowy dla logiki biznesowej aplikacji. Zaleca się skonfigurowanie monitorowania aplikacji za pomocą [usługi Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-monitoring-aspnet), narzędzia do monitorowania aplikacji platformy Azure.

## <a name="cluster-monitoring"></a>Monitorowanie klastrów

Jednym z celów sieci szkieletowej usług jest uczynienie aplikacji odpornymi na awarie sprzętu. Ten cel jest osiągany za pomocą możliwości usług systemowych platformy do wykrywania problemów z infrastrukturą i szybko pracy awaryjnej obciążeń do innych węzłów w klastrze. Ale co zrobić, jeśli same usługi systemowe mają problemy? Lub jeśli podczas próby wdrożenia lub przeniesienia obciążenia, zasady umieszczania usług są naruszane? Sieci szkieletowej usług zapewnia diagnostyki dla tych i innych problemów, aby upewnić się, że są informowani o tym, jak platforma sieci szkieletowej usług współdziała z aplikacjami, usługami, kontenerami i węzłami.

W przypadku klastrów systemu Windows zaleca się skonfigurowanie monitorowania klastra za pomocą [dzienników Agent diagnostyki](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) i [usługi Azure Monitor](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-setup).

W przypadku klastrów systemu Linux dzienniki usługi Azure Monitor są również zalecanym narzędziem do monitorowania platformy Azure i infrastruktury. Diagnostyka platformy Linux wymaga innej konfiguracji, jak wspomniano w [zdarzeniach klastra Sieci Linuksa w Syslog](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-syslog).

## <a name="infrastructure-monitoring"></a>Monitorowanie infrastruktury

[Dzienniki usługi Azure Monitor](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-agent) jest zalecane do monitorowania zdarzeń na poziomie klastra. Po skonfigurowaniu agenta usługi Log Analytics z obszarem roboczym, jak opisano w poprzednim łączu, będzie można zbierać metryki wydajności, takie jak wykorzystanie procesora CPU, liczniki wydajności .NET, takie jak wykorzystanie procesora CPU na poziomie procesu, liczniki wydajności sieci szkieletowej usług, takie jak # wyjątków z niezawodnej usługi, i metryki kontenera, takie jak wykorzystanie procesora CPU.  Należy napisać dzienniki kontenerów do stdout lub stderr, tak aby były one dostępne w dziennikach usługi Azure Monitor.

## <a name="watchdogs"></a>Strażnicy

Ogólnie rzecz biorąc watchdog jest oddzielną usługą, która obserwuje kondycję i obciążenie między usługami, pinguje punkty końcowe i zgłasza nieoczekiwane zdarzenia kondycji w klastrze. Może to pomóc w zapobieganiu błędom, które mogą nie zostać wykryte tylko na podstawie wydajności jednej usługi. Watchdogs są również dobrym miejscem do hostowania kodu, który wykonuje działania naprawcze, które nie wymagają interakcji użytkownika, takich jak czyszczenie plików dziennika w magazynie w określonych odstępach czasu. Zobacz przykładową implementację usługi watchdog w [zdarzeniach klastra sieci szkieletowej sieci szkieletowej usługi w syslogu](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="next-steps"></a>Następne kroki

* Rozpocznij instrumentowanie aplikacji: [Generowanie zdarzeń na poziomie aplikacji i dzienników](service-fabric-diagnostics-event-generation-app.md).
* Przejdź przez kroki, aby skonfigurować usługę Application Insights dla aplikacji za pomocą [monitora i zdiagnozować aplikację ASP.NET Core w sieci szkieletowej usług.](service-fabric-tutorial-monitoring-aspnet.md)
* Dowiedz się więcej na temat monitorowania platformy i zdarzeń, które zapewnia usługa Fabric dla Ciebie: [Generowanie zdarzeń na poziomie platformy i dziennika.](service-fabric-diagnostics-event-generation-infra.md)
* Konfigurowanie integracji dzienników usługi Azure Monitor z siecią szkieletową usług: [konfigurowanie dzienników usługi Azure Monitor dla klastra](service-fabric-diagnostics-oms-setup.md)
* Dowiedz się, jak skonfigurować dzienniki usługi Azure Monitor do monitorowania kontenerów: [Monitorowanie i diagnostyka kontenerów systemu Windows w sieci szkieletowej usług Azure.](service-fabric-tutorial-monitoring-wincontainers.md)
* Zobacz przykładowe problemy z diagnostyką i rozwiązania z siecią szkieletową usług: [diagnozowanie typowych scenariuszy](service-fabric-diagnostics-common-scenarios.md)
* Dowiedz się więcej o ogólnych zaleceniach dotyczących monitorowania zasobów platformy Azure: [Najważniejsze wskazówki — monitorowanie i diagnostyka.](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)