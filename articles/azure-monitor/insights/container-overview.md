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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: magoedte
ms.openlocfilehash: d137576b4beb5cf36dce99ffb1869049f37b60b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60494647"
---
# <a name="overview-of-monitoring-containers-in-azure"></a>Omówienie monitorowania kontenerów na platformie Azure
Dzięki systemowi Azure można skutecznie monitorować i zarządzać obciążeń wdrożonych na kontenery usługi Azure Kubernetes lub Docker. Należy zrozumieć, jak działają kontenerów przy użyciu wielu aplikacji mikrousług w celu dostarczenia usługi reliable service w dużej skali i usługi monitorowania plan pomocy technicznej. Ten artykuł zawiera krótkie omówienie zarządzania i monitorowania pomagają zrozumieć ich możliwości platformy Azure i które są odpowiednie na podstawie własnych wymagań.

Za pomocą [usługi Azure Monitor dla kontenerów](container-insights-overview.md), możesz wyświetlić wydajności i kondycji infrastruktury kontenera systemu Linux w skrócie i szybko zbadać problemy. Dane telemetryczne są przechowywane w obszarze roboczym usługi Log Analytics i zintegrowane w witrynie Azure portal, gdzie możesz eksplorować, filtrować i segmentu zagregowane dane za pomocą pulpitów nawigacyjnych, aby śledzić obciążenie, wydajności i kondycji.  

Dla kontenerów działających poza hostowana usługa Kubernetes usługi Azure, usługi Log Analytics [rozwiązanie Windows i kontener platformy Docker](../../azure-monitor/insights/containers.md) ułatwia wyświetlanie oraz zarządzanie nimi hostach kontenerów Windows i platformy Docker. Z obszaru roboczego usługi Log Analytics można wyświetlić szczegóły spisu, wydajności i zdarzeń z węzłów i kontenery w środowisku. Możesz wyświetlić szczegółowe informacje o inspekcji polecenia używane z kontenerami pokazywanie i kontenerów można rozwiązać, wyświetlania i przeszukiwania scentralizowanych dzienników bez konieczności uzyskania zdalnego dostępu do hostów platformy Docker lub Windows.

Aby osiągnąć holistycznego lub monitorowania aplikacji end-to-end, wszelkich zależności czy jest on platformy Azure lub lokalnymi zasobami, należy monitorować za pomocą usługi Azure Monitor lub usługi Log Analytics.  Warstwa aplikacji powinny być włączone, aby można było dodać dodatkową warstwę świadomości kondycji, zarówno na poziomie platformy i aplikacji, za pomocą usługi Application Insights. Na poziomie platformy są zestawy SDK usługi Application Insights dla [Kubernetes]( https://github.com/Microsoft/ApplicationInsights-Kubernetes), [Docker](https://hub.docker.com/r/microsoft/applicationinsights/), i [usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights). Dla aplikacji mikrousług jest obsługa [Java](../../azure-monitor/app/java-get-started.md), [Node.js](../../azure-monitor/learn/nodejs-quick-start.md), [.NET](../../azure-monitor/app/asp-net.md), [platformy .NET Core](../../azure-monitor/app/asp-net-core.md), a także wiele innych [języków/platformy](../../azure-monitor/app/platforms.md). 

W przeciwnym razie problemy zaczną niezidentyfikowanych, może mieć wpływ na dostępność aplikacji i nie zostaną spełnione cele poziomu usługi.  
