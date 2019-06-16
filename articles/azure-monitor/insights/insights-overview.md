---
title: Omówienie wglądu w szczegółowe dane w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Szczegółowe informacje zawierają dostosowane środowisko monitorowania, w usłudze Azure Monitor dla określonej aplikacji i usług. Ten artykuł zawiera krótki opis każdego z szczegółowe informacje, które są obecnie dostępne.
services: azure-monitor
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: bwren
ms.openlocfilehash: 8cb39a174c570b7019e872d731f49252a9505406
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66247232"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Omówienie wglądu w szczegółowe dane w usłudze Azure Monitor
Szczegółowe informacje zapewniają dostosowane środowisko monitorowania dla określonej aplikacji i usług. Przechowują dane w [platforma danych usługi Azure Monitor](../platform/data-platform.md) i korzystać z innych funkcji usługi Azure Monitor analizy i generowania alertów, ale może zbierać dodatkowe dane i zapewniają unikatowego interfejsu użytkownika w witrynie Azure portal. Uzyskiwanie wglądu w dane z **Insights** części menu usługi Azure Monitor w witrynie Azure portal.

Poniższe sekcje zawierają krótki opis szczegółowe dane, które są obecnie dostępne w usłudze Azure Monitor. Zobacz szczegółową dokumentację, aby uzyskać szczegółowe informacje na każdym.

## <a name="application-insights"></a>Application Insights
Usługa Application Insights jest rozszerzalną usługą zarządzania wydajnością aplikacji (APM) dla deweloperów sieci Web na wielu platformach. Użyj tej usługi do monitorowania aplikacji internetowej na żywo. Działa to w przypadku aplikacji na różnych platformach, w tym .NET, Node.js i Java EE hostowany lokalnych, hybrydowych lub dowolnej chmurze publicznej. Ponadto integruje się z procesem DevOps i ma punkty połączenia z szeroką gamą narzędzi programistycznych.

Zobacz [co to jest usługa Application Insights?](../app/app-insights-overview.md).

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Usługa Azure Monitor dla kontenerów
Usługa Azure Monitor dla kontenerów monitorów wydajności obciążeń kontenerów wdrożona albo w usłudze Azure Container instances lub zarządzane klastry Kubernetes hostowanych na platformie Azure Kubernetes Service (AKS). Monitorowanie kontenerów ma krytyczne znaczenie, szczególnie w przypadku korzystania z klastra produkcyjnego na dużą skalę z wieloma aplikacjami.

Zobacz [usługi Azure Monitor, aby uzyskać przegląd kontenerów](../insights/container-insights-overview.md).

![Usługa Azure Monitor dla kontenerów](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Usługa Azure Monitor dla grup zasobów (wersja zapoznawcza)
Usługa Azure Monitor dla grup zasobów ułatwia klasyfikowanie i diagnozowanie problemów napotykanych poszczególnych zasobów, jednocześnie oferując kontekstu dotyczącego kondycji i wydajności grupy zasobów jako całości.

Zobacz [monitorowanie grup zasobów za pomocą usługi Azure Monitor (wersja zapoznawcza)](../insights/resource-group-insights.md).

![Usługa Azure Monitor dla grup zasobów](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Usługa Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)
Usługa Azure Monitor dla maszyn wirtualnych monitoruje usługi Azure virtual machines (VM) i zestawy skalowania maszyn wirtualnych na dużą skalę. Analizuje ona wydajność i kondycję Twoich maszyn wirtualnych z systemami Windows i Linux oraz monitoruje ich procesy i zależności od innych zasobów oraz procesów zewnętrznych.

Zobacz [co to jest usługa Azure Monitor dla maszyn wirtualnych?](vminsights-overview.md)

![Usługa Azure Monitor dla maszyn wirtualnych](media/insights-overview/vm-insights.png)

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [platforma danych usługi Azure Monitor](../platform/data-platform.md) wykorzystywane przez szczegółowych informacji.
* Dowiedz się więcej o różnych [źródeł danych używanych przez usługi Azure Monitor](../platform/data-sources.md) i różne rodzaje danych zbieranych przez każdą wgląd w dane.
