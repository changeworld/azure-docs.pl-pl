---
title: Omówienie monitorowania kontenerów platformy Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie różnych metod dostępnych na platformie Azure na potrzeby monitorowania kontenerów na platformie Azure do szybkiego zrozumienia klastrów kondycji i dostępności.
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
ms.date: 09/24/2018
ms.author: magoedte
ms.openlocfilehash: 5b5e8442e6d6762fda875460544aa4369e54d49b
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2018
ms.locfileid: "52889025"
---
# <a name="overview-of-monitoring-containers-in-azure"></a>Omówienie monitorowania kontenerów na platformie Azure
Dzięki systemowi Azure można skutecznie monitorować i zarządzać obciążeń wdrożonych na kontenery usługi Azure Kubernetes lub Docker. Należy zrozumieć, jak działają kontenerów przy użyciu wielu aplikacji mikrousług w celu dostarczenia usługi reliable service w dużej skali i usługi monitorowania plan pomocy technicznej. Ten artykuł zawiera krótkie omówienie zarządzania i monitorowania pomagają zrozumieć ich możliwości platformy Azure i które są odpowiednie na podstawie własnych wymagań.

Za pomocą [usługi Azure Monitor dla kontenerów](container-insights-overview.md), możesz wyświetlić wydajności i kondycji infrastruktury kontenera systemu Linux w skrócie i szybko zbadać problemy. Dane telemetryczne są przechowywane w obszarze roboczym usługi Log Analytics i zintegrowane w witrynie Azure portal, gdzie możesz eksplorować, filtrować i segmentu zagregowane dane za pomocą pulpitów nawigacyjnych, aby śledzić obciążenie, wydajności i kondycji.  

Dla kontenerów działających poza hostowana usługa Kubernetes usługi Azure, usługi Log Analytics [rozwiązanie Windows i kontener platformy Docker](../../azure-monitor/insights/containers.md) ułatwia wyświetlanie oraz zarządzanie nimi hostach kontenerów Windows i platformy Docker. Z obszaru roboczego usługi Log Analytics można wyświetlić szczegóły spisu, wydajności i zdarzeń z węzłów i kontenery w środowisku. Możesz wyświetlić szczegółowe informacje o inspekcji polecenia używane z kontenerami pokazywanie i kontenerów można rozwiązać, wyświetlania i przeszukiwania scentralizowanych dzienników bez konieczności uzyskania zdalnego dostępu do hostów platformy Docker lub Windows.

Aby osiągnąć holistycznego lub monitorowania aplikacji end-to-end, wszelkich zależności czy jest on platformy Azure lub lokalnymi zasobami, należy monitorować za pomocą usługi Azure Monitor lub usługi Log Analytics.  Warstwa aplikacji powinny być włączone, aby można było dodać dodatkową warstwę świadomości kondycji, zarówno na poziomie platformy i aplikacji, za pomocą usługi Application Insights. Na poziomie platformy są zestawy SDK usługi Application Insights dla [Kubernetes]( https://github.com/Microsoft/ApplicationInsights-Kubernetes), [Docker](https://hub.docker.com/r/microsoft/applicationinsights/), i [usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights). Dla aplikacji mikrousług jest obsługa [Java](../../application-insights/app-insights-java-get-started.md), [Node.js](../../application-insights/app-insights-nodejs-quick-start.md), [.Net](../../application-insights/app-insights-asp-net.md), [.Net Core](../../application-insights/app-insights-asp-net-core.md), a także wiele innych [języków/platformy](../../application-insights/app-insights-platforms.md). 

W przeciwnym razie problemy zaczną niezidentyfikowanych, może mieć wpływ na dostępność aplikacji i nie zostaną spełnione cele poziomu usługi.  
