---
title: Omówienie aplikacji Insights w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Szczegółowe informacje zapewniają niestandardowe środowisko monitorowania w usłudze Azure Monitor dla określonych aplikacji i usług. Ten artykuł zawiera krótki opis każdego z szczegółowych informacji, które są obecnie dostępne.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: 15ea7698c9e90fa8b0dfa20f71b552a2b0e9c7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657252"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Omówienie aplikacji Insights w usłudze Azure Monitor
Szczegółowe informacje zapewniają dostosowane środowisko monitorowania dla określonych aplikacji i usług. Przechowują dane na [platformie danych Usługi Azure Monitor](../platform/data-platform.md) i wykorzystują inne funkcje usługi Azure Monitor do analizy i alertów, ale mogą zbierać dodatkowe dane i zapewniać unikatowe środowisko użytkownika w witrynie Azure portal. Uzyskaj dostęp do szczegółowych informacji z sekcji **Insights** w menu Azure Monitor w witrynie Azure portal.

Poniższe sekcje zawierają krótki opis szczegółowych informacji, które są obecnie dostępne w usłudze Azure Monitor. Szczegółowe informacje na temat każdego z nich można znaleźć w szczegółowej dokumentacji.

## <a name="application-insights"></a>Application Insights
Usługa Application Insights jest rozszerzalną usługą zarządzania wydajnością aplikacji (APM) dla deweloperów sieci Web na wielu platformach. Użyj tej usługi do monitorowania aplikacji internetowej na żywo. Działa dla aplikacji na wielu różnych platformach, w tym .NET, Node.js i Java EE, hostowanych lokalnie, hybrydowych lub dowolnej chmury publicznej. Integruje się również z procesem DevOps i ma punkty połączenia z różnymi narzędziami programistycznymi.

Zobacz [Co to jest usługa Application Insights?](../app/app-insights-overview.md).

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Usługa Azure Monitor dla kontenerów
Usługa Azure Monitor dla kontenerów monitoruje wydajność obciążeń kontenerów wdrożonych w wystąpieniach kontenerów platformy Azure lub zarządzanych klastrach kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS). Monitorowanie kontenerów ma kluczowe znaczenie, zwłaszcza podczas uruchamiania klastra produkcyjnego na dużą skalę z wieloma aplikacjami.

Zobacz [Azure Monitor dla kontenerów omówienie](../insights/container-insights-overview.md).

![Usługa Azure Monitor dla kontenerów](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Usługa Azure Monitor dla grup zasobów (wersja zapoznawcza)
Usługa Azure Monitor dla grup zasobów pomaga klasyfikować i diagnozować wszelkie problemy napotykane przez poszczególne zasoby, jednocześnie oferując kontekst kondycji i wydajności grupy zasobów jako całości.

Zobacz [Monitorowanie grup zasobów za pomocą usługi Azure Monitor (wersja zapoznawcza)](../insights/resource-group-insights.md).

![Usługa Azure Monitor dla grup zasobów](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Usługa Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza)
Usługa Azure Monitor dla maszyn wirtualnych monitoruje na dużą skalę maszyny wirtualne platformy Azure i zestawy skalowania maszyn wirtualnych. Analizuje ona wydajność i kondycję Twoich maszyn wirtualnych z systemami Windows i Linux oraz monitoruje ich procesy i zależności od innych zasobów oraz procesów zewnętrznych.

Zobacz [Co to jest usługa Azure Monitor dla maszyn wirtualnych?](vminsights-overview.md)

![Usługa Azure Monitor dla maszyn wirtualnych](media/insights-overview/vm-insights.png)

## <a name="azure-monitor-for-networks-preview"></a>Usługa Azure Monitor dla sieci (wersja zapoznawcza)
[Usługa Azure Monitor for Networks](network-insights-overview.md) zapewnia kompleksowy widok kondycji i metryk dla wszystkich zasobów sieciowych. Zaawansowane funkcje wyszukiwania ułatwiają identyfikowanie zależności zasobów, umożliwiając scenariusze, takie jak identyfikowanie zasobów hostujących witrynę, po prostu wyszukując nazwę witryny.

![Usługa Azure Monitor dla sieci](media/insights-overview/network-insights.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [platformie danych usługi Azure Monitor](../platform/data-platform.md) korzystającej z szczegółowych informacji.
* Dowiedz się więcej o różnych [źródłach danych używanych przez usługę Azure Monitor](../platform/data-sources.md) i różnych rodzajach danych zebranych przez każdy z szczegółowych informacji.
