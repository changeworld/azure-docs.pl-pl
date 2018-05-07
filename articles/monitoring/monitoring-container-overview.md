---
title: Omówienie usługi Azure kontenery monitorowania | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie różnych metod dostępnej na platformie Azure, aby monitorować kontenerów na platformie Azure, aby szybko poznać klastrów kondycji i dostępności.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2018
ms.author: magoedte
ms.openlocfilehash: 0d511c1f6dfd482e5754741da15b2852ee77c11e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="overview-of-monitoring-containers-in-azure"></a>Omówienie monitorowania kontenerów na platformie Azure
Przy użyciu platformy Azure można efektywnie monitorować i zarządzać nimi obciążeń wdrożonych na Azure kontenery systemem Kubernetes lub Docker. Należy zrozumieć, jak działają kontenery z wieloma aplikacjami mikrousługi w celu dostarczenia niezawodnej usługi na dużą skalę i monitorowanie planu obsługi. Ten artykuł zawiera krótkie omówienie zarządzania i możliwości monitorowania na platformie Azure, aby pomóc zrozumieć każdy i które są odpowiednie zgodnie z wymaganiami.

Przy użyciu [kondycji kontenera Azure Monitor](monitoring-container-health.md), możesz wyświetlić wydajność i kondycję infrastruktury kontenera systemu Linux w skrócie i szybko badania problemów. Dane telemetryczne są przechowywane w obszarze roboczym analizy dzienników i zintegrowane w portalu Azure, w którym można sprawdzić, filtrowanie, a segment zagregowanych danych za pomocą pulpitów nawigacyjnych, aby śledzić ogólne informacje dotyczące ładowania, wydajności i kondycji.  

Dla kontenerów uruchomiona poza Azure Kubernetes usługi hostowanej, analizy dzienników [systemu Windows i kontener Docker rozwiązania](../log-analytics/log-analytics-containers.md) ułatwia wyświetlanie i zarządzanie nimi hosty kontenera systemu Windows i Docker. Z obszaru roboczego analizy dzienników można wyświetlić szczegóły spisu, wydajności i zdarzeń z węzłów i kontenery w środowisku. Można wyświetlić szczegółowe informacje o inspekcji przedstawiający poleceń używanych przez kontenery, i rozwiązać kontenery wyświetlanie i wyszukując scentralizowane dzienniki bez konieczności zdalny dostęp do hostów Docker lub systemu Windows.

Uzyskanie całościowe lub monitorowania aplikacji end-to-end, wszelkie zależności czy jego platformy Azure lub lokalnie zasobów, powinny być monitorowane Azure Monitor lub analizy dzienników.  Warstwa aplikacji powinny być włączone, aby można było dodać dodatkową warstwę świadomości kondycji, zarówno na poziomie platformy i aplikacji przy użyciu usługi Application Insights. Na poziomie platformy jest Application Insights SDK dla [Kubernetes]( https://github.com/Microsoft/ApplicationInsights-Kubernetes), [Docker](https://hub.docker.com/r/microsoft/applicationinsights/), i [sieci szkieletowej usług](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights). Dla aplikacji mikrousługi jest obsługa [Java](../application-insights/app-insights-java-get-started.md), [Node.js](../application-insights/app-insights-nodejs-quick-start.md), [.Net](../application-insights/app-insights-asp-net.md), [.Net Core](../application-insights/app-insights-asp-net-core.md), a także wiele innych [języków/platform](../application-insights/app-insights-platforms.md). 

W przeciwnym razie problemów Przejdź niezidentyfikowanego który może mieć wpływ na dostępność aplikacji i nie zostaną spełnione cele poziomu usługi.  
